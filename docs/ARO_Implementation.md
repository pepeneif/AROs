# ARO Implementation Specifications

## Overview

This document outlines the technical specifications for implementing Agent Run Organizations (AROs) based on the whitepaper framework. The implementation focuses on multi-blockchain compatibility, hands off agent-driven operations, and token-based performance evaluation by the users "clients" of projects using this mechanism.

## Core Architecture

### Multi-Blockchain Framework

The protocol should be blockchain agnostic, allowing agents to operate across multiple blockchains.

Basically the only agent that requires skills to interact with blockchains is the treasurer agent.


#### Treasury Management

The treasurer agent wallet will be capable of holding multiple tokens and native assets across various blockchains.

The project treasury can be held in the maximum number of possible tokens in the maximum number of blockchains possible. 

Treasury management will be handled by a specialized treasurer agent, which possesses the exclusive skills required to oversee assets across all supported blockchains (within reasonable limits for assets and blockchains).

Upon the dismissal of any manager agent, the treasurer agent will seamlessly transition to serve the newly selected manager agent, chosen from challenger agents based on their track record and client confidence.

The weighting of track record versus client confidence will be initially established by the project's founder at launch and can be adjusted through proposals from manager agents during their tenure.

- **Minimum Supported Blockchains at launch**: Ethereum, Polygon, Solana, Bitcoin, Lightning Network, TARO Assets

- **Extensibility**: Modular skill system allowing addition of new blockchains via plugin architecture and third party skills marketplace

- **Wallet Management**: The treasurer agent operates the treasury wallet or wallets holding the project's assets across all supported blockchains, under the succession safety rules: key rotation on every leadership change, M-of-N multisig with at least one non-agent signer, a 72h transition freeze, and a 5% per-cycle withdrawal cap (see Whitepaper §4.7). The non-agent signer is a **timelocked execution key with delay-only power** — no veto, no proposal rights — publicly identified and rotated on every succession.

- **Cross-Chain Operations**: Support for atomic swaps, bridging

### Token System

- **Project Token**: ERC-20/SPL/BEP-20 compatible token representing client confidence and loyalty
- **Not Native Tokens**: Agents do not hold blockchain native tokens (ETH, SOL, BTC) directly
- **Utility Functions**:
  - Client access credential
  - Governance signaling mechanism
  - Performance-based rewards distribution
  - Service access (no claim on protocol revenue)

### Governance Mechanism

- **Performance Metric**: Manipulation-resistant performance score over governance cycles (whole-cycle, multi-venue, commit-reveal price measurement with an assigned deterministic committer; median-of-medians with ±25% outlier discard). The **price signal alone decides transitions**; audited exogenous revenue is a **separate gate** (emissions/withdrawals, `K=2` freeze), not a weighted share of the transition (weakest-link, see Whitepaper §4.6). Revenue counting excludes related-party payments, uses cash basis for delivered services, and applies a payer-concentration cap (no payer > 20%, top-5 ≤ 60%).
  - Robustness gates: venue depth ≥ max(USD 500,000, 10× cost-to-move-X%), ≥20% float participation, ≥3 venues of which ≥2 not treasury-seeded; below the gates the cycle resolves as *indecided* (caretaker continuation capped at two cycles).
  - No-revenue freeze: if audited exogenous revenue fails to grow for two consecutive cycles (K=2), emissions and treasury withdrawals freeze.
- **Cycle Duration**: Configurable (default: monthly), proposed by managing agent
- **Anti-APCD controls**: bonding curve excluded from the governance feed; per-client inventory cap (5% of float, governance weight capped alike); aggregate per-cycle redemption cap; correlated-accumulation detection across wallets
- **Revenue definition (anti self-reference)**: only fees collected in external assets for services delivered; membership-token trading volume on any venue never counts toward revenue
- **Evaluation Criteria**: Freely proposed by agents, influenced by client buying/selling behavior
- **Transition Logic**: Challenger agents can replace managing agent if token performance falls below threshold

## Agent Architecture

### Agent Roles

- **Manager Agent**: Primary decision-maker, responsible for proposing and executing operations
- **Challenger Agents**: Competing agents that can challenge the manager's performance
- **Treasurer Agent**: Specialized agent responsible for managing the project's treasury across all supported blockchains
- **Voting Delegation Agent**: Tracks the token delegation of clients to support challenger agents 


### Agent Types

1. **Managing Agent**
   - Executes organizational strategy
   - Deploys sub-agents for specialized functions
   - Publishes cycle reports and performance targets
   - Proposes governance cycle parameters

2. **Challenger Agents**
   - Propose alternative strategies
   - Compete for management control
   - Can assume management if selected by market

3. **Sub-Agents**
   - Specialized execution units (risk management, treasury, product execution)
   - Operate under managing agent authority
   - Transparent reporting requirements

### Agent Skills System

Agents possess modular skills for blockchain interactions:

- **Wallet Creation**: Generate addresses/keys for supported blockchains
- **Transaction Execution**: Send/receive assets across networks
- **DEX Integration**: Interact with decentralized exchanges
- **Bridge Operations**: Cross-chain asset transfers
- **Lightning Operations**: Channel management and payments
- **TARO Asset Handling**: RGB protocol integration for asset issuance

### Skill Extensibility

New blockchain skills can be added through:

- Plugin architecture
- Standardized interface contracts
- Runtime skill loading
- Community-contributed skill modules

## Smart Contract Layer

### Cross-Chain Governance Contract

- **Platform Agnostic**: Core logic deployable on multiple chains
- **Token Integration**: Accepts project token from any supported network
- **Agent Registry**: Tracks agent deployments and permissions
- **Cycle Management**: Handles governance cycle timing and transitions

### Performance Oracle

- **Price Feeds**: Multi-source price aggregation for token valuation
- **Cross-Chain Data**: Oracle network for blockchain-agnostic data
- **Performance Calculation**: Automated evaluation against proposed criteria

## Client Interface

### Dashboard Features

- **Multi-Chain Portfolio**: Unified view of holdings across blockchains
- **Agent Performance**: Real-time monitoring of agent strategies
- **Token Allocation**: Interface for reallocating tokens to challenger agents
- **Governance Participation**: Voting through capital allocation

### API Integration

- **Agent Communication**: REST/WebSocket APIs for agent interactions
- **Blockchain Abstraction**: Unified interface for multi-chain operations
- **Real-time Updates**: Live price feeds and performance metrics

## Security Considerations

### Agent Isolation

- Sandboxed execution environments
- Permission-based skill access
- Audit trails for all agent actions

### Fund Protection

- Multi-signature requirements for large transactions
- Timelock mechanisms for critical operations
- Emergency pause functionality

### Client Safeguards

- Transparent reporting requirements
- Independent performance verification
- Exit mechanisms for dissatisfied clients

## Development Roadmap

### Phase 1: Core Framework
- Agent skill system implementation
- Basic wallet management across 3 blockchains
- Project token deployment

### Phase 2: Governance Layer
- Cross-chain governance contracts
- Performance evaluation system
- Agent transition logic

### Phase 3: Advanced Features
- Additional blockchain integrations
- Sub-agent framework
- Advanced client dashboard

### Phase 4: Production Deployment
- Security audits
- Mainnet deployment
- Community governance activation