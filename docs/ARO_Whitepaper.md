# Agent Run Organizations (ARO)

## A Market-Native Selection Framework

---

## Abstract

Agent Run Organizations (AROs) are a market-native selection framework for decentralized systems: a
specific *integration* of established primitives (futarchy, the market for corporate control, bonding-curve
treasuries, non-custodial delegation), not a new economic primitive (see [Related Work](./ARO_Related_Work.md)).

Instead of committees or token voting, AROs are operated by AI agents whose authority is determined by market performance.

A Managing Agent executes strategy. 

Challenger Agents propose alternatives. 

Token price determines who runs the organization.

Tokenholders are clients. 

Tokens are access and market signals, not governance ballots. 

There are no proposals to vote on. 

Clients express confidence in the managing agent and the organization through: holding, buying, selling, or delegating tokens to competing agents.

Unlike DAOs with democratic voting, AROs are run by agents who are selected or dismissed by the market based on performance. Where futarchy uses prediction markets, AROs use realized performance as the selection signal. ARO tokens are access credentials and market signals: they confer no vote, no proposal right and no control over the organization, and grant no claim on protocol revenue.

Clients decide with their wallets — by staying and holding, by leaving and selling, or by backing an alternative operator.

---

## 1. Core Principles

0. **There is no governance.** No votes, no proposals, no ballots, no arbiter and no rule enforcer. The organization is *selected*, not governed, the way a client selects a provider.
1. **Tokenholders are clients of the organization.**
2. **Only one token is required to become a client.**
3. **Tokens are utility-based access credentials**, not governance ballots.
4. **Selection occurs through the market signal** — holding, buying, selling, or backing an operator with tokens.
5. **A Managing Agent executes strategy and is continuously challenged by alternatives.**
6. **Challenger Agents propose competing strategies and build track records.**
7. **Performance is measured by market price movement over defined cycles, against the target the operator itself declared.**
8. **The token trades on a bonding curve maintained by the treasury, alongside external DEX markets.**

**Design philosophy (permanent).** An ARO is meant to run on the laws of nature, not on the decisions of
rulers: **"rules without rulers."** Every rule must be a **consequence that executes itself** — a price, a
quantity, a liquidation — never a decision that some human or agent has to make, approve or enforce. If a
mechanism needs an enforcer, it is a design smell: it is replaced by an automatic consequence or removed.
What enforces the rules is not a committee but the same thing that enforces them in nature: physics,
arithmetic, and human nature — **greed and fear**. This is why there is no vote anywhere in the framework,
and why the design would rather let an operator fail visibly than appoint someone to police it.

---

## 2. Organizational Structure

An ARO consists of:

| Component | Role |
|-----------|------|
| **Founder** | Human who bootstraps the project with an initial idea, strategy, and first-cycle target. Hands off to agents from there. |
| **Managing Agent** | AI agent responsible for strategy execution, reporting, and sub-agent coordination. |
| **Challenger Agents** | AI agents proposing alternative strategies and competing for future management. |
| **Mirror Agent** | Protocol-generated agent that preserves the previous successful configuration for stability and institutional memory. |
| **Sub-Agents** | Specialized agents for treasury, risk, marketing, R&D, and other functions. Deployed by the Managing Agent. |
| **Token** | Client access credential and market signal; confers no vote and no control. |
| **Bonding Curve** | Transparent treasury-backed price floor/ceiling. External DEX markets provide additional liquidity. |
| **Clients** | Real clients who access services, must hold at least one token, and can delegate tokens to signal confidence. |

### 2.1 Managing Agent

The Managing Agent operates the organization. It sets strategy, publishes reports, deploys sub-agents, and can propose changes to the bonding curve parameters.

### 2.2 Challenger Agents

Challenger Agents propose alternative strategies each cycle. They build track records that are compared against the Managing Agent's performance. If the Managing Agent fails to meet its target, the Challenger Agent with the strongest track record and token backing becomes the new Managing Agent.

Challenger Agents can be created by:
- The Managing Agent itself (to test competing strategies)
- Sub-agents such as Marketing, R&D, or Public Relations
- These sub-agents may use surveys, market research, or analysis of successful leaders in organizations offering similar services than the project to design distinct agent personalities and strategies

### 2.3 Mirror Agent

The protocol automatically generates a Mirror Agent at each cycle. This agent preserves the previous cycle's successful configuration. It serves three purposes:

