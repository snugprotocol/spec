# The Snug Protocol

> **MCP connects agents to tools. Snug connects agents to apps.**

Snug is an open protocol for **user-built micro apps that think through their host's AI agent at runtime**. A Snug app is a small sandboxed app (often a single HTML file) that a user creates through conversation with a product's AI assistant. At runtime the app exchanges versioned JSON **envelopes** with the host agent over a strict boundary — the agent is the app's mind; the app is a body. Apps reach the user's own services only through the host, inside a human-approved, host-frozen ceiling. Users own their apps and their data as ONE portable `.snug` file (a SQLite database they can open with ordinary tools, optionally sealed with a passphrase only they hold).

- **[SPEC.md](SPEC.md)** — **Specification 1.0**, the complete normative specification
- **[schemas/](schemas/)** — JSON Schemas for every message type (published byte-identical from the reference implementation)
- **[whitepaper/](whitepaper/)** — [the whitepaper](whitepaper/snug-protocol-whitepaper.pdf) (PDF, edition 3 — the 1.0 edition): design rationale, threat model, security properties
- **[implementations.md](implementations.md)** — known implementations

## Status

**Specification 1.0 — NORMATIVE** (2026-08-22). [SPEC.md](SPEC.md) is the complete specification in one document: the wire protocol (13 frames — the nine core frames plus the net and open-url pairs — the chat envelope, and rules R1–R7), the Portable User Database Format at storage schema 6 (including `.snug` naming and the `SNUGENC1` protected form), connected applications (requirements, grants, credential custody, and the host executor), runtime contracts and the app chat surface, and linked-device connections. One section is explicitly **provisional** and so marked: §17 (standing approvals). The wire protocol's core has been published and stable since v0.1; all 14 schemas in [schemas/](schemas/) are byte-identical exports from the reference implementation.

**History.** v0.1 (wire-protocol core) and the v0.2/v0.3 drafts finalised into 1.0; the retired draft filenames are pointer stubs and their full text lives in git history. Post-1.0, additive changes bump the minor; a change that breaks a conforming implementation bumps the major. Every change lands as a single commit traceable to its task in the reference repo.

## Contributing

This spec is maintained through the reference implementation's engineering process — protocol proposals and discussion happen in [`snugprotocol/snug`](https://github.com/snugprotocol/snug) issues/discussions; every spec change lands here as a single traceable commit. Direct PRs to this repo are welcome for typos/clarity only.

## License

[MIT](LICENSE) · Security contact: security@snugprotocol.org

---
Maintainer: [Jeetu Maker](https://jeetu.tech.voyage)
