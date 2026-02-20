# Agent Run Organizations (ARO)

## A Market-Native Governance Framework

---

## Abstract

Agent Run Organizations (AROs) are a new governance primitive for decentralized systems. Instead of committees or token voting, AROs are operated by AI agents whose authority is determined by market performance.

A Managing Agent executes strategy. Challenger Agents propose alternatives. Token price determines who runs the organization.

Tokenholders are clients. Tokens are loyalty and coordination instruments, not governance ballots. There are no proposals to vote on. Clients express preferences through capital allocation: holding, buying, selling, or delegating tokens to competing agents.

Unlike DAOs with democratic voting, AROs are run by agents who can be hired or fired based on performance. Unlike futarchy — where prediction markets may or may not control execution — ARO tokens directly determine operational authority through real price outcomes.

Governance becomes economic behavior. Clients vote with their wallets.

---

## 1. Core Principles

1. **Tokenholders are clients of the organization.**
2. **One token is required to become a client.**
3. **Tokens are utility-based loyalty instruments**, not governance ballots.
4. **Governance occurs through capital allocation** — holding, buying, selling, or delegating tokens.
5. **A Managing Agent executes strategy and is continuously challenged by alternatives.**
6. **Challenger Agents propose competing strategies and build track records.**
7. **Performance is measured by token price movement over defined cycles.**
8. **The token trades on a bonding curve maintained by the treasury, alongside external markets.**

---

## 2. Organizational Structure

An ARO consists of:

| Component | Role |
|-----------|------|
| **Founder** | Human who bootstraps the project with an initial idea, strategy, and first-cycle target. Hands off to agents after cycle one. |
| **Managing Agent** | AI agent responsible for strategy execution, reporting, and sub-agent coordination. |
| **Challenger Agents** | AI agents proposing alternative strategies and competing for future management. |
| **Mirror Agent** | Protocol-generated agent that preserves the previous successful configuration for stability and institutional memory. |
| **Sub-Agents** | Specialized agents for treasury, risk, marketing, R&D, and other functions. Deployed by the Managing Agent. |
| **Token** | Client credential, governance signal, and fee-capture instrument. |
| **Bonding Curve** | Transparent treasury-backed price floor/ceiling. External markets provide additional liquidity. |
| **Clients** | Tokenholders who access services, delegate tokens, and determine agent authority through capital allocation. |

### 2.1 Managing Agent

The Managing Agent operates the organization. It sets strategy, publishes reports, deploys sub-agents, and can propose changes to the bonding curve parameters.

### 2.2 Challenger Agents

Challenger Agents propose alternative strategies each cycle. They build track records that are compared against the Managing Agent's performance. If the Managing Agent fails to meet its target, the Challenger Agent with the strongest track record and token backing becomes the new Managing Agent.

Challenger Agents can be created by:
- The Managing Agent itself (to test competing strategies)
- Sub-agents such as Marketing, R&D, or Public Relations
- These sub-agents may use surveys, market research, or analysis of successful leaders in similar organizations to design distinct agent personalities and strategies

### 2.3 Mirror Agent

The protocol automatically generates a Mirror Agent at each cycle. This agent preserves the previous cycle's successful configuration. It serves three purposes:

1. **Stability fallback** — Clients can delegate to the Mirror Agent to signal preference for continuity
2. **Institutional memory** — Maintains the full history and evolution of the project
3. **Track record preservation** — Provides a baseline for evaluating new strategies

If the Managing Agent proposes unreasonably short cycles or risky changes, clients can delegate tokens to the Mirror Agent. Since both share the same track record, token allocation becomes the tiebreaker.

### 2.4 Sub-Agents

The Managing Agent deploys specialized sub-agents for:

- Treasury management
- Risk management
- Product design and marketing
- Research and development
- Hiring human contractors
- Public relations
- Any function the Managing Agent deems necessary

Sub-agents operate under the Managing Agent's authority and transfer to a new Managing Agent if leadership changes.

### 2.5 Treasurer Agent

A specialized sub-agent responsible for wallet operations and treasury management. Key management and memory storage solutions are still under development.

---

## 3. The Token and Price Discovery

### 3.1 Dual Market Structure

ARO tokens exist in two interconnected markets:

1. **Bonding Curve (Treasury)** — The organization's treasury acts as a transparent market maker, buying and selling tokens at a published curve. This provides always-available liquidity and a known price floor.

2. **External Markets (DEXs)** — Tokens can be listed on permissionless exchanges. Market forces and arbitrage converge external prices with the bonding curve.

This structure gives clients multiple exit paths and lets the broader market participate in price discovery.

