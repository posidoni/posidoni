# Hi, my name is Mikhail

### Senior Backend Engineer (Go) · High-Scale Distributed Systems

[Email](mailto:mikhail.n.kuznetsov@gmail.com) · [LinkedIn](https://linkedin.com/in/mikenk) · [Telegram](https://t.me/mikhailnkuz)

I build and operate **high-scale backend systems in Go** — the kind where latency, reliability and correctness directly move the product. 6+ years across large marketplaces and ride-hailing ([@ozontech](https://github.com/ozontech), [@avito-tech](https://github.com/avito-tech), [@inDriver](https://github.com/inDriver)), crypto & Web3 (Blum, GotBit), payments & antifraud, and infrastructure / SRE. I like the hard, low-level parts: profiling, sharding, connection pooling, observability, and making services boringly reliable under load.

> 🔭 **Open to work** — Senior Go backend / infra / SRE roles, **remote**. Most recently at inDrive on the Pricing Platform. CV available on request — just email me.

## 🚀 What I've shipped

- 🗄️ **Zero-downtime database split** — split a ~1 TB monolithic database into **24 shards** with no downtime at **[Ozon](https://en.wikipedia.org/wiki/Ozon)** — one of Russia's largest e-commerce platforms (NASDAQ IPO 2020) — then sped up Kafka consumers/producers **2.5×** under tight resource quotas.
- 🌍 **Real-time pricing at global scale** — owned services behind inDrive's *recommended price* across a system spanning **14 AWS EKS clusters**; added metrics, tracing and dashboards that sharply cut on-call time-to-diagnose.
- 🛡️ **Payment antifraud from scratch** — built scoring covering **50%+ of payment traffic** at **[Avito](https://en.wikipedia.org/wiki/Avito.ru)** — one of the world's most-visited classifieds platforms — and fixed a hidden PgBouncer/PostgreSQL bug that took daily credit-score recompute for **2M+ users** from **75% → 99.99%**.
- 📈 **Scaled a Go service to 22M DAU** — at **Blum**, a Telegram-based crypto exchange, rewrote the launcher-bot backend from a TypeScript MVP to Go (profiled with pprof / Jaeger), sustaining **200K concurrent** users; shipped a Telegram Stars payment gateway and redesigned a **2.5 PB** ScyllaDB store down to ~100 TB.
- 🔐 **DevSecOps by default** — GitOps with secret-scanning and linting gates in CI (`gitleaks`, `shellcheck`, `hadolint`), minimal hardened images. Honestly, I think most of this should just be every engineer's baseline.
- ⛓️ **Blockchain infra & multi-chain backend** — at **GotBit**, a major crypto market maker (140+ specialists, 50+ token launches), tech-led two live EVM chain forks on modified `go-ethereum` (managed across 24 servers), and shipped backend across EVM / Solana / TON — including a Dedust (TON DEX) SDK that enabled market-making on the network.
- 🤖 **AI-agentic open-source tooling** — built [shell-skill](https://github.com/posidoni/shell-skill), where every rule ships as a CI-checked runnable example instead of asserted advice; content was produced through a multi-agent research workflow (parallel agents against primary sources — kernel man pages, official docs — under a cite-or-don't-write-it discipline) and packaged as an installable Claude Code plugin.

## 🛠️ Tech I reach for

**Languages:** Go (primary) · SQL · Python · TypeScript · a bit of Rust & C/C++
**Data stores:** PostgreSQL (PgBouncer / Odyssey) · ScyllaDB · Kafka · Redis · ClickHouse · MongoDB
**Infra & orchestration:** AWS · Kubernetes (EKS) · Helm · ArgoCD · Terraform · Temporal · Docker · Vault
**Observability & CI/CD:** Prometheus · Grafana · Thanos · Jaeger · OpenTracing · GitHub Actions · GitLab CI
**Web3:** EVM & `go-ethereum` internals · Solidity · Solana · TON · DEX & ERC-20/721/1155 integrations
**AI-agent tooling:** Claude Code · multi-agent research workflows · agent skills

## ☕ Beyond work

I actually love this stuff outside of a job, too.

- I share [Primeagen](https://www.youtube.com/@ThePrimeagen/videos)'s philosophy — the more you do something, the better you get, the more interesting it becomes. I've been lucky to have basically zero boring projects (well, except coding a `decimal.h` library in C11 with bit-shifts once — though I then used `decimal` types every day, so maybe that's karma 😁).
- [TJ](https://www.youtube.com/@teej_dv/videos)'s talks pushed me to give Neovim a real shot a few years back. Been a happy user ever since — I can appreciate other editors, but Neovim is just too good (and I love what the team does with the architecture and RPC protocol).
- Still my favourite take on how the internet works: [*"POV: I'm on my third coffee and you just asked me how the internet works"*](https://www.youtube.com/watch?v=jjKFXlFNR4E&t=231s).
- I gave Nix & NixOS a fair 3-week chance, but I'm still not sure they solve the problems they claim to. (Maybe I missed a proper intro — who knows.)

## ✍️ Writing

- [My Journey to Understanding Saga (2025-01-13)](./blog/My_Journey_To_Understanding_Saga_2025_01_13_07_25.md)
- [Discord Antifraud UX — Case Study](./blog/Discord_Antifraud_UX_Reverse.md)

## 📫 Reach me

Always happy to talk shop, take feedback, or just say hi. Errata appreciated too 🙏

- 📧 **Email:** [mikhail.n.kuznetsov@gmail.com](mailto:mikhail.n.kuznetsov@gmail.com)
- 💬 **Telegram:** [@mikhailnkuz](https://t.me/mikhailnkuz)
- 💼 **LinkedIn:** [in/mikenk](https://linkedin.com/in/mikenk)

**Languages:** English — C2 · Russian — Native

---

<sub>All opinions are my own and not the views of any current or past employer, contract counterparty, or other affiliated entity.</sub>
