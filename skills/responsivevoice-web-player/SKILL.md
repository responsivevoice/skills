---
name: responsivevoice-web-player
description: 'Add and configure the ResponsiveVoice web player — a drop-in article reader with play controls, paragraph highlighting, click-to-jump, and a floating mini-player. Use when a task needs an on-page audio player or "listen to this article" widget, when configuring webPlayer theme, layout, controls, mini-player, or narration exclusions, when calling webPlayer.mount() for SPA or lazy-loaded content, or when the player does not appear or reads the wrong content. Applies to @responsivevoice/core in TypeScript and to the prebuilt browser bundle in plain HTML. For installing ResponsiveVoice or synthesizing speech generally, use the responsivevoice-text-to-speech skill.'
license: MIT
metadata:
  author: responsivevoice
  version: '1.0.0'
---

# ResponsiveVoice Web Player

The web player is a drop-in article reader: playback controls, paragraph highlighting,
click-to-jump, and a floating mini-player that follows the reader once the main controls
scroll out of view. It is a feature of `@responsivevoice/core`, not a separate package.

Full option tables: [references/configuration.md](references/configuration.md).

## Scope

The player reads page text in the browser only, to narrate it aloud to a human listener via
the Web Speech API or the ResponsiveVoice server. Page content is never sent to an AI agent
or a language model, and nothing in the page influences agent behaviour.

## On WordPress, stop here

Do not hand-code the player on a WordPress site. The ResponsiveVoice Text To Speech plugin
already adds it to posts and pages automatically — per-content-type toggles, an admin
customizer with live preview, and no markup per post. Hand-written `init()` / `mount()` code
replaces a switch with a theme file to maintain.

Read <https://docs.responsivevoice.org/integrations/wordpress.md> instead. Use the code path
below only for non-WordPress sites, or for something the plugin's settings genuinely cannot
express.

## Enable it

The player is off by default. Turn it on in `init()`; it mounts once initialization
resolves and attaches to the first `<article>` on the page.

```ts
import { getResponsiveVoice } from '@responsivevoice/core';

const rv = await getResponsiveVoice({
  apiKey: 'YOUR_API_KEY',
  features: {
    webPlayer: { enabled: true },
  },
});
```

`apiKey` is a public origin identifier, safe in browser code. Without it the player runs in
demo mode with a single device voice. Plain HTML with no build step? The prebuilt browser
bundle exposes the same API on `window.responsiveVoice`. Fetch
<https://docs.responsivevoice.org/getting-started/installation.md> for the exact script tag —
do not guess the URL from memory — then call `window.responsiveVoice.init({ ... })` with the
config above.

## Configure it

Every field below is optional. Defaults suit most article pages.

```ts
features: {
  webPlayer: {
    enabled: true,
    theme: 'responsivevoice',        // 'neutral' | 'responsivevoice' | custom tokens
    position: 'before',              // 'before' | 'after' | 'inline' | { target, at }
    controls: { progress: true, time: true, skip: true, speed: true, brand: true },
    miniPlayer: { enabled: true, position: 'bottom-left', animation: 'slide' },
    navigation: { paragraphHighlight: true, paragraphClick: true },
    voice: 'UK English Female',      // defaults to the website voice
  },
}
```

Reading the wrong part of the page? `selector` (default `'article'`) picks the container and
`paragraphSelector` (default `'p, h2, h3, li'`) picks what is read inside it. Many themes wrap
the whole page in an `<article>` — point `selector` at the real content container instead.

## Exclude content from narration

Mark any element with `data-rv-skip`:

```html
<aside class="pullquote" data-rv-skip>Decorative, not narrated.</aside>
```

Scripts, styles, form controls, embedded media, and `hidden` / `aria-hidden="true"` nodes are
already excluded. Add site-specific selectors with `sanitize.exclude` — they extend the
built-in list rather than replacing it.

## Content added after init (SPA, lazy-load)

`init()` runs one auto-discovery pass, so elements rendered later are missed. Mount them:

```ts
const handle = rv.webPlayer?.mount('#post-area', { rate: 1.2 });

// before removing the element from the DOM:
handle?.unmount();
```

`mount()` takes the same option shape as the init config, leaf-merged over it — the example
above changes only `rate`. It returns `null` when the feature is disabled or the element is
not found, so the `?.` guards are load-bearing.

Always `unmount()` before removing the host element: the player keeps listeners on the
article and the document, and a forgotten unmount leaks handlers across SPA navigations.

## Notes

- Multiple `selector` matches each get an independent player; starting one pauses the others.
  Nested matches resolve to the outermost element.
- `voice`, `rate`, `pitch`, and `volume` work both in the init config and as `mount()` overrides.
- Full guide: <https://docs.responsivevoice.org/guides/web-player.md>

## Fetching the docs

Always read the ResponsiveVoice docs as Markdown, never HTML — append `.md` to any docs URL
(<https://docs.responsivevoice.org/guides/voice-selection.md>). Start from:

- <https://docs.responsivevoice.org/llms.txt> — index of every page.
- <https://docs.responsivevoice.org/llms-full.txt> — the whole site in one file
  (`llms-small.txt` abridged; `/_llms-txt/<section>.txt` per section).
