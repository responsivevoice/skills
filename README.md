<p align="center">
  <img src="https://cdn.responsivevoice.org/assets/logo-250.svg" width="180" height="180" alt="ResponsiveVoice">
</p>

<h1 align="center">ResponsiveVoice Agent Skills</h1>

<p align="center">
  Installable <a href="https://www.skills.sh/docs/faq">Agent Skills</a> that teach AI agents how to install and run ResponsiveVoice text-to-speech.
</p>

---

## Install

```bash
npx skills add responsivevoice/skills
```

Installs into any agent that reads filesystem skills — Claude Code, Cursor, GitHub Copilot,
Gemini CLI, Cline, Windsurf, Codex, Zed, and more. The skill folder also works if copied
directly into `~/.claude/skills/` or `.claude/skills/`.

Install one skill instead of all of them with `--skill` (repeatable), or preview the choices
with `--list`:

```bash
npx skills add responsivevoice/skills --skill responsivevoice-php   # one skill
npx skills add responsivevoice/skills --skill '*'                   # all of them
npx skills add responsivevoice/skills --list                        # preview, pick interactively
```

## What's inside

| Skill                                                                              | Teaches                                                            |
| ---------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| [`responsivevoice-text-to-speech`](skills/responsivevoice-text-to-speech/SKILL.md) | Install + run TTS via the browser library, REST API, or the SDKs   |
| [`responsivevoice-php`](skills/responsivevoice-php/SKILL.md)                       | Call TTS from PHP with the Composer SDK — framework-free + Laravel |
| [`responsivevoice-web-player`](skills/responsivevoice-web-player/SKILL.md)         | Add and configure the on-page article web player                   |

`responsivevoice-text-to-speech` covers the browser library (`@responsivevoice/core`), the
Node REST client (`@responsivevoice/api-client`), and the REST API — plus the demo /
API-key / API-secret authentication model. It routes PHP tasks to `responsivevoice-php` and
points at the REST API for languages whose native SDKs are not published yet.

`responsivevoice-php` covers the official Composer SDK (`responsivevoice/sdk`) — installing
it, synthesizing to an MP3, listing voices, and wiring it into Laravel.

`responsivevoice-web-player` covers the drop-in article reader — theme, layout, controls,
mini-player, paragraph highlighting, narration exclusions, and imperative `mount()`.

Skills install independently (`--skill <name>`), so each one stands alone rather than
depending on another.

## Conventions

Every skill here ends with the same verbatim `## Fetching the docs` block, pointing agents at
the Markdown docs (`.md` twin, `llms.txt`, `llms-full.txt`) instead of HTML. Carry it into any
new skill unchanged.

## Links

- Documentation: <https://docs.responsivevoice.org>
- API spec: <https://texttospeech.responsivevoice.org/openapi.json>
- Support: [support@responsivevoice.org](mailto:support@responsivevoice.org)

## License

MIT — see [LICENSE](LICENSE).
