---
name: Capminal
description: Now all OpenClaw agents can interact with Cap Wallet
version: 0.2.0
author: AndreaPN
tags: [capminal, cap-wallet, crypto, wallet, balance]
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
> "You need to configure CAP_API_KEY to use Cap Wallet. Please provide your API key or set the CAP_API_KEY environment variable."

## API Endpoints

### Get Wallet Balance

Retrieve the current balance of the Cap Wallet.

**Request:**
```bash
curl -X GET "https://terminal-api.dackieswap.xyz/api/wallet/balance" \
  -H "CAP_API_KEY: YOUR_API_KEY"
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
        "logo": "https://static.debank.com/image/coin/logo_url/eth/...",
        "possible_spam": false,
        "verified_contract": true,
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
        "logo": "https://static.debank.com/image/eth_token/logo_url/...",
        "possible_spam": false,
        "verified_contract": true,
        "usd_price": 0.6481,
        "usd_value": 993.0639667560174
      }
    ]
  }
}
```

### Parsing Response to Human-Readable Format

**IMPORTANT:** Always parse the API response into a clean, readable format for the user.

**Example output format:**
```
Cap Wallet Balance
━━━━━━━━━━━━━━━━━━━━━
Address: 0xBff8...e96f
Total Balance: $2,040.25 USD

Token Holdings:
┌─────────────┬──────────────┬────────────┐
│ Token       │ Amount       │ USD Value  │
├─────────────┼──────────────┼────────────┤
│ ETH         │ 0.088        │ $200.58    │
│ VIRTUAL     │ 1,532.27     │ $993.06    │
└─────────────┴──────────────┴────────────┘
```

**Key fields to display:**
- `data.address` - Wallet address (truncate middle for readability)
- `data.balance` - Total USD value
- For each token in `data.tokens`:
  - `symbol` - Token symbol
  - `balance_formatted` - Token amount
  - `usd_value` - USD equivalent

## Usage Instructions

When user asks about wallet balance, portfolio, cap wallet, or similar:

1. **Check API Key**: Verify `CAP_API_KEY` is available. If not, ask user to provide it.
2. **Make API Call**: Use curl or appropriate HTTP client to call the balance endpoint
3. **Format Response**: Present the balance data in a readable format to the user

**Trigger keywords:** balance, wallet, portfolio, cap wallet, holdings, assets

## Example Interaction

**User:** "Show me my Cap Wallet balance"

**Agent should:**
1. Check if `CAP_API_KEY` is set
2. If not set, ask user for the key
3. If set, make the API call:
   ```bash
   curl -X GET "https://terminal-api.dackieswap.xyz/api/wallet/balance" \
     -H "CAP_API_KEY: $CAP_API_KEY"
   ```
4. Return formatted balance to user

## Error Handling

| Error | Action |
|-------|--------|
| Missing API key | Ask user to provide CAP_API_KEY |
| 401 Unauthorized | API key is invalid, ask user to check and update |
| 429 Rate Limited | Wait and retry after a moment |
| Network error | Inform user and retry |
