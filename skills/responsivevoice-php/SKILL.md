---
name: responsivevoice-php
description: 'Call ResponsiveVoice text-to-speech from PHP with the official Composer SDK. Use when a PHP or Laravel task needs to synthesize speech, generate an MP3 from text, add TTS or voice output, list voices, or integrate `responsivevoice/sdk`. Covers the framework-free client and a Laravel service-provider setup. For the browser library, the Node client, or the raw REST API, use the responsivevoice-text-to-speech skill.'
license: MIT
metadata:
  author: responsivevoice
  version: '1.0.0'
---

# ResponsiveVoice PHP SDK

`responsivevoice/sdk` is the official Composer package for calling the ResponsiveVoice
REST API from PHP. It synthesizes text to audio and lists voices; a call returns the raw
MP3 bytes.

## Writing PHP? Continue

This skill is for server-side PHP. For a different surface, stop and use the
`responsivevoice-text-to-speech` skill instead:

- **A web page that speaks in the browser** — `@responsivevoice/core`.
- **A TypeScript or Node backend** — `@responsivevoice/api-client`.
- **Another language** — the REST API directly.

## Authentication (read before installing)

The SDK is server-to-server, so it needs both credentials:

- **`X-API-Key`** — your registered website's identifier.
- **`X-API-Secret`** — a real credential, shown once at creation. Keep it server-side;
  never commit it or ship it to a browser.

Pass them from the environment, never inline. Get them by
[registering a free account](https://responsivevoice.org/register): copy the key from your
website's "Your site code" snippet and create a secret under "Server-to-server API secrets"
in the [dashboard](https://app.responsivevoice.org).

## Install

```bash
composer require responsivevoice/sdk
```

Requires PHP 8.2+ and pulls in Guzzle as the HTTP client.

## Synthesize speech

Build a `Configuration`, call `SynthesisApi::v2TextSynthesizeGet()`, and write the returned
bytes to a file:

```php
<?php

require __DIR__ . '/vendor/autoload.php';

use GuzzleHttp\Client;
use ResponsiveVoice\Sdk\Api\SynthesisApi;
use ResponsiveVoice\Sdk\ApiException;
use ResponsiveVoice\Sdk\Configuration;

$config = (new Configuration())
    ->setApiKey('X-API-Key', getenv('RESPONSIVEVOICE_API_KEY'))
    ->setApiKey('X-API-Secret', getenv('RESPONSIVEVOICE_API_SECRET'));

$api = new SynthesisApi(new Client(), $config);

try {
    $audio = $api->v2TextSynthesizeGet('Hello from PHP', 'UK English Female');
} catch (ApiException $e) {
    fwrite(STDERR, "Synthesis failed (HTTP {$e->getCode()}): {$e->getMessage()}\n");
    exit(1);
}

file_put_contents('speech.mp3', $audio);
```

`ApiException::getCode()` is the HTTP status. The default host is the production API; override
it with `setHost()` only to point at a different deployment.

## List voices

```php
use ResponsiveVoice\Sdk\Api\VoicesApi;

$api = new VoicesApi(new Client(), $config);

$all = $api->v2VoicesGet()->getVoices();
$australian = $api->v2VoicesByLanguageLangGet('en-AU')->getVoices();

foreach ($all as $voice) {
    printf("%s\t%s\t%s\n", $voice->getName(), $voice->getLang(), $voice->getGender() ?? '');
}
```

## Laravel

Bind the SDK through a service provider and drive it from config, an Artisan command, or a
job — including the Octane-safe credential setup: [references/laravel.md](references/laravel.md).

## Notes

- Voice names are friendly strings like `UK English Female` / `US English Male`. Pass a
  `lang` (BCP-47, e.g. `pt-BR`) when you don't have a voice name.
- Output is MP3. `pitch`, `rate`, and `volume` accept `0.0`–`2.0` (`volume` `0.0`–`1.0`).
- Full PHP SDK guide: <https://docs.responsivevoice.org/sdks/php.md>
- OpenAPI spec: <https://texttospeech.responsivevoice.org/openapi.json>

## Fetching the docs

Always read the ResponsiveVoice docs as Markdown, never HTML — append `.md` to any docs URL
(<https://docs.responsivevoice.org/guides/voice-selection.md>). Start from:

- <https://docs.responsivevoice.org/llms.txt> — index of every page.
- <https://docs.responsivevoice.org/llms-full.txt> — the whole site in one file
  (`llms-small.txt` abridged; `/_llms-txt/<section>.txt` per section).
