# Saga

The Saga design pattern is one of those concepts that can be challenging to grasp initially. I struggled with it myself, as I’m sure many other engineers do. However, after working in Avito, where the entire FinTech system is essentially a 3-tiered saga, I’ve developed a much deeper understanding of this pattern.

In this article, I’ll share insights from my experience, walk you through common pitfalls, and provide a practical perspective on how to approach sagas effectively.

## What problem do we constantly have while doing network IO?

I would define it as follows. We have 2 network calls:

```txt
- call (A) - some call to other microservice. Let's say, it is a JSON RPC call
to EVM Full Node API issuing transaction broadcast.

- call (B) - some database RDBMS (PostgreSQL for simplicity) statement.

like:
UPDATE, INSERT, DELETE ...
At least in PSQL any single statement is wrapped in transaction.

Usually, it may be something more difficult. Let's say:
BEGIN;
SELECT FOR UPDATE ... some row
if the value > 0:
update
else
ROLLBACK;
COMMIT;
```

## Question: is it possible to make this atomic?

Short answer: no.

Unsigned Short: no, absolutely no, do not every try.

## But let's try anyway and start with naive solution

### Naive solution. Ok, let's call network inside tx. If anything goes wrong,
we rollback

tldr: this is a fatal mistake 🙂 Let me show you why.

```txt

(A) BEGIN;
    (B) network call
(A) COMMIT / ROLLBACK;

^^ at the first glance, everything is may seem to be fine here. But the devil is in details.
```

1. How long network call will take? Do we have any guarantees (SLAs)?
If it takes too long and there are enough such requests, PSQL will hang,
because there are not anough connections available to the DBs.

2. Why are we so sure application will not just shut down after B? No,
really. And what will we have? Best case: uncommited tx, but successfull
rpc call. At worst: again, DB will hang, for example if there are many
txes stuck on this commit/rollback step.

3. Ok, even if everything works fine, what if we have network segmentation?
i.e. network is in principle unreliable (with all due regard to network
engineers, this is just an assumption). What if we have a 'slow-lorry'
style attack?

4. Also think about resilience and cascade failures. Here DB / network call
are so tigthlyh coupled, that if one fails, this will lead to cascadee
failure. Even if e.g. (B) works perferctly fine and there are some DB
issues.

5. Also, here we have at least 3 round trips - do we actually want them?
We want to just (1) update db; (2) do rpc (in any order). This scheme
is so notorious for having low performance, that it is absolutely forbidden by
Ozon DBAs.

Also, no fancy stuff will fix this schema. To name a few:

* BEGIN STATEMENT TIMEOUT 10 sec; - generally, to me this is a good
practice, also this is a decent starting point. However, still, it
doesn't really solve anything. And for high load apps, in my opinion, such
timeouts are a bad idea.

* If you are writing golang, `defer func()  { ... tx }()` will not help
either, as defer is not some kind of magic and won't help with network
call received, but not acked.

### What is _network segmentation_? Why network is _unreliable_?

- This is actually quite simple concept. In my opinion, from the developer PoV
it should be assumed that any network call is unreliable and may fail.

-  Demo 1. Simple case. Network call fails.

```txt

[Server A] ---- RPC REQUEST ---> [Server B]

... B has successfully received the request
...  it handles the request. everything went nice.
... B builds response

[Server A] <--✂️-----RPC RESPONSE ---[Server B]
^^ but something happens with the network and response is never
received. This illustrates simple network segmentation.

Response could also be delievered partially.
Or, let's say, server A is shut down.

As you see, many things could go wrong with any network call.

In professional data centers these troubles are not so commmon, but the
traffic is so high, that:

if that chance of network seg. is 1e-6 (0.000001%)
and we handle 1e+6 (1.000.000) transactions, 1 transaction will be
lost/corrupted.

```

- Demo 2. More realistic example. Network calls succeeds, but we can't accept it

```txt

[Server A] --- RPC REQUEEST -> [Server B]
timeout = 1±9ms second
^^ 🚩 in real life, all requests have some sort of timeouts or deadlines.

This is common sense both for the:

- in-cluster trusted APIs (in this case timeout may be set accoirding to known service NFRs)
- external  APIs (usually set under some SLA or just by common sense, as external network = PvP enabled 💀)

📘 SRE Hint: also, jitters are quite a good idea.

Alright, we made an RPC request. Everything is perferctly fine, but
Server B is just slow. It received our request, executed logic perfectly
fine, but time has already run out, A closed the socket (context deadline
exceeded) and B can never deliver response to A.

This is also extremely common in production e.g. when we are rolling new release
with canary. Very small, but noticable percent of requests is usually  lost,
because the pod that had received them is dead and is replaced by the new one.
```

