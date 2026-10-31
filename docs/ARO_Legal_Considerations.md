# ARO Legal Considerations — v1

**Status:** pre-alpha 0.0.1 — conceptual. This is design hygiene, not legal advice. It records the
language and design choices that keep the ARO token framed as a **utility / access credential** rather
than an investment instrument, so that a later, jurisdiction-specific legal review starts from clean
ground. A fuller regulatory analysis is maintained separately and is out of scope for this public repo.

---

## 1. Why this matters

AROs are, by construction, organizations whose members hold a token and whose management is disciplined
by market performance. That structure is genuinely novel in its automation, but it is exactly the shape
regulators scrutinize when a token confers **control over an organization** together with an
**expectation of profit from a third party's efforts** (the agents'). In the United States this is the
*Howey* test; the EU, UK, UAE and Switzerland apply their own but related lenses. The design must not
create the impression of an investment contract.

Two rules follow:
- The token is a **credential and a coordination signal**, not a claim on revenue or profits.
- The whitepaper is a **design document**, not a prospectus.

---

## 2. Language rules for the whitepaper and any public material

| Avoid | Why | Use instead |
|-------|-----|-------------|
| "Fee capture — exposure to protocol revenue" | Reads as a promise of return = investment contract | "Tokens grant access to protocol services. They do not represent a claim on protocol revenue." |
| "Investment thesis" / "investors" | Casts the document as a prospectus | "Value proposition" / "participants" / "clients" |
| "Target appreciation threshold" as the *selection* criterion | Makes price the standard, implying profit expectation | Prefer non-price service metrics (service usage, retention, treasury sustainability); where price is used, describe it as a *performance reference*, not a return |
| "Rewards", "yield", "returns", "upside" | Financial-return framing | "Access", "participation", "coordination" |
| "The token will appreciate" | Forward-looking profit claim | No forward-looking price language at all |

**Rule of thumb:** if a sentence could appear in a securities offering document, it does not belong here.

---

## 3. Utility framing (what the token is)

The token is designed to function as:
1. **A client credential.** Holding at least one token grants access to the organization's services.
2. **A market signal.** Clients express confidence or no-confidence by holding, buying, selling or backing
   an operator — a *market mechanism*: there is no vote to cast, and no ballot exists.
3. **Not a security or a share.** No dividend, no revenue share, no redemption right, no claim on assets,
   no expectation of profit derived from the efforts of others.

Custody, key management and any fee mechanics must be specified so that holding a token never *entitles*
the holder to a cash flow from the protocol's operations.

---

## 4. Structural notes (for the build phase, not now)

- **Now:** no entity. Development happens under the author's own name/IP; the git history is preserved as
  evidence of authorship. The deleted Litepaper stays in history — rewriting history to remove it would
  look like spoliation.
- **If the project is later handed to a foundation:** a Swiss *Stiftung* fits a pure utility token; if
  economic elements remain in the token, a commercial vehicle (e.g. Swiss GmbH) that later transfers to a
  foundation is the safer path. This decision belongs to the build phase and requires counsel.
- **Authorship record:** see [`AUTHORS.md`](../AUTHORS.md).

---

## 4b. The "sales-linked value + code-is-law" thesis (open question)

Whitepaper §7.4 proposes that token value track **real sales** (collateralization / crowdfunding-like)
rather than a profit claim, and asks whether an agent that executes the protocol strictly can escape most
securities and crowdfunding rules. Treat that as an **untested thesis**, and note what it does *not* do:

- **Conduct is not the test.** Securities analysis asks what the buyer expects and whether they rely on
  others' efforts. A strict, unappealable rule that price must rise is itself a profit expectation; "the
  agent will not abuse" is not a defense.
- **"Code is Law" weakens while the code is upgradeable.** An updatable treasury contract is a
  discretionary decision point every time it is upgraded, which is precisely what the regimes police.
  *Nuance from the 2026-10-31 reframe:* the author's claim is about the **settlement** — an executed
  consequence cannot be reversed, censored, bribed, exempted or reinterpreted after the fact. That claim is
  real and is the reason an ARO has no arbiter. It is **not** a defense before a regulator: the code that
  defines the consequence can still be upgraded, and finality is not a legal test.
- **A named exemption must be earned, not invoked.** Crowdfunding regimes (e.g. Reg CF/ECSP in the US,
  ECSP in the EU) apply to token sales too; the path is disclosure + limits inside an exemption, not the
  argument that no exemption is needed.
- **Red lines for the framing to stay utility:** no claim on revenue or profit, no forward-looking price
  language, value accrues only from real-service demand, and any buyback/collar is funded **only** from
  audited exogenous revenue — never from new buyers' money (that would be Ponzi-shaped, not
  collateralization).

Make/lose decision on this thesis is a job for counsel in the build phase; this file only fixes the framing
rules so the public documents do not assume the answer.

**Facts only — no legal conclusion.** Because AROs removes every right of control (no vote, no proposal,
no arbiter), the public documents must state only what the token **is not** — no vote, no proposal right, no
control, no claim on revenue, no redemption right, no promised buyback — and the fact that selection is
automatic. They must **never** state a legal conclusion ("therefore it is not a security"): that conclusion
belongs to a court or a regulator. Removing holder control is **not** a defence — it makes the holder the most
passive participant, and it is irrelevant under regimes that do not require control at all (Singapore's
collective investment scheme has no voting requirement). The legal question — including the separate
market-operator question — is open and goes to **counsel in the jurisdiction of issuance**.

**Membership / inventory framing (Whitepaper §6.1).** Framing the token as a warehouse-club membership with
a resale market (buyers accumulate memberships and resell at a curve premium, bearing inventory risk) is
useful but not a complete defense: a purchase made *only* to resell higher is still a profit expectation,
and if the organization runs the resale venue it operates a market in its own membership tokens. Keep the
same red lines as above, and do not present the resale framing as settling the question.

- **Resale by agents makes it worse, not better.** Algorithmic accumulation and resale by agents is
  economically an automated trading system over an organization-issued instrument; "a club where AI agents
  arbitrage memberships" reads to a regulator as a financial market, not a buyers' club. Do not present
  agent resale as supportive of the utility framing.
- **If the bonding curve is retained as an ARO-operated mechanism**, counsel must evaluate whether it
  triggers market-operator, exchange, ATS or similar obligations in each relevant jurisdiction,
  **independently of** the securities analysis.
- **Issuer control of the price venue is the worst fact pattern:** an issuer that runs the market in its own
  token and also argues that token is not a security is arguing against its own structure. Prefer a
  third-party venue, or restrict the curve to primary issuance/redemption (mint on entry, redeem on exit) and
  keep the secondary market external.

---

## 5. Out of scope here

This file does not analyze any specific jurisdiction's token regime, does not opine on securities law,
and is not legal advice. It exists so the public documents avoid the most common self-inflicted framing
errors before any real review. Keep regulatory analysis in a private, adviser-reviewed document.
