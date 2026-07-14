# Language SDKs

## Available now

- **TypeScript / Node** — use [`@responsivevoice/api-client`](clients.md): typed
  `synthesize()` with retries. Install `npm install @responsivevoice/api-client`.
- **Any other language** — call the [REST API](rest-api.md) directly. It returns raw audio
  bytes, so any HTTP client writes an MP3 today; see the Python example there.

## Coming soon — native SDKs

Native SDKs for Python, Go, PHP, and Java are generated from the OpenAPI spec and **not yet
published**. Do not attempt to install them yet — until each is released, use the REST API
above (it works from every one of these languages today).

When published, the install commands will be:

| Language | Source repo                                                 | Install (on release)                       |
| -------- | ----------------------------------------------------------- | ------------------------------------------ |
| Python   | [sdk-python](https://github.com/responsivevoice/sdk-python) | `pip install responsivevoice`              |
| Go       | [sdk-go](https://github.com/responsivevoice/sdk-go)         | `go get github.com/responsivevoice/sdk-go` |
| PHP      | [sdk-php](https://github.com/responsivevoice/sdk-php)       | `composer require responsivevoice/sdk-php` |
| Java     | [sdk-java](https://github.com/responsivevoice/sdk-java)     | Maven dependency `org.responsivevoice:sdk` |

All will authenticate with the same `X-API-Key` + `X-API-Secret` credentials as the REST
API (see [auth.md](auth.md)). Check each repo's releases page for availability.
