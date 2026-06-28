# Hi, I'm Misha 👋

### Senior Backend Engineer (Go) · High-Scale Distributed Systems

[![Open to Work](https://img.shields.io/badge/Open_to_Work-Senior_Go_Backend_·_Remote-2ea44f?style=flat-square)](mailto:mikhail.n.kuznetsov@gmail.com)
[![Email](https://img.shields.io/badge/Email-mikhail.n.kuznetsov-26428b?style=flat-square&logo=gmail&logoColor=white)](mailto:mikhail.n.kuznetsov@gmail.com)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-mikenk-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://linkedin.com/in/mikenk)
[![Telegram](https://img.shields.io/badge/Telegram-mikhailnkuz-26A5E4?style=flat-square&logo=telegram&logoColor=white)](https://t.me/mikhailnkuz)

I build and operate **high-scale backend systems in Go** — the kind where latency, reliability and correctness directly move the product. 6+ years across large marketplaces and ride-hailing (**Ozon**, **Avito**, **inDrive**), payments & antifraud, and infrastructure / SRE. I like the hard, low-level parts: profiling, sharding, connection pooling, observability, and making services boringly reliable under load.

> 🔭 **Open to work** — Senior Go backend / infra / SRE roles, **remote**. Most recently at inDrive on the Pricing Platform. CV available on request — just email me.

## 🚀 What I've shipped

- 🗄️ **Zero-downtime database split** — split a ~1 TB monolithic database into **24 shards** with no downtime at **Ozon**, then sped up Kafka consumers/producers **2.5×** under tight resource quotas.
- 🌍 **Real-time pricing at global scale** — owned services behind inDrive's *recommended price* across a system spanning **14 AWS EKS clusters**; added metrics, tracing and dashboards that sharply cut on-call time-to-diagnose.
- 🛡️ **Payment antifraud from scratch** — built scoring covering **50%+ of payment traffic** at **Avito** (FinTech), and fixed a hidden PgBouncer/PostgreSQL bug that took daily credit-score recompute for **2M+ users** from **75% → 99.99%**.
- 📈 **Scaled a Go service to 22M DAU** — rewrote a Telegram backend from a TypeScript MVP to Go (profiled with pprof / Jaeger), sustaining **200K concurrent** users; redesigned a **2.5 PB** ScyllaDB store down to ~100 TB.
- 🔐 **DevSecOps by default** — GitOps with secret-scanning and linting gates in CI (`gitleaks`, `shellcheck`, `hadolint`), minimal hardened images. Honestly, I think most of this should just be every engineer's baseline.
- ⛓️ **Blockchain infra (earlier on)** — tech-led two EVM chain forks on modified `go-ethereum`, plus multi-chain backend across EVM / Solana / TON. Fun systems work — these days my focus is mainstream high-scale backend & infra.

## 🛠️ Tech I reach for

**Languages:** Go (primary) · SQL · Python · TypeScript · a bit of Rust & C/C++
**Data stores:** PostgreSQL (PgBouncer / Odyssey) · ScyllaDB · Kafka · Redis · ClickHouse · MongoDB
**Infra & orchestration:** AWS · Kubernetes (EKS) · Helm · ArgoCD · Terraform · Temporal · Docker · Vault
**Observability & CI/CD:** Prometheus · Grafana · Thanos · Jaeger · OpenTracing · GitHub Actions · GitLab CI

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
