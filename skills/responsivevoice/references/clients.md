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

### Install (CDN bundle)

The CDN build exposes a global `responsiveVoice` with the same methods as the npm instance
(`init`, `speak`, `cancel`, `pause`, `resume`, `getVoices`, `isPlaying`):

```html
<script src="https://cdn.responsivevoice.org/sdk/latest/responsivevoice.js"></script>
<script>
  responsiveVoice.init({ apiKey: 'YOUR_API_KEY' });
  responsiveVoice.speak('Hello world', 'UK English Female');
</script>
```

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
| `transport`      | `string`  | `'chunks'`            |

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
  format: 'mp3',
});

const el = new Audio(audio.url);
el.play();
URL.revokeObjectURL(audio.url);
```

**On a server — add `apiSecret` (sent as `X-API-Key` / `X-API-Secret`; origin check skipped):**

```typescript
const client = new ResponsiveVoiceAPIClient({
  apiKey: 'YOUR_API_KEY',
  apiSecret: 'YOUR_API_SECRET', // server-side only
});
```

Default `baseUrl` is `https://texttospeech.responsivevoice.org/v2`; `timeout` 30000 ms;
`retryAttempts` 3.
