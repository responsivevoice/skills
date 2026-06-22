# Authentication & credentials

ResponsiveVoice has three access modes. Pick by where your code runs.

| Mode             | Needs             | Where it runs       | What you get                                 |
| ---------------- | ----------------- | ------------------- | -------------------------------------------- |
| Demo             | nothing           | browser             | device's built-in voice only                 |
| API key only     | `apiKey`          | browser             | full voice catalog after domain verification |
| API key + secret | `apiKey` + secret | server / SDK / REST | full catalog from any backend or language    |

## The key is an identifier, the secret is a credential

- **API key** — your registered website's public identifier. It establishes _origin
  identity_, not authorization, so it is safe in browser code. The browser library
  (`@responsivevoice/core`) authenticates by origin and needs only the key.
- **API secret** — a real credential. The REST API and the SDKs require it alongside the
  key, sent as the `X-API-Secret` header. Keep it server-side; never embed it in browser
  code or commit it to source control.

## Getting credentials

1. [Register for a free account](https://responsivevoice.org/register).
2. A default website is created automatically. Its identifier is your **API key** — copy it
   from the "Your site code" snippet (`key: XXXXX`) in the [dashboard](https://app.responsivevoice.org).
3. For server/SDK/REST use, create an **API secret** under "Server-to-server API secrets".
   It is shown **once** at creation — copy it immediately. You can revoke and recreate it
   anytime, but a lost secret can't be retrieved.
4. For browser use, **verify your website's domain** in the dashboard so requests from your
   site are recognized — this unlocks the full voice set.

## Header names

The REST API and SDKs send:

```
X-API-Key: $RV_API_KEY
X-API-Secret: $RV_API_SECRET
```

In the generated SDKs the two schemes are named `apiKeyHeader` and `apiSecretHeader` — see
[sdks.md](sdks.md) for the exact per-language wiring.
