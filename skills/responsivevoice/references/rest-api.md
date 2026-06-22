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
  -H 'X-API-Key: YOUR_API_KEY' \
  -H 'X-API-Secret: YOUR_API_SECRET' \
  --output speech.mp3
```

Prefer GET for deterministic requests; identical parameters always return identical audio,
so it is safe for CDN edge caching. A `POST /v2/text/synthesize` with a JSON body exists for
non-cacheable requests.

### Parameters

| Param    | Required             | Values                                                           |
| -------- | -------------------- | ---------------------------------------------------------------- |
| `text`   | yes                  | text to speak (plain or SSML on compatible engines)              |
| `voice`  | one of voice / lang  | friendly name, e.g. `UK English Male`                            |
| `lang`   | one of voice / lang  | BCP-47 code, e.g. `en-US`, `pt-BR`, `ja-JP`                      |
| `engine` | no (default `g1`)    | `g1` `g2` `g3` `g5` (standard); `gwn` `msv` `oai` (premium BYOK) |
| `gender` | no                   | `male` / `female` (used when no voice name given)                |
| `pitch`  | no (default `1.0`)   | `0.0`–`2.0`                                                      |
| `rate`   | no (default `1.0`)   | `0.0`–`2.0`                                                      |
| `volume` | no (default `1.0`)   | `0.0`–`1.0` (`0` returns `204` with no audio body)               |
| `format` | no (engine-specific) | `mp3` / `ogg` / `wav`                                            |

Format support per engine: `g1/g2/g3` → mp3 only; `g5` → ogg only; `gwn` → mp3, ogg;
`msv`/`oai` → mp3, ogg, wav.

Premium voices (Azure, OpenAI, Google Cloud) resolve by the same friendly `voice` name as
standard voices — you do not need to set `engine` or a provider voice id; the server maps the
name to the right engine. Set `engine` only to force a specific provider.

## From a programming language

The endpoint returns raw audio bytes, so any HTTP client writes a file. Native language
SDKs are coming soon; until then call the REST API directly. Python example:

```python
import requests

resp = requests.get(
    "https://texttospeech.responsivevoice.org/v2/text/synthesize",
    params={"text": "Hello world", "voice": "US English Female", "format": "mp3"},
    headers={"X-API-Key": "YOUR_API_KEY", "X-API-Secret": "YOUR_API_SECRET"},
)
resp.raise_for_status()
with open("speech.mp3", "wb") as f:
    f.write(resp.content)
```

The same shape ports to any language: GET the URL with the two headers and write the
response body to a file.

## List voices

```bash
curl https://texttospeech.responsivevoice.org/v2/voices \
  -H 'X-API-Key: YOUR_API_KEY' \
  -H 'X-API-Secret: YOUR_API_SECRET'
```

Also: `GET /v2/voices/{name}`, `GET /v2/voices/by-language/{lang}`.

## More

- Interactive reference: <https://texttospeech.responsivevoice.org/reference>
- OpenAPI spec: <https://texttospeech.responsivevoice.org/openapi.json>
- From TypeScript/Node, prefer [`@responsivevoice/api-client`](clients.md) for typing and retries.