### 3.2 Bonding Curve Modifications

The Managing Agent can propose changes to the bonding curve. These changes do not take effect immediately. Clients who disagree can sell their tokens on the current curve before the change activates — effectively a veto through exit.

---

## 4. Governance Cycle

### 4.1 Cycle Structure

The system begins with a **monthly governance cycle**. The Managing Agent may propose shorter cycles, but never longer than the original duration. This ensures increasing responsiveness without governance slowdown.

### 4.2 First Cycle (Bootstrap)

The Founder publishes:
- Initial strategy
- Target token appreciation threshold
- Initial bonding curve parameters

After the first cycle, the Founder steps back. The system becomes fully agent-operated.

### 4.3 Ongoing Cycles

**At cycle start**, the Managing Agent publishes:
- Performance report from previous cycle
- Strategy for the upcoming cycle
- Target appreciation threshold (X%)
- Any proposed bonding curve modifications

**During the cycle**, clients:
- Hold tokens (confidence)
- Buy tokens (strong confidence)
- Sell tokens (no confidence)
- Delegate tokens to Challenger Agents (preference for alternative)

**At cycle end**, the protocol evaluates:
- If token appreciation ≥ target → Managing Agent continues
- If token appreciation < target → Challenger Agent with best track record and most delegated tokens becomes new Managing Agent

### 4.4 Token Delegation

Clients can delegate tokens to Challenger Agents as a signal of confidence. Delegation is **non-custodial** — tokens remain in the client's wallet but are counted toward the Challenger Agent's backing. This requires a smart contract to track delegation state.

---

## 5. Challenger Agent Framework

### 5.1 Creation and Pruning

New Challenger Agents can be spun up by the Managing Agent or its sub-agents. The system requires a pruning mechanism to remove agents that fail to gain support over multiple cycles. This keeps the competitive field manageable for clients.

Specific pruning thresholds (e.g., zero delegation for X consecutive cycles) should be defined at deployment based on the organization's needs.

### 5.2 Competition Dynamics

Challenger Agents propose strategies simultaneously with the Managing Agent. Their proposals are published on-chain, creating a visible track record. Over time, clients can evaluate:

- Which agents consistently propose sound strategies
- Which agents would have outperformed the Managing Agent
- Which agents align with client preferences

This creates a talent pipeline. Strong Challenger Agents become credible successors.

---

## 6. Client Access Model

Wallets holding **at least one token** are recognized as clients. This grants:

- Access to products and services
- Participation in governance cycles
- Ability to delegate tokens to Challenger Agents
- Interaction with protocol infrastructure

The organization may operate real economic services:

- Decentralized exchanges
- DeFi primitives
- Financial infrastructure
- Any value-generating system the Managing Agent deploys

---

## 7. Tokenomics

### 7.1 Token Functions

| Function | Description |
|----------|-------------|
| **Client credential** | Confirms membership and access rights |
| **Governance signal** | Delegation to agents expresses preference |
| **Fee capture** | Exposure to protocol revenue |

### 7.2 Supply

- Initial supply defined at genesis
- Emissions tied to measurable value creation
- Emission adjustments proposed by Managing or Challenger Agents

### 7.3 Revenue Allocation

Protocol revenue may be allocated to:

- Buyback and burn
- Treasury reserves
- Strategic reinvestment
- Performance-aligned emissions

---

## 8. Comparison to Existing Models

| Feature | DAO (Token Voting) | Futarchy | ARO |
|---------|-------------------|----------|-----|
| Governance mechanism | Proposal voting | Prediction markets | Capital allocation |
| Execution | Human-mediated | Often human-mediated | Agent-executed |
| Token role | Governance ballot | Prediction stake | Client credential + signal |
| Market link | None or indirect | Indirect | Direct |
| Accountability | Vote outcomes | Prediction accuracy | Real price performance |
| Human involvement | High | Medium | Low (bootstrap only) |

**DAOs** separate voting from execution. Voters face no consequence for bad decisions.

**Futarchy** uses prediction markets to forecast outcomes, but the link between prediction and execution is often indirect.

**AROs** make price the direct accountability mechanism. The agent that delivers value retains authority. The agent that fails is replaced. No voting. No governance theater. Markets select operators.

---

## 9. Conclusion

Agent Run Organizations align execution, incentives, and governance into a single feedback loop:

1. Agents propose strategies
2. Clients delegate capital
3. Markets determine success
4. Winners retain authority
5. Losers are replaced

This creates a system where:

- Execution is efficient
- Incentives are aligned
- Governance is accountable
- Human involvement is minimized

The result is a self-optimizing organization that can scale indefinitely.