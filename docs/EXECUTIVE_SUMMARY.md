Executive Summary: Forge Atlas Smart Contracts
Protocol Scope & Deployment Model

Forge Atlas provides core infrastructure for game economies.

All modules described in this document are independently deployable. Core infrastructure does not require the deployment of financial systems such as token liquidity, exchange infrastructure, or market-based modules.

Financial components, including token issuance, liquidity provisioning, and exchange functionality, are optional and may be deployed and operated by separate entities or third parties.

Forge Atlas does not require, operate, or assume the existence of these components.

Objective

Implement a modular smart contract platform where:

The protocol layer is minimal and revenue-generating
Game economies are composed via explicit module deployment
No contract assumes another exists unless explicitly wired
Governance and tokenization are optional and introduced only after revenue exists
Core Rules
Protocol contracts never depend on game modules
Game modules may depend on protocol contracts
All value routing is explicit and configurable
Each Layer Module is a separate Github repo, functional on its own
Enforcement Rule
Any constraint described in this document must be enforced at the smart contract level
If a behavior is not explicitly allowed, it must be impossible on-chain
Protocol contracts never mint NFTs directly. All NFT mint execution occurs through the Immutable Minting API
Layer 0 — Core Infrastructure

Maintained by Forge Atlas. Provides shared infrastructure only.

Core infrastructure is limited to configuration, routing, and system-level coordination. It does not require or assume the presence of exchange, liquidity, or token market functionality.

0.1) ENERGY Token

Protocol-level settlement unit used for accounting, optional liquidity pairing, and developer settlement.

ENERGY may be issued and managed independently of Forge Atlas core infrastructure.

Backed by approved stablecoins held by the protocol treasury
Treasury deploys collateral to yield venues (e.g. AAVE on Base)
Yield accrues to the protocol treasury only
ENERGY is minted via deposits of approved stablecoins under defined constraints
Redemption is subject to:
48 hour timelock
per wallet withdrawal limits
rolling protocol-wide caps
Redemptions may be paused or disabled
ENERGY is issued on Immutable and is not bridgeable

Constraints:

Fully collateralized treasury principal
Yield is additive only
No rebasing
No staking or yield distribution
ENERGY is not a gameplay asset
0.2) Protocol Admin & Routing

Central configuration authority for protocol-level parameters and value routing.

Define protocolWallet
Define protocol fee percentages
All changes subject to 48 hour timelock

Routing:

ENERGY yield → protocolWallet
Protocol-level fees → protocolWallet

No protocol-level routing sends value directly to end users.

Modules may not override protocol routing rules.

0.3) Native DEX (Uniswap v4)

Optional module. Independently deployable.

The DEX is not required for Forge Atlas core functionality and may be deployed and operated separately.

ENERGY-paired pools only
No pools deployed by default
Dynamic fee support via hooks

Default Fee:

0.30% total
80% → LPs
20% → protocol

Fee decay logic supported for fair launches.

Layer 1 — gameFactory

Permissionless. Each module is deployable separately and optional.

Used by teams to deploy and manage independent game economies.

1.1) Game Admin

Per-game configuration:

Set teamWallet

Revenue routing:

teamWallet (100% default)
Optional additional addresses

Constraints:

gameFactory is infrastructure only
No implied yield or financial return
No pooling of user funds
1.2) Free Mint Token (FMT)

Game-scoped, non-transferable activity token.

Minted via gameplay or in-game actions
Burned on spend
Generates LUCK

Constraints:

Not transferable
No external redemption
No financial or market function
1.3) Shop Module

Optional module for selling game items.

Items priced in FMT, ENERGY, or backend-minted
NFTs minted via Immutable

Constraints:

No custody of user funds
Revenue routed directly to team-defined addresses
1.4) Multiplayer Module (FMT Only)
FMT escrow only
Outcome verified via signed backend message
All FMT burned

Constraints:

No external assets
No custody beyond execution
1.5) NFT Module
Standard ERC721 / ERC1155 via Immutable
Passive by default

Constraints:

No implicit financial or governance rights
1.6) govToken Launch & Liquidity

Optional module. Independently deployable.

Token launch and liquidity provisioning are not required components of Forge Atlas infrastructure and may be deployed and managed externally.

Constraints:

No implied yield, dividends, or profit sharing
Liquidity is initialization only
No protocol guarantees
Layer 2 — Advanced Economic Modules

Standalone. Optional. Independently deployable.

Not required for game or token launch.

All modules:

Require explicit funding
Have no mint authority
Operate deterministically
