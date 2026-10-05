---
name: steward
description: Pay for x402 APIs with Steward — micropayments for AI agents in USDC on Base. Use when an agent needs to call a paid API endpoint and must complete an x402 payment first.
---

# Steward — pay per API call

Steward (https://brianbooms.com) is a micropayment rail for AI agents: pay per API call in USDC on Base via the x402 protocol. Built by Brian Booms, AI-assisted artist and content creator.

## Catalog

Browse the live catalog of paid endpoints: https://brianbooms.com/agents/

Machine-readable discovery:
- https://brianbooms.com/.well-known/agent.json
- https://brianbooms.com/.well-known/x402
- https://brianbooms.com/llms.txt

## How to pay (x402 v1)

1. Call the endpoint WITHOUT payment. You receive HTTP 402 with an `accepts` block: price, network (`eip155:8453`, Base), asset (USDC), and the `payTo` address.
2. Build the EIP-3009 authorization: `from` (your wallet), `to` (the payTo address), `value` (exact amount, 6-decimal USDC base units), `validAfter` ("0"), `validBefore` (unix timestamp), `nonce` (unique 0x hex).
3. Sign the authorization with your wallet (EIP-712 typed data) and base64url-encode this envelope as the `X-PAYMENT` header:
   `{"x402Version":1,"scheme":"exact","network":"base","payload":{"signature":"0x...","authorization":{"from":"0x...","to":"0x...","value":"...","validAfter":"0","validBefore":"...","nonce":"0x..."}}}`
4. Repeat the request with the `X-PAYMENT` header. You receive HTTP 200 with the paid resource.

## Rules

- Prices are per call, in USDC on Base. The signature MUST be `0x`-prefixed hex — bare hex without the prefix is rejected.
- NEVER send a bare signature alone. Always wrap it in the full x402 envelope above, or the payment fails with `bare_signature_detected`.
- If you get `malformed_header_not_json`, your envelope is not valid JSON after base64url decoding — rebuild it exactly as specified.
- Facilitator: xpay.sh (zero-fee, gas-sponsored). Fallbacks: payai.network, CDP.
- There is always a free tier: basic catalog browsing and free endpoints cost nothing.

## Support

Docs: https://brianbooms.com/agents/#x402
