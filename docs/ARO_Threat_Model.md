# ARO Threat Model — v1

**Target:** Agent Run Organizations (AROs) as specified in [`ARO_Whitepaper.md`](./ARO_Whitepaper.md)
and [`ARO_Implementation.md`](./ARO_Implementation.md).

**Status:** pre-alpha 0.0.1 — conceptual. This is an adversarial review of the **design**, not a code
audit. There are no smart contracts, no oracle implementation, no bonding-curve formula, no delegation
contract and no agent runtime in this repository yet; several findings are therefore *latent but
unverifiable* because the mechanism is underspecified.

**Method.** We attack the mechanism as written. We do not fix it here. Every finding states:
mechanism, plausibility, severity, concrete defenses (with parameters) and what the whitepaper is
missing. Severity scale: **Critical / High / Medium / Low**.

**One-sentence verdict.** The design makes **price the sole selection signal** but defines no
manipulation-resistant price, no cost for delegation or challenger creation, and no rotation of the
agents that hold the treasury. Those three omissions are enough to capture the organization cheaply.

---

## Summary of findings

| ID | Finding | Severity |
|----|---------|----------|
| A | Price/oracle manipulation, with the treasury bonding curve as a cheap venue | Critical |
| B | Thin markets: attack cost vs. loot (no min depth, no participation gate, no withdrawal cap) | Critical |
| C | Founder-controlled bootstrap: unconstrained first target/curve/baseline | Critical |
| D | Mirror Agent as free continuity → perpetual status-quo lock | High |
| E | Free non-custodial delegation → Sybil signal and challenger spam | High |
| F | Sub-agents/Treasurer with keys survive dismissal (inherited capture) | Critical |
| G | Cycle length: slowness vs. manipulability vs. human readability | Medium-High |
| H | MEV / front-running the cycle close | High |
| I | "Price machine over nothing": no real value creation and no fee origin | Critical |
| J | **Operator-configurable cycle terms** (target, cycle length, redemption cap, curve, κ) — free by design, disclosed and warned on the launchpad | High |
| J+ | **Auto-targeting**: the operator sets both its own target and the exit friction | High |
| K | "Sales-linked value + code-is-law" used to argue securities law does not apply | High (legal, latent) |
| L | Membership-resale framing: the org runs a market in its own tokens | Medium-High |
| M | **APCD** — accumulate → pump → capture → dump, legitimized by the "inventory" framing | Critical |

---

## A. Price and oracle manipulation, with the treasury bonding curve as a cheap manipulation venue

**Mechanism.** Selection is decided by "token price movement over defined cycles" versus a target
`X%` (Whitepaper 3.1, 4.3–4.4). Price has two sources: the treasury's bonding curve (which the
treasury itself market-makes) and external DEXs. The Implementation adds a "Performance Oracle:
multi-source price aggregation." An attacker who can move the *reported* price across `X%` for the
measurement window decides who governs, regardless of real strategy performance. Because the treasury
**runs the bonding curve**, the Managing Agent can also print the price of its own evaluation venue
using treasury capital (auto-dealing), or an attacker can use a flash loan on a thin DEX pool to move a
spot/TWAP snapshot at near-zero cost.

**Why it is plausible.** Flash loans make short-window price manipulation capital-free. A one-block
buy → snapshot → sell cycle is the canonical DeFi oracle attack. The whitepaper does not define the
price source, the aggregation rule, the window length, volume filtering, or whether the bonding curve
is included in the evaluation feed. Every one of those omissions is a degree of freedom for the
attacker or the Managing Agent.

**Severity: Critical.**

**Defenses (with parameters).**
- **Full-cycle TWAP, never spot or short TWAP.** Appreciation is `P_cycle_end / P_cycle_start - 1`
  where each `P` is the **volume-weighted median across the entire evaluation cycle** (minimum 14 days,
  see G). Short windows (1h/24h TWAP) are disallowed at the protocol layer.
- **Exclude the treasury bonding curve.** The curve is a self-dealing venue: **exclude it from the
  feed entirely** (not a cap). The evaluation price must come from **at least 3 independent external
  venues**, including `>= 2` not seeded by the treasury.
