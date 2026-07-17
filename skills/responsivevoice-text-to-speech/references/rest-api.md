# REST API

Generate speech audio from any language or platform — no website required. Use for
server-side generation, mobile/non-browser clients, and batch processing.

**Base URL:** `https://texttospeech.responsivevoice.org/v2`

## Authentication

This reference covers **server-to-server** calls, which send both headers together:

- `X-API-Key` — your website's public identifier
- `X-API-Secret` — your non-public server credential

A browser caller does not use the secret — it sends the `apiKey` alone and is authorized by
the verified-origin flow instead (see [clients.md](clients.md)). See [auth.md](auth.md) for
where to get the credentials.

## Synthesize (GET — CDN-cacheable, idempotent)

```bash
curl "https://texttospeech.responsivevoice.org/v2/text/synthesize?text=Hello+world&voice=US+English+Female" \
  -H "X-API-Key: $RV_API_KEY" \
  -H "X-API-Secret: $RV_API_SECRET" \
  --output speech.mp3
```

Prefer GET for deterministic requests; identical parameters always return identical audio,
so it is safe for CDN edge caching.

### Parameters

| Param    | Required           | Values                                                                   |
| -------- | ------------------ | ------------------------------------------------------------------------ |
| `text`   | yes                | text to speak (max 4000 characters)                                      |
| `voice`  | `voice` or `lang`  | a voice `name` from [`/v2/voices`](#list-voices), e.g. `UK English Male` |
| `lang`   | `voice` or `lang`  | BCP-47 code, e.g. `en-US`, `pt-BR` — use when you pass no `voice`        |
| `pitch`  | no (default `1.0`) | `0.0`–`2.0`                                                              |
| `rate`   | no (default `1.0`) | `0.0`–`2.0`                                                              |
| `volume` | no (default `1.0`) | `0.0`–`1.0` (`0` returns `204` with no audio body)                       |

Provide **either** `voice` or `lang`. `voice` is the friendly `name` returned by the voices
endpoint; the server resolves it to the right voice. Use `lang` alone when you have no specific
voice in mind.

## From a programming language

The endpoint returns raw audio bytes, so any HTTP client writes a file. PHP has a native
Composer SDK (see [sdks.md](sdks.md)); from every other language call the REST API directly.
Python example:

```python
import os

import requests

resp = requests.get(
    "https://texttospeech.responsivevoice.org/v2/text/synthesize",
    params={"text": "Hello world", "voice": "US English Female"},
    headers={"X-API-Key": os.environ["RV_API_KEY"], "X-API-Secret": os.environ["RV_API_SECRET"]},
)
resp.raise_for_status()
with open("speech.mp3", "wb") as f:
    f.write(resp.content)
```

The same shape ports to any language: GET the URL with the two headers and write the
response body to a file.

## List voices

Fetch the catalog first, then synthesize with a voice's `name`. Each entry has a `name` (pass
it as `voice`) and its `lang`:

```bash
curl https://texttospeech.responsivevoice.org/v2/voices \
  -H "X-API-Key: $RV_API_KEY" \
  -H "X-API-Secret: $RV_API_SECRET"
```

Response (one object per voice, other fields omitted):

```json
[
  { "name": "UK English Female", "lang": "en-GB" },
  { "name": "US English Male", "lang": "en-US" }
]
```

Pick a `name` and pass it as the `voice` parameter to synthesize. Narrow the list with
`GET /v2/voices/by-language/{lang}`, or fetch one with `GET /v2/voices/{name}`.

## More

- Interactive reference: <https://texttospeech.responsivevoice.org/reference>
- OpenAPI spec: <https://texttospeech.responsivevoice.org/openapi.json>
- From TypeScript/Node, prefer [`@responsivevoice/api-client`](clients.md) for typing and retries.
