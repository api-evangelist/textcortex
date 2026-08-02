---
name: Generate text with the TextCortex Responses API
description: Use the OpenAI Responses-compatible endpoint to generate text from a single input string or message list, optionally streaming.
api: openapi/textcortex-openapi.json
operations: [listModels, createResponse]
---

# Generate text with the TextCortex Responses API

TextCortex mirrors the OpenAI Responses contract at `POST /responses` on
`https://api.textcortex.com/v1`. Authenticate with `Authorization: Bearer <api_key>`.

## Steps

1. **Pick a model** — call `listModels` (`GET /models`) and choose an `id` (for example
   `kimi-k2-5`).
2. **Create a response** — call `createResponse` (`POST /responses`) with
   `{ "model": "<id>", "input": "Write a short product tagline.", "store": false }`.
   `input` may be a plain string or an array of role/content messages. Read generated
   content from the `output[]` array; token usage is in `usage`.
3. **Stream (optional)** — set `"stream": true` to receive a `text/event-stream` of
   response events.

## Rules

- `store` must be `false` or omitted — stored responses are not supported by this endpoint.
- Errors follow the OpenAI error envelope `{"error":{"message","type","code","param"}}`.
- 401 = missing/invalid key; 402 = insufficient credits (check `retrieveBalance`);
  404 = unknown model.
