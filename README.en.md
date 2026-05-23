# anime-x-cover-skill

[中文](README.md) | **English**

A [Claude Code](https://docs.claude.com/en/docs/claude-code) skill (also works in Codex) that turns an article into a paste-and-go cover-image prompt — or directly generates the cover.

**Two cover families**, each with its own style variants:

- **`anime-pop`** — Japanese anime pop visual: EVA / Persona 5 / Akira
- **`mega-chinese-type`** — Giant Chinese perspective typography visual: esports key visual / movie poster / editorial sports / fashion campaign (v1 ships with `esports-key-visual`; the others are placeholders for future styles)

Designed for X/Twitter covers and Chinese long-form publishing.

```
You: /anime-cover-prompt path/to/article.md
Skill: 📌 fields: @yourhandle / Claude Code Vol.3 / 2026.05.09
       → auto-picks a family (anime-pop / mega-chinese-type) + style variant
       → decisions JSON + English gpt-image-2 prompt
       → (Codex) cover image written to ~/Downloads/

# Force a family + style
You: /anime-cover-prompt path/to/article.md --family mega-chinese-type --style esports-key-visual
```

## What it does

- Reads an article → **picks a family** (anime-pop / mega-chinese-type, or `auto` based on mood) → **picks a style** within that family → writes an English image prompt tuned for `gpt-image-2`
- Title text in the final image is **100% identical** to the original article title (no paraphrasing, no translation)
- Forces the four angular fields — `handle` / `vol` / `no` / `date` — to appear in the composition
- On Codex with a built-in image-generation tool, it generates the image directly and copies it to `~/Downloads/<article>-cover.png`
- On Claude Code / hosts without image tools, it emits the prompt for you to paste into ChatGPT

**Not a general image-prompt generator** — this is a narrow, opinionated cover generator. Two cover families, fixed aspect-ratio defaults (5:2 for X), fixed field layout.

## Family × Style

### Family `anime-pop` — Japanese anime pop visual

| Style | Vibe | Reference file |
|-------|------|----------------|
| `eva` | Neon Genesis Evangelion — terminal HUD, red/orange/purple/green/blue palette modes | `references/styles/anime-pop/eva.md` |
| `p5` | Persona 5 — high-contrast red/black/white, halftone, slashing diagonals | `references/styles/anime-pop/p5.md` |
| `akira` | Akira — neon-on-black, kanji accents, motion blur, urban-decay grit | `references/styles/anime-pop/akira.md` |

### Family `mega-chinese-type` — Giant Chinese perspective typography visual

| Style | Vibe | Reference file |
|-------|------|----------------|
| `esports-key-visual` | International-tier esports finals key visual — giant Chinese perspective title + red/black, purple/green, ice/red, neon palette modes + slashing / shattered / tunnel / hero-up / surround perspectives | `references/styles/mega-chinese-type/esports-key-visual.md` |
| `movie-poster` (planned) | Cinematic poster — projection shadows, dramatic lighting | — |
| `editorial-sports` (planned) | Editorial sports campaign — ground-track typography, speed lines | — |
| `fashion-campaign` (planned) | Fashion runway campaign — wall-perspective typography, surround composition | — |

Each family file and style file is loaded **on demand** — only the chosen pair (one family + one style) is read per invocation; no cross-family mixing.

## Install

```bash
# Claude Code
git clone https://github.com/ryrenz/anime-x-cover-skill ~/.claude/skills/anime-cover-prompt

# or symlink from anywhere
ln -s "$(pwd)/anime-x-cover-skill" ~/.claude/skills/anime-cover-prompt
```

The skill registers itself as `/anime-cover-prompt` via the `name:` frontmatter in `SKILL.md`.

## Usage

```bash
/anime-cover-prompt path/to/article.md
/anime-cover-prompt path/to/article.md --family anime-pop --style eva --aspect 5:2
/anime-cover-prompt path/to/article.md --family mega-chinese-type --style esports-key-visual
/anime-cover-prompt path/to/article.md --date 2026.05.03 --vol "Claude Code Vol.3"
/anime-cover-prompt path/to/article.md --reset      # clear persisted config, re-prompt
/anime-cover-prompt                                  # no path → paste the article inline
```

Flags:

- `--family` `anime-pop` | `mega-chinese-type` (default `auto`, routed by article mood)
- `--style` must be a style defined under the chosen family (default `auto`, routed by family-internal table)
- `--aspect` `5:2` (X cover, default) | `2.35:1` (WeChat banner) | `3:2` | `4:5` | `1:1` | `16:9` | `9:16`
- `--lang` `zh` (default) | `en` | `mix`
- `--handle` / `--vol` / `--no` / `--date` — one-shot overrides for this call

## Family routing (auto mode)

| Article mood | Recommended family |
|---|---|
| Cold / order / precision / technical / debugging / rebellion / awakening / dystopia / cyber / urban | `anime-pop` |
| Heat / speed / competition / confrontation / launch / breakthrough / cinematic narrative / commercial / campaign | `mega-chinese-type` |

When both families' keywords hit:

- Article is about **internal systems, technical process, personal psychology** → `anime-pop`
- Article is about **outcome of confrontation, viral moment, commercial announcement, contrastive impact** → `mega-chinese-type`

## Config

Persistent fields live in `~/.config/anime-cover-prompt/config.yaml`:

- `handle` — your @ handle, e.g. `@yourhandle`
- `date_default` — `today` or a fixed date like `2026.05.09`

Per-article fields (`vol`, `no`, `date`) are **never** carried across articles — the skill asks each time or accepts CLI overrides.

## Output

Three modes, picked top-down based on host capability:

1. **Host has built-in image generation (Codex, etc.)** — generates the image directly, saves it to `~/Downloads/<basename>-cover.png`.
2. **Claude Code or any host without image tools, but [`codex` CLI](https://github.com/openai/codex) installed locally** — auto-shells out to `codex exec` to delegate the generation; image lands in `~/Downloads/` the same way, no manual paste step.
3. **Neither host image tool nor codex CLI (or user explicitly wants prompt only)** — emits a decisions JSON + a paste-ready English prompt for ChatGPT (gpt-image-2).

> 💡 Want Claude Code to also generate images directly? Install codex CLI (`brew install codex` or see the codex docs), run `codex login` once, and the skill auto-detects and enables mode 2.

## Contributing

Adding a new style variant is intentionally cheap:

- **Add a style under an existing family**: drop a file under `references/styles/<family>/`, then add an entry in `SKILL.md`'s Style Routing table and the family file's Style References section
- **Add a whole new family**: drop a file under `references/families/`, ship at least one style under `references/styles/<new-family>/`, and add an entry in `SKILL.md`'s Family Routing table

Each (family + style) pair is isolated — new templates don't touch existing ones.

PRs welcome.

## License

MIT — see [LICENSE](LICENSE).
