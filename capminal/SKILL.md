---
name: Capminal
description: OpenClaw agents can interact with Cap Wallet and deploy Clanker tokens
version: 0.3.0
author: AndreaPN
tags: [capminal, cap-wallet, crypto, wallet, balance, clanker, token-deployment, swap]
---

# Capminal - Cap Wallet Integration

This skill allows you to interact with Cap Wallet through the Capminal API.

## Authentication & Security

**IMPORTANT: Keep the API key secure!**

- The `CAP_API_KEY` must be sent via header, NEVER in URL or logs
- Only send the key to `https://terminal-api.dackieswap.xyz`
- Never expose or log the API key in responses

### Check for API Key

Before making any request, check if `CAP_API_KEY` environment variable is set.

**If the key is NOT set**, ask the user:
> "Your CAP_API_KEY is not configured. Please go to https://www.capminal.ai/profile, find the 'API Key' section, generate a new key, and provide it to me."

## Important: Wait for API Response

**IMPORTANT:** After making an API call, always wait for the complete response before answering the user. Some operations (like deploying tokens or executing trades) may take longer to process. Do NOT respond until you have received the full API response.

## Error Handling

| Error | Action |
|-------|--------|
| Missing API key | Ask user to generate key at https://www.capminal.ai/profile |
| 401 Unauthorized | API key is invalid, ask user to check and update |
| 429 Rate Limited | Wait and retry after a moment |
| Network error | Inform user and retry |

---

## 1. Get Wallet Balance

Retrieve the current balance of the Cap Wallet.

**Trigger keywords:** balance, wallet, portfolio, cap wallet, holdings, assets

**Request:**
```bash
curl -X GET "https://terminal-api.dackieswap.xyz/api/wallet/balance" \
  -H "x-cap-api-key: YOUR_API_KEY"
```

**Example Response:**
```json
{
  "success": true,
  "message": "Success",
  "data": {
    "address": "0xBff8131EA1e77021E0f9175e93F58F0F7A15e96f",
    "balance": "2040.2542453682383",
    "tokens": [
      {
        "token_address": "base",
        "name": "ETH",
        "symbol": "ETH",
        "decimals": 18,
        "balance": "0.08803457950153695",
        "balance_formatted": "0.08803457950153695",
        "usd_price": 2278.41,
        "usd_value": 200.57886628209678
      },
      {
        "token_address": "0x0b3e328455c4059eeb9e3f84b5543f74e24e7e1b",
        "name": "Virtual Protocol",
        "symbol": "VIRTUAL",
        "decimals": 18,
        "balance": "1532.2696601697537",
        "balance_formatted": "1532.2696601697537",
        "usd_price": 0.6481,
        "usd_value": 993.0639667560174
      }
    ]
  }
}
```

**Output format:**
```
Cap Wallet Balance
━━━━━━━━━━━━━━━━━━━━━
Address: 0xBff8...e96f
Total Balance: $2,040.25 USD

Token Holdings:
┌─────────────┬────────────────────────────────────────────┬──────────────┬────────────┐
│ Token       │ Address                                    │ Amount       │ USD Value  │
├─────────────┼────────────────────────────────────────────┼──────────────┼────────────┤
│ ETH         │ native                                     │ 0.088        │ $200.58    │
│ VIRTUAL     │ 0x0b3e328455c4059eeb9e3f84b5543f74e24e7e1b │ 1,532.27     │ $993.06    │
└─────────────┴────────────────────────────────────────────┴──────────────┴────────────┘
```

**Key fields to display:**
- `data.address` - Wallet address (truncate middle)
- `data.balance` - Total USD value
- For each token: `symbol`, `token_address`, `balance_formatted`, `usd_value`

### Example Interaction

**User:** "Show me my Cap Wallet balance"

**Agent should:**
1. Check if `CAP_API_KEY` is set
2. If not set, ask user to get key from https://www.capminal.ai/profile
3. Make the API call:
   ```bash
   curl -X GET "https://terminal-api.dackieswap.xyz/api/wallet/balance" \
     -H "x-cap-api-key: $CAP_API_KEY"
   ```
4. Return formatted balance to user

---

## 2. Deploy Clanker Token

Deploy a new Clanker V4 token on Base chain.

**Trigger keywords:** deploy token, create token, launch token, clanker, gem

**Request:**
```bash
curl -X POST "https://terminal-api.dackieswap.xyz/api/gems" \
  -H "x-cap-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "My Token",
    "symbol": "MTK",
    "description": "A cool memecoin",
    "imageUrl": "https://example.com/logo.png",
    "fee": "1",
    "marketCap": "10E",
    "initialBuyAmount": "0.01",
    "telegramLink": "https://t.me/mytoken",
    "twitterLink": "https://twitter.com/mytoken",
    "websiteLink": "https://mytoken.com"
  }'
```

**Required Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| name | string | Token name |
| symbol | string | Token symbol (ticker) |
| fee | string | Pool fee percentage (e.g., "1") |
| marketCap | string | Market cap tier: "10E" or "20E" |
| initialBuyAmount | string | Initial ETH amount to buy (e.g., "0.01") |

