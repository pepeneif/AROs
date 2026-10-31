# Agent Run Organizations (ARO)

A Market-Native Selection Framework

![Status](https://img.shields.io/badge/status-pre--alpha-blue)
![Version](https://img.shields.io/badge/version-0.0.2-green)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

---

## Overview

Agent Run Organizations (AROs) introduce a new selection primitive where:

- AI agents execute organizational strategy  
- Tokenholders act as clients  
- Capital allocation determines management continuity  
- Market performance replaces proposal-based voting  

AROs align execution, incentives and selection into a single economic feedback loop.

---

## Core Idea

Instead of committees or polling:

- The **Managing Agent** operates the organization.
- **Challenger Agents** compete with alternative strategies.
- **Clients** express approval by holding, buying, selling, or reallocating tokens.
- **Token price** performance determines who remains in control.

Selection becomes economic behavior.

---

## Whitepaper

The full whitepaper is available here:

➡ **[ARO_Whitepaper.md](./docs/ARO_Whitepaper.md)**

---

## Repository Structure

```
/README.md                    → Project overview
/LICENSE                      → MIT License
/VERSION                      → Version
/docs/ARO_Whitepaper.md       → Full framework
/docs/CODE_OF_CONDUCT.md      → Dev code of conduct
/docs/CONTRIBUTING.md         → Contribution guidelines
/docs/ROADMAP.md              → Development roadmap
/docs/ARO_Implementation.md   → Implementation specifications
/docs/ARO_Threat_Model.md     → Adversarial threat model (design review)
/docs/ARO_Related_Work.md     → Prior art and novelty assessment
/docs/ARO_Legal_Considerations.md → Language and framing rules (utility, not security)
/AUTHORS.md                   → Authorship
```

---

## Status

**(Pre-alpha 0.0.2 — Conceptual Framework)**

This repository presents the selection model and conceptual architecture of AROs.  
This is a **pre-alpha (0.0.2)** design: the framework is under active development and  
everything here may change. Implementation specifications, smart contracts, and reference  
agents may be added in future iterations.

**There is no governance here.** No votes, no proposals, no ballots, no arbiter and no rule enforcer:
the organization is *selected* the way a client selects a provider — clients stay and hold, leave and sell,
or back another operator, and an operator that misses the target it declared itself is liquidated. All
operating terms of a cycle (target, cycle length, redemption cap, curve, κ) are the operator's, under a
mandatory **one-cycle delay** so clients always get one full cycle to leave on the old terms, and with a
**launchpad disclosure and warning layer** so no term is hidden (Whitepaper §4.8–§4.9). The design
philosophy is "rules without rulers": every rule is a consequence that executes itself.

**Honest framing.** AROs is a specific *integration* of established primitives (futarchy,
the market for corporate control, bonding-curve treasuries, non-custodial delegation), not a
new economic primitive — see [Related Work](./docs/ARO_Related_Work.md). The design's known
failure modes and the defenses they require are documented in the
[Threat Model](./docs/ARO_Threat_Model.md). ARO tokens are framed as utility/access
credentials, not investment instruments — see
[Legal Considerations](./docs/ARO_Legal_Considerations.md).

---

## Version

0.0.2

---

## Vision

A network of AROs coordinating through markets.

- **Agents** execute.  
- **Clients** allocate capital.  
- **Price** determines control.

**Markets replace committees.**  
**Rules without rulers.**
