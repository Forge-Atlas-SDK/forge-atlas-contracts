# Forge Atlas Public Executive Summary

## Document Scope

This document describes the Forge Atlas smart contract modules only.

Forge Atlas contracts are designed to work alongside external payment, platform, and distribution systems, including Apple IAP, Google Play Billing, Steam, Stripe, and other approved payment flows.

Those integrations are handled by the Forge Atlas SDK, backend, and game-specific implementation logic. They are not part of this smart contract scope.

The contracts provide configurable on-chain modules for game environments, tokenized activity, item configuration, NFT mint authorization, multiplayer settlement, and team-controlled routing.

---

## Overview

Forge Atlas provides modular smart contract infrastructure for game economies.

The contracts give game teams reusable modules for:

- Creating game environments
- Configuring team-controlled routing
- Managing non-transferable game activity tokens
- Authorizing item and NFT delivery
- Supporting shops, battle passes, and mystery boxes
- Settling FMT-based multiplayer flows
- Managing game-scoped NFT collections

Forge Atlas is infrastructure only. It does not require teams to launch tokens, liquidity pools, marketplaces, stablecoins, or financial products.

Forge Atlas modules do not provide staking, dividends, yield, profit sharing, redemption rights, or investment rights.

Forge Atlas does not custody NFTs, payments, stablecoins, or user funds. Teams control their own wallets, assets, routing, and game economy configuration.

Each module is optional, independently deployable, and explicitly wired by the game team.

---

## Core Product

`gameFactory` is the core Forge Atlas smart contract product.

It allows teams to deploy and manage independent game environments using optional, configurable modules.

Forge Atlas helps teams configure and operate:

1. Environment Module
2. Game Admin
3. Free Mint Token
4. Shop Module
5. Multiplayer Module
6. NFT Module

The system is designed so teams can start with simple gameplay and monetization modules, then add more advanced economy modules later only if needed.

---

## gameFactory

`gameFactory` allows teams to deploy and manage independent game environments.

Each game environment can define its own admin controls, wallets, routing, items, tokens, collections, and backend authorization logic.

By default, no stablecoins are enabled. Teams may optionally configure approved on-chain stablecoins for their own game environment where supported.

External purchases may include IAP, Stripe, Steam, or other approved payment systems. The smart contracts do not process those payments directly.

When external purchases are used, the contracts only react to backend-signed authorization after the external purchase is confirmed.

---

## 1. Environment Module

The Environment Module is the central configuration layer for each game environment.

It defines admin settings, revenue routing, fee parameters, stablecoin permissions, and module configuration used by connected gameFactory modules.

### Capabilities

- Create and configure a game environment
- Define the environment owner or admin
- Define the primary `teamWallet`
- Configure revenue routing
- Configure fee parameters
- Configure approved stablecoins, if enabled by the team
- Expose read-only configuration to connected modules
- Apply timelocks to sensitive configuration changes
- Support optional additional destination wallets

### Stablecoin Configuration

Stablecoin support is optional and disabled by default.

Teams may choose whether to enable approved on-chain stablecoins for their own environment.

### Routing Rules

- `teamWallet` receives 100% by default
- Additional destination wallets are optional
- Routing can be percentage-based
- Routing destinations must sum to 100%
- All routing is explicit and configurable
- Connected modules read routing rules from the Environment Module
- Modules may not override environment routing rules

### Admin Controls

Sensitive changes may be subject to a timelock, including:

- Updating `teamWallet`
- Updating fee parameters
- Updating routing destinations
- Updating routing percentages
- Enabling or disabling connected modules
- Enabling or disabling approved stablecoins

### Design Rules

- Each environment is controlled by the deploying team
- Forge Atlas provides tools only
- Forge Atlas does not enforce a specific business model
- Forge Atlas does not custody NFTs, payments, stablecoins, or user funds
- Forge Atlas does not pool funds across games
- All routing is transparent, explicit, and module-readable

---

## 2. Game Admin

Game Admin controls game-specific settings inside an environment.

It is used for high-level game configuration and module management.

### Capabilities

- Configure game metadata
- Enable or disable supported modules
- Manage game-level permissions
- Configure supported token or payment types
- Set game-specific rules for connected modules
- Manage operational controls for live game economy settings

### Design Rules

- Game Admin controls game configuration, not protocol-wide settings
- Game-specific settings are scoped to that environment only
- Modules only use the Game Admin settings they are explicitly connected to
- No module is assumed to exist unless deployed and wired

---

## 3. Free Mint Token

Free Mint Token, or FMT, is a game-scoped, non-transferable activity token.

It is designed for gameplay actions, progression systems, rewards, and in-game economy loops.

### Capabilities

- Mint FMT through gameplay or in-game actions
- Burn FMT when spent
- Generate LUCK from FMT burns
- Configure mint rules per game
- Use Forge Atlas backend attestations for mint authorization
- Allow teams to self-host an equivalent signer if needed

### Team Minting

Teams may optionally mint FMT for operational needs.

