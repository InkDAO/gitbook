# Subgraph

## Overview

The **InkDAO Subgraph** is a GraphQL API that indexes and provides efficient access to on-chain data from the InkDAO protocol. Built on [Goldsky](https://goldsky.com/), it tracks all content creation, subscriptions, and user activity across the platform in real-time.

The subgraph monitors events from the **MarketPlace** contract (ERC-6909 multi-token implementation), maintaining a comprehensive view of the entire InkDAO ecosystem within a single contract.

## Why Use the Subgraph?

While you can query blockchain data directly, the subgraph provides several advantages:

* **Fast Queries:** Pre-indexed data means instant access to complex queries
* **Historical Data:** Access complete historical records of all platform activity
* **Aggregated Stats:** Get platform-wide statistics and user metrics without manual calculations
* **GraphQL Interface:** Flexible, efficient queries with exactly the data you need
* **Real-time Updates:** Data updates as new blocks are mined

## Event Handlers

The subgraph listens to the following smart contract events from the MarketPlace contract:

### PostCreated

**Event Signature:**
```solidity
event PostCreated(
    uint256 indexed tokenId,
    string postTitle,
    string postCid,
    string thumbnailCid,
    address indexed author,
    uint256 costInNativeInWei
);
```

**Handler:** `handlePostCreated`

**Actions:**
- Creates or updates Asset entity with post metadata
- Creates or updates Creator entity for the author
- Increments creator's total assets and asset worth
- Updates GlobalStats (total assets, creators, asset worth)
- Tracks user statistics

**Use Case:** Triggered when a creator publishes a new post on the platform

---

### PostSubscribed

**Event Signature:**
```solidity
event PostSubscribed(
    uint256 indexed tokenId,
    address indexed subscriber,
    uint256 totalCost
);
```

**Handler:** `handlePostSubscribed`

**Actions:**
- Updates Asset entity (increments total subscribers)
- Updates Creator entity (increments total subscribers and earnings)
- Creates or updates Holder entity for the subscriber
- Updates GlobalStats (purchases, volume, revenue)
- Tracks user statistics

**Use Case:** Triggered when a user subscribes to a post (purchases access)

## Architecture

```
┌──────────────────────┐
│  Smart Contract      │
│                      │
│  MarketPlace         │
│  (ERC-6909)          │
│                      │
│  Events:             │
│  - PostCreated       │
│  - PostSubscribed    │
└──────────┬───────────┘
           │ Events
           ▼
┌──────────────────────┐
│  Subgraph            │
│                      │
│  Event Handlers:     │
│  - handlePostCreated │
│  - handlePostSubscribed│
│                      │
│  Mappings:           │
│  - marketPlace.ts    │
└──────────┬───────────┘
           │ Indexes
           ▼
┌──────────────────────┐
│  GraphQL API         │
│                      │
│  Entities:           │
│  - Asset             │
│  - Creator           │
│  - Holder            │
│  - GlobalStats       │
└──────────────────────┘
```

## Key Features

### Real-time Indexing
- Events are indexed as soon as blocks are mined
- Sub-second query response times
- Automatic updates on new transactions

### ERC-6909 Optimization
- Single contract to monitor (vs. N contracts in ERC-20 approach)
- Simpler event structure
- More efficient indexing
- Lower infrastructure costs

### Non-Transferable Tokens
- No secondary market transfer events to track
- Simpler holder tracking
- Permanent subscription records
- Clear ownership history

### Platform Analytics
- Real-time creator earnings (100% to creators)
- User engagement metrics
- Content performance analytics

## Benefits Over Direct Contract Queries

| Feature | Direct Contract | Subgraph |
|---------|----------------|----------|
| **Query Speed** | Slow (RPC calls) | Fast (indexed) |
| **Historical Data** | Limited | Complete |
| **Complex Queries** | Multiple calls | Single query |
| **Filtering** | Client-side | Server-side |
| **Sorting** | Manual | Built-in |
| **Aggregations** | Manual calculation | Pre-computed |
| **Pagination** | Complex | Simple |
| **Cost** | RPC rate limits | Free queries |

## Resources

* [The Graph Documentation](https://docs.goldsky.com/)
* [GraphQL Documentation](https://graphql.org/learn/)
* [InkDAO Smart Contracts](../smart-contracts/README.md)