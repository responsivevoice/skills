---
name: responsivevoice-text-to-speech
description: 'Install and run ResponsiveVoice text-to-speech in a browser, on a server, or from any language. Use when a task needs to speak text aloud, add TTS or voice output, read content out loud, integrate @responsivevoice/core or @responsivevoice/api-client, or call the ResponsiveVoice REST API from Python, Go, PHP, Java, or any other language. Native language SDKs are coming soon; until then use the REST API. For configuring the on-page web player UI, use the responsivevoice-web-player skill.'
license: MIT
metadata:
  author: responsivevoice
  version: '1.1.0'
---

# ResponsiveVoice Text-to-Speech

ResponsiveVoice turns text into spoken audio. It ships as a browser library, a Node REST
client, and a language-agnostic REST API that works from any language today. Native
Python, Go, PHP, and Java SDKs are coming soon.

## Choose your surface

| You are building                                             | Use                                               | Reference                                                                                   |
| ------------------------------------------------------------ | ------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| A web page that speaks in the browser                        | `@responsivevoice/core` (npm or CDN)              | [references/clients.md](references/clients.md)                                              |
| A TypeScript/Node app, backend, or CLI that fetches audio    | `@responsivevoice/api-client` (browser or server) | [references/clients.md](references/clients.md)                                              |
| A backend or CLI in another language (Python, Go, PHP, Java) | REST API directly (native SDKs soon)              | [references/rest-api.md](references/rest-api.md) · [references/sdks.md](references/sdks.md) |

## Authentication (read before installing)

Three modes — pick by surface. Full detail in [references/auth.md](references/auth.md).

- **Demo mode** — no account, no key. The browser library speaks with the device's
  built-in voice only. Good for a first run.
- **API key only** — a registered website identifier passed to the browser library. It is
  an _origin identity_, not a secret; after you verify your domain it unlocks the full
  server voice catalog. No secret in browser code.
- **API key + API secret** — required for the REST API and the SDKs. Sent as the
  `X-API-Key` and `X-API-Secret` headers. The secret is shown once at creation, is
  server-to-server only, and must never ship in browser code.

Get credentials: [register free](https://responsivevoice.org/register), then read the API
key from your website's "Your site code" snippet and create a secret under "Server-to-server
API secrets" in the [dashboard](https://app.responsivevoice.org).

## Quickstart (browser, demo mode — no key)

Install `@responsivevoice/core` and speak with the device's built-in voice — no account:

```bash
npm install @responsivevoice/core
```

```ts
import { getResponsiveVoice } from '@responsivevoice/core';

const rv = await getResponsiveVoice({});
rv.speak('Hello world');
```

Add `apiKey` and verify your domain to unlock the full catalog and named voices:

```ts
const rv = await getResponsiveVoice({ apiKey: 'YOUR_API_KEY' });
rv.speak('Hello world', 'UK English Female');
```

No build step? A prebuilt browser bundle is published. Fetch
<https://docs.responsivevoice.org/getting-started/installation.md> for the exact script tag —
do not guess the URL from memory.

## Quickstart (server, REST API)

```bash
curl "https://texttospeech.responsivevoice.org/v2/text/synthesize?text=Hello+world&voice=US+English+Female" \
  -H "X-API-Key: $RV_API_KEY" \
  -H "X-API-Secret: $RV_API_SECRET" \
  --output speech.mp3
```

## Notes

- Voice names are friendly strings like `UK English Female` / `US English Male`; the server
  resolves them. Pass `lang` (BCP-47, e.g. `pt-BR`) instead when you don't have a voice name.
- Default audio is MP3. `pitch`, `rate`, `volume` accept `0.0`–`2.0` (`volume` `0.0`–`1.0`).
- On-page article player with controls and highlighting? Use the `responsivevoice-web-player` skill.
- OpenAPI spec: <https://texttospeech.responsivevoice.org/openapi.json>

## Fetching the docs

Always read the ResponsiveVoice docs as Markdown, never HTML — append `.md` to any docs URL
(<https://docs.responsivevoice.org/guides/voice-selection.md>). Start from:

- <https://docs.responsivevoice.org/llms.txt> — index of every page.
- <https://docs.responsivevoice.org/llms-full.txt> — the whole site in one file
  (`llms-small.txt` abridged; `/_llms-txt/<section>.txt` per section).
