# anime-x-cover.skill

[中文](README.md) | **English**

A [Claude Code](https://docs.claude.com/en/docs/claude-code) skill (also works in Codex) that turns an article into a paste-and-go cover-image prompt — or directly generates the cover — in **EVA / Persona 5 / Akira** aesthetics. Designed for X/Twitter covers and Chinese long-form publishing.

```
You: /anime-cover-prompt path/to/article.md
Skill: 📌 fields: @yourhandle / Claude Code Vol.3 / 2026.05.09
       → decisions JSON + English gpt-image-2 prompt
       → (Codex) cover image written next to the article
```

## What it does

- Reads an article → picks a style (EVA / P5 / Akira, or `auto` based on mood) → writes an English image prompt tuned for `gpt-image-2`.
- Title text in the final image is **100% identical** to the original article title (no paraphrasing, no translation).
- Forces the four angular fields — `handle` / `vol` / `no` / `date` — to appear in the composition.
- On Codex with a built-in image-generation tool, it generates the image directly and copies it next to the article as `<article>-cover.png`.
- On Claude Code / hosts without image tools, it emits the prompt for you to paste into ChatGPT.

**Not a general image-prompt generator** — this is a narrow, opinionated cover generator for anime-flavored article covers. Three styles, fixed aspect-ratio defaults (5:2 for X), fixed field layout.

## Styles

| Style | Vibe | Reference file |
|-------|------|----------------|
| `eva` | Neon Genesis Evangelion — terminal HUD, NERV-style color palettes (red/orange/purple/green/blue modes) | `references/styles/eva.md` |
| `p5` | Persona 5 — high-contrast red/black/white, halftone, slashing diagonals | `references/styles/p5.md` |
| `akira` | Akira — neon-on-black, kanji accents, motion blur, urban-decay grit | `references/styles/akira.md` |

Each style file is loaded on demand — only the chosen style is read per invocation.

## Install

This is a Claude Code / Codex skill. Clone it into your skills directory:

```bash
# Claude Code
git clone https://github.com/<your-org>/anime-x-cover.skill ~/.claude/skills/anime-cover-prompt

# or symlink from anywhere
ln -s "$(pwd)/anime-x-cover.skill" ~/.claude/skills/anime-cover-prompt
```

The skill registers itself as `/anime-cover-prompt` via the `name:` frontmatter in `SKILL.md`.

## Usage

```bash
/anime-cover-prompt path/to/article.md
/anime-cover-prompt path/to/article.md --style eva --aspect 5:2
/anime-cover-prompt path/to/article.md --date 2026.05.03 --vol "Claude Code Vol.3"
/anime-cover-prompt path/to/article.md --reset      # clear persisted config, re-prompt
/anime-cover-prompt                                  # no path → paste the article inline
```

Flags:

- `--style` `eva` | `p5` | `akira` (default `auto`, routed by article mood)
- `--aspect` `5:2` (X cover, default) | `2.35:1` (WeChat banner) | `3:2` | `4:5` | `1:1`
- `--lang` `zh` (default) | `en` | `mix`
- `--handle` / `--vol` / `--no` / `--date` — one-shot overrides for this call

## Config

Persistent fields live in `~/.config/anime-cover-prompt/config.yaml`:

- `handle` — your @ handle, e.g. `@yourhandle`
- `date_default` — `today` or a fixed date like `2026.05.09`

Per-article fields (`vol`, `no`, `date`) are **never** carried across articles — the skill asks each time or accepts CLI overrides.

## Output

Two modes depending on host capability:

1. **Codex with image generation** — generates the image, then copies it to `<article-dir>/<article-basename>-cover.png`. Completion requires the file to actually exist on disk.
2. **Claude Code / no image tool** — emits a decisions JSON + a paste-ready English prompt for ChatGPT (gpt-image-2).

## Contributing

The three style references are isolated — adding a fourth style is a single new file under `references/styles/` plus a routing entry in `SKILL.md`'s Style Routing section.

PRs welcome.

## License

MIT — see [LICENSE](LICENSE).
