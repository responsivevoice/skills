# Laravel integration

Bind the SDK in a service provider and read credentials from config, so controllers,
commands, and jobs receive ready-to-use `SynthesisApi` / `VoicesApi` instances.

## Config

`config/responsivevoice.php`:

```php
<?php

return [
    'host' => env('RESPONSIVEVOICE_API_HOST', 'https://texttospeech.responsivevoice.org'),
    'key' => env('RESPONSIVEVOICE_API_KEY'),
    'secret' => env('RESPONSIVEVOICE_API_SECRET'),
];
```

Set `RESPONSIVEVOICE_API_KEY` and `RESPONSIVEVOICE_API_SECRET` in `.env`.

## Service provider

```php
<?php

namespace App\Providers;

use GuzzleHttp\Client;
use GuzzleHttp\ClientInterface;
use Illuminate\Support\ServiceProvider;
use ResponsiveVoice\Sdk\Api\SynthesisApi;
use ResponsiveVoice\Sdk\Api\VoicesApi;
use ResponsiveVoice\Sdk\Configuration;

class ResponsiveVoiceServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        $this->app->singleton(Configuration::class, fn () => (new Configuration())
            ->setHost(config('responsivevoice.host'))
            ->setApiKey('X-API-Key', config('responsivevoice.key'))
            ->setApiKey('X-API-Secret', config('responsivevoice.secret')));

        $this->app->bind(ClientInterface::class, fn () => new Client());

        foreach ([VoicesApi::class, SynthesisApi::class] as $api) {
            $this->app->bind($api, fn ($app) => new $api(
                $app->make(ClientInterface::class),
                $app->make(Configuration::class),
            ));
        }
    }
}
```

Register it in `bootstrap/providers.php`.

Build a fresh `Configuration` as shown — do not mutate the shared
`Configuration::getDefaultConfiguration()`. That static lives for the whole process, so under
Octane or any long-lived worker a credential written to it survives into later requests.

## Artisan command

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use ResponsiveVoice\Sdk\Api\SynthesisApi;
use ResponsiveVoice\Sdk\ApiException;

class SpeakCommand extends Command
{
    protected $signature = 'rv:speak {text} {--voice=UK English Female} {--out=speech.mp3}';

    protected $description = 'Synthesize text to speech';

    public function handle(SynthesisApi $synthesis): int
    {
        try {
            $audio = $synthesis->v2TextSynthesizeGet($this->argument('text'), $this->option('voice'));
        } catch (ApiException $e) {
            $this->error("Synthesis failed (HTTP {$e->getCode()}): {$e->getMessage()}");

            return self::FAILURE;
        }

        file_put_contents($this->option('out'), $audio);
        $this->info(sprintf('Wrote %s (%d bytes)', $this->option('out'), strlen($audio)));

        return self::SUCCESS;
    }
}
```

```bash
php artisan rv:speak "Hello from Laravel" --voice="US English Female"
```