## Do we have any hope?

Yes, we have, and the solution is actually quite simple and elegant.
However, please, note. It will  introduce some complexity.

Before reading to the solution, please, consider the following:

1) Do you really need to have such strong atomicity? I mean, maybe you are
developing in such domain, where it doesn't really matter. Also, these small
amount of failed txes could be recovered - either by logs or by some automation.

2) Can the system (network calls) designed in such a way, that  is safe to
retry?

```txt
rpc call. __idempotent__. We can retry it as many time as we want, until
we are sure everything is fine.

db transaction.
```


In my opinion, the 2nd solution is preferred. Although it's still not always
possible.

## Saga

> The term “saga” refers to Long Lived Transactions (LLT). The name “SAGA” comes from the concept of a long story with many parts, just like a distributed transaction. In a SAGA, each part of the story is a local transaction, and together, they form the complete story.

Alright, let's define everything bit by bit.

### What is transaction?

-  First, let's figure our what is _transaction_ - a quite popular notion.

* RDBMS transaction (like begin/commit/rollback). Yes, but this is too
narrow. Transactions are possible even without RDBMS.

* arbitrary financial action/will (in olschool fintech) or special public
actions (e.g. native transfer,  contract calls, anything) in DeFi? No,
also too narrow, limits scope to the very specific domains.

* ⭐ some workflow in a single program or system of several programs (e.g. services).
Sometimes this workflow may represent some end-to-end business logic, or
just logically bound sequence of actions.  This workflow is intended to
be _atomic_, have some kind of _lifecycle_ - like start, phase 1,2,3 and
finish. It should not be arbitrary lost - every action matters

I suggest us the 3rd definition. Let me illustrate it with my experience from
Avito.

User enters website URL and logins to the website. Fills in cart with some
items and clicks 'checkout'. On the checkout page user is redirected to the
secure form  (payment gateway), user enters credit card details, clicks
'ok', redirected to wait page, waits a little and volia - the user sees the
happy green message that everything went well. - this is a _simplified_
example from my Avito job.

Here there is clearly a single transaction - payment for goods. But where does
it start? On the click of 'checkout' button? Alright, let's assume so. But when
does it end? When we show the green screen?

The truth is that this whole example is a complex transaction that must be
carried out carefully without losing any step. On the checkout page, there is
already some cart - we can't lose an item. Also after the payment there are
delivery, return of item A withing a week, return of item B within 2 weeks - all
under same transaction.

In this tx, at least ~10 services are directly involved each of them having very
different DBs - some of them do not even support transactions :) There is an
also complex async flow and messages - yet, we still call this business tx, and
it works without major errors.

> To conclude.
>
> When we are talking about `sagas` and generally in system design,
> by _transaction_ we mean much more than just a single db.
>
> We mean being able to provide atomicity beyond single database entity.
> We may _not even care about transactions in a single DB_, the goal is to just
> provide good UX and solve business problem.

### How make something with 10 network calls _transactional_?

Here, let's take a simpler example and make it into sage.

```txt

Logic:
1. create item in database of A
2. check item price in B. if price is too low/high -> item is not
available for purchase.
3. check items measurements. are they ok?  if not, item is not
available.

4. send message to some service

5. wait for async notification. if everything is ok, nice, item is
createdf.
Commit? Rollback?
```

- Suppose we need to design item creation flow. Logic is quite complex, handler
`HTTP POST /v1/item/create` must be fast (p99 <100ms). Here saga seems like
a reasonable solution.

- My checklist of calling anything saga
    - we have complex transaction
    - some steps are async
    - steps are spread in many microservices. No single HTTP handler can do this
    operation transactionally and fast.
    - there is some lifecycle to entity or we have __state machine__

#### Orchestration based Saga

- I think, this one is easiest to understand and most intuitive.

- The idea is that if we have this complex multi-step logic and we clearly
building state machine, let's create a service what will be responsible for this
workflow.

- Any network call may fail. But failures are recoverable, maybe we can ask user
even to _compensate_ them (e.g. put correct price and item will be created).

