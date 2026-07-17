# Language SDKs

## Available now

- **PHP** — install the official Composer SDK with `composer require responsivevoice/sdk`,
  then synthesize with `SynthesisApi::v2TextSynthesizeGet()`. The
  [`responsivevoice-php`](../../responsivevoice-php/SKILL.md) skill covers the framework-free
  client and a Laravel setup; full guide at <https://docs.responsivevoice.org/sdks/php.md>.
- **TypeScript / Node** — use [`@responsivevoice/api-client`](clients.md): typed
  `synthesize()` with retries. Install `npm install @responsivevoice/api-client`.
- **Any other language** — call the [REST API](rest-api.md) directly. It returns raw audio
  bytes, so any HTTP client writes an MP3 today; see the Python example there.

## Coming soon — native SDKs

Native SDKs for Python, Go, and Java are generated from the OpenAPI spec and **not yet
published**. Do not attempt to install them yet — until each is released, use the REST API
above (it works from every one of these languages today).

| Language | Source repo                                                 |
| -------- | ----------------------------------------------------------- |
| Python   | [sdk-python](https://github.com/responsivevoice/sdk-python) |
| Go       | [sdk-go](https://github.com/responsivevoice/sdk-go)         |
| Java     | [sdk-java](https://github.com/responsivevoice/sdk-java)     |

Check each repo's releases page for availability and its README for the install command. All
authenticate with the same `X-API-Key` + `X-API-Secret` credentials as the REST API (see
[auth.md](auth.md)).
