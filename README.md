# anime-x-cover.skill

**中文** | [English](README.en.md)

[Claude Code](https://docs.claude.com/en/docs/claude-code) / Codex skill,把一篇文章变成可直接粘进 ChatGPT (gpt-image-2) 的封面 prompt —— 或者在支持的宿主里直接出图 —— 风格统一锁定在 **EVA / Persona 5 / Akira** 三家。专为 X/Twitter 封面与中文长图文设计。

```
You: /anime-cover-prompt path/to/article.md
Skill: 📌 fields: @yourhandle / Claude Code Vol.3 / 2026.05.09
       → decisions JSON + 英文 gpt-image-2 prompt
       → (Codex) 封面图自动写到文章同目录
```

## 它做什么

- 读文章 → 选风格(EVA / P5 / Akira,或 `auto` 按文章情绪路由) → 写一段为 `gpt-image-2` 调校过的英文 image prompt。
- 最终图里的标题文字**100% 等于原文标题**,不改写,不翻译。
- 强制把四个角字段 —— `handle` / `vol` / `no` / `date` —— 放进构图。
- 在 Codex 这种带 image generation 工具的宿主里,直接出图并复制一份到文章同目录,文件名 `<article>-cover.png`。
- 在 Claude Code / 没有 image 工具的宿主里,输出一段可直接粘到 ChatGPT 的 prompt。

**不是通用 image prompt 生成器** —— 这是一个非常窄、非常有偏见的封面生成器:三种风格,固定的默认比例(X 用 5:2),固定的字段摆放规则。

## 风格

| Style | Vibe | Reference 文件 |
|-------|------|----------------|
| `eva` | 新世纪福音战士 —— terminal HUD,NERV 风格调色板(红/橙/紫/绿/蓝五个 mode) | `references/styles/eva.md` |
| `p5` | 女神异闻录 5 —— 高对比红/黑/白,网点,斜切构图 | `references/styles/p5.md` |
| `akira` | 阿基拉 —— 黑底霓虹,汉字点缀,运动模糊,都市颓废 | `references/styles/akira.md` |

风格文件**按需加载** —— 一次调用只读所选的那一份。

## 安装

这是个 Claude Code / Codex skill,把仓库克隆到 skills 目录就能用:

```bash
# Claude Code
git clone https://github.com/ryrenz/anime-x-cover.skill ~/.claude/skills/anime-cover-prompt

# 或者从任意位置软链
ln -s "$(pwd)/anime-x-cover.skill" ~/.claude/skills/anime-cover-prompt
```

skill 通过 `SKILL.md` 里 `name:` 字段自动注册成 `/anime-cover-prompt` 命令。

## 用法

```bash
/anime-cover-prompt path/to/article.md
/anime-cover-prompt path/to/article.md --style eva --aspect 5:2
/anime-cover-prompt path/to/article.md --date 2026.05.03 --vol "Claude Code Vol.3"
/anime-cover-prompt path/to/article.md --reset      # 清空持久化 config,重新询问
/anime-cover-prompt                                  # 不带路径 → 让你直接粘贴文章
```

参数:

- `--style` `eva` | `p5` | `akira`(默认 `auto`,按文章 mood 路由)
- `--aspect` `5:2`(X 封面,默认) | `2.35:1`(公众号横幅) | `3:2` | `4:5` | `1:1`
- `--lang` `zh`(默认) | `en` | `mix`
- `--handle` / `--vol` / `--no` / `--date` —— 本次调用的一次性覆盖

## 配置

持久化字段写在 `~/.config/anime-cover-prompt/config.yaml`:

- `handle` —— 你的 @ 账号,比如 `@yourhandle`
- `date_default` —— `today` 或一个固定日期(如 `2026.05.09`)

每篇文章独立字段(`vol`、`no`、`date`)**绝不跨文章沿用** —— 每次都重新问或由命令行传入。

## 输出

两种模式,取决于宿主能力:

1. **Codex + image generation 工具** —— 直接出图,再复制到 `<article-dir>/<article-basename>-cover.png`。只有文件真实落盘才算完成。
2. **Claude Code / 没有 image 工具** —— 输出 decisions JSON + 一段可粘到 ChatGPT (gpt-image-2) 的英文 prompt。

## 贡献

三个风格 reference 互相隔离 —— 新增第四种风格只需要在 `references/styles/` 下加一个新文件,并在 `SKILL.md` 的 Style Routing 段加一条路由规则。

欢迎 PR。

## License

MIT —— 见 [LICENSE](LICENSE)。