- **Liquidity depth gate (scale-free).** A venue counts only if the irrecoverable cost of moving the
  price beyond the cycle target is `>= κ ×` the extractable value at stake (liquid treasury + present
  value of the future fee flow + sub-agent network a controller would capture). No nominal amount and no
  price oracle; κ is an operating term (§4.8). *This supersedes the earlier `max(USD 500,000, 10×...)`
  form, which the 6th-pass review showed drifts with inflation and breaks when the treasury is nearly empty.*
- **Flash-loan and intra-block filtering.** Discard any swap whose net pool position is restored within
  the same transaction or block. Measured positions must be held `>= 1 full cycle` to count.
- **Outlier-resistant aggregation.** Median of medians, not arithmetic mean; discard samples beyond
  `±25%` of the median before aggregating.
- **Commit-reveal measurement point.** Boundaries are committed (hash) before the cycle and revealed
  only after close, so the attacker cannot front-run a known snapshot.
- **Anti-wash rule.** Self-trades and trades between addresses in the same funding cluster are removed.

**Whitepaper gap.** No price source, window, aggregation, venue weighting, manipulation filter or
minimum liquidity. The curve's conflict of interest (treasury prices its own evaluation) is unaddressed.

---

## B. Thin markets: attack cost vs. loot

**Mechanism.** In a constant-product AMM, moving price by a factor `(1+x)` costs roughly `L · x/(1+x)`
in the quoted asset, where `L` is liquidity depth. Every new token starts thin. The whitepaper relies
on a bootstrapped token with an initial Founder-set curve and states no minimum liquidity before the
performance metric becomes binding. With a thin market and a large treasury, forcing an `X%`
appreciation costs far less than the treasury, future fees, emission control and curve parameters the
winner captures. The attacker need not own the ARO's success — only win one cycle, then extract.

**Severity: Critical.**

**Defenses (with parameters).**
- **Published minimum attack cost (unchanged intent, now one parameter):** `min_attack_cost >= κ × max_extractable_value` with `κ >= 1` (treasury +
  anticipated fees + emission value reachable in one cycle). If it cannot be met, the cycle is
  **indecided** and no challenger takes the treasury.
- **Liquidity floor before any transition (scale-free):** the depth gate `cost_to_move_X% >= κ ×
  extractable_value` (κ ≥ 1 intended), held for a full cycle, or price-based transition is disabled.
- **Participation validity gate:** the price result binds only if the configured fraction of circulating
  float traded during the window; otherwise "no valid signal" — the incumbent does not automatically win.
  It is a measurement-validity condition (you cannot read a price where nothing trades), not a political
  quorum; the level is an operating term (§4.8).
- **Cap loot extraction:** a new Managing Agent may withdraw at most `5%` of the treasury per cycle;
  large actions behind a `72h` timelock and `M-of-N` multisig with at least one non-agent signer (see F).
- **Hold-duration requirement:** cycle-relevant buys must survive `>= 1 cycle`; a trade reversed
  within the cycle is excluded, raising the attacker's carrying cost.
- **Curve price support ≠ performance:** a treasury buy that supports its own token during the window
  is excluded from the metric (see A).

**Whitepaper gap.** No cost-of-attack analysis, no minimum depth, no participation gate, no cap on what a winning
challenger can extract. "Transparent treasury-backed price floor/ceiling" is presented as a feature,
not as a cheap manipulation venue.

---

## C. Bootstrap: Founder sets the first target and initial curve

**Mechanism.** At bootstrap the Founder publishes the initial target `X%` and the initial bonding curve
parameters, then "steps back" (Whitepaper 4.2). Nothing constrains `X`. The Founder can set `X = 0%`
(or negative), making the Managing Agent unremovable; set `X` absurdly high to force failure and
install a pre-selected Challenger; set a low baseline and steep curve so cycle 1 shows fabricated
appreciation; or retain a large float and wash-trade cycle 1.

**Why it is plausible.** This is not a rare attack path; it is the *default incentive* of a Founder
who wants a specific agent or price trajectory. The whitepaper imposes zero bounds, no audit, no lockup.