Team minting can be:

- Timelocked
- Disabled per game
- Limited by game configuration

### Design Rules

- FMT is not transferable
- FMT has no external redemption
- FMT is not a financial asset
- FMT does not require shops, NFTs, governance, liquidity, or stablecoins

---

## 4. Shop Module

The Shop Module lets teams sell or distribute game items using configurable pricing and delivery logic.

### Supported Item Types

- Consumables
- Collectibles
- Equipment
- Mystery boxes
- Battle passes

### Pricing Options

Items may be priced in:

- FMT
- Team-enabled stablecoins
- Backend-signed authorization after an external purchase is confirmed

Stablecoin pricing is optional and disabled by default unless enabled by the team for that environment.

External purchases may include IAP, Stripe, Steam, or other approved payment systems. The smart contracts do not process those payments directly.

### Delivery Options

The Shop Module can support:

- Direct item delivery
- Optional recipient addresses
- Gifting
- No-login purchase flows
- NFT mint authorization events
- Immutable NFT delivery through the team’s configured minting flow

### NFT Delivery

When a shop item produces an NFT:

- Forge Atlas contracts emit mint authorization events
- Mint execution occurs through the team’s configured Immutable minting flow
- NFTs use Immutable-compatible ERC721 or ERC1155 collections

### Restricted Mint Phases

Teams may optionally use restricted mint phases where NFTs are delivered through a team-controlled restricted-delivery contract before user withdrawal is enabled.

This allows teams to prevent trading until a `tradingEnabled` flag is activated.

After trading is enabled, users may withdraw NFTs to their wallet.

### Scarcity Controls

The Shop Module supports:

- Global supply caps
- Wallet allowlists
- Per-address mint limits
- Phased cap expansion
- Optional non-transferable item behavior

### LUCK Generation

LUCK generation is configurable per game.

Supported sources may include:

- FMT spend
- Gameplay actions
- Other explicitly configured in-game actions

LUCK is an activity and leaderboard signal. It does not represent equity, yield, redemption rights, or profit participation.

### Design Rules

- Shop revenue routes according to Environment Module settings
- Forge Atlas does not custody payments, NFTs, stablecoins, or user funds
- Item behavior is explicit and configurable

---

## 5. Multiplayer Module

The Multiplayer Module supports FMT-based match settlement.

It is designed for games that want competitive or multiplayer flows without using external assets.

### Flow

1. Players lock FMT into a match contract
2. The match is played off-chain
3. The configured backend submits a signed match outcome
4. The contract verifies the signature
5. Locked FMT is settled according to the result

### Supported Features

- Up to 16 players
- Match IDs or nonces for replay protection
- Timeout-based refunds
- Deterministic settlement
- Optional team-funded FMT bonus pools
- AI participants, if explicitly identified and capped

### Design Rules

- FMT only
- No stablecoins or external assets
- FMT spent or settled through the module is burned
- FMT burns generate LUCK

---

## 6. NFT Module

The NFT Module lets teams launch and manage game-scoped NFT collections.

Collections can exist independently or be connected to other Forge Atlas modules.

### Capabilities

- Launch new NFT collections
- Migrate existing collections from other chains
- Configure supply
- Configure metadata
- Configure media references
- Set mint pricing in FMT or team-enabled stablecoins
- Support standalone or PFP-style assets

Stablecoin mint pricing is optional and disabled by default unless enabled by the team for that environment.

### Minting

NFT collections use Immutable-compatible ERC721 or ERC1155 standards.

Mint execution occurs through the team’s configured Immutable minting flow to preserve marketplace compatibility.

### Design Rules

- NFTs are passive by default
- NFTs have no implicit gameplay effects
- NFTs have no implicit financial rights
- NFTs have no implicit governance rights
- NFT staking is not supported
- NFTs only affect game logic if explicitly wired by another module
- Forge Atlas does not custody NFTs

---

## Contract Boundaries

Forge Atlas smart contracts are intended to provide configurable game systems for tokens, items, mint authorization, routing, and multiplayer settlement.

They do not:

- Process fiat payments directly
- Custody team funds
- Custody user funds
- Custody team NFTs
- Custody user NFTs
- Provide staking
- Provide yield
- Provide dividends
- Provide profit sharing
- Provide redemption rights
- Provide investment rights
- Require stablecoins
- Require liquidity pools
- Require governance tokens
- Require marketplaces

Payment processing, platform billing, fiat settlement, user account systems, analytics, dashboards, and SDK-level integrations are handled outside this smart contract scope.

---

## Product Philosophy

Forge Atlas is built around explicit, modular game economy infrastructure.

The system is designed to be:

- Simple for teams to deploy
- Flexible across different game types
- Compatible with Immutable minting and marketplace infrastructure
- Non-custodial by default
- Conservative by default
- Clear about what each module does and does not do
- Usable without token launches, liquidity pools, stablecoins, or financial mechanics

Forge Atlas lets teams start with practical game economy tools first, then expand into more advanced systems only when the game needs them.
