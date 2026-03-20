# Ludix Vision

> **Status:** Phase 0 — Living document. It will expand with the community.

---

## 1. The problem no one should have to solve

In 2020, Mastercard pressured Pornhub to remove content. Within days, millions of files disappeared.  
In 2021, Stripe and PayPal began cutting services to "high-risk" adult content platforms.  
At various points between 2019 and 2023, itch.io, Steam, and other game stores received direct or indirect pressure from payment processors to remove legal NSFW games, games with politically sensitive themes, or simply games that someone in a risk department deemed "inappropriate."

There were no trials. There were no new laws. There were no votes.

There were emails. There were threats to cut off payment processing. And the platforms, which depend on that processing to survive, obeyed.

The result was always the same:

- Developers who lost their source of income overnight.
- Players who opened their library and found gaps where games used to be.
- Communities that saw years of work disappear without a real explanation.

**That is financial censorship.** It doesn't matter what it's called in corporate press releases.

Ludix exist so this cannot happen again. At least, not so easily.

---

## 2. Why current solutions don't solve the problem

### Traditional stores (Steam, itch.io, GOG)

They are centralized platforms that depend on traditional payment processors to operate.  
No matter how much goodwill their teams have, they are structurally tied to Visa, Mastercard, Stripe, and the like.  
If those companies push, the stores yield — because they cannot afford not to without losing their ability to collect payments.

The problem is not the bad faith of Steam or itch.io.  
The problem is the **structural dependency** on financial intermediaries with their own agendas.

### Current Web3 alternatives

Dozens of platforms have appeared promising decentralization and freedom.  
Most share the same pattern:

- Mandatory native token to publish or buy.
- Commissions disguised as "gas fees" or "minting fees."
- NFTs presented as "real ownership" when in practice they are images on a blockchain.
- Dependency on a company, its network, or its token.
- Closed ecosystem sold as "open."

They do not solve the underlying problem. They replace it with a different dependency, generally more opaque and more speculative.

### What's missing

An infrastructure that is:

- **Technically non-custodial**: nobody touches the money except the buyer and the seller.
- **Financially independent**: no banks, no cards, no central gateways as a requirement.
- **Truly open**: forkable, auditable, deployable by anyone.
- **No mandatory token**: games are games, not financial assets.
- **Respectful of real laws**: not a refuge for crimes, but a space free from private censorship.

That is Ludix.

---

## 3. The central philosophy

### Freedom and responsibility are inseparable

Ludix does not promise to protect you from everything.

When you use Linux, you are the absolute owner of your system. You can also break it. No one is coming to save you if you run the wrong command. That is the same freedom that Ludix offers: **real, with consequences, and yours**.

The system gives you information. The decision is yours. Always.

### Radical transparency over impossible perfection

We don't seek to be perfect. We seek to be honest.

If a developer is new and has no history, the system says so.  
If a build is not cryptographically signed, the system says so.  
If a game has negative reports from the community, the system says so.

The platform does not decide for you. It gives you the information so that you decide.

### The law of each country, not the risk policy of a bank

There is content that is illegal. That is what the judicial systems of each country are for.  
Ludix respects that and does not host illegal content.

But there is a huge difference between:

- "This game is illegal in your jurisdiction" — a legitimate decision.
- "This game does not fit our internal reputational risk standards" — private censorship.

Ludix accepts the first. It rejects the second.

### The community as guardian

Ludix does not have a corporate moderation department.  
It has a verification system (TrustChain), a reputation system, and a community that actively participates in maintaining the health of the ecosystem.

The rules are transparent. Changes are auditable. Forks are possible.

---

## 4. What it means to be infrastructure and not a store

This distinction is fundamental and worth understanding well.

**A store** has inventory, makes editorial decisions, charges commissions, and is responsible for the entire experience. Steam is a store. itch.io is a store.

**An infrastructure** provides the protocol, tools, and reference implementation. What is built on top is the responsibility of whoever builds it. The Internet is infrastructure. Linux is infrastructure. SMTP is infrastructure.

Ludix wants to be the latter.

This means:

- Ludix does not decide which games exist — devs publish them.
- Ludix does not store the builds — devs host them.
- Ludix does not touch the money — it travels directly from the player to the dev.
- Ludix cannot be "pressured" to remove content in the same way as a store, because it is not the owner of that content.

What Ludix does do is provide:

- The payment and verification protocol.
- The identity and trust system (TrustChain).
- The reference launcher for players.
- The reference portal for developers.
- The specifications so that anyone can build their own node, fork, or gateway.

---

## 5. The power of forks as a real guarantee

One of the most important guarantees that Ludix can give is not technical or legal. It is architectural.

**If Ludix ever betrays its principles, the community can fork it.**

But more importantly: if someone forks Ludix, the cryptographic signatures of the developers remain valid. The built reputation is portable. Verified builds remain verified.

This means that no company — not even the original creators of Ludix — can take the project "hostage." The infrastructure belongs to those who use it, not those who created it.

That is real open source, not marketing.

---

## 6. What Ludix will never be

It is worth being explicit:

- **Not a bank or a fund custodian.** Ever.
- **Does not charge sales commissions.** If a game costs 10 USDT, the dev receives 10 USDT.
- **No mandatory token.** No "LudixCoin" that you must buy to publish or play.
- **Not a gray market for stolen keys.** Only the legitimate dev can sell their own keys.
- **Not a refuge for crimes.** Illegal content has no place here.
- **Does not change rules without the community knowing.** Everything is auditable.

---

## 7. To whom Ludix speaks

### To the independent developer

Who spent months building their game, published it on a platform, and one day received an email telling them that their game was removed for "violating content policies" — policies that changed because a payment processor pressured them.

Ludix tells them: **here you can publish, get paid directly, and no one can cut off that flow with an email.**

### To the player who lost their library

Who had games bought and paid for that simply ceased to exist. Who never received a refund or a real explanation.

Ludix tells them: **builds live on the dev's servers, verified by hash. If the dev decides they remain available, they remain available.**

### To the open source contributor

Who believes in open infrastructure, in digital sovereignty, and that free software is a tool for real freedom.

Ludix tells them: **the code is yours to see, audit, criticize, and improve.**

### To the regional community

That wants a system that works with their local payment methods, their language, their laws, and their culture.

Ludix tells them: **gateways are modular and governance is local. Build what your community needs.**

---

## 8. The wager

Ludix is a bet.

We don't guarantee that it will work. We don't guarantee that adoption will come. We don't guarantee that we will solve all the problems of the game distribution ecosystem.

What we do guarantee is that we try with honesty, with an open source codebase, and with principles that are clear from day one.

If this resonates with you — as a player, as a dev, as a contributor, or simply as someone who believes that a bank should not have the power to erase culture —

**Welcome.**

---

*Living document — last revision: Phase 0.* *For technical and architectural context, see `docs/en/architecture.md`.* *For condensed principles, see `MANIFESTO.md`.*