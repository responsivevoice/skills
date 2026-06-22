# Client libraries (core & api-client)

Two npm clients: `@responsivevoice/core` speaks in the browser; `@responsivevoice/api-client`
fetches audio over REST. Which credentials they take depends on **where the code runs**, not
which package you pick.

## Two authentication systems

Pick by execution context (full credential detail in [auth.md](auth.md)):

- **Origin-verified (browser).** Send the `apiKey` only — no secret. The browser attaches the
  request origin automatically, and the server authorizes it against your verified domain. The
  `apiKey` is an identifier, so it is safe to ship in client-side code.
- **Secret (server-to-server).** Send `apiKey` + `apiSecret` (as `X-API-Key` / `X-API-Secret`).
  No origin is involved — the secret authorizes the call. Server-side only; **never ship the
  secret to a browser.**

`@responsivevoice/core` only ever uses the origin-verified flow. `@responsivevoice/api-client`
works in **either** context: pass the secret on a server, omit it in a browser.

## `@responsivevoice/core` — speak in the browser

Speaks via the browser's Web Speech API and falls back to the server when a voice is missing.
Origin-verified flow only — it never takes a secret.

### Install (npm)

```bash
npm install @responsivevoice/core
# or: pnpm add @responsivevoice/core / yarn add @responsivevoice/core
```

```typescript
import { getResponsiveVoice } from '@responsivevoice/core';

const rv = await getResponsiveVoice({ apiKey: 'YOUR_API_KEY' });
rv.speak('Hello world', 'UK English Female');

rv.speak('Hello world', 'UK English Female', {
  pitch: 1.0,
  rate: 1.0,
  volume: 1.0,
  onstart: () => console.log('Started'),
  onend: () => console.log('Finished'),
});
```

`getResponsiveVoice()` is the recommended async factory (creates the singleton and calls
`init` internally). `rv.cancel()` stops all speech.

### Package CDNs (jsDelivr / unpkg) — ESM only

jsDelivr and unpkg serve the npm **ESM** build, not a global. Consume them exactly like the
npm package — `import { getResponsiveVoice }` through the async-factory flow above — not as a
`<script>` tag expecting a global `responsiveVoice`.

### Browser bundle (CDN, no build step)

For an easy, legacy-compatible `<script>` that exposes a global `responsiveVoice` (`init`,
`speak`, `cancel`, `pause`, `resume`, `getVoices`, `isPlaying`), see
[@responsivevoice/core → Browser bundle (CDN)](https://github.com/responsivevoice/core#browser-bundle-cdn).

### Demo mode

Omit `apiKey` (`init({})`) to run with the device's built-in voice only — no account needed.
Register and **verify your website's domain** in the dashboard to unlock the full voice
catalog. The browser library authenticates by origin; it never needs the API secret.

### Init options

| Option           | Type      | Default               |
| ---------------- | --------- | --------------------- |
| `apiKey`         | `string`  | —                     |
| `defaultVoice`   | `string`  | `'UK English Female'` |
| `forceFallback`  | `boolean` | `false`               |
| `characterLimit` | `number`  | `100`                 |

## `@responsivevoice/api-client` — fetch audio (browser or server)

A lower-level REST client that returns an audio Blob/URL rather than speaking. `apiKey` is
required; `apiSecret` is optional and selects the auth system.

```bash
npm install @responsivevoice/api-client
```

**In a browser — `apiKey` only (origin-verified, like core). Do not include a secret:**

```typescript
import { ResponsiveVoiceAPIClient } from '@responsivevoice/api-client';

const client = new ResponsiveVoiceAPIClient({ apiKey: 'YOUR_API_KEY' });

const audio = await client.synthesize({
  text: 'Hello, world!',
  voice: 'UK English Female',
});

const el = new Audio(audio.url);
el.onended = () => URL.revokeObjectURL(audio.url);
el.play();
```

**On a server — add `apiSecret` (sent as `X-API-Key` / `X-API-Secret`; origin check skipped):**

```typescript
const client = new ResponsiveVoiceAPIClient({
  apiKey: process.env.RV_API_KEY,
  apiSecret: process.env.RV_API_SECRET, // server-side only
});
```

Default `baseUrl` is `https://texttospeech.responsivevoice.org/v2`; `timeout` 30000 ms;
`retryAttempts` 3.
