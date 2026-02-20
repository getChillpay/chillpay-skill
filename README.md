# chillpay-skill

Escrow and token locking infrastructure for AI agents on Base blockchain.

The trust layer for the OpenClaw ecosystem.

---

## What is chillpay?

chillpay is an on-chain escrow and token locking API. It lets AI agents and humans create verifiable payment guarantees without trusting each other — everything is settled on Base.

Use cases:
- Agent hires human via RentAHuman → escrow payment until task is complete
- DAO locks treasury tokens for a vesting period
- Agent-to-agent payment with conditional release

---

## Quick Start

1. Get an API key at [chillpay.dev](https://chillpay.dev)
2. Read the [SKILL-v.1.md](./SKILL-v.1.md) for full API documentation
3. Make your first escrow call

```bash
curl -X POST "https://eckmmctnjcfkzawfcwqj.supabase.co/functions/v1/escrow?action=create" \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{ "chain": "base", "deposit_tx_hash": "0x..." }'
```

---

## Supported Tokens — Base Mainnet

USDC, USDT, WETH, DAI, WBTC

---

## Smart Contracts

| Contract | Address |
|----------|---------|
| TokenLocker | [`0x583EeF5651888642882dA8D19A8061905e70EE45`](https://basescan.org/address/0x583EeF5651888642882dA8D19A8061905e70EE45) |
| TokenEscrow | [`0xec356784E7CED38Fa3b04828a91813624225E673`](https://basescan.org/address/0xec356784E7CED38Fa3b04828a91813624225E673) |

**Fee:** 1% flat on all transactions  
**Network:** Base (Chain ID: 8453)

---

## v1 Security Notes

- Token whitelist active — only verified ERC20 tokens accepted
- All operations verified on-chain via RPC, never trusted from agent input
- Reentrancy mitigated at API level in v1 — full `nonReentrant` fix in v2

---

## Links

- [Website](https://chillpay.dev)
- [Basescan](https://basescan.org)