```txt
saga_name: Item Creation

[Saga Orchestrator] <-- Databse (state_machine)
[ Item_id, state, error, retries, created_at, updated_at ]
1     created  nulll    0       2021-01-01  2021-01-02
2     created  nulll    0       2021-01-01  2021-01-02
3     checking_price  network err    3       2021-01-01  2021-01-02
4     created  nulll    0       2021-01-01  2021-01-02
5     created  nulll    0       2021-01-01  2021-01-02
```

Step 1. Define a saga orchestration service. This service has a (usually) RDBMS
that allows transactional updates. This service stores  the state of transaction
(in this case - sate of 'item' being created).

```txt
saga_name: Item Creation

[Saga Orchestrator] <-- Databse (state_machine)
[ Item_id, state, error, retries, created_at, updated_at ]
3     checking_price  network err    3       2021-01-01  2021-01-02
^^^ 🚩

either service itself, some timers or other service checks this item.
and pushes it thgough pipeline of state machine.

here, it is safe:

1. rpc call
2. only if full success, update item
```

Step 2.  The idea is to push items through a state machine. Each network call
may fail, but we should (where appropriate) retry those fails. The failure may
be terminal.

```txt
saga_name: Item Creation

[Saga Orchestrator] <-- Databse (state_machine)
[ Item_id, state, error, retries, created_at, updated_at ]
3     checking_price   invalid_price       2021-01-01  2021-01-02
^^^ 🚩
```

Step 3.   Sometimes either as a part of logic, or by accident there may be some
invalid (undesired) state for item. This way,  we may issue _compensating_* call.

Also, because this is just a stable and some service, we may subscribe to async
events and via transactional out/in-boxes update these items in transaction.

* - compensating calls are sometimes used in the other meanings. I will
leave them out of the scope of the article.

### Choreography based Saga

Choreography saga derives from the same ideas, but as in microservices
architecture it is assumed each service has its own database, in theory,
services could just communicate without needing a central orchestration service.

In this approach, services communicate directly using events (e.g., via Kafka).
Each service updates its state independently and triggers subsequent steps by publishing events.

There is a lot said about the pros and cons of each, but here also team dynamics
plays a major role, in my opinion. Also this saga (to me) seems a much harder to
reason about and implement.

While this eliminates the need for a central orchestrator, it is harder to reason about and debug.
Thus, I will omit this saga from this quick tutorial.

## So what is a saga?

- (?) just a state machine in PostgreSQL 🙂 that helps us solve very hard
problems with network being unreliable?

- this state machine also tracks `retries`, `state`, `reason`, etc. significant
things that allow us to do our best to finish each tx gracefully.

- states and clear logic also allow us to introduce _compensating actions_

## Why is saga important?

- Generally, I would describe Avito fintech as a large 3-layerd saga :)
Despite the fact that [public Avito book has specifically recommended to avoid
this pattern (as far as it is stated, due to high complexity)](https://github.com/avito-tech/playbook/blob/master/development-principles.md) or at least seriously
warns about it.

  However, I would say for fintech this architecture with orchestration based
  saga is quite appropriate and significantly simplifies lots of things.

- I have used PSQL 'queues'  quite a lot in past, and  lots of them would
qualify as saga, because they included some other service acting in its own
db transactionally and this event fixating in the 'queue'.

  On LinkedIn I mention 'Product Cards Transfer' products that I've contributed
  in Ozon.  And this project essentially was a classical, textbook orchestration
  based saga. CMS (system that actually created items there) = pure saga too!

  We just called them 'state machines'. At the same time, I find value in using
  the specific notions other engineers understands. Also, 'state machine' has
  kind of the different connotation, this is not a notion that describes the
  architectural level.

  Below is the project description from LinkedIn:

        Ozon | Product Cards Transfer
        One of our team's public flagship products — items cards transfer — solves major pain point of many sellers. Before that, in case of any M&A or even legal status change sellers would have to manually transfer each card from 1st account to the 2nd. This was especially painful for the top sellers with 10M+ cards.

        Now our service makes this a one-click UX. The game-changer is that we also properly transfer positive reviews & ratings - that was not possible in past & required lots of eng. effort 🔥

    > `properly transfer positive reviews & ratings - that was not possible in past & required lots of eng. effort`
    This communication was async messages that our service received somewhere.
    Also the transactions (like in Avito) were nested.

    The seller made a `request` - this transaction had its own saga.
    But also each `request` had >=1 items - each of them was also controlled in
    a same way in a same service.

- This way, I have worked with sagas almost since day 1 at my professional life. 🙂
But understood them really well only after 3 years into it.

## Credits

- [microservices.io - Saga](https://microservices.io/patterns/data/saga.html)
