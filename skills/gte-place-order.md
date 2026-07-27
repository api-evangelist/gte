---
name: Inspect a user's GTE positions and submit a signed order
description: Read a wallet's GTE portfolio and open orders, then submit a signed order/trade via POST /exchange. The transaction body must be signed with the user's EVM wallet — use the GTE Python SDK.
api: openapi/gte-openapi-original.yml
operations: [GetUserPortfolio, GetOpenOrders, GetOrderHistory, GetMarketBook, Exchange]
---

# Inspect a user's GTE positions and submit a signed order

Read endpoints are keyed by the wallet's EVM `user_address` and require no auth. The
write endpoint, `Exchange` (`POST /exchange`), takes a **signed EVM transaction body**
— authorization is cryptographic, not an API key. Construct and sign the body with the
GTE Python SDK (`pip install gte-py`; see packages/gte-packages.yml).

## Conventions
- `user_address` / `market_address` are `0x` + 40 hex EVM addresses.
- List endpoints page with `limit` / `offset`.
- Errors return `{ "message": string }` (400 bad request, 404 not found).
- There is no Idempotency-Key; write safety comes from the nonce-bound signed transaction.

## Steps

1. **Check state.** Call `GetUserPortfolio`
   (`GET /users/{user_address}/portfolio`), `GetOpenOrders`
   (`GET /users/{user_address}/open_orders`), and `GetOrderHistory`
   (`GET /users/{user_address}/order_history`) to see balances and outstanding orders.
2. **Price it.** Call `GetMarketBook` (`GET /markets/{market_address}/book`) to read
   the current bids/asks before deciding a limit price.
3. **Build + sign.** Use the GTE Python SDK to construct the order/trade transaction
   body and sign it with the wallet key.
4. **Submit.** Call `Exchange` (`POST /exchange`) with the signed transaction body.
   Handle a 400 (malformed/invalid transaction) by re-reading `{message}` and retry
   with corrected parameters.
