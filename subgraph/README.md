# Subgraph

## Overview

The **InkDAO Subgraph** is a GraphQL API that indexes and provides efficient access to on-chain data from the InkDAO protocol. Built on [The Graph](https://thegraph.com/), it tracks all content creation, purchases, and user activity across the platform in real-time.

The subgraph monitors events from the DXmaster contract and all dynamically created DXasset contracts, maintaining a comprehensive view of the entire InkDAO ecosystem.

## Why Use the Subgraph?

While you can query blockchain data directly, the subgraph provides several advantages:

* **Fast Queries:** Pre-indexed data means instant access to complex queries
* **Historical Data:** Access complete historical records of all platform activity
* **Aggregated Stats:** Get platform-wide statistics and user metrics without manual calculations
* **GraphQL Interface:** Flexible, efficient queries with exactly the data you need
* **Real-time Updates:** Data updates as new blocks are mined

## Event Handlers

The subgraph listens to the following smart contract events:

### DXmaster Events

**AssetAdded**
- Triggered when a new asset is published
- Creates Asset and Creator entities
- Updates GlobalStats

**AssetBought**
- Triggered when a user purchases an asset
- Creates Purchase and Holder entities
- Updates creator earnings and platform statistics

### DXasset Events (Dynamic)

**Transfer**
- Triggered when asset tokens are transferred between users
- Updates Purchase balances for both parties
- Tracks secondary market activity

## Architecture

```
┌─────────────────┐
│  Smart Contracts│
│                 │
│   - DXmaster    │
│   - DXasset     │
└────────┬────────┘
         │ Events
         ▼
┌─────────────────┐
│  Subgraph       │
│                 │
│  - Event        │
│    Handlers     │
│  - Mappings     │
└────────┬────────┘
         │ Indexes
         ▼
┌─────────────────┐
│  GraphQL API    │
│                 │
│  - Queries      │
│  - Subscriptions│
└─────────────────┘
```

## Resources

* [The Graph Documentation](https://thegraph.com/docs/)
* [AssemblyScript Documentation](https://www.assemblyscript.org/)
* [GraphQL Documentation](https://graphql.org/learn/)
* [InkDAO Smart Contracts](../smart-contracts/README.md)