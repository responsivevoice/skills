# Web player configuration

Every option lives under `features.webPlayer` in the `init()` config, and the same shape is
accepted by `webPlayer.mount(selectorOrElement, overrides)` — leaf-merged over the init config.

## Top level

| Parameter           | Type                                                       | Default                |
| ------------------- | ---------------------------------------------------------- | ---------------------- |
| `enabled`           | `boolean`                                                  | `false`                |
| `selector`          | `string` (CSS)                                             | `'article'`            |
| `paragraphSelector` | `string` (CSS)                                             | `'p, h2, h3, li'`      |
| `position`          | `'before' \| 'after' \| 'inline'` \| `{ target, at }`      | `'before'`             |
| `theme`             | `'neutral' \| 'responsivevoice'` \| theme tokens           | `'neutral'`            |
| `controls`          | object — see [Controls](#controls)                         | all `true`             |
| `navigation`        | object — see [Navigation](#navigation)                     | all `true`             |
| `layout`            | object — see [Layout](#layout)                             | `shrink` + `block`     |
| `miniPlayer`        | `boolean` \| object — see [Mini-player](#mini-player)      | enabled, `bottom-left` |
| `sanitize`          | object — see [Sanitize](#sanitize)                         | enabled, no extras     |
| `voice`             | `VoiceSelector` — name `string`, `RegExp`, or query object | website default        |
| `pitch`             | `number` `0`–`2`                                           | `1`                    |
| `rate`              | `number` `0`–`2`                                           | `1`                    |
| `volume`            | `number` `0`–`1`                                           | `1`                    |

`selector` matching several elements gives each its own independent player; starting one
pauses the others. Nested matches resolve to the outermost element.

## position

Keyword form is relative to `selector`. Object form mounts against any other element.

| Parameter | Type                              | Default    |
| --------- | --------------------------------- | ---------- |
| `target`  | `string` (CSS) — **required**     | —          |
| `at`      | `'inside' \| 'before' \| 'after'` | `'inside'` |

```ts
position: { target: '#article-player', at: 'inside' }
```

## theme

Preset name (`'neutral'` or `'responsivevoice'`), or a token object. Every token is an
optional CSS color string; omitted tokens fall back to the neutral defaults.

| Token        | Applies to                                                        |
| ------------ | ----------------------------------------------------------------- |
| `bg`         | Mini-player background and the play button icon colour.           |
| `fg`         | Foreground text — speed label and skip-back / skip-forward icons. |
| `muted`      | Secondary text — time label and mini-player status text.          |
| `accent`     | Reserved for custom themes; not used by built-in styles.          |
| `accentSoft` | Background of the currently-playing paragraph highlight.          |
| `hover`      | Paragraph hover background.                                       |
| `border`     | Mini-player border colour.                                        |
| `track`      | Main player background and the mini-player progress ring track.   |
| `fill`       | Play button face, progress bar fill, and mini-player ring fill.   |

## controls

The play / pause button is always shown. `skip` toggles both skip buttons together.

| Parameter  | Type      | Default | Shows                                              |
| ---------- | --------- | ------- | -------------------------------------------------- |
| `progress` | `boolean` | `true`  | Progress bar through the narrated article.         |
| `time`     | `boolean` | `true`  | Elapsed / total label (e.g. `0:42 / 3:15`).        |
| `skip`     | `boolean` | `true`  | Skip-back and skip-forward (one paragraph a time). |
| `speed`    | `boolean` | `true`  | Speed button — cycles 0.5× through 3×.             |
| `brand`    | `boolean` | `true`  | ResponsiveVoice brand icon.                        |

## navigation

Independent of each other: highlight without click is "follow along, no skipping ahead".

| Parameter            | Type      | Default | Does                                                    |
| -------------------- | --------- | ------- | ------------------------------------------------------- |
| `paragraphHighlight` | `boolean` | `true`  | Visual "currently playing" indicator on each paragraph. |
| `paragraphClick`     | `boolean` | `true`  | Click any paragraph to start narration there.           |

## layout

| Parameter | Type                  | Default    | Does                                                      |
| --------- | --------------------- | ---------- | --------------------------------------------------------- |
| `mode`    | `'shrink' \| 'fill'`  | `'shrink'` | `shrink` sizes to content; `fill` stretches to container. |
| `display` | `'inline' \| 'block'` | `'block'`  | `block` takes its own line; `inline` flows with text.     |

## mini-player

`miniPlayer: true` is shorthand for `{ enabled: true }`.

| Parameter   | Type                                   | Default         |
| ----------- | -------------------------------------- | --------------- |
| `enabled`   | `boolean`                              | `true`          |
| `position`  | corner keyword \| offset object        | `'bottom-left'` |
| `animation` | `'none' \| 'fade' \| 'slide' \| 'pop'` | `'slide'`       |

Corners: `'top-left'`, `'top-right'`, `'bottom-left'`, `'bottom-right'`.

Offset object takes CSS length strings (`'80px'`, `'2rem'`, `'5%'`) on `top`, `right`,
`bottom`, `left`. **At least one side is required. `top` with `bottom` is rejected, and `left`
with `right` is rejected** — pick one side per axis.

```ts
miniPlayer: { enabled: true, position: { bottom: '2rem', right: '80px' }, animation: 'pop' }
```

`animation` is forced instant under `prefers-reduced-motion: reduce`.

## sanitize

| Parameter | Type       | Default | Does                                                          |
| --------- | ---------- | ------- | ------------------------------------------------------------- |
| `enabled` | `boolean`  | `true`  | Strips non-narratable nodes from narrated text.               |
| `exclude` | `string[]` | `[]`    | Extra CSS selectors to skip, composed with the built-in list. |

When enabled, scripts, styles, form controls, embedded media, and `hidden` /
`aria-hidden="true"` nodes are excluded; a paragraph left empty after sanitization is dropped
from the reading flow. `exclude` adds to the built-in list rather than replacing it. Any
element marked `data-rv-skip` is skipped regardless.

Keep it on for normal sites — `enabled: false` narrates raw matched text and is mainly a
debugging aid for unusual markup.

## Placing the player away from the article

Many themes wrap the whole page in an `<article>`, so the default `selector` puts the player
above the page title and narrates navigation or calls to action. Use a real content container
plus a dedicated player slot:

```html
<div id="article-player"></div>
<div id="article-preview">
  <h2>A better way to experience the web</h2>
  <p>Visitors can listen while they commute or rest their eyes.</p>
</div>
```

```ts
rv.webPlayer?.mount('#article-preview', {
  theme: 'responsivevoice',
  paragraphSelector: 'h2, p',
  position: { target: '#article-player', at: 'inside' },
});
```

## Troubleshooting

| Symptom                   | Cause                                                                             |
| ------------------------- | --------------------------------------------------------------------------------- |
| Player doesn't appear     | `init()` didn't resolve, `enabled` isn't `true`, or `selector` matched nothing.   |
| `mount()` returned `null` | Feature disabled, or the target element wasn't in the DOM yet.                    |
| Wrong content read / gaps | `paragraphSelector` mismatch, or sanitize dropped the nodes (empty ones are cut). |
| Mini-player never shows   | `miniPlayer.enabled` is `false`, or the main player never scrolled out of view.   |
| Handlers leak across SPAs | `handle.unmount()` wasn't called before removing the host element.                |

Content rendered after `init()` is missed by the one-time auto-discovery pass — mount it
explicitly rather than re-initializing.
