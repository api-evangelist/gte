---
name: Look up GTE tokens, markets, and order books
description: Read GTE's public market-data endpoints — discover tokens and markets, and pull candles, trades, and the live order book for a market. No authentication required.
api: openapi/gte-openapi-original.yml
operations: [GetTokens, SearchTokens, GetTokenByAddress, GetMarkets, SearchMarkets, GetMarketByAddress, GetMarketBook, GetMarketCandles, GetMarketTrades]
---

# Look up GTE tokens, markets, and order books

GTE is a non-custodial decentralized exchange on MegaETH. All market-data reads are
public and unauthenticated — no API key or wallet is needed. Base URL:
`https://api-testnet.gte.xyz/v1`.

## Conventions
- Resources are keyed by EVM addresses: `token_address` and `market_address` are `0x` + 40 hex.
- List endpoints page with `limit` and `offset` query params.
- Errors return `{ "message": string }` (HTTP 400 for bad input, 404 when not found).

## Steps

1. **Find a token.** Call `SearchTokens` (`GET /tokens/search?q=...`) or list with
   `GetTokens` (`GET /tokens?limit=&offset=&marketType=`). Use `GetTokenByAddress`
   (`GET /tokens/{token_address}`) once you have an address.
2. **Find its market.** Call `SearchMarkets` (`GET /markets/search?q=...`) or list
   with `GetMarkets`, then `GetMarketByAddress` (`GET /markets/{market_address}`).
3. **Read the book.** Call `GetMarketBook` (`GET /markets/{market_address}/book`) for
   bids/asks.
4. **Read history.** Call `GetMarketCandles`
   (`GET /markets/{market_address}/candles?interval=&startTime=&endTime=`) for OHLC,
   and `GetMarketTrades` (`GET /markets/{market_address}/trades?limit=&offset=`) for
   the trade tape.