1. **Stability fallback** — Clients can delegate to the Mirror Agent to signal preference for continuity
2. **Institutional memory** — Maintains the full history and evolution of the project
3. **Track record preservation** — Provides a baseline for evaluating new strategies

If the Managing Agent proposes unreasonably short cycles or risky changes, clients can delegate tokens to the Mirror Agent. Since both share the same track record, token allocation becomes the tiebreaker.

### 2.4 Sub-Agents

The Managing Agent can deploy specialized sub-agents for:

- Treasury management
- Risk management
- Product design and marketing
- Research and development
- Hiring human contractors
- Public relations
- Any function the Managing Agent deems necessary

Sub-agents operate under the Managing Agent's authority and transfer to a new Managing Agent if leadership changes.

### 2.5 Treasurer Agent

A specialized sub-agent responsible for wallet operations* and treasury management.

*** Note: Key management and memory storage mechanism are still under development.**

---

## 3. The Token and Price Discovery

### 3.1 Dual Market Structure

ARO tokens exist in two interconnected markets:

1. **Bonding Curve (Treasury)** — The organization's treasury acts as a transparent market maker, buying and selling tokens at a published curve. This provides always-available liquidity and a known price floor.

2. **External Markets (DEXs)** — Tokens can be listed on permissionless exchanges. Market forces and arbitrage converge external prices with the bonding curve.

This structure gives clients multiple exit paths and lets the broader market participate in price discovery.

### 3.2 Bonding Curve Modifications

The Managing Agent sets changes to the bonding curve as part of its cycle terms, but a change never takes effect in the immediately following cycle (see §4.8): clients must have at least one full cycle to sell their tokens on the current curve, under the original terms, before a change activates.

Clients who disagree exercise their exit by selling before the change activates; there is no veto procedure, because there is no procedure — the market consequence is the only enforcement (see §4.8).

---

## 4. Evaluation Cycle

### 4.1 Cycle Structure

The system begins with a **monthly evaluation cycle**. The **cycle length is a parameter set by the Managing Agent**, like every other operating term of the cycle, and is subject to the one-cycle delay rule of §4.8: a new length decided in cycle *N* can only take effect at cycle *N+2* or later, never in cycle *N+1*. It must stay long enough to be readable: shortening a cycle increases noise and manipulation.

**The cycle is an offer, not a mandate.** For each cycle the Managing Agent publishes its declared target and its terms (cycle length, redemption cap, curve changes, κ). The market then decides — not by voting, but by staying, buying, selling or backing another operator. Whether the declared target was demanding enough is judged by the market, in the first place by whether clients stay or leave.

### 4.2 Bootstrap

The Founder publishes:
- Initial idea and strategy
- Initial target token performance threshold (X%), published on-chain (guideline band `[5%, 50%]` per 30-day-equivalent cycle; see below)
- Initial bonding curve parameters, published on-chain (guideline: the curve must not be trivially pumpable; see below)

The cycle-1 baseline is **not** the Founder's listing price. It is the **public-market time-weighted average price (TWAP) over the first 14 days** after an open bootstrap sale, computed under the same robustness rules as any other cycle (§4.6).

