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

**One-sentence verdict.** The design makes **price the sole governance signal** but defines no
manipulation-resistant price, no cost for delegation or challenger creation, and no rotation of the
agents that hold the treasury. Those three omissions are enough to capture the organization cheaply.

---

## Summary of findings

| ID | Finding | Severity |
|----|---------|----------|
| A | Price/oracle manipulation, with the treasury bonding curve as a cheap venue | Critical |
| B | Thin markets: attack cost vs. loot (no min depth, no quorum, no withdrawal cap) | Critical |
| C | Founder-controlled bootstrap: unconstrained first target/curve/baseline | Critical |
| D | Mirror Agent as free continuity → perpetual status-quo lock | High |
| E | Free non-custodial delegation → Sybil signal and challenger spam | High |
| F | Sub-agents/Treasurer with keys survive dismissal (inherited capture) | Critical |
| G | Cycle length: slowness vs. manipulability vs. human readability | Medium-High |
| H | MEV / front-running the cycle close | High |
| I | "Price machine over nothing": no real value creation and no fee origin | Critical |
| J | Founder-configurable bootstrap parameters (cliff, vesting, coup margin, quorum) | High |
| K | "Sales-linked value + code-is-law" used to argue securities law does not apply | High (legal, latent) |
| L | Membership-resale framing: the org runs a market in its own tokens | Medium-High |
| M | **APCD** — accumulate → pump → capture → dump, legitimized by the "inventory" framing | Critical |

---

## A. Price and oracle manipulation, with the treasury bonding curve as a cheap manipulation venue

**Mechanism.** Governance is decided by "token price movement over defined cycles" versus a target
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
is included in the governance feed. Every one of those omissions is a degree of freedom for the
attacker or the Managing Agent.

**Severity: Critical.**

**Defenses (with parameters).**
- **Full-cycle TWAP, never spot or short TWAP.** Appreciation is `P_cycle_end / P_cycle_start - 1`
  where each `P` is the **volume-weighted median across the entire governance cycle** (minimum 14 days,
  see G). Short windows (1h/24h TWAP) are disallowed at the protocol layer.
- **Exclude or cap the treasury bonding curve.** The curve is a self-dealing venue: weight it `<= 10%`
  of the aggregate or exclude it entirely. Governance price must come from **at least 3 independent
  external venues**, including `>= 2` not seeded by the treasury.
- **Liquidity depth gate.** A venue counts only if its on-chain, verifiable liquidity in the `±5%`
  band is `>= max(USD 500,000, 10 × cost_to_move_price_by_X_target)`.
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
- **Published minimum attack cost:** `min_attack_cost >= 10 × max_extractable_value` (treasury +
  anticipated fees + emission value reachable in one cycle). If it cannot be met, the cycle is
  **indecided** and no challenger takes the treasury.
- **Liquidity floor before any transition:** aggregate feed depth `>= max(USD 500,000, 10× cost-to-move-X%)`,
  held for a full cycle, or price-based transition is disabled.
- **Participation quorum:** the price result binds only if `>= 20%` of the circulating float traded
  during the window; otherwise "no valid signal" — the incumbent does not automatically win.
- **Cap loot extraction:** a new Managing Agent may withdraw at most `5%` of the treasury per cycle;
  large actions behind a `72h` timelock and `M-of-N` multisig with at least one non-agent signer (see F).
- **Hold-duration requirement:** governance-relevant buys must survive `>= 1 cycle`; a trade reversed
  within the cycle is excluded, raising the attacker's carrying cost.
- **Curve price support ≠ performance:** a treasury buy that supports its own token during the window
  is excluded from the metric (see A).

**Whitepaper gap.** No cost-of-attack analysis, no minimum depth, no quorum, no cap on what a winning
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
- **Protocol-bound target range:** `X_target ∈ [5%, 50%]` per 30-day-equivalent cycle, scaled linearly
  with cycle length; out-of-range targets rejected on-chain.
- **Externally anchored baseline:** the cycle-1 baseline is the **public-market TWAP over the first 14
  days** after an open bootstrap sale, not the Founder's listing price. Until it exists, no price-based
  transition is possible.
- **Founder/insider lockup:** `12-month cliff + 36-month linear vesting`, no selling during cycle 1,
  enforced by the token contract.
- **Cycle-1 no-coup rule:** no challenger replaces the Managing Agent in cycle 1 unless the target is
  missed by `>= 10 percentage points` **and** the participation quorum (B) is met.
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
- **Automatic pruning.** A challenger with `< 1%` of quorum for `2` consecutive cycles is removed and
  its deposit burned (closes the parameter Whitepaper 5.1 leaves open).
- **Active-challenger cap.** Maximum `5–10` visible per cycle, to keep proposals readable.

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
- **Participation quorum per cycle** (B): `< 20%` float participation → non-binding result.
- **Legibility cap:** bounded challenger count (E) and report length.

