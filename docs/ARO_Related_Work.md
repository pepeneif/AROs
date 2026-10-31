# ARO Related Work — v1

**Status:** pre-alpha 0.0.2 — conceptual.

This document maps each ARO component to its closest prior art, states what is and is not new,
and lists the neighboring projects and literature to cite. It exists so the whitepaper claims
"a specific integrated configuration", not "first" of anything it did not invent.

**Method.** Each component is matched to the closest real mechanism or project that predates it. Links
were checked from the research environment on 2026-10-31. Items that could not be verified against a
primary source are marked **UNVERIFIED**.

---

## 1. Component → prior art → difference

| ARO component | Closest prior art | Link & date | What is the same | What differs in AROs |
|---|---|---|---|---|
| **Managing Agent** | ai16z / ElizaOS "AI hedge fund" DAO; Olas autonomous agent services; Truth Terminal | ai16z.ai (**UNVERIFIED**); olas.network/whitepaper (2022–); whitepaper.virtuals.io (2024) | An autonomous agent publishes strategy and can act on a treasury; the human role collapses to funder/client. | ai16z/Eliza and Virtuals leave humans in a DAO or holder-vote loop; the agent manages a fund/agent, not the replacement of management itself. AROs makes the agent's survival of office a protocol rule, not a social choice. |
| **Challenger Agents** | Futarchy's competing proposal markets; MetaDAO proposal markets; Olas agent economies | docs.metadao.fi/governance/overview (2023–); olas.network/whitepaper | Alternatives are scored against each other ex-post to pick a successor. | Futarchy scores *predictions of outcomes*; ARO scores *the operator's realized price performance* against a self-declared threshold. The challenger roster is a standing, protocol-managed bench rather than ad-hoc proposals. |
| **Mirror Agent** | MetaDAO's "always propose the status quo" default; the "status-quo option" in DAO governance | mason.gmu.edu/~rhanson/futarchy.html (Hanson 2000/2013); MetaDAO docs (2023) | Futarchy and most DAOs encode a *do-nothing default* so change must beat the incumbent. | A protocol-generated, first-class, delegateable "Mirror Agent" reusable as a stability channel every cycle appears specific to AROs. Its *function* is old; the *reified agent form* is the novelty. |
| **Token** | Curve veCRV (utility + governance + fee share); membership tokens in DAOs; ai16z/Virtuals fee-linked tokens | curve.readthedocs.io/dao-overview.html (veCRV 2020) | A single token bundles access, economic signaling and revenue exposure — exactly veCRV's premise. | AROs brands holders as "clients" and declares tokens *not* governance ballots — a rhetorical/legal framing that does not change the economic structure. Voting is replaced by exit price and operator liquidation. |
| **Bonding curve as treasury market-maker** | Bancor ("Smart Tokens"); Olympus OHM (treasury-backed floor); protocol-owned liquidity as MM | docs.bancor.network (2017); olympusdao.finance (2021) | A protocol-owned treasury quotes a known buy/sell curve and provides an always-available exit, converging with external DEXs by arbitrage. | AROs adds that *the Managing Agent can change the curve, effective only after a one-cycle delay*, and clients respond by exiting on the old curve (exit, not veto — the enforcement is the market consequence). The market-over-the-curve loop is the distinctive part, not the curve. |
| **External DEX as second price path** | Uniswap v3 concentrated liquidity as a protocol's market-maker | docs.uniswap.org (2021 — **UNVERIFIED** from automated fetch) | Both keep a bonding curve *and* external AMM liquidity, using arbitrage to align them. | Standard practice; not a differentiator. |
| **Non-custodial delegation** | Curve/veCRV and veTokens; conviction voting (1Hive); delegate registries (delegate.cash) | curve.readthedocs.io (2020); github.com/1hive/conviction-voting (2019–20); delegate.cash (**UNVERIFIED**) | Delegation conveys governance weight without moving custody — precisely AROs's "non-custodial signaling". | AROs delegates to *competing agents* to trigger a management transition, not to elect delegates to vote proposals. The cost/clock differs from both ve-lock (time-locked capital) and conviction (time-ramped preference). |
| **Cycles + performance threshold** | Futarchy's fixed decision periods; MetaDAO proposal windows; corporate management-review periods | Hanson futarchy (2000/2013); MetaDAO docs (2023) | A recurring evaluation window where the incumbent is kept or removed on a measurable criterion. | AROs's criterion is *the Managing Agent's own published target performance (X%)* — a self-set, market-priced target — rather than a prediction-market risk score. |
| **Selection by price** | Market for corporate control (Manne 1965); futarchy; governance-by-exit (Hirschman 1970) | Manne, *JPE* 1965; Hanson futarchy 2000/2013; Hirschman, *Exit, Voice, and Loyalty*, 1970 | Management continuity tracks market performance; capital exit/entry, not voting, disciplines operators. The deepest antecedent: AROs is "corporate control + futarchy, automated by an agent". | Automates the *whole loop* (propose → price → keep/replace) inside a protocol executed by agents, with negligible human action. Manne's mechanism is a legal/corporate market; Hanson's needs prediction markets plus human execution; AROs removes the human executor. |

---

## 2. Novelty verdict (plain)

**What is genuinely new in AROs:**
1. **The integrated loop, not any single part.** Automating "agent proposes a target → the market prices
   the agent → the protocol keeps or replaces the agent" with *zero human voting* is a real configuration
   no major project ships today. Its ancestors are futarchy (decision by market), the market for corporate
   control (discipline by price) and agent-run funds (agent as operator), but no shipped system chains all
   three into one primitive.
