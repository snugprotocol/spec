# Whitepaper

**[The Snug Protocol: An Open Protocol for Agent-Backed Personal Software](snug-protocol-whitepaper.pdf)** — Jeetu Maker, August 2026. A4, 22 pages.

The design rationale behind the specification: why the protocol is shaped the way it is,
what it defends against, and what it deliberately does not attempt.

**Contents.** The market-of-one problem and the three questions models do not answer
(isolation, custody, continuity) · design goals and non-goals · the three-actor architecture
· the wire protocol — nine frames, the chat envelope, and rules R1–R6 · the security model:
an explicit threat model in which application code is treated as hostile, the two hard
constraints (C1 token boundary, C2 sandbox integrity), and residual risks stated candidly ·
the portable user database (v0.2 draft) · conformance · related work — the Model Context
Protocol, vendor application platforms, and the local-first tradition · limitations and
future work.

Seven original figures cover the actor model, system architecture, the frame lifecycle, the
trust boundary, the one-file data layout, runtime materialisation, and the execution modes.

## Scope and status

The paper describes **spec v0.1** (normative) and the **v0.2 draft** (the Portable User
Database Format, published for review and marked as draft throughout). Where the paper and
the specification differ, [SPEC.md](../SPEC.md) governs.

Every normative claim traces to [SPEC.md](../SPEC.md),
[SPEC-v0.2-draft.md](../SPEC-v0.2-draft.md), or [schemas/](../schemas/); the frame table and
protocol constants are verified against those files by a conformance check in the reference
implementation, so the paper cannot drift from the wire as the protocol moves.

## License

[MIT](../LICENSE), as with the specification and its schemas.
Security contact: security@snugprotocol.org