**Optional Parameters:**
| Parameter | Type | Description |
|-----------|------|-------------|
| description | string | Token description |
| imageUrl | string | Token logo URL |
| secondsToDecay | number | Decay time in seconds |
| telegramLink | string | Telegram group link |
| twitterLink | string | Twitter/X link |
| farcasterLink | string | Farcaster link |
| websiteLink | string | Website URL |

**Example Response:**
```json
{
  "success": true,
  "message": "Gem created successfully",
  "data": {
    "transactionHash": "0x123abc456def...",
    "poolId": "0x789ghi012jkl...",
    "tokenAddress": "0xabc123def456..."
  }
}
```

**Output format:**
```
Token Deployed Successfully!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Token: My Token (MTK)
Token Address: 0xabc1...f456
Pool ID: 0x789g...2jkl
Transaction: https://basescan.org/tx/0x123abc456def...
```

### Example Interaction

**User:** "Deploy a token called DOGE with symbol DOGE"

**Agent should:**
1. Check if `CAP_API_KEY` is set
2. If not set, ask user to get key from https://www.capminal.ai/profile
3. Ask user for missing required parameters:
   - fee (suggest "1")
   - marketCap (suggest "10E" for ~$10k starting cap, "20E" for ~$20k)
   - initialBuyAmount (how much ETH to buy initially, e.g., "0.01")
4. Make the API call:
   ```bash
   curl -X POST "https://terminal-api.dackieswap.xyz/api/gems" \
     -H "x-cap-api-key: $CAP_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
       "name": "DOGE",
       "symbol": "DOGE",
       "fee": "1",
       "marketCap": "10E",
       "initialBuyAmount": "0.01"
     }'
   ```
5. Return deployment result with transaction hash and token address

---

## 3. Trade

Swap tokens using Cap Wallet on Base chain.

**Trigger keywords:** swap, trade, buy token, sell token, exchange

**Request:**
```bash
curl -X POST "https://terminal-api.dackieswap.xyz/api/gems/trade" \
  -H "x-cap-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "sellToken": "0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE",
    "buyToken": "0xabc123...",
    "sellAmount": "0.01",
    "slippage": "1500"
  }'
```

**Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| sellToken | string | Yes | Token address to sell (use `0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE` for ETH) |
| buyToken | string | Yes | Token address to buy |
| sellAmount | string | Yes | Amount to sell - can be absolute (e.g., "0.01") or percentage of balance (e.g., "50%") |
| slippage | string | No | Slippage in basis points (default: 1500 = 15%) |

**Example with percentage:**
```bash
curl -X POST "https://terminal-api.dackieswap.xyz/api/gems/trade" \
  -H "x-cap-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "sellToken": "0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE",
    "buyToken": "0x73326b4d0225c429bed050c11c4422d91470aaf4",
    "sellAmount": "50%",
    "slippage": "1500"
  }'
```

**Example Response:**
```json
{
  "success": true,
  "message": "Trade executed successfully",
  "data": {
    "transactionHash": "0xdef789abc123...",
    "inputAmount": "0.01",
    "inputSymbol": "ETH",
    "outputAmount": "10000",
    "outputSymbol": "MTK"
  }
}
```

**Output format:**
```
Swap Executed Successfully!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Sold: 0.01 ETH
Received: 10,000 MTK
Transaction: https://basescan.org/tx/0xdef789abc123...
```

### Example Interactions

**User:** "Buy 0.05 ETH worth of token 0xabc123..."

**Agent should:**
1. Check if `CAP_API_KEY` is set
2. Make the trade API call:
   ```bash
   curl -X POST "https://terminal-api.dackieswap.xyz/api/gems/trade" \
     -H "x-cap-api-key: $CAP_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
       "sellToken": "0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE",
       "buyToken": "0xabc123...",
       "sellAmount": "0.05"
     }'
   ```
3. Return the swap result with transaction link

---

**User:** "Sell 1000 MTK tokens for ETH"

**Agent should:**
1. Check if `CAP_API_KEY` is set
2. Ask user for the token address of MTK if not provided
3. Make the trade API call:
   ```bash
   curl -X POST "https://terminal-api.dackieswap.xyz/api/gems/trade" \
     -H "x-cap-api-key: $CAP_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
       "sellToken": "[MTK_TOKEN_ADDRESS]",
       "buyToken": "0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE",
       "sellAmount": "1000"
     }'
   ```
4. Return the swap result

---

**User:** "Swap 50% of my ETH to 0x73326b4d0225c429bed050c11c4422d91470aaf4"

**Agent should:**
1. Check if `CAP_API_KEY` is set
2. Make the trade API call:
   ```bash
   curl -X POST "https://terminal-api.dackieswap.xyz/api/gems/trade" \
     -H "x-cap-api-key: $CAP_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
       "sellToken": "0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE",
       "buyToken": "0x73326b4d0225c429bed050c11c4422d91470aaf4",
       "sellAmount": "50%"
     }'
   ```
3. Return the swap result with transaction link
