# Ludix – Open Infrastructure for Game Distribution (Work in Progress)

> **Status:** Phase 0 – Architecture, design, and documentation.  
> No production-ready code exists yet. The goal of this early phase is to build  
> a solid conceptual and technical foundation before writing a single line of code.

---

## 🚀 What is Ludix?

**Ludix** is an open-source, censorship-resistant infrastructure for game distribution.  
It allows players to pay developers **directly**, without intermediaries like:

- payment processors (Stripe, PayPal),
- card networks (Visa, Mastercard),
- or centralized storefronts.

Payments are made using **stablecoins**, and Ludix never touches user funds.  
Developers also host their own builds (or use decentralized storage).  
Ludix simply provides the protocol, tooling, and reference implementation.

This project is not a marketplace business.  
It's **infrastructure** for a free and open game economy.

---

## 🎯 Why does Ludix exist?

Recent events have shown that large financial institutions can pressure stores  
to remove or block entire categories of games — not because they are illegal,  
but because they conflict with corporate policies or lobby pressure.

In practice:

> **If a card processor doesn’t like your game, it may disappear.**

This is not sustainable.  
Ludix is designed to make the distribution of digital games **independent**  
from corporate financial censorship.

For the full philosophical context, see:  
`README.es.md` and `docs/es/context-censorship.md`.

---

## 🧩 What does Ludix include?

Ludix is composed of several modular components:

### **1. Ludix Core API (backend)**
- FastAPI + PostgreSQL (proposed)
- Users, developers, games, builds, payments, trust/reputation

### **2. Chain Watcher**
- Listens to on-chain stablecoin transfers
- Verifies valid payments to developer wallets
- Notifies the backend of confirmed purchases  
- Ludix never holds custodial funds

### **3. Launcher (desktop)**
- Built with Tauri + React
- Login, game catalog, purchasing, downloads, library
- Downloads come directly from the developer’s host

### **4. Developer Portal**
- Publish and manage games
- Register builds (with hashes)
- Configure prices, wallets, metadata

### **5. TrustChain**
- Multi-layer developer verification:
  - domain ownership,
  - public keys,
  - signed builds,
  - reputation,
  - optional integrations (GitHub, itch.io, Steam Partner)
- Prevents impersonation and fraudulent publishers

### **6. Local Payment Gateways (optional)**
- Plugins for systems like Pix, UPI, MeliDólar, etc.
- Each region can add its own payment methods without touching core logic
- Gateways call the backend using the same interface as the Chain Watcher

---

## 🛡 Project Principles

- **Direct payments**: player → developer  
- **Zero platform fees**: Ludix never takes a cut  
- **Non-custodial**: Ludix does not hold user or developer funds  
- **Open-source first**: auditable, forkable, self-hostable  
- **No token requirement**: no native coin, no “Web3 tax”  
- **Decentralization by architecture**, not by hype  
- **Freedom with responsibility**:
  - illegal content is not allowed  
  - legal but controversial content should not be censored by banks  

---

## 📌 Project Roadmap (High-Level)

### **Phase 0 – Foundations (current)**
- Documentation
- Architecture design
- Specifications
- Repository and structure setup

### **Phase 1 – MVP**
- Backend
- Basic Chain Watcher
- Launcher MVP
- Developer Portal MVP

### **Phase 2 – TrustChain**
- Developer verification
- Reputation system
- Restrictions for external key selling (Steam Keys, etc.)

### **Phase 3 – Local Gateways**
- Plugin framework
- Example gateway (mock)
- First real regional gateway

### **Phase 4 – Ecosystem**
- Community contributions
- Full English documentation
- Production deployments

---

## 📘 Documentation

Full documentation is currently being drafted in **Spanish**:

- `README.es.md`  
- `docs/es/*`  

English translations will be added progressively under:  

- `docs/en/*`

---

## 🤝 Contributing

We are **not accepting major code contributions yet**  
(because there is no stable base to build on).

However, you can contribute by:

- discussing architecture and design,  
- reviewing flows,  
- helping with documentation,  
- planning gateways and modules,  
- UX/UI proposals for the launcher,  
- branding contributions (colors, identity).

When the first stable code appears:

- `CONTRIBUTING.md` will be updated,
- good-first-issues will be published,
- coding standards will be documented.

---

## 📄 License

The license has not been finalized.  
The expected options are:

- MIT  
- Apache 2.0  
- GPLv3  

This section will be updated before the first major code commit.

---

## ❓ FAQ

### **Will Ludix have its own token?**
No.  
Ludix rejects token-based dependency.  
Any future token experiment will be optional and non-core.

### **Is Ludix a marketplace?**
No.  
It is **infrastructure**, not a commercial storefront.

### **Can developers sell Steam Keys?**
Yes — but **only verified developers** (TrustChain).  
No third-party resellers.

### **Does Ludix allow illegal content?**
No.  
Illegal content is not accepted.  
Legal but controversial content is allowed, within reasonable boundaries.

### **Where is the code?**
Coming soon.  
Phase 0 is dedicated to design so the code can be built once — correctly.

---

## 🌟 Final note

Ludix exists for one core purpose:

> **To ensure no game disappears from the world simply because  
> a financial corporation decides it should.**

If this mission resonates with you — as a gamer, a developer, or an open-source enthusiast —  
you are welcome here.
