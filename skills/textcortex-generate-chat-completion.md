---
name: Generate a chat completion with TextCortex
description: Discover an available model, check API credit balance, and generate an OpenAI-compatible chat completion (optionally streaming).
api: openapi/textcortex-openapi.json
operations: [listModels, retrieveBalance, createChatCompletion]
---

# Generate a chat completion with TextCortex

TextCortex exposes an OpenAI-compatible API at `https://api.textcortex.com/v1`.
Authenticate every request with `Authorization: Bearer <api_key>` (generate the key at
https://app.textcortex.com/user/dashboard/settings/api-key).

## Steps

1. **Pick a model** — call `listModels` (`GET /models`). Use an `id` from the returned
   `data[]` (for example `kimi-k2-5`). Optionally call `retrieveModel`
   (`GET /models/{model_id}`) to read `served_from_country_code` for data-residency needs.
2. **Check credits (optional)** — call `retrieveBalance` (`GET /balance`) to read
   `remaining_credits`. If credits are exhausted, generation returns HTTP 402
   `Insufficient API credits`.
3. **Create the completion** — call `createChatCompletion` (`POST /chat/completions`) with
   `{ "model": "<id>", "messages": [{"role":"user","content":"..."}] }`. Read the reply
   from `choices[0].message.content` and token usage from `usage`.
4. **Stream (optional)** — set `"stream": true` to receive a `text/event-stream` of chunks
   instead of a single JSON body.

## Rules

- Errors use the OpenAI error envelope: `{"error":{"message","type","code","param"}}` —
  not RFC 9457. Handle `type` in `[invalid_request_error, server_error]`.
- 401 = missing/invalid key; 402 = out of credits; 404 = unknown model id.
- No idempotency key is supported; do not assume safe automatic retries of POSTs.