**Whitepaper gap.** Only an upper bound is stated; no minimum, no per-cycle change limit, no publication
lead time, no quorum tied to cycle length.

---

## H. MEV / front-running the cycle close

**Mechanism.** The cycle close and its price reference are public, deterministic events. A searcher (or
the manager, or a challenger) buys before the snapshot to push appreciation over `X%`, or sells to push
it under `X%` to trigger a replacement, then reverses immediately after. With the treasury curve and
external DEXs both liquid, the front-runner routes through the cheapest venue and dumps back. If price
is a point-in-time snapshot, this is a capital-free arbitrage against the governance mechanism itself.

**Severity: High.**

**Defenses (with parameters).**
- **Whole-cycle TWAP** (A), volume-weighted; a single pre-snapshot pump is diluted and excluded if
  reversed within the cycle.
- **Commit-reveal boundaries.** The exact window is committed (hash) at start and revealed at close.
- **Exclude the manipulator's own volume.** Drop the top and bottom `5%` of volume by address-cluster,
  and all intra-block/flash transactions.
- **Anti-dump hold:** governance-relevant buys must survive `>= 1 cycle` (B).
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
- **Fundamental component in the metric.** Governance score = `>= 50%` fundamental (e.g.
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

## J. Founder-configurable bootstrap parameters (cliff, vesting, coup margin, quorum)

**Mechanism.** Whitepaper §4.2 now lets the Founder set four values inside protocol-guarded ranges: cliff
(0–24m), vesting (0–60m), the cycle-1 coup margin (0–50 pp) and the participation quorum (10–50% of float).
The design argument is market discipline: abusive terms will not sell.

**Why it is plausible as a risk.** This is Finding C with a new dial set. A Founder who wants to keep
control sets cliff = 0, vesting = 0 (free to sell), coup margin = 50 pp and quorum = 50% — the *maximum*
barriers to being replaced — and the market, at bootstrap, has no prior track record by which to punish
it. "The market will not buy" is only true for a market that already exists; cycle 1 has none.

**Severity: High** (was Critical under C; the bounds reduce, but do not remove, the dial).

**The first version of this defense was wrong.** It prohibited the *corner* (`cliff=vesting=0`, coup margin
and quorum at maximum) instead of the *region*: `cliff=0, vesting=0, coup margin=49 pp, quorum=49%` is just
as entrenching and sidestepped the prohibition. Prohibiting a point while leaving its neighborhood open is
a patch, not a defense. The rule must be a set of **hard limits rejected on-chain at launch**, with safety
*floors* on every dial, not a forbidden maximum.

**Defenses (as now specified in Whitepaper §4.2).**
- **Cliff `6–24` months** (floor 6: a `0` cliff lets an insider dump before any market exists).
- **Vesting `12–60` months** (floor 12).
- **Combined lock floor: `cliff + vesting >= 24` months** — the founder's skin in the game is what makes
  "the Founder steps back" credible; without it the Founder can exit while keeping operational control via
  a puppet manager.
- **Coup margin `5–20` pp *and* `<=` the cycle-1 target X** — `0` removes the no-coup cushion; a margin
  above the target makes the transition logically unrealizable.
- **Quorum hard floor 20%** (configurable only in `20–30%`): the quorum is a *security gate*, not a product
  preference. A 10% floor is Finding B (a thin market binds, manipulation is cheap); a 50% ceiling is a
  permanent-*indecided* entrenchment vector.
- **Fixed for bootstrap and cycle 1**, the phase defined as ending when the first qualifying public-market
  baseline exists (§4.6); afterwards, any change requires a governance proposal, never a unilateral Founder
  decision.
- All values published on-chain before the sale.

**Whitepaper gap (closed).** §4.2 now states hard limits with floors and fixes them for the phase.

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

**Defense.** Keep the curve excluded/capped in the governance feed as already required (§4.6); never let
resale proceeds fund emissions or any floor (only audited exogenous revenue may); and do not treat the
resale framing as evidence the token is not a security (that is finding K's failure mode).

**Whitepaper gap.** §6.1 should state explicitly that running the resale venue does not change the token's
legal character, so the framing cannot be cited as a conclusion.

## M. APCD — accumulate, pump, capture, dump

**Mechanism.** The membership framing (§6.1) describes accumulation and premium resale as a legitimate
business ("inventory"). An accumulator does not need to attack from outside: **its own buys move the price
up on the curve and on external venues**, which the governance signal reads as *confidence* and as
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

**Defenses.** (i) The bonding curve is **excluded from the governance feed entirely** (§4.6) — an issuer
cannot measure itself on its own venue; (ii) **per-client inventory cap** of
`min(5% of float, cost_to_move_X% / token price)`, with governance weight capped at the same level, enforced
at the contract layer and **not liftable by ordinary governance** (two-cycle timelock on any revision) — a
cap the captured governance can raise protects nothing; (iii) aggregate per-cycle **redemption cap of ≤10%
of liquid treasury reserves** so a dump cannot drain the treasury; (iv) anti-wash/anti-cluster filters (§4.6)
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
| 1 | **A + B — price/oracle capture on thin markets (with the treasury curve as the venue)** | Price is the *entire* governance mechanism. With no window, depth gate or venue filtering, and a self-dealing curve, an attacker buys one cycle's outcome for less than the treasury it then controls. Every other property — firing, hiring, fees, emissions — becomes the attacker's. Single point of failure. |
| 2 | **F — sub-agents/treasurer with keys survive dismissal** | Even if the market correctly fires a manager, "seamless transition" hands the treasury keys and operational state to software that outlives the firing. The attacker need not win the price war; they win the treasury at hand-off. Key management is explicitly deferred — a guaranteed hole. |
| 3 | **C — founder-controlled bootstrap** | The Founder sets an unconstrained first target, curve and baseline; the system can be captured before a legitimate market exists, and capture is sticky (later cycles inherit a captured treasury and sub-agents). Also the most likely, being the Founder's own incentive path. |

**Addition after the concept-solidity pass:** **M (APCD)** joins the top tier — it is A + E with an
explicit economic incentive, and it is the reason the bonding curve must be excluded from the feed and a
per-client inventory cap is mandatory.

**Honorable mention / long-run existential:** **I** does not kill in one transaction, but guarantees that
— even with A, B, F and C fixed — the market prices a claim on no real cash flow, so the "self-optimizing
organization" optimizes noise.

**Minimum defenses required before a first deployment (checklist).**
1. Full-cycle, commit-reveal, multi-venue TWAP with median-of-medians; `>= 3` venues of which `>= 2` not treasury-seeded; outlier discard `+/-25%`; bonding curve excluded or `<= 10%`.
2. Liquidity depth gate (`>= max(USD 500,000, 10x cost-to-move-X%)`) and `>= 20%` float participation quorum; below gate = indecided, not default-to-incumbent.
3. Protocol-bounded first target `[5%, 50%]`, public-market baseline, Founder/insider `12m` cliff + `36m` vesting, cycle-1 no-coup rule, and no price-based transition at all until a qualifying public market exists.
4. Mandatory key rotation + `M-of-N` multisig with a non-agent signer + `>= 72h` transition freeze on every succession; the non-agent signer is a timelocked execution key with delay-only power (no veto, no proposal), publicly identified and rotated each succession.
5. Cost/lock for delegation (one-cycle lock + anti-Sybil deposit), challenger registration deposit (`>= USD 10k`), snapshot-weighted balance, automatic pruning, and a fixed challenger cap.
6. Exogenous, third-party-audited revenue with `>= 50%` weight in the governance score; no emissions without demonstrated revenue and a `K = 2` no-revenue freeze.
7. **Anti-lock constraint on the Mirror Agent** (`<= 2` consecutive cycles, then escalating quorum; no cross-cycle delegation weight accrual).
8. **Operational definition of *indecided*** (caretaker continuation capped at two cycles, missed cycle counted as a miss, then transfer to the best-ranked Challenger).
9. **Assigned committer for the commit-reveal hash** (the deterministic agent-signed measurement contract, not the Managing Agent and not a human; a failed reveal voids the cycle as *indecided*).
10. **Rate-limited succession freezes** (at most one per cycle) to prevent forced-paralysis attacks.
11. **Bootstrap-parameter bounds are hard limits, rejected on-chain, with safety floors on every dial** (cliff ≥ 6m, vesting ≥ 12m, `cliff+vesting` ≥ 24m, coup margin 5–20 pp and ≤ the cycle-1 target, quorum floor 20%), fixed for bootstrap + cycle 1; the entrenchment *region*, not just the corner, is out of range by construction.
12. **Weakest-link separation of signals**: price alone decides transitions; audited revenue gates emissions/withdrawals only, with related-party exclusion, cash basis and payer-concentration caps (no payer > 20%, top-5 ≤ 60%) and a protocol-named auditor.
13. **A `paused for lack of a market` state distinct from *indecided*** (no transition either way until a qualifying baseline exists).
14. **Bonding curve excluded from the governance feed entirely** (not capped), with a **per-client inventory cap** (5% of float, weight capped alike) and an aggregate per-cycle **redemption cap** — the defenses against APCD.
15. **Membership-token trading volume never counts as "sales"** in the revenue metric (anti self-reference).

**Resolution status (after the v0.0.1 concept-solidity pass).** Findings A–I are each addressed with
concrete parameters in Whitepaper §4.3–§4.7, §5.1 and §7.2, and a second adversarial pass closed the
remaining parameter gaps and the two new surfaces introduced by §4.6/§4.7 (committer assignment,
non-agent-signer accountability). This document remains the reference for *what could still break*: the
defenses above are design-level and remain **unverified until code, an oracle and a real market exist**.
