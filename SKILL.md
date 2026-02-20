# chillpay Skill for OpenClaw

**Version:** 1.0.0  
**Author:** chillpay  
**Description:** Escrow and token locking infrastructure for AI agents on Base blockchain.

---

## What this skill does

Allows AI agents to:
- Create and release escrow payments between agents or humans
- Lock tokens with time-based or condition-based release
- Verify the status of any escrow or lock on-chain
- Generate API keys for new integrations

All operations are verified 100% on-chain via RPC. The agent only needs to submit a transaction hash — chillpay reads the blockchain directly.

---

## Base URL

```
https://eckmmctnjcfkzawfcwqj.supabase.co/functions/v1
```

> **Note:** This URL will migrate to `https://api.chillpay.ai/v1` once the custom domain is configured. Update your integration accordingly when v2 is released.

---

## Authentication

All requests require an API key in the header:

```
x-api-key: YOUR_API_KEY
```

---

## Endpoints

### 1. Create Escrow

Lock funds in escrow pending a condition or manual release.

```
POST /escrow?action=create
```

**Headers:**
```
x-api-key: YOUR_API_KEY
Content-Type: application/json
```

**Body:**
```json
{
  "chain": "base",
  "deposit_tx_hash": "0x..."
}
```

**Supported chains:** `base`, `base-sepolia`

**Response:**
```json
{
  "escrow_id": "7",
  "status": "locked",
  "depositor": "0x...",
  "beneficiary": "0x...",
  "amount": "100000000",
  "token": "0x...",
  "chain": "base",
  "tx_hash": "0x..."
}
```

---

### 2. Release Escrow

Release funds to the beneficiary once conditions are met.

```
POST /escrow?action=release
```

**Body:**
```json
{
  "escrow_id": "7",
  "chain": "base"
}
```

**Response:**
```json
{
  "escrow_id": "7",
  "status": "released",
  "release_tx": "0x..."
}
```

---

### 3. Lock Tokens

Lock tokens for a fixed duration (vesting, commitment, collateral).

```
POST /lock
```

**Body:**
```json
{
  "chain": "base",
  "lock_tx_hash": "0x..."
}
```

**Response:**
```json
{
  "lock_id": "12",
  "status": "locked",
  "owner": "0x...",
  "amount": "500000000",
  "token": "0x...",
  "unlock_time": 1740000000,
  "chain": "base"
}
```

---

### 4. Unlock Tokens

Unlock tokens after the lock period has expired.

```
POST /unlock
```

**Body:**
```json
{
  "lock_id": "12",
  "chain": "base"
}
```

---

### 5. Check Status

Query the current status of any escrow or lock.

```
GET /status?id=7&type=escrow&chain=base
```

**Query params:**
- `id` — escrow_id or lock_id
- `type` — `escrow` or `lock`
- `chain` — `base` or `base-sepolia`

**Response:**
```json
{
  "id": "7",
  "type": "escrow",
  "status": "locked",
  "amount": "100000000",
  "token": "0x...",
  "chain": "base",
  "created_at": "2026-02-19T10:00:00Z"
}
```

---

### 6. Generate API Key

Create a new API key for an agent or integration.

```
POST /create-api-key
```

> **Authentication required.** This endpoint requires a valid `x-api-key` header — an existing key is needed to provision new ones. Agents cannot self-register without prior authorization. Contact chillpay to obtain your first key.

**Headers:**
```
x-api-key: YOUR_EXISTING_API_KEY
Content-Type: application/json
```

**Body:**
```json
{
  "label": "my-agent-v1"
}
```

**Response:**
```json
{
  "api_key": "tl_live_...",
  "label": "my-agent-v1",
  "created_at": "2026-02-19T10:00:00Z"
}
```

---

## Smart Contracts — Base Mainnet

| Contract | Address |
|----------|---------|
| TokenLocker | [`0x583EeF5651888642882dA8D19A8061905e70EE45`](https://basescan.org/address/0x583EeF5651888642882dA8D19A8061905e70EE45) |
| TokenEscrow | [`0xec356784E7CED38Fa3b04828a91813624225E673`](https://basescan.org/address/0xec356784E7CED38Fa3b04828a91813624225E673) |

**Admin fee:** 1% on all transactions  
**Network:** Base (Chain ID: 8453)

---

## Usage Example — Agent-to-Human Payment

```bash
# Step 1: Human deploys a task, agent locks payment in escrow
# (Transaction sent on-chain by agent wallet first)

# Step 2: Register the escrow via API
curl -X POST "https://eckmmctnjcfkzawfcwqj.supabase.co/functions/v1/escrow?action=create" \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{ "chain": "base", "deposit_tx_hash": "0x..." }'

# Step 3: Work is completed. Release funds to human.
curl -X POST "https://eckmmctnjcfkzawfcwqj.supabase.co/functions/v1/escrow?action=release" \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{ "escrow_id": "7", "chain": "base" }'
```

---

## Error Codes

| Code | Meaning |
|------|---------|
| `401` | Invalid or missing API key |
| `400` | Bad request — check body params |
| `404` | Escrow or lock not found |
| `409` | Already released or unlocked |
| `500` | On-chain verification failed |

---

## Key Design Principle

> chillpay never trusts agent-submitted data. Every operation is verified directly on Base blockchain via RPC before being recorded. The transaction hash is the source of truth.

---

## Links

- **Website:** https://chillpay.ai  
- **Docs / GitHub:** https://github.com/chillpay  
- **Base Mainnet Explorer:** https://basescan.org
