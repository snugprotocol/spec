# Whitepaper

**[The Snug Protocol: An Open Protocol for Agent-Backed Personal Software](snug-protocol-whitepaper.pdf)** —
Jeetu Maker, August 2026. A4, 33 pages. **Edition 3 — the 1.0 edition**, covering
**Specification 1.0**.

The design rationale behind the specification: why the protocol is shaped the way it is,
what it defends against, and what it deliberately does not attempt.

**Contents.** The market-of-one problem and the four questions models do not answer
(isolation, custody, continuity, connection) · design goals and non-goals · the
three-actor architecture · the wire protocol — thirteen frames, the chat envelope, and
rules R1–R7 · the security model: an explicit threat model in which application code is
treated as hostile, the hard constraints (C1 token boundary, C2 sandbox integrity), and
residual risks stated candidly · the portable user database, its `.snug` naming rule, and
the optional `SNUGENC1` passphrase container · connected applications — requirements,
grants, frozen host ceilings, and the ten-gate executor · linked-device connections — the
helper surface for providers that authenticate a device session, with its custody split
and pseudonymisation backstop · runtime contracts and the app data surface · conformance ·
related work — the Model Context Protocol, vendor application platforms, and the
local-first tradition · limitations and future work.

Ten original figures cover the actor model, system architecture, the frame lifecycle, the
trust boundary, the one-file data layout, runtime materialisation, the execution modes,
the connection lifecycle, the executor's gate sequence, and the linked-device surface.

## Scope and status

The paper describes **Specification 1.0** — the wire protocol (core normative since
v0.1, additively extended), storage at schema 6, connected applications, runtime
contracts, and linked-device connections, all normative at 1.0 (standing approvals is
explicitly provisional). Where the paper and the specification differ,
[SPEC.md](../SPEC.md) governs.

Every normative claim traces to [SPEC.md](../SPEC.md) or [schemas/](../schemas/); the
frame inventory and protocol constants are verified against those files by a conformance
check in the reference implementation, so the paper cannot drift from the wire as the
protocol moves.

Editions 1 (22 pages, spec v0.1 + the v0.2 draft) and 2 (the v0.3 release candidate)
remain available in this repository's history.
