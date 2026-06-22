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

## What's inside

| Skill                                                | Teaches                                                          |
| ---------------------------------------------------- | ---------------------------------------------------------------- |
| [`responsivevoice`](skills/responsivevoice/SKILL.md) | Install + run TTS via the browser library, REST API, or the SDKs |

The skill covers the browser library (`@responsivevoice/core`), the Node REST client
(`@responsivevoice/api-client`), the REST API, and the Python/Go/PHP/Java SDKs — plus the
demo / API-key / API-secret authentication model.

## Links

- Documentation: <https://docs.responsivevoice.org>
- API spec: <https://texttospeech.responsivevoice.org/openapi.json>
- Support: [support@responsivevoice.org](mailto:support@responsivevoice.org)

## License

MIT — see [LICENSE](LICENSE).