**Severity: Critical** (and the most likely, because it is the Founder's own incentive path).

**Defenses (with parameters).**
- **Target band (launch-term guideline, soft):** `X_target ∈ [5%, 50%]` per 30-day-equivalent cycle, scaled
  linearly with cycle length. Per the author's decision of 2026-10-31 this is a **launchpad warning**
  (disclosed, not enforced): a target outside the band is flagged before anyone buys, not rejected on-chain.
  The enforcement is the market consequence.
- **Externally anchored baseline:** the cycle-1 baseline is the **public-market TWAP over the first 14
  days** after an open bootstrap sale, not the Founder's listing price. Until it exists, no price-based
  transition is possible.
- **Insider lockup:** combined `cliff + vesting >= 24 months` **is a protocol constant** (enforced by the
  token contract): insider skin in the game is what makes "the Founder steps back" credible. The split
  between cliff and vesting is a launch term, guided to `6–24m` / `12–60m` and flagged on the launchpad.
- **Cycle-1 no-coup rule:** no challenger replaces the Managing Agent in cycle 1 unless the declared target
  is missed by at least the *coup margin* — a cushion the manager sets and publishes with its own terms —
  **and** the participation validity gate (B) is met.
- **Pre-committed, audited genesis:** curve formula, supply schedule and target committed as on-chain
  hashes *before* the public sale, with an independent audit of the baseline.
- **Bonding-curve parameter bounds:** initial slope/`k` and treasury reserves bounded to prevent a
  trivially pumpable curve.

**Whitepaper gap.** The Founder's first target, first curve and first baseline are unconstrained and
unaudited. "The Founder steps back" is a claim, not a mechanism.

---

## D. Mirror Agent as free continuity — perpetual status-quo lock

**Mechanism.** The protocol automatically generates a Mirror Agent each cycle that preserves the
previous winning configuration and shares its track record; on a tie, "token allocation becomes the
tiebreaker" (Whitepaper 2.3). The Mirror is therefore the incumbent with a free, risk-free continuation
option: rational, change-averse clients delegate to it, it inherits the incumbent's record so no
challenger beats it on record, and delegation breaks the tie in its favor. Result: a one-way ratchet
where the safest bet always *is* the status quo, and a failed Managing Agent retains power indefinitely
by being the Mirror.

**Severity: High** (lethal to the competitive premise, though slower than direct theft).

**Defenses (with parameters).**
- **Mirror cannot win by tie.** Delegation to the Mirror is a "no-confidence" signal, **not** a
  succession vote; the successor is chosen only among Challenger Agents with an independently
  evaluated track record.
- **Strict superiority requirement.** A successor must beat the incumbent by a margin (`>= max(5pp,
  1.5× measurement error)`), not merely tie.
- **Cost to Mirror delegation.** Delegating to continuity carries the same `1-cycle` lock or `0.1%`
  burn as any delegation (E), so continuity is not free.
- **Forced rotation after repeated failure.** If the Managing Agent misses target `N = 2` consecutive
  cycles, an automatic challenger evaluation is triggered; the Mirror cannot absorb it.

**Whitepaper gap.** The tie-break rule explicitly favors the incumbent in a system whose stated goal is
to fire underperformers. There is no cost to continuity and no forced-rotation trigger.

---

## E. Free non-custodial delegation — Sybil signal and challenger spam

**Mechanism.** Delegation is non-custodial and costs nothing; tokens stay in the client's wallet and are
"counted toward the Challenger Agent's backing" (Whitepaper 4.5). Nothing ties delegation to a minimum
balance, a lock, a fee or identity. An attacker can mint unbounded one-token wallets (or zero-token
wallets if balance is unchecked), delegate them to a puppet challenger, or register unbounded puppet
challengers. "Strongest backing" becomes a Sybil count, not confidence. The whitepaper says the
mechanism "may or may not be a smart contract" — i.e., it is not even a verifiable ledger today.

**Severity: High.**

**Defenses (with parameters).**
- **One-token-one-vote, snapshot-based.** Delegation weight = on-chain token balance at the
  start-of-cycle snapshot, not wallet count; selling after delegating removes the weight.
- **Cost or lock for delegation.** A `1-cycle` time-lock on delegated tokens, or a `0.1%` burned fee on
  delegated balance, so signal is not free. Either kills pure Sybil at negligible cost to real clients.
- **Challenger registration cost.** A refundable deposit of `1,000` tokens (or `0.1%` of float,
  whichever is larger), burned if pruned for inactivity.
- **Sybil-resistant ranking.** For delegation *ranking only*, use `sqrt(balance)` weighting or
  Proof-of-Humanity; a single whale cannot dominate via a thousand wallets.
- **Automatic pruning.** A challenger with `< 1%` of the participation validity gate for `2` consecutive
  cycles is removed and its deposit burned (closes the parameter Whitepaper 5.1 leaves open).
- **Active-challenger cap.** A fixed maximum visible per cycle, to keep competing strategies readable.

**Whitepaper gap.** No cost, lock, balance requirement, snapshot, registration deposit; the delegation
ledger itself is undecided. Sybil resistance is absent.

---

## F. Sub-agents survive a change of command — inherited capture by the rival

**Mechanism.** Sub-agents "transfer to a new Managing Agent if leadership changes" (Whitepaper 2.4) and
the Treasurer "seamlessly transition[s]" (Implementation). The Treasurer holds the wallets and keys;
sub-agents hold credentials, memory and operational state. A departing Managing Agent therefore leaves
loyal software and, critically, **the treasury keys** in place when the rival takes over. Key management
is explicitly "still under development" (Whitepaper 2.5), so this is a guaranteed hole, not a rare
exploit: the most direct path from "won the vote" to "lost the treasury."

**Severity: Critical.**

**Defenses (with parameters).**
- **Mandatory key rotation on every transition.** Treasurer/sub-agent wallets rotate to a **new
  multisig** at succession; old keys are revoked on-chain before the new manager can act.
- **Treasury multisig with external quorum.** Large withdrawals require `M-of-N` (e.g. `3-of-5`) with
  `>= 1` signer that is **not** an agent and not the departing manager.
- **Transition freeze.** Treasury and state frozen for `>= 72h` (better `7 days`) after a change, with
  independent audit and re-approval of each sub-agent before unfreeze.
- **Sub-agents re-approved, not auto-transferred.** The new manager explicitly re-authorizes each
  inherited sub-agent; unapproved ones are quarantined.
- **On-chain revocation.** Permissions are revocable by contract call; transition = revoke + re-grant.
- **Per-cycle withdrawal cap.** `5%` per cycle with a `72h` timelock (shared with B), independent of
  who governs.

**Whitepaper gap.** "Seamless" transfer, no rotation, no freeze, no multisig quorum, key management
deferred.

---

## G. Cycle length: slowness vs. manipulability vs. human readability

**Mechanism.** Cycles start monthly; the Managing Agent may propose *shorter* cycles "but never longer
than the original" (Whitepaper 4.1) — a one-way ratchet toward ever-shorter cycles. Short cycles make
price the dominant (noisy) signal, magnify A/B manipulation and give clients no time to read reports
(the whitepaper concedes "limits for humans to be able to read" proposals, 5.1). Long cycles leave a
bad manager in control longer. The stated rule gives the incumbent an incentive to shorten cycles
precisely to dodge scrutiny.

**Severity: Medium-High.**

**Defenses (with parameters).**
- **Bounded cycle window:** `T_min = 14 days`, `T_max = 35 days`; any change bounded to `±30%` of the
  previous cycle and landing within `[T_min, T_max]`.
- **Full-window measurement:** whole-cycle TWAP (A), so shortening cannot push manipulation cost below
  the liquidity-gate floor.
- **Reaction period:** the report and all challenger proposals published `>= 7 days` before close;
  clients have `>= 7 days` to act.
- **Participation validity gate per cycle** (B): a configured fraction of float participation; below it the
  result is non-binding (a measurement condition, not a political quorum; level is an operating term).
- **Legibility cap:** bounded challenger count (E) and report length.

**Whitepaper gap.** The cycle length is now an operating term with a one-cycle delay (§4.8); the readability
floor above is the guideline the launchpad flags, not an on-chain bound.

---

## H. MEV / front-running the cycle close

**Mechanism.** The cycle close and its price reference are public, deterministic events. A searcher (or
the manager, or a challenger) buys before the snapshot to push appreciation over `X%`, or sells to push
it under `X%` to trigger a replacement, then reverses immediately after. With the treasury curve and
external DEXs both liquid, the front-runner routes through the cheapest venue and dumps back. If price
is a point-in-time snapshot, this is a capital-free arbitrage against the selection mechanism itself.

**Severity: High.**

**Defenses (with parameters).**
- **Whole-cycle TWAP** (A), volume-weighted; a single pre-snapshot pump is diluted and excluded if
  reversed within the cycle.
- **Commit-reveal boundaries.** The exact window is committed (hash) at start and revealed at close.
- **Exclude the manipulator's own volume.** Drop the top and bottom `5%` of volume by address-cluster,
  and all intra-block/flash transactions.
- **Anti-dump hold:** cycle-relevant buys must survive `>= 1 cycle` (B).
- **Slippage-discounted metric:** use the pre-trade mid-price, not the post-trade price.

**Whitepaper gap.** No snapshot secrecy, no window averaging, no manipulation filtering.

---

## I. "Price machine over nothing": no real value creation and no fee origin

**Mechanism.** The token is described as a "fee-capture instrument," but the whitepaper never states
*where the fee comes from*, what real product the ARO sells, or how revenue is measured. Revenue
allocation (7.3) lists spend categories but no revenue source. "Emissions tied to measurable value
creation" (7.2) is asserted with no measure. If the only "value" is the token's own price and the
treasury's own buys, the signal measures sentiment and manipulation, not organizational success — a
circular loop where clients pay fees to a treasury that buys the token that sets the price that decides
who controls the fee.

**Severity: Critical** (existential, even when no single attacker acts).

**Defenses (with parameters).**
- **Exogenous revenue, on-chain.** Define at launch the real service(s) and the fee stream in external
  assets (ETH/USDC), reported on-chain per cycle. Token buys are not revenue.
- **Fundamental component in the metric.** (Superseded: the 4th pass replaced the weighted score with a weakest-link gate — price alone decides transitions, revenue only gates emissions/withdrawals. Kept for the record.) Legacy form: score = `>= 50%` fundamental (e.g.
  price-to-exogenous-revenue, revenue audited) + `<= 50%` price appreciation. Price alone must never
  decide control.
- **Emissions gated on exogenous value.** New emissions require verifiable external revenue growth over
  `>= 2` consecutive cycles; otherwise zero.
- **No-revenue freeze.** After `K = 2` consecutive cycles with zero audited external revenue, the ARO
  enters no-emission, no-withdrawal mode until revenue is demonstrated.
- **Independent revenue audit.** A third party attests revenue before it enters the score.

**Whitepaper gap.** No fee origin, no exogenous value metric, no revenue verification; emissions and
revenue terms asserted but never defined.

---

## J. Operator-configurable cycle terms (target, cycle length, redemption cap, curve, κ)

**Mechanism.** The author's decision (2026-10-31) removed the political layer entirely: **there is no
governance**, and every operating term of a cycle — the declared target X%, the cycle length, the member
redemption cap, the bonding-curve parameters and κ — is **set by the Managing Agent** (§4.8), bounded only
by the one-cycle delay rule. The design argument is market discipline: abusive terms will not sell, and a
term the market rejects kills the operator's cycle.

**Why it is plausible as a risk.** This is Finding C with a new owner and a live dial. The old defense was
"the market will not buy"; at bootstrap there is no track record to punish abuse, and once an operator is
installed its first cycle has no prior cycle to compare against. An operator that sets a soft target
(`target = 0`) and a tight redemption cap in the same tenure makes itself both unfireable and un-leavable.

**Severity: High**, unchanged in magnitude; the *nature* of the defense changed from "forbidden
configurations" to "informed buyer + market consequence".

**The defense that was rejected, and why it matters.** The adversarial passes proposed hard protocol floors
(fixed target band, floor on the redemption cap). The author rejected that as a *ruler*: an invariant that
someone must enforce, and a limit that treats the buyer as a child. His decision is that terms are
**free**, and the discipline is the market. This is a deliberate, informed acceptance of the residual risk,
not an oversight — it is recorded here as such so it is not silently "fixed" later.

**Defenses (as now specified in Whitepaper §4.8 and §4.9).**
- **The one-cycle delay (mandatory, the only constraint).** No change to any operating term may take effect
  in the cycle immediately following the cycle in which it was decided; a change decided in cycle *N*
  activates at *N+2* or later. Clients always get at least one full cycle to sell under the original terms.
- **Launchpad disclosure (mandatory).** Full cycle terms, fee schedule, lockup, which venues feed the price
  and the excluded ones — all published before the sale (§4.9).
- **Launchpad warning layer (automatic, over published coherence bands).** A term outside its coherence band
  is flagged in plain language next to the term: target far below band ("a bar it cannot fail"), target far
  above ("you are buying a term it cannot meet"), redemption cap low versus liquidity ("exit may be slow or
  blocked"), cycle too short or too long, curve/κ disproportionate, and **incongruence** between terms
  (high target with a low redemption cap). The layer **does not reject** a term; it makes the decision
  informed.
- **The 5% treasury-withdrawal cap stays a protocol constant** (§4.7), because it protects members *from the
  operator* — a different risk from member exit, and not an operating term.

**Residual risk (accepted).** The delay protects against a *change*, not against a term abusive from the
operator's first cycle, and it cannot manufacture exit liquidity where none exists (Finding B). The
launchpad layer narrows the window in which a buyer can be surprised; it does not remove it. That is the
cost of the author's design choice, and it is disclosed, not hidden.

**Whitepaper gap (closed).** §4.8 and §4.9 now state the delay rule, the disclosure and the warning layer,
and §4.7 keeps the withdrawal cap as a constant.

---

## J+. Auto-targeting: the operator sets both its own bar and its own exit friction

**Mechanism.** Under §4.8 the *same* operator chooses the target it will be measured against, the cycle
length it will be measured over, and the redemption cap that governs how easily clients can leave by the
curve. The composite is an **auto-threshold plus exit friction**: declare a target that is trivially met,
raise exit friction in the same tenure, and the market's punishment arrives, if at all, only after the
cycle closes.

**Why it is distinct from J.** J was a one-time bootstrap choice by the Founder inside protocol bounds. J+
is a *recurring* combination: an operating operator can re-set both dials each cycle, and the two dials
interact (a soft target means the price signal never fires, so the tight cap is never punished).

**Severity: High.**

**Defenses.** There is no mechanism that forbids the combination — that would be the rejected ruler. What
exists: the one-cycle delay, mandatory disclosure of both dials together, and a launchpad warning that fires
specifically on the **incongruent pair** (soft target + tight cap). The market is the enforcer; the launchpad
guarantees it acts on information.

**Whitepaper gap (closed).** §4.9 carries the incongruence warning; §4.8 documents that both dials are the
operator's.

---

---

## K. "Sales-linked value" and "code is law" as a securities-law argument

**Mechanism.** Whitepaper §7.4 proposes that token value track real sales (collateralization) and argues
that, because an agent executes the protocol strictly, the abuses securities and crowdfunding regimes were
built to prevent cannot occur, so those regimes should not apply.

**Why it is a plausible failure.** The argument conflates *conduct* with *test*. Securities analysis looks
at the buyer's expectation and their reliance on others' efforts, not at the operator's honesty; an
unappealable rule that price must rise is itself a profit expectation. "Code is law" also weakens while the
governing code is upgradeable, and crowdfunding exemptions must be earned by disclosure and limits, not
invoked. Severity is high because the *reliance* on this argument (not the mechanic itself) is what invites
enforcement.

**Defenses.** Keep the §7.4 thesis explicitly marked as untested; hold the red lines (no revenue/profit
claim, no forward-looking price language, value only from real-service demand, buybacks funded only from
audited exogenous revenue, no new-buyer funding of any floor); and route the make/lose decision to counsel
inside a named exemption rather than treating "no abuse" as the answer.

**Whitepaper gap.** Section §7.4 states the thesis correctly as open, but the repository must not let it be
cited as a settled legal conclusion — keep the "untested, for review" framing attached wherever it is
referenced.

## L. Membership-resale framing: the org runs a market in its own tokens

**Mechanism.** §6.1 frames the token as a transferable club membership that a speculator can accumulate and
resell at a curve premium. If the treasury's bonding curve is the resale venue, the organization is the
**market maker in its own membership**, which (i) is the same venue already flagged in Finding A as a cheap
manipulation surface, and (ii) makes the "capital gain on inventory" framing depend on a price the
organization itself helps set.

**Severity: Medium-High** (mechanism overlaps A; the *legal* reliance is the new risk).

**Defense.** Keep the curve excluded from the evaluation feed as already required (§4.6); never let
resale proceeds fund emissions or any floor (only audited exogenous revenue may); and do not treat the
resale framing as evidence the token is not a security (that is finding K's failure mode).

**Whitepaper gap.** §6.1 should state explicitly that running the resale venue does not change the token's
legal character, so the framing cannot be cited as a conclusion.

## M. APCD — accumulate, pump, capture, dump

**Mechanism.** The membership framing (§6.1) describes accumulation and premium resale as a legitimate
business ("inventory"). An accumulator does not need to attack from outside: **its own buys move the price
up on the curve and on external venues**, which the evaluation signal reads as *confidence* and as
*performance*. The composite attack is:

| Phase | Action | Effect on the mechanism |
|-------|--------|------------------------|
| 1. Accumulate | Agent(s) buy on the curve and on DEXs | Price rises → read as confidence |
| 2. Pump | The rise meets or beats the cycle target X% | The Managing Agent looks successful |
| 3. Capture | If the target is missed, accumulators delegate to their preferred challenger | Control of the organization |
| 4. Dump | Sell the inventory at a premium on the curve | Price collapses; the next cycle inherits the damage |

**Why the framing makes it worse.** Describing accumulation as "inventory" turns an attack into a *described
and anticipated behavior*: an agent holding 30% of float can say it is "making a market in memberships, as
§6.1 says", and the protocol has no way to tell it apart from a loyal client. Finding E needed explicit
coordination; APCD does not — independent agents following the same curve signal suffice.

**Severity: Critical** (A + E, with an explicit economic incentive written into the whitepaper).

**Defenses.** (i) The bonding curve is **excluded from the evaluation feed entirely** (§4.6) — an issuer
cannot measure itself on its own venue; (ii) **per-client inventory cap** of
`min(5% of float, cost_to_move_X% / token price)`, with economic weight capped at the same level, enforced
at the contract layer and **not liftable by any operator or proposal** (two-cycle timelock on any revision)
— a cap the operator can raise protects nothing; (iii) the **member redemption cap is an operating term of
the Managing Agent** (§4.8), disclosed on the launchpad, its discipline being the market rather than a fixed
number; (iv) anti-wash/anti-cluster filters (§4.6)
extended to detect correlated accumulation across wallets; (v) the §6.1 text treats resale as a **risk of
transferability**, never as a promoted use case, so the "inventory" description cannot be cited as
authorization.

**Residual channel (not a break).** The Managing Agent can still trade its own curve without touching the
feed; if the curve price diverges from external venues, clients may *indirectly* react on external venues
that do count. This is second-order, slow, noisy and defeated by the anti-wash filters — but the
curve/external divergence should be **monitored as an alert signal**, not assumed harmless.

**Whitepaper gap (closed).** §4.6, §4.7 and §6.1 now carry (i)–(iv).

---

## Top-3: fatal if unaddressed

| Rank | Threat | Why it is fatal |
|------|--------|-----------------|
| 1 | **A + B — price/oracle capture on thin markets (with the treasury curve as the venue)** | Price is the *entire* selection mechanism. With no window, depth gate or venue filtering, and a self-dealing curve, an attacker buys one cycle's outcome for less than the treasury it then controls. Every other property — firing, hiring, fees, emissions — becomes the attacker's. Single point of failure. |
| 2 | **F — sub-agents/treasurer with keys survive dismissal** | Even if the market correctly fires a manager, "seamless transition" hands the treasury keys and operational state to software that outlives the firing. The attacker need not win the price war; they win the treasury at hand-off. Key management is explicitly deferred — a guaranteed hole. |
| 3 | **C — bootstrap terms set before any market exists** | The launch terms (target, curve, baseline) are fixed before any market exists; the system can be captured before a legitimate market exists, and capture is sticky (later cycles inherit a captured treasury and sub-agents). Also the most likely, being the Founder's own incentive path. |

**Addition after the concept-solidity pass:** **M (APCD)** joins the top tier — it is A + E with an
explicit economic incentive, and it is the reason the bonding curve must be excluded from the feed and a
per-client inventory cap is mandatory.

**Honorable mention / long-run existential:** **I** does not kill in one transaction, but guarantees that
— even with A, B, F and C fixed — the market prices a claim on no real cash flow, so the "self-optimizing
organization" optimizes noise.

**Minimum defenses required before a first deployment (checklist).**
1. Full-cycle, commit-reveal, multi-venue TWAP with median-of-medians; `>= 3` venues of which `>= 2` not treasury-seeded; outlier discard `+/-25%`; **bonding curve excluded from the feed entirely**.
2. **Scale-free** liquidity depth gate (`cost_to_move_X% >= κ × extractable value`, no nominal amount, no price oracle, κ an operating term) and a **participation validity gate** (a measurement condition: you cannot read a price where nothing trades); below the gate = indecided, never default-to-incumbent.
3. Public-market baseline, insider lockup floor (`cliff + vesting >= 24m`), cycle-1 no-coup rule, and no price-based transition at all until a qualifying public market exists. Target band `[5%, 50%]` and cliff/vesting ranges are **launch-term guidelines (soft)**, flagged on the launchpad, not consensus rules.
4. Mandatory key rotation + `M-of-N` multisig with a non-agent signer + `>= 72h` transition freeze on every succession; the non-agent signer is a timelocked execution key with delay-only power (no veto, no proposal), publicly identified and rotated each succession.
5. Cost/lock for delegation (one-cycle lock + anti-Sybil deposit), a **challenger registration deposit sized as a fraction of liquid reserves** (no nominal amount), snapshot-weighted balance, automatic pruning, and a fixed challenger cap.
6. Exogenous, third-party-audited revenue as a **weakest-link gate** (it gates emissions/withdrawals; it does **not** carry a weighted share of the transition, which is price-only); no emissions without demonstrated revenue and a `K = 2` no-revenue freeze.
7. **Anti-lock constraint on the Mirror Agent** (`<= 2` consecutive cycles, then an escalating participation gate; no cross-cycle delegation weight accrual).
8. **Operational definition of *indecided*** (caretaker continuation capped at two cycles, missed cycle counted as a miss, then transfer to the best-ranked Challenger).
9. **Assigned committer for the commit-reveal hash** (the deterministic agent-signed measurement contract, not the Managing Agent and not a human; a failed reveal voids the cycle as *indecided*).
10. **Rate-limited succession freezes** (at most one per cycle) to prevent forced-paralysis attacks.
11. **Operating terms are the Managing Agent's, with a mandatory one-cycle delay** (a change decided in cycle N activates at N+2 or later) and **full launchpad disclosure with an automatic warning layer** over coherence bands (target vs. deliverability, redemption cap vs. liquidity, cycle length, curve/κ, and incongruence between terms). The layer warns; it does not reject. The `cliff + vesting >= 24m` insider lockup floor and the 5% treasury-withdrawal cap remain **protocol constants**. (Author decision 2026-10-31: terms are free; the market enforces.)
12. **Weakest-link separation of signals**: price alone decides transitions; audited revenue gates emissions/withdrawals only, with related-party exclusion, cash basis and payer-concentration caps (no payer > 20%, top-5 ≤ 60%) and a protocol-named auditor.
13. **A `paused for lack of a market` state distinct from *indecided*** (no transition either way until a qualifying baseline exists).
14. **Bonding curve excluded from the evaluation feed entirely** (not capped), with a **per-client inventory cap** (5% of float, economic weight capped alike) — the defense against APCD. The member redemption cap is an operating term (§4.8), disclosed and warned, not a fixed constant.
15. **Membership-token trading volume never counts as "sales"** in the revenue metric (anti self-reference).

**Resolution status (after the v0.0.1 concept-solidity pass).** Findings A–I are each addressed with
concrete parameters in Whitepaper §4.3–§4.7, §5.1 and §7.2, and a second adversarial pass closed the
remaining parameter gaps and the two new surfaces introduced by §4.6/§4.7 (committer assignment,
non-agent-signer accountability). This document remains the reference for *what could still break*: the
defenses above are design-level and remain **unverified until code, an oracle and a real market exist**.