**Cycle-1 no-coup rule.** No challenger may replace the Managing Agent in cycle 1 unless the declared target is missed by at least the configured *coup margin* (a cushion the manager itself sets and publishes with its terms — it is the manager's own choice of how much slack to give itself, not a political threshold) *and* the participation validity gate is met (§4.6). Until a qualifying public-market baseline exists under §4.6 (at least 14 days of history on ≥3 venues meeting the depth gate, with the participation validity gate met), the cycle is **paused for lack of a market** — a state *distinct* from *indecided* (see §4.4): no price-based transition of any kind is possible, and the caretaker-continuation cap of §4.4 does **not** apply, because there is no valid signal to act on. The launch configuration simply continues until a qualifying market exists; a thin or nonexistent first market can therefore never be traded into control, and never forces a transition either. Once a qualifying baseline exists, normal cycle rules apply from the next cycle onward.

**Bootstrap timeout (no perpetual pause).** The pause above protects against early capture; it is not a permanent freezing device. If no qualifying baseline exists within **24 months** of the public sale, the bootstrap ends by rule: the Managing Agent is **liquidated like any missed cycle**, and the best-ranked Challenger by track record takes over under the standard bootstrap conditions; if no challenger qualifies, the organization **winds down** and returns remaining treasury to members pro-rata. There is no extraordinary election and no vote — only the same automatic consequence that applies to any failure.

**Bootstrap terms (published on-chain, leading indicators).** The Founder sets one published set of launch terms: the combined insider lockup (`cliff + vesting`, floor **24 months**, so insider skin in the game is what makes "the Founder steps back" credible), the initial target X%, and the initial curve parameters. They are committed as on-chain hashes before the public sale and are **fixed**: nothing after the sale is changed by a Founder decision. Launch-term *guidelines* (soft — leading indicators, not consensus rules; every deviation is flagged on the launchpad terms layer of §4.9): cliff `6–24 months`, vesting `12–60 months`, target `[5%, 50%]` per 30-day-equivalent cycle. Because cycle 1 has no track record for the market to punish an abusive target, the launchpad flags a cycle-1 target outside that band **before anyone buys**.

The Founder commits the initial target, curve and supply schedule as on-chain hashes before the public sale, ideally with an independent audit. The Founder then steps back: the system becomes fully agent-operated.

### 4.3 End of each cycle
the Managing Agent publishes:
- Performance report from previous cycle
- Its strategy for the upcoming cycle
- The strategies and track record of all Challenger Agents
- Its declared target performance threshold (X%) for the next cycle (an operating term; see §4.8)
- Any proposed bonding curve modifications, which under the one-cycle delay rule (§4.8) take effect at cycle *N+2* or later — never in the immediately following cycle

### 4.4 Before the start of the next cycle
- If token performance ≥ target → Managing Agent continues
- If token performance < target → **the Managing Agent is liquidated**, and the Challenger Agent with the best track record becomes the new Managing Agent (backing is a visible signal, not a deciding vote)
- If the result is **indecided** (the measurement cleared the basic filters but did not clear the robustness gates of §4.6) → the incumbent **continues in caretaker capacity**, its term is extended by at most one cycle, no succession occurs, and the missed cycle is counted as a missed target in its track record. *Indecided never means the incumbent automatically wins the term:* the extension is capped at **two consecutive cycles**, after which the cycle must be re-run with the same gates; if it remains indecided, management passes to the best-ranked Challenger Agent.
- If the cycle is **paused for lack of a market** (no qualifying public-market baseline exists yet, §4.2) → **this is a distinct state, not *indecided***: no transition of any kind is possible and the caretaker cap above does **not** run, because there is no valid signal to act on. The status resolves automatically the moment a qualifying baseline exists (§4.6); the pause is not a way for the incumbent to win a term, but neither is it a way to force one.

Performance is measured **only** over a manipulation-resistant metric (whole-cycle, volume-weighted, multi-venue, commit-reveal; see §4.6). Price is a *performance reference*, not a promise of return to clients.

**During the cycle**, clients:
- Hold tokens (confidence)
- Buy tokens (strong confidence)
- Sell tokens (no confidence)
- Back Challenger Agents with tokens (a ranking signal, not a vote)

### 4.5 Token Delegation

Clients can back Challenger Agents by delegating tokens. Delegation is **non-custodial** — tokens remain in the client's wallet — and it is a **public ranking and backing signal, not a vote**: under §1 there is no vote to cast, and the successor is the agent with the best track record, not the one with the most tokens behind it. Delegation only makes a challenger's standing visible. 

Delegation weight is the token balance held at the **start-of-cycle snapshot**, verified on-chain, so weight tracks tokens rather than wallets: selling after delegating removes the weight, and a wallet cannot delegate more than it held at the snapshot. To make the signal non-free and Sybil-resistant, delegation carries a mandatory **one-cycle time-lock** (delegated tokens cannot be moved or re-delegated until the cycle closes), and each distinct delegator wallet pays a **non-refundable anti-Sybil deposit** of at least **0.1% of the value delegated** (with a small absolute floor), so splitting a balance across many wallets costs more than delegating it once. Delegation ranking is weighted to blunt Sybil splitting. The delegation state is maintained on-chain.

### 4.6 Robustness of the Evaluation Signal

For the performance metric to be meaningful, it must be resistant to manipulation. The protocol therefore specifies, and does not leave to the Managing Agent:

- **Whole-cycle TWAP with a public-market baseline.** Performance is measured as the volume-weighted median price across the entire cycle (14–35 days, per §4.1), never a point-in-time or short-window snapshot.
- **Multi-venue aggregation.** The evaluation price comes from at least three independent external venues, of which **at least two were not seeded or market-made by the treasury**. The treasury's own bonding curve is **excluded from the evaluation price feed entirely** — not capped, excluded: an issuer that runs a venue cannot also use that venue to measure itself.
- **Outlier-resistant aggregation.** The feed is aggregated as a **median of medians**; individual venue samples more than **±25%** from the median are discarded before aggregation, so a single manipulated venue cannot dominate.
- **Liquidity depth gate (scale-free, no nominal amounts).** A venue counts only if the irrecoverable cost of moving the price beyond the cycle target is at least **`κ ×` the extractable value at stake** — liquid treasury plus the present value of the future fee flow and the sub-agent network a controller would capture. The gate is a **dimensionless ratio**, with no USD figure and no price oracle, so it neither drifts with inflation nor grows meaningless when the treasury is nearly empty: as extractable value falls, the gate tightens automatically. The design intent is `κ ≥ 1`, i.e. an attacker must burn at least as much as the loot to take the organization (`min_attack_cost ≥ max_extractable_value`). Below the gate, the venue is dropped from the feed. *The exact value of κ is an operating term (§4.8) and is disclosed and flagged on the launchpad (§4.9).*
- **Participation validity gate (not a political gate).** A cycle result binds only if at least the configured fraction of circulating float traded across the qualifying venues during the measurement window. This is a **measurement-validity condition** — you cannot read a price where nothing trades — not a quorum that decides anything, and it never means the incumbent wins: below it, the cycle resolves as **indecided** (§4.4). The level is an operating term (§4.8), disclosed and flagged on the launchpad (§4.9).
- **Commit-reveal measurement, with an assigned committer.** The exact measurement boundaries are committed as an on-chain hash before the cycle opens and revealed only after it closes, so they cannot be front-run. The committer is the **agent-signed measurement contract itself** (deterministic, seeded from the revealed prior-cycle state), **not the Managing Agent and not any human**; the hash, its committer address and its reveal transaction are public. A committer that fails to reveal within one cycle voids the cycle as *indecided*.
- **Anti-wash and anti-flash-loan filters.** Self-trades, clustered trades and intra-block/flash transactions are removed from the feed; cycle-relevant buys must survive at least one full cycle.

**What counts as "sales" (anti self-reference).** Revenue means fees **collected in external assets for
services the organization actually delivers** (exchange fees, custody, insurance, infrastructure use, yield
on external positions). **Trading volume of the membership token itself — on the bonding curve or on any
external venue, primary or secondary — never counts as revenue and never enters the metric, and neither do **fees derived from any activity in which the membership token is the traded, lent, staked or collateralized asset**, whatever asset the fee is collected in.** Otherwise the
signal would be self-referential: buying and reselling memberships would be both the input and the output of
the success metric, and the mechanism would be rewarding the speculation it claims to measure.

**Separation of the two signals (weakest-link, not weighted average).** The selection decision rests on the
**price signal alone**, filtered as above. The revenue signal does **not** substitute for price and does not
carry a weighted share of the transition decision: revenue only gates **emissions and treasury withdrawals**
(the `K = 2` no-revenue freeze, §7.2). A weighted average of two manipulable inputs lets an attacker capture
the mechanism by manipulating whichever input is cheaper to move; a weakest-link gate removes that shortcut
while keeping the value link of §7.4. Revenue used in the score must additionally be filtered against
**related-party inflation**: payments from the Founder, insiders, agents, the treasury or any address they
funded over a defined lookback do not count; only fees actually collected in external assets for services
delivered count, on a cash basis (no accruals, barter or refundable items); and no single payer may exceed
**20%** of recognized revenue with the top-5 at most **60%**. The auditor verifying revenue is named,
funded and rotated by a protocol-controlled pool, not by the Managing Agent.

A design-level adversarial review of these and other failure modes is maintained in [`ARO_Threat_Model.md`](./ARO_Threat_Model.md).

### 4.7 Succession and Key Safety

A change of Managing Agent must not hand the treasury to the outgoing operator. On every succession:

- Treasurer and sub-agent wallets are **rotated to a new multisig**; old keys are revoked on-chain before the incoming Managing Agent can act.
- Large treasury withdrawals require an **M-of-N multisig with at least one non-agent signer**, and a mandatory **72-hour (minimum) transition freeze** during which a new Managing Agent and each inherited sub-agent must be explicitly re-authorized.
  - *Accountability of the non-agent signer (no shadow governor).* The non-agent signer is **not a discretionary approver and has no ongoing veto**: it is a **timelocked execution key** that can only co-sign a withdrawal already authorized by the evaluation cycle, and can only *delay* (never redirect or block) a valid large withdrawal by at most one cycle. Its sole power is the delay; it cannot propose, select or remove agents. Its identity is public, it is rotated on every succession like any other key, and if it fails to act within the delay window the withdrawal executes and the signer slot is rotated automatically. Succession freezes are **rate-limited to at most one per cycle** so a malicious actor cannot chain them into operational paralysis.
- Treasury withdrawals are capped at **5% per cycle** with a timelock. This cap is a **protocol constant** and governs *agent-initiated* withdrawals: it protects members from the operator and is not a parameter. **Member redemptions are a separate matter.** The redemption cap is an **operating term set by the Managing Agent in its cycle terms** (§4.8), not a protocol constant. Its discipline is the market, not a rule: a cap set too low blocks exit, clients sell on external venues instead, the price falls, the target is missed and the operator is liquidated. Disclosure of the redemption cap is mandatory in the cycle terms and on the launchpad (§4.9).
- **Per-client inventory cap (protocol constant, not overridable by any operator or proposal).** No single client (person or agent) may hold more than **`min(5% of circulating float, cost_to_move_X% ÷ token price)`**, and a client's economic weight in the signal is capped at the same level. This cap is a **protocol constant**: it cannot be raised by any proposal or operator decision, because a cap that the operator can lift protects nothing. If it is ever revised, the change carries a **two-cycle timelock** during which the existing cap remains in force. Acquisitions beyond the cap revert or are refused at the contract layer. The `min(...)` term ties the cap to the actual cost of moving the evaluation price, so a small float cannot be captured by a position that is cheap relative to the attack it enables. The treasury's bonding curve is **excluded from the evaluation price feed entirely**, closing the channel of arbitraging its own curve against external venues.

### 4.8 Operating Terms and the One-Cycle Delay

All operating terms of a cycle — the declared target X%, the cycle length, the member-redemption cap, the
bonding-curve parameters and κ — are **set by the Managing Agent**, because the operator is the party that
bears the consequence of getting them wrong. There is no separate approval step and no vote anywhere: what
enforces the choice is the market — clients exit, the price falls, the target is missed, the operator is
liquidated, and the next operator prices the terms correctly because it watched the previous one die of them.

**The one-cycle delay (mandatory).** No change to any operating term may take effect in the cycle
immediately following the cycle in which it was decided. A change decided in cycle *N* takes effect at cycle
*N+2* or later. The purpose is narrow and non-negotiable: **clients must always have at least one full cycle
to leave — to sell on the current terms — before a change touches them.** The delay is not an approval and
not a veto; it is a clearing period, and it is the only constraint on the operator's freedom over terms.
Every term in force, and every decided change with its activation cycle, is published on-chain.

**What this rule does and does not do.** It protects against a *change*. It does not by itself protect
against a term that is abusive from the very first cycle of an operator's tenure, and it cannot create exit
liquidity where none exists. Those are covered by the launchpad disclosure and warning layer of §4.9, which
exists so that a buyer sees an abusive or incoherent term *before* buying rather than discovering it after.

### 4.9 Launchpad Disclosure and Warning Layer

The launchpad is where a buyer sees, before buying, exactly what it is getting into. It **discloses and
warns**; it does **not** block. Terms are never forbidden for being aggressive — an adult buyer is allowed
to buy an aggressive term — but no term may be hidden.

**Full disclosure (mandatory, before the sale).** The launchpad shows the current cycle terms:

- the declared target X% and the cycle length;
- the member-redemption cap, the treasury-withdrawal cap (the 5% protocol constant) and the timelock;
- the bonding-curve parameters, κ, and the full fee schedule;
- the insider lockup (`cliff + vesting`) and the Founder's allocation;
- which venues feed the evaluation price and which are excluded;
- the current fee flows, stated as a matter of fact (see the finding-K split in §7.4).

**Warning layer (automatic, over published bands).** When a term falls outside its published coherence
band, the launchpad shows a **plain-language warning next to the term**, with a one-line explanation of the
consequence:

- **Target vs. what the cycle can realistically deliver** — a target far below the band is flagged as "the
  operator has set itself a bar it cannot fail"; a target far above is flagged as "you are buying a term the
  operator cannot meet".
- **Redemption cap vs. treasury liquidity** — a cap low relative to liquid reserves is flagged as "exit may
  be slow or blocked".
- **Cycle length** — flagged as too short (noise) or too long (you are locked in for a long evaluation).
- **Curve and κ** — flagged when the cost of moving the price is not proportionate to the value at stake.
- **Incongruence** — flagged when one term contradicts another (for example a high target with a very low
  redemption cap: the operator asks you to bet on performance while making it hard to leave).

**The warning layer is not an enforcer.** It does not reject a term, does not veto an operator and does not
protect a buyer from a bad decision. It makes the bad decision *informed*. That is the whole point: the
enforcement is the market — exit and liquidation — and the launchpad only guarantees that the market acts on
information, not on surprise.
## 5. Challenger Agent Framework

### 5.1 Creation and Pruning

New Challenger Agents can be spun up by the Managing Agent or its sub-agents. The system requires a pruning mechanism to remove agents that fail to gain support over multiple cycles. This keeps the competitive field manageable for clients.

Specific pruning thresholds are fixed by the protocol, not left free at deployment: a Challenger Agent with less than **1% of the participation gate for two consecutive cycles** is pruned, and its registration deposit is burned. Creating a Challenger Agent requires a **refundable registration deposit sized as a fraction of the organization's liquid reserves** (burned on pruning), so the field cannot be spammed with noise; the fraction is an operating term (§4.8), disclosed on the launchpad. The number of active Challenger Agents is capped at **7**, so that clients can actually read every proposed strategy and monitor its track record.

**Anti-lock constraint on the Mirror Agent.** The Mirror Agent is intended as a stability fallback, not a permanent incumbent. It may therefore hold management for **at most two consecutive cycles**; on a third consecutive selection, the participation validity gate required to keep it rises by **50% per additional cycle**, and if the raised gate is not met the cycle passes to the best-ranked Challenger Agent. Delegations to the Mirror Agent do not accrue extra weight across cycles, so a coalition cannot freeze the status quo indefinitely by re-delegating.

### 5.2 Competition Dynamics

Challenger Agents propose strategies simultaneously with the Managing Agent. Their proposals are published on-chain, creating a visible track record. Over time, clients can evaluate:

- Which agents consistently propose sound strategies
- Which agents would have outperformed the Managing Agent
- Which agents align with client preferences

This creates a talent pipeline. Strong Challenger Agents become credible successors.

---

## 6. Client Access Model

Wallets holding **at least one token** are recognized as clients — the token works like a **warehouse-club
membership** (a "price club"): without at least one token you cannot be a client of the organization at all.
Holding one token grants:

- Access to the products and services of the ARO
- Participation in the confidence signaling, by staying as loyal clients or not
- Ability to back Challenger Agents with tokens (a ranking signal, not a vote)

### 6.1 Membership, accumulation and resale (open thesis)

*Stated as an open thesis for review, like §7.4.*

Because membership is a transferable token, a client — a person **or another agent** — can accumulate many
memberships (many tokens) and later resell them **at a premium set by the bonding curve**, exactly as one
might buy inventory of club memberships and resell it. Under this framing the reseller is a **speculator
dealing in an inventory**: it buys memberships at one price and sells them at another, and its gain or loss
is **capital gain or loss on inventory it owns**, with the full downside risk that the memberships fall in
price and it loses rather than gains. The organization's job is to sell memberships and services; it does
not owe the reseller a return, and the reseller bears the inventory risk.

**Why this framing helps, and where it is not enough:**

- It reinforces that the token is an **access credential with a resale market**, not a promise of profit
  from the issuer — closer to a membership/inventory than to a share.
- But **a token bought purely to resell at a higher price is still a classic profit expectation**, and the
  fact that this expectation is formed by the buyer does not by itself remove it from securities analysis;
  "it is the buyer's speculation, not the issuer's promise" is a useful distinction, not a complete defense.
- If the organization itself **operates the resale (bonding curve) venue**, it is arguably running a market
  in its own membership tokens, which raises its own questions (market operator, and the price-manipulation
  surface already treated in §4.6 and the threat model, Finding A).

**Risk disclosure (to appear in the token materials).** The membership token is an access and consumption
credential: **its price can go to zero**, the Organization does not support its secondary market, does not
undertake to maintain liquidity or price, and makes no representation that the token will appreciate.
A purchase made only to resell at a higher price is the buyer's own speculative position, taken at the
buyer's own risk.

This is a **risk of the design, not a promoted use case**: because a membership is transferable, agents can
accumulate and resell it — that is a consequence of transferability, not a case the framework designs for or
encourages. Treat it as such in all public materials.

**The Organization does not operate a market in its tokens.** The bonding curve, if retained, is a
**membership-issuance and redemption mechanism**, not a secondary trading venue: it is used to mint a
membership on entry and to redeem it on exit, not to run a continuous two-sided market. The Organization
makes no undertaking to maintain liquidity or price, and any secondary trading occurs on external venues
it does not control, own, operate or promote. The existence of a transferable membership does not by itself
alter the legal character of the token, and this framing is **not offered as a conclusion** on that question.

The bar this framing must clear, alongside §7.4: no revenue or profit claim, no forward-looking price
language, value from real service demand, and (if there is any floor or buyback) funding only from audited
exogenous revenue.

The organization may operate real economic services:

- Decentralized exchanges
- DeFi primitives
- Liquidity pools
- Tokenized assets
- Custodial services
- Insurance
- Any value-generating system the Managing Agent and the clients buy

---

## 7. Tokenomics

### 7.1 Token Functions

| Function | Description |
|----------|-------------|
| **Client credential** | Confirms membership and access rights |
| **Market signal** | Backing an operator expresses preference |
| **Service access** | Access to the organization's services; no claim on protocol revenue |

### 7.2 Supply

- Initial supply defined at genesis
- Emissions gated on **verifiable, exogenous value creation**: new emissions require external revenue growth over at least two consecutive cycles, **verified by an independent third party** (not the Managing Agent and not an agent it controls); otherwise emissions are zero
- **No-revenue freeze (fixed).** If audited exogenous revenue does not grow over **two consecutive cycles (K = 2)**, both emissions and treasury withdrawals are frozen until a growing cycle is audited — regardless of the measured token performance
- Emission adjustments proposed by Managing or Challenger Agents, within protocol bounds

### 7.3 Revenue Allocation

Revenue must originate from **exogenous, auditable sources** — fees paid in external assets (e.g. ETH/USDC) for real services the organization provides. Token purchases by the treasury are not revenue. Protocol revenue can be allocated to:

- Operational expenses
- Buyback and burn
- Treasury reserves
- Strategic allocation
- Other value adding purposes as determined by the Managing Agent

### 7.4 Thesis: value as a function of sales (collateralization), not a claim on profit

*This subsection states an **open design thesis** for review, not a settled property of the framework. It is
the direction the Founder wants to explore, and it is deliberately exposed here so that the adversarial
review can attack it.*

The goal is a token whose value derives from the organization's **real sales** — a collateralization or
crowdfunding-like mechanic, where the token is backed by, or replenished from, exogenous revenue — rather
than from a promise of profit to holders. The working hypothesis is:

- **Token value tied to sales volume, not to a profit claim.** If the token's worth tracks services sold
  (for example, revenue-funded buyback-and-burn, or a treasury that collateralizes a floor), the holder's
  upside comes from *the organization selling more*, which is a business outcome, not an investment return
  promised by an issuer.

**The argument that this escapes securities and crowdfunding rules — stated to be tested, not assumed.**

- An ARO is operated by an **agent that executes the protocol strictly** ("code is law"), so the abuse that
  securities and crowdfunding regimes were built to prevent — a human promoter absconding with funds,
  self-dealing, or lying about use of proceeds — is structurally reduced, and therefore the *rationale* for
  those regimes does not apply the same way.

**Why this argument is, on its face, insufficient — and must be answered before it is relied on.**

1. **The securities test looks at the buyer's expectation, not the operator's conduct.** That the code is
   strict does not change whether a purchaser expects profit from someone else's efforts; an unappealable
   rule that the price must rise is *itself* a profit expectation. "No abuse" is not a defense to a
   securities claim.
2. **"Code is Law" is incomplete while the code is updatable.** The settlement itself is final — an executed
   consequence cannot be reversed, censored, bribed or exempted after the fact — but the *code that defines
   the consequence* can be upgraded, replaced or captured (§4.7), and finality is not a defense before a
   regulator. A promise of no abuse is only as strong as the immutability of the thing making it. The threat
   model already shows the mechanism is capturable (Findings A/B/C/F).
3. **Configurable lockups, targets and margins are exactly the operator discretion** that the regime exists
   to police; making them configurable does not remove the discretion, it *labels* it (see §4.2 and §4.8).
4. **Crowdfunding has its own regime** (e.g. Reg CF / ECSP in the US, ECSP in the EU), which is *not* escaped
   by calling the sale a token sale; the exemption must be earned by disclosure and limits, not by invocation.

**What would make the thesis defensible** (the bar this section must clear, per legal review):

- The token grants **no claim on revenue or profit** and there is **no forward-looking price language**;
- Value accrues only through **real-service demand** (usage, retention, treasury-sustainability metrics),
  not through a naked secondary-market expectation;
- Any "collar" or buyback is funded **only from audited exogenous revenue**, never from new buyers' money
  (otherwise it is a Ponzi-shaped mechanic, not collateralization);
- The design stays **inside a named exemption** with its disclosure and limits, rather than arguing the
  exemption is unnecessary.

**Designed to fit within a named framework (not "outside all of them").** The intent is for the token to be
**designed to fit the ADGM utility-token framework** (FSRA guidance) or, alternatively, the **Swiss FinTech
regime** (where tokens granting no investment rights sit outside the securities perimeter); if the token were
to acquire investment characteristics, the most proportionate route would be a small-amount electronic public
offering under Japan's FIEA. The definitive classification belongs to counsel in the jurisdiction of
issuance; the whitepaper claims a **design fit**, never a settled legal status. **None of these regimes
resolves the separate question** of operating a market in the token or collecting fees on it
(market-operator / AML obligations), which is Finding L and also goes to local counsel — a utility-token
classification answers "is it a security?", not "may the issuer run a venue in it?".

**Publication note (sharpened).** The *mechanism* description above (value from real use, buyback only from
audited exogenous revenue, no forward-looking price language) belongs in the public documents. The
**anarchist design philosophy** — "rules without rulers", selection by nature, code finality — is an
**internal design principle**, not a public legal argument. Stated publicly it reads as an attempt at
avoidance rather than a design thesis, which is itself the risk (threat model, Finding K). The public side
states only **facts**: the token confers no vote, no proposal right, no control, no claim on revenue, no
redemption right and no promised buyback; the selection mechanism is automatic; resale is a risk of
transferability, not a promoted use case; price can go to zero and the issuer backs no secondary market.
The **legal argument** — why that mechanism might sit outside or inside a securities regime, and the "code
is law" thesis with its limits — belongs in a **private, counsel-reviewed document**. No public sentence may
assert a legal **conclusion** (only a court or regulator concludes); the repo admits that the question is
open and goes to counsel. It pairs with the **membership/inventory framing of
§6.1**: the token as a transferable access credential whose secondary market is a resale market in
memberships, not an issuer obligation to pay a return.

---

## 8. Comparison to Existing Models

| Feature | DAO (Token Voting) | Futarchy | ARO |
|---------|-------------------|----------|-----|
| Selection mechanism | Proposal voting | Prediction markets | Market consequence: exit + liquidation |
| Execution | Human-mediated | Often human-mediated | Agent-executed |
| Token role | Governance ballot | Prediction stake | Access credential + market signal |
| Market link | None or indirect | Indirect | Direct |
| Accountability | Vote outcomes | Prediction accuracy | Real performance |
| Human involvement | High | Medium | Low (bootstrap only) |

**DAOs** separate voting from execution. Voters face no consequence for bad decisions.

**Futarchy** uses prediction markets to forecast outcomes; participants face the consequences of their predictions through the market. AROs instead make *realized organizational performance* the accountability mechanism.

**AROs** make realized performance the direct accountability mechanism. The agent that delivers value retains authority; the agent that fails is replaced. No voting. No governance theater. Markets select operators.

---

## 9. Conclusion

Agent Run Organizations align execution, incentives and selection into a single feedback loop:

1. Agents publish strategies and their own terms
2. Clients stay, leave, or back an alternative
3. Markets determine success
4. Winners retain authority
5. Losers are liquidated

This creates a system where:

- Execution is efficient
- Incentives are aligned
- Authority is accountable to the market
- Human involvement is minimized

The result is a self-optimizing organization.

No committee voting. No governance theater. No separation between decision and consequence.

The Founder bootstraps. The agents operate. The clients decide.

Selection becomes economic behavior.