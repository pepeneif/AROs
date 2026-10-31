# Changelog

All notable changes to the AROs concept are documented here. This project follows a
`major.minor.patch` scheme; pre-alpha, so the minor field moves on each completed review cycle.

## [0.0.2] — 2026-10-31 — First adversarial review cycle

The first full pass of adversarial review is complete: the design was attacked by an independent
threat-model review and a legal-framing review, the prior art was mapped to state the novelty honestly,
the whitepaper was hardened with concrete parameters, and the political layer was removed in favour of
selection by market consequence. Still conceptual — no contracts, no oracle, no curve formula and no
agent runtime exist yet.

### Added
- `docs/ARO_Threat_Model.md` — adversarial design review: 9 findings with mechanism, plausibility,
  severity and parameterized defenses; top-3 fatal risks; minimum-defense checklist before deployment.
- `docs/ARO_Related_Work.md` — component-by-component prior-art map, novelty verdict and neighbor projects.
- `docs/ARO_Legal_Considerations.md` — language and framing rules to keep the token a utility credential.
- `AUTHORS.md` — authorship record.

### Changed
- Whitepaper: added robustness rules for the governance signal (§4.6) and succession/key-safety rules
  (§4.7); bounded cycle length (14–35 days) and the first-cycle target ([5%, 50%]); anchored the
  baseline to a public-market TWAP with Founder lockup; made delegation non-free / Sybil-resistant;
  set challenger pruning and registration-deposit rules; reframed the token (removed "fee capture /
  exposure to protocol revenue"); tied emissions to audited exogenous revenue.
- Whitepaper: removed the futarchy/securities jab from the abstract and reframed accountability as
  "realized performance".
- Implementation: performance metric now manipulation-resistant and revenue-weighted; wallet management
  now points to the succession-safety rules.
- README: documented the new docs and added an honest-framing note.
- Repository hygiene: untracked `.DS_Store`, added `.gitignore`, fixed `CODE_OF_CONDUCT` link.

### Fixed (second adversarial pass — Auditor + Abogado)
- Legal language: removed the residual "fee-capture instrument" from the §2 component table; renamed the
  governance criterion from "appreciation" to "performance" throughout (§4.2–§4.4, §4.6); "Strategic
  investment" → "Strategic allocation"; "Clients delegate capital" → "delegate tokens"; dropped
  "scale indefinitely".
- Closed parameter gaps the first pass left in prose: fixed venue depth gate (≥ USD 500k or 10×
  cost-to-move-X%), ≥20% float participation, ≥3 venues of which ≥2 not treasury-seeded, ±25% outlier
  discard, delegation one-cycle lock + anti-Sybil deposit, challenger registration deposit ≥ USD 10k and
  a fixed cap of 7, snapshot-weighted delegation, K=2 no-revenue freeze, third-party revenue audit.
- Added the cycle-1 no-coup rule and the "no qualifying public market → indecided, no transition" case.
- Gave an operational definition of *indecided* (caretaker continuation, capped at two cycles).
- Closed the two surfaces the first pass introduced: assigned committer for the commit-reveal hash, and
  made the non-agent signer a delay-only timelocked key with public identity and per-succession rotation;
  rate-limited succession freezes to at most one per cycle; added the Mirror Agent anti-lock constraint.
- Threat model: expanded the deployment checklist to 10 mandatory defenses and added a resolution status.

### Added (third pass — Founder-configured bootstrap & sales-linked value)
- Whitepaper §4.2: the four bootstrap values the Founder configures (cliff, vesting, cycle-1 coup margin,
  participation quorum) are now explicit, each with a protocol-guarded range and a default, published
  on-chain before the sale and fixed for the bootstrap phase.
- Whitepaper §7.4: new open thesis — token value as a **function of real sales** (collateralization /
  crowdfunding-like) instead of a profit claim, including the "code-is-law, therefore securities law should
  not apply" argument stated *to be tested*, its four stated weaknesses, and the bar it must clear.
- `ARO_Legal_Considerations.md` §4b: framing rules for that thesis (conduct is not the test; code-is-law
  weakens under upgradability; a named exemption must be earned; red lines for utility framing).
- `ARO_Threat_Model.md`: new findings **J** (Founder-configurable bootstrap parameters, High) and
  **K** (sales-linked value + code-is-law as a securities argument, High/legal) and a checklist item 11
  forbidding the entrenchment corner (cliff+vesting = 0, coup margin and quorum at maximum).
- Whitepaper §6: the token is stated as a **warehouse-club membership** (no token ⇒ not a client) and a new
  §6.1 states the accumulation/resale thesis (a speculator buys and resells memberships at a curve premium,
  bearing inventory risk and reporting capital gain/loss) as an *open thesis*, with its limits.
- `ARO_Threat_Model.md`: new finding **L** (the org runs a market in its own membership tokens, Medium-High).

### Fixed (fourth pass — reconciliation after adversarial review)
- **§4.2 rebuilt with safety floors, not forbidden corners**: cliff `6–24m`, vesting `12–60m`, combined
  `cliff+vesting >= 24m`, coup margin `5–20 pp` and `<=` the cycle-1 target, quorum floor `20%`
  (configurable only `20–30%`) — all hard limits rejected on-chain, fixed for bootstrap + cycle 1.
- **Two specification contradictions reconciliated**: §4.2's configurable quorum vs §4.6's "fixed ≥20%"
  (now one hard floor of 20%), and §4.2's "no transition at all" vs §4.4's indecided caretaker cap (now a
  distinct **paused for lack of a market** state that neither forces nor blocks a transition).
- **Weakest-link separation of signals**: price alone decides transitions; audited revenue only gates
  emissions/withdrawals, with related-party exclusion, cash basis, payer-concentration caps and a
  protocol-named auditor (the weighted-average shortcut is removed).
- **§6.1 reframed** as a *risk of transferability*, not a promoted use case, with an explicit statement that
  the Organization does not operate a market in its tokens.
- **§7.4 split public/private**: the legal "code is law" argument moves to a private counsel document; the
  public text now claims a **design fit** with ADGM / Swiss FinTech (Japan FIEA as the security-adjacent
  route), never a settled status.
- `ARO_Legal_Considerations.md §4b`: agent resale makes the framing worse, issuer-run venue is the worst fact
  pattern, and market-operator/ATS exposure must be assessed independently of securities.
- `ARO_Threat_Model.md`: finding J rewritten (region, not corner); checklist grown to 13 items.

### Fixed (fifth pass — APCD and self-reference, after Auditor could not read the branch)
- **Bonding curve excluded from the governance feed entirely** (not capped at 10%): an issuer that runs a
  venue cannot use it to measure itself.
- **New finding M — APCD (accumulate → pump → capture → dump)**, Critical, with defenses: per-client
  inventory cap (5% of float, governance weight capped alike), aggregate per-cycle **redemption cap** (bank-run
  guard) and correlated-accumulation detection.
- **Anti self-reference rule**: membership-token trading volume on any venue (primary or secondary, curve or
  external) **never counts as "sales"** in the revenue metric — only fees in external assets for services
  delivered do.
- **§6.1 risk disclosure** added (price can go to zero; no support for the secondary market; no appreciation
  representation).
- `ARO_Implementation.md`: anti-APCD controls and the revenue definition fixed.

### Fixed (sixth pass — closing Auditor's verification gaps)
- **Inventory cap made a protocol constant, not governance-overridable**: `min(5% of float, cost_to_move_X% / token price)`, with a two-cycle timelock on any revision (the captured governance can no longer lift the cap that stops APCD).
- **Redemption cap given a number**: ≤10% of the treasury's liquid reserves per cycle, pro-rata.
- **Anti self-reference tightened**: fees derived from any activity where the membership token is the traded/lent/staked/collateralized asset are excluded too (closes the own-DEX fee back door).
- **Bootstrap timeout added**: no perpetual pause — 24 months without a qualifying baseline triggers an extraordinary election or a wind-down with pro-rata return.
- **Nominal thresholds indexed to a USD oracle** (depth gate re-based each cycle) so they do not drift with inflation.
- `ARO_Threat_Model.md`: M's defenses updated; the curve/external divergence recorded as a monitored alert, not a broken defense.

### Changed (seventh pass — the anarchist reframe: "no governance", author decision 2026-10-31)
- **The political layer is removed.** The author's permanent design principle — *rules without rulers*; every
  rule must be a consequence that executes itself, never a decision someone has to enforce — replaces the
  vocabulary and the structure that the earlier passes had built. §4 is now the **Evaluation Cycle**; the
  abstract, principles, comparison table and conclusion no longer speak of governance, ballots or votes.
- **No governance at all.** There is no vote, no proposal, no quorum that decides and no arbiter. Clients
  stay and hold, leave and sell, or back another operator; the successor is the Challenger with the best
  track record. Delegation survives only as a **public ranking/backing signal**, never as a deciding vote.
- **Operating terms belong to the Managing Agent** — the declared target X%, the cycle length, the member
  redemption cap, the curve parameters and κ — with a single, mandatory constraint: the **one-cycle delay**
  (a change decided in cycle *N* cannot take effect before *N+2*, so clients always get one full cycle to
  leave on the original terms). New §4.8.
- **New §4.9 Launchpad Disclosure and Warning Layer.** Full disclosure of every cycle term before the sale,
  plus an automatic plain-language warning when a term falls outside its coherence band (target vs.
  deliverability, redemption cap vs. liquidity, cycle length, curve/κ, and incongruence between terms). It
  warns; it does not block. This is the author's chosen substitute for hard protocol floors.
- **Nominal amounts removed.** The `max(USD 500,000, …)` depth-gate floor and the `USD 10,000` challenger
  deposit are gone. The gate is now **scale-free**: `cost_to_move_X% ≥ κ × extractable value` — no USD
  figure, no price oracle, and it tightens automatically as the treasury empties. κ is an operating term.
- **The redemption cap is no longer a protocol constant**: it is an operating term of the Managing Agent. The
  **5% treasury-withdrawal cap stays a protocol constant** (it protects members *from* the operator) and the
  **`cliff + vesting ≥ 24m` insider lockup floor stays a constant**.
- **"Perpetual Founder" removed as a category**: the obligation is the Managing Agent's. The stuck-bootstrap
  exit is no longer an election but the ordinary consequence — **liquidation and hand-off to the best-ranked
  Challenger**, or a wind-down pro-rata. New §4.2 text.
- **Finding J rewritten as *operator-configurable cycle terms*** and **new finding J+ (auto-targeting)**: the
  operator sets both its own bar and the exit friction. The rejected protocol floors are recorded as a
  deliberate, disclosed residual risk, not an oversight. Finding K sharpened (code *finality* is real but is
  not a legal defense); Finding L worsened (the fee back door stays open and no utility regime closes it).
- **Design-philosophy section added** to the whitepaper §1; README reframed accordingly.
- `PROPUESTA-reframe-anarquista.md` retains the round-4 proposal and the two adversarial reports' conclusions.

## [0.0.1] — 2026-10-30

Initial public pre-alpha: repository hygiene (`.gitignore`, untracked `.DS_Store`), README status and
version badges, `AUTHORS.md` authorship record, license and code of conduct. The concept and the
whitepaper first became public here.

### Notes
- Still conceptual: no contracts, no oracle, no bonding-curve formula, no delegation contract, no agent
  runtime. The threat model attacks the specification; several findings are latent and unverifiable
  until code exists.
