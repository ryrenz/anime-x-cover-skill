# anime-x-cover-skill

**中文** | [English](README.en.md)

[Claude Code](https://docs.claude.com/en/docs/claude-code) / Codex skill，把一篇文章变成可直接粘进 ChatGPT (gpt-image-2) 的封面 prompt —— 或者在支持的宿主里直接出图。

**两个封面统一模板（family）**，每个 family 下挂自己的风格变体：

- **`anime-pop`** —— 日式动漫流行视觉系：EVA / Persona 5 / Akira
- **`mega-chinese-type`** —— 巨型中文透视标题视觉系：电竞赛事主视觉 / 电影海报 / 编辑式运动海报 / 时装大片（第一版只挂 `esports-key-visual`，后续可扩展）

专为 X/Twitter 封面与中文长图文设计。

```
You: /anime-cover-prompt path/to/article.md
Skill: 📌 fields: @yourhandle / Claude Code Vol.3 / 2026.05.09
       → 自动选 family（anime-pop / mega-chinese-type）+ 风格变体
       → decisions JSON + 英文 gpt-image-2 prompt
       → (Codex) 封面图自动写到 ~/Downloads/

# 强制指定 family + 风格
You: /anime-cover-prompt path/to/article.md --family mega-chinese-type --style esports-key-visual
```

## 它做什么

- 读文章 → **选 family**（anime-pop / mega-chinese-type，或 `auto` 按文章 mood 路由）→ **再选 style** → 写一段为 `gpt-image-2` 调校过的英文 image prompt
- 最终图里的标题文字 **100% 等于原文标题**，不改写，不翻译
- 强制把四个角字段 —— `handle` / `vol` / `no` / `date` —— 放进构图
- 在 Codex 这种带 image generation 工具的宿主里，直接出图并复制一份到 `~/Downloads/<article>-cover.png`
- 在 Claude Code / 没有 image 工具的宿主里，输出一段可直接粘到 ChatGPT 的 prompt

**不是通用 image prompt 生成器** —— 这是一个非常窄、非常有偏见的封面生成器：两个统一模板、固定的默认比例（X 用 5:2）、固定的字段摆放规则。

## Family × Style

### Family `anime-pop` —— 日式动漫流行视觉系

| Style | Vibe | Reference 文件 |
|-------|------|----------------|
| `eva` | 新世纪福音战士 —— terminal HUD，红/橙/紫/绿/蓝五个 palette mode | `references/styles/anime-pop/eva.md` |
| `p5` | 女神异闻录 5 —— 高对比红/黑/白，网点，斜切构图 | `references/styles/anime-pop/p5.md` |
| `akira` | 阿基拉 —— 黑底霓虹，汉字点缀，运动模糊，都市颓废 | `references/styles/anime-pop/akira.md` |

### Family `mega-chinese-type` —— 巨型中文透视标题视觉系

| Style | Vibe | Reference 文件 |
|-------|------|----------------|
| `esports-key-visual` | 国际级电竞赛事主视觉 —— 巨型中文透视字 + 红黑 / 紫绿 / 青红 / 霓虹四个 palette mode + 斜切 / 断裂 / 隧道 / 仰视 / 环绕五种透视 | `references/styles/mega-chinese-type/esports-key-visual.md` |
| `movie-poster`（待加） | 电影海报感 —— 投影长阴影，戏剧化光源 | — |
| `editorial-sports`（待加） | 运动品牌编辑式海报 —— 地面赛道字，速度感 | — |
| `fashion-campaign`（待加） | 时装走秀大片 —— 墙面透视字，环绕包围结构 | — |

每个 family 文件和 style 文件**按需加载** —— 一次调用只读所选那一对（一个 family + 一个 style），不混搭。

## 安装

这是个 Claude Code / Codex skill，把仓库克隆到 skills 目录就能用：

```bash
# Claude Code
git clone https://github.com/ryrenz/anime-x-cover-skill ~/.claude/skills/anime-cover-prompt

# 或者从任意位置软链
ln -s "$(pwd)/anime-x-cover-skill" ~/.claude/skills/anime-cover-prompt
```

skill 通过 `SKILL.md` 里 `name:` 字段自动注册成 `/anime-cover-prompt` 命令。

## 用法

```bash
/anime-cover-prompt path/to/article.md
/anime-cover-prompt path/to/article.md --family anime-pop --style eva --aspect 5:2
/anime-cover-prompt path/to/article.md --family mega-chinese-type --style esports-key-visual
/anime-cover-prompt path/to/article.md --date 2026.05.03 --vol "Claude Code Vol.3"
/anime-cover-prompt path/to/article.md --reset      # 清空持久化 config,重新询问
/anime-cover-prompt                                  # 不带路径 → 让你直接粘贴文章
```

参数：

- `--family` `anime-pop` | `mega-chinese-type`（默认 `auto`，按文章 mood 路由）
- `--style` 必须是所选 family 下挂的 style 名（默认 `auto`，按 family 内路由表）
- `--aspect` `5:2`（X 封面，默认）| `2.35:1`（公众号横幅）| `3:2` | `4:5` | `1:1` | `16:9` | `9:16`
- `--lang` `zh`（默认）| `en` | `mix`
- `--handle` / `--vol` / `--no` / `--date` —— 本次调用的一次性覆盖

## Family 路由（auto 模式）

| 文章 mood 关键词 | 推荐 family |
|---|---|
| 冷峻 / 秩序 / 精密 / 技术 / 调试 / 反叛 / 觉醒 / 末日 / 赛博 / 都市 | `anime-pop` |
| 热血 / 速度 / 竞技 / 对抗 / 上线 / 突破 / 电影感叙事 / 商业 / 广告大片 | `mega-chinese-type` |

如果同时命中两个 family：

- 文章核心是**内部系统、技术过程、个人心理** → `anime-pop`
- 文章核心是**对抗结果、传播爆点、商业宣告、对比冲击** → `mega-chinese-type`

## 配置

持久化字段写在 `~/.config/anime-cover-prompt/config.yaml`：

- `handle` —— 你的 @ 账号，比如 `@yourhandle`
- `date_default` —— `today` 或一个固定日期（如 `2026.05.09`）

每篇文章独立字段（`vol`、`no`、`date`）**绝不跨文章沿用** —— 每次都重新问或由命令行传入。

## 输出

三种模式，按宿主能力**从上到下选第一个能跑的**：

1. **宿主有内置 image generation 工具（Codex 等）** —— 直接出图，落盘到 `~/Downloads/<basename>-cover.png`。
2. **Claude Code 等无 image 工具，但本机装了 [`codex` CLI](https://github.com/openai/codex)** —— 自动 shell 调 `codex exec` 转手出图，同样落盘到 `~/Downloads/`，用户无感知中间一步。
3. **既无 image 工具，也没装 codex CLI（或用户明示只要 prompt）** —— 输出 decisions JSON + 一段可粘到 ChatGPT (gpt-image-2) 的英文 prompt。

> 💡 想让 Claude Code 也能直接出图：`brew install codex`（或参考 codex CLI 安装文档），然后 `codex login` 跑一次，skill 会自动检测并启用模式 2。

## 贡献

新增风格变体非常容易：

- **在已有 family 下加一个 style**：在 `references/styles/<family>/` 加一个文件，并在 `SKILL.md` 的 Style Routing 表里 + family 文件的 Style References 段加一条路由
- **新加一个完整的 family**：在 `references/families/` 加一个 family 文件，在 `references/styles/<new-family>/` 下挂至少一个 style，并在 `SKILL.md` 的 Family Routing 表里加一条路由

每对（family + style）相互隔离 —— 加新模板不会影响已有模板。

欢迎 PR。

## License

MIT —— 见 [LICENSE](LICENSE)。