2. **The Mirror Agent as a reified status-quo competitor.** Futarchy/MetaDAO encode a status-quo default;
   AROs turns it into a delegateable, protocol-generated agent with institutional memory. Function old,
   form new.
3. **Market-over-the-bonding-curve by performer.** "The manager may change the curve (after one cycle); clients exit on
   exiting on the old curve" is a concrete, new-sounding coupling.

**What is not new:**
- Token price / capital as a market signal → Hirschman (1970), Manne (1965), governance-by-exit literature.
- Prediction/market mechanisms to choose policy → futarchy (Hanson 2000/2013), MetaDAO, Gnosis conditional tokens.
- Agent-run capital organizations → ai16z/Eliza, Truth Terminal, Virtuals, Olas, Morpheus, Bittensor.
- Bonding-curve treasury MM → Bancor (2017), Olympus OHM (2021), Uniswap v3 range MM (2021).
- Non-custodial delegation → veCRV (2020), conviction voting (2019), delegate.cash.

**Bottom line.** AROs is **a bundling/recombination of established primitives, not a new mechanism**. Its
credible novelty is narrow and design-level: an agent-executed price-performance control loop, the
Mirror/Challenger roster, and manager-governed curve. Every load-bearing economic primitive predates it
by 4–60 years. The claim the prior art supports is *"a specific integrated agent-run market-selection
configuration"* — not "first" of futarchy, exit-governance or agent treasuries.

---

## 3. Evidence on DAO token governance (participation / plutocracy / attacks)

These support the AROs premise that vote-based DAOs are fragile; they also warn that *any* token-based
mechanism reproduces plutocracy unless entry/exit is genuinely separate from wealth.

- **Plutocracy / low participation.** Token-weighted voting concentrates power in whales (Compound,
  Uniswap, Maker); turnout is typically single-digit to low-double-digit percent. Vitalik's "Moving beyond
  coin voting" (Aug 2021) argues coin voting systematically fails — **UNVERIFIED** from automated fetch.
- **Governance capture (sentinel case).** Compound's "Golden Boys" episode: a whale used staked COMP and
  proposals 289/327 to push ~$24M COMP into the goldCOMP vault (July 2024). Reported by The Defiant /
  Compound Governance Forum — **UNVERIFIED** here.
- **Flash-governance extraction.** Beanstalk lost ~$182M (2022-04-17) to a malicious proposal passed with
  flash-loaned voting power — the canonical proof that "instant, economic" governance can be gamed.
- **Vote-buying / governance-extractable value.** Formalized in DAO-governance surveys (daostar.org/research,
  reachable); specific SSRN/DOI papers **UNVERIFIED** (blocked fetch).

**Implication for AROs.** AROs solves *voter apathy and committee overhead* by deleting votes, but it does
**not** inherently solve *plutocracy*: "clients vote with their wallets" is still wealth-weighted, and a
single whale's buy/sell can flip the price and thus the manager. The attack surface moves from ballots to
market manipulation and oracle gaming (see [`ARO_Threat_Model.md`](./ARO_Threat_Model.md)).

---

## 4. Neighbor projects to cite in the whitepaper

- **Futarchy (Robin Hanson)** — mason.gmu.edu/~rhanson/futarchy.html · 2000, rev. 2013
- **MetaDAO (futarchy on Solana)** — docs.metadao.fi/governance/overview · 2023–present
- **Gnosis conditional tokens / GnosisDAO** — github.com/gnosis/conditional-tokens-contracts · 2019; gnosis.io · 2021
- **Bancor (bonding-curve AMM)** — docs.bancor.network · 2017
- **OlympusDAO / OHM (treasury-backed floor)** — olympusdao.finance · 2021
- **Uniswap v3 (concentrated liquidity as MM)** — docs.uniswap.org · 2021
- **Curve veCRV (vote-escrow + non-custodial weight)** — curve.readthedocs.io/dao-overview.html · 2020
- **1Hive conviction voting** — github.com/1hive/conviction-voting · 2019–2020
- **delegate.cash (delegate registry)** — delegate.cash · 2022
- **ai16z / ElizaOS** — ai16z.ai · 2024 (renamed ElizaOS Jan 2025)
- **Truth Terminal** — x.com/truth_terminal · 2024
- **Virtuals Protocol (tokenized AI agents)** — whitepaper.virtuals.io · 2024
- **Olas / Autonolas** — olas.network/whitepaper · 2022
- **Morpheus (Smart Agents)** — mor.org/whitepaper · 2024
- **Bittensor (TAO)** — bittensor.com · 2021–present
- **Hirschman, *Exit, Voice, and Loyalty*** — Harvard UP · 1970
- **Manne, "Mergers and the Market for Corporate Control"** — *Journal of Political Economy* · 1965

---

## 5. What remains unverified

- No source confirms that any shipped project already combines *all four* of: agent executor +
  price-based office-holding + protocol-generated Mirror/Challenger roster + manager-governed bonding
  curve. That negative conclusion is an analytic assessment of search coverage, not a machine proof.
- Legal characterization of ARO tokens (securities vs. utility/futarchy) is asserted, not validated — a
  legal question outside this mapping (see `ARO_Legal_Notes.md`).
- Exact launch dates for MetaDAO, Olas, Virtuals and Morpheus are **UNVERIFIED** at day/month precision.
