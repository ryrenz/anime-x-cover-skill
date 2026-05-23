---
name: anime-cover-prompt
description: Read an article and produce a paste-and-go gpt-image-2 prompt. Two cover families — anime-pop (EVA / Persona 5 / Akira) and mega-chinese-type (giant Chinese perspective typography, esports key visual, editorial sports, movie poster). Outputs decision JSON + English image prompt. Use when user wants to generate an article cover prompt with anime / cyberpunk / mecha-terminal / red-black-rebel style, or with giant-Chinese-typography / esports / sports-poster / cinematic-cover style.
version: 0.4.0
---

# Anime / Cover Prompt

文章 → 生成封面图或直接可粘贴进 ChatGPT (gpt-image-2) 的封面 prompt。

**两个封面统一模板（family）**：

| Family | 气质 | Styles |
|---|---|---|
| `anime-pop` | 日式动漫流行视觉：机甲控制室、青年反叛、手绘赛璐璐都市 | `eva` / `p5` / `akira` |
| `mega-chinese-type` | 巨型中文透视标题 + 高冲突撞色 + 电影级构图 + 国际级广告海报 | `esports-key-visual` (后续可扩展 movie-poster / editorial-sports / fashion-campaign) |

每个 family 的通用规范在 `references/families/<family>.md`，每个具体 style 的视觉细节在 `references/styles/<family>/<style>.md`，**按需加载**，不在本文件常驻。

---

## Invocation

```
/anime-cover-prompt path/to/article.md
/anime-cover-prompt path/to/article.md --family anime-pop --style eva --aspect 5:2
/anime-cover-prompt path/to/article.md --family mega-chinese-type --style esports-key-visual
/anime-cover-prompt path/to/article.md --date 2026.05.03 --vol "Claude Code Vol.3"
/anime-cover-prompt path/to/article.md --reset      # 清空 config,重新问一次
/anime-cover-prompt                                  # 不带参数 → 让用户粘贴文章
```

参数:
- `--family` `anime-pop` | `mega-chinese-type`（默认 auto，根据文章 mood 判断）
- `--style` 必须是所选 family 下挂的 style 名（不指定走 family 内 auto 路由）
- `--aspect` `5:2`（Twitter,默认）| `2.35:1`（公众号）| `3:2` | `4:5` | `1:1` | `16:9` | `9:16`
- `--lang` `zh`（默认）| `en` | `mix`
- `--handle` / `--vol` / `--no` / `--date`：单次覆盖 config 值
- `--reset`：清空 `~/.config/anime-cover-prompt/config.yaml` 并重新询问 handle

---

## Image Output Policy

当本轮直接生成了图片时,**最终交付物必须落到 `~/Downloads/`**。

默认目标路径:

```text
~/Downloads/<article-basename>-cover.png
```

例如:

```text
~/articles/文章标题.md
→ ~/Downloads/文章标题-cover.png
```

如果用户没有提供本地文章路径(直接粘贴文章正文),用文章标题做 slug 当 basename;实在拿不到就用时间戳 `cover-YYYYMMDD-HHMM.png`。

平台规则:

- **Codex**:如果使用内置 `image_gen` 工具,它会先把图片保存到 Codex 默认生成目录。生成后必须定位最新生成的图片,保留默认原图,再复制一份到 `~/Downloads/`。不要把“默认生成目录里有图”当成完成。
- **Claude Code / 其他环境**:如果图片工具支持指定输出路径,优先直接写到 `~/Downloads/`;如果只能写默认目录,同样保留默认原图并复制到 `~/Downloads/`。
- 如果写入 `~/Downloads/` 需要权限批准,必须请求批准并完成复制;只有用户拒绝权限时,才报告“已生成但无法复制到 ~/Downloads”。
- 用户明确要求“只要 prompt / 不要出图”时,才跳过图片生成与复制。

完成判定:本轮承诺出图时,`~/Downloads/<basename>-cover.png` 不存在,本 skill 就没有完成。

## Required Angular Fields

字段分两类:**全局持久化**(stable across all articles)与**每篇必给**(per-article)。

### 全局持久化(从 `~/.config/anime-cover-prompt/config.yaml` 读)

| 字段 | 示例 | 说明 |
|---|---|---|
| `handle` | `@yourhandle` | Twitter / 微信账号,**真稳定** |
| `date_default` | `today` 或 `2026.05.09` | 默认日期策略 |

### 每篇必给(不持久化,**禁止跨文章沿用**)

| 字段 | 示例 | 说明 |
|---|---|---|
| `vol` | `创业 Vol.2` / `Claude Code Vol.2` / `设计 Vol.5` | 系列标识,**每篇可能不同**,必须当次传入或询问 |
| `no` | `No.013` 或 `null` | 期号,可选;null 时 vol 即完整系列 ID |
| `date` | `2026.05.09` | 默认按 `date_default` 解析,可 `--date` 覆盖 |

**例外**:同一 session 内同一文章路径的连续调用,可沿用前一轮 vol(用户在重新生成同一篇),但需在 fields 行明示沿用。

### 加载逻辑(每次调用必须严格执行)

1. 读 config.yaml → 拿到 `handle` 和 `date_default`。`handle` 缺失则问一次并写盘。
2. 解析 `vol`:CLI `--vol` > 同 session 同文章前一轮值 > 询问用户(基于文章主题给建议)。**绝不允许跨不同文章沿用**。
3. 解析 `no`:CLI `--no` > vol 字符串自带 `No.X` > null。
4. 解析 `date`:CLI `--date` > `date_default`(`today` 解析为本地当日)。
5. 在 Decisions 之前明示一行:

   > 📌 fields: `<handle> / <vol[ + No]> / <date>`(--vol/--no/--date 可单次覆盖)

四项**必须出现在最终图中**,prompt 里不允许遗漏。

---

## Workflow

被调用时执行如下步骤,**步骤 1-2 内部完成不输出,步骤 3 输出 fields 一行,步骤 4-11 内部完成不输出过程**:

1. 加载持久字段(读 config.yaml)
2. 读文章
3. 解析 vol/no/date,**输出 fields 一行**
4. **判断 family**(若 `--family` 未指定):见下方 Family Routing
5. **判断 style**(若 `--style` 未指定):在所选 family 内按 Style Routing
6. **加载 family 文件**:`references/families/<family>.md`(**只读所选那一份**)
7. **加载 style 文件**:`references/styles/<family>/<style>.md`(**只读所选那一份**)
8. 应用 family + style 的合并规范,生成 decisions
   - 若 family 为 `anime-pop` 且 style 为 `eva`,必须按 EVA reference 先选择 `palette_mode`,禁止无理由默认橙色
   - 若 family 为 `mega-chinese-type`,必须在 decisions 中写出 `palette_mode` 与 `perspective_mode` 两项
9. 撰写英文 image prompt,opener 用所选 family + style reference 推荐用语,绝不使用作品名 / 联赛名 / 品牌名直字面 cue
10. 校验:title 100% 等于原文标题;角色字段齐全;无原作 IP 文字;标题块面积满足所选 family 的硬约束(anime-pop ≥ 70%;mega-chinese-type 50%–80%)
11. 判断运行环境与可用能力,按下列优先级选第一个命中的分支(用户明确要求"只要 prompt"时直接跳到 11c):
   - **11a. 宿主有内置 image generation 工具(典型:Codex)**:直接调用宿主内置工具出图,不要把 prompt 当最终结果交给用户再手动跑一遍。调用前可短暂说明正在直接生成图片;调用后遵守宿主工具规则,不要再输出 prompt、解释或追问。
   - **11b. 宿主无内置 image generation 工具,但本机 `codex` CLI 可用**(典型:Claude Code 中 `command -v codex` 命中):shell 调 `codex exec` 转手出图,见下方"Codex CLI Bridge"段。**只有 11b 真的失败(codex 退出码非 0、超时、或未生成目标文件)时才降级到 11c**。
   - **11c. 既无宿主 image_gen,也没有可用的 codex CLI / 用户明确要求只要 prompt**:输出 decisions JSON + 英文 image prompt(走"Output Format"段)。
12. 如果走了 11a 或 11b,按 Image Output Policy **复制到 `~/Downloads/<basename>-cover.png`**(只复制到这一个位置,不要顺手 +1 复制到任何其它路径,除非用户在本次调用明示要求多个目标)。完成前必须 `ls` 验证目标 `*-cover.png` 存在。

---

## Codex CLI Bridge(11b 的执行细节)

当宿主无内置 image_gen 但 `command -v codex` 命中时,按以下流程转手:

1. **检测**:`command -v codex` 返回路径即视为可用。可选再跑 `codex --version` 拿版本号。
2. **构造 wrapper 任务**(写入 `/tmp/anime-cover-codex-task.txt` 或类似临时文件):
   ```
   Use your built-in image generation tool to render the cover image described below.
   Save the resulting PNG to exactly ONE path:
     ~/Downloads/<basename>-cover.png
   Do not save to any other location (no Desktop, no extra copies, no article directory)
   unless explicitly listed above. The file must exist on disk before you finish.
   Do not output the prompt back as text; generate the image.
   Do not ask follow-up questions; the prompt below is complete.

   === IMAGE PROMPT START ===
   <步骤 9 撰写出的完整英文 image prompt>
   === IMAGE PROMPT END ===

   After generating, verify with `ls -la '<target path>'` that the PNG exists.
   ```
   `<basename>` 来源:有本地文章路径就用文章 basename,纯粘贴正文就用标题 slug,实在拿不到就 `cover-YYYYMMDD-HHMM`。
3. **执行**:
   ```
   codex exec --sandbox danger-full-access --skip-git-repo-check --ephemeral - < /tmp/anime-cover-codex-task.txt
   ```
   - `--sandbox danger-full-access`:`~/Downloads/` 通常在 cwd 之外,需要广权写盘。
   - `--skip-git-repo-check`:skill 调用可能不在 git 仓库内。
   - `--ephemeral`:不持久化 Codex session 文件。
4. **校验**:codex 退出码非 0、stderr 含明显错误、或 `ls "<target>"` 找不到文件 → 视为 11b 失败,降级到 11c(输出 decisions + 英文 prompt 让用户自己粘)。
5. **不要做的**:
   - 不要把 prompt body 再额外输出给用户(11b 的最终交付物就是 PNG 本身,prompt 是中间过程)
   - 不要顺手把 PNG 也复制到文章同目录 / Desktop 等额外位置 —— 默认输出位置永远只有 `~/Downloads/` 一处
   - 不要在 wrapper 里给 codex 留"多目标兜底"模板,精确写出唯一目标路径

---

## Family Routing

**只是路由,完整 family 规范在 family 文件**。

| 文章 mood 关键词 | family | Reference |
|---|---|---|
| 冷峻 / 秩序 / 精密 / 技术 / 调试 / 反叛 / 觉醒 / 末日 / 赛博 / 都市 | **anime-pop** | [references/families/anime-pop.md](references/families/anime-pop.md) |
| 热血 / 速度 / 竞技 / 对抗 / 上线 / 突破 / 电影感叙事 / 商业 / 广告大片 | **mega-chinese-type** | [references/families/mega-chinese-type.md](references/families/mega-chinese-type.md) |

如果文章同时命中两个 family 的关键词，按"画面气质需要"二选一：

- 文章核心是**内部系统、技术过程、个人心理**——选 `anime-pop`
- 文章核心是**对抗结果、传播爆点、商业宣告、对比冲击**——选 `mega-chinese-type`

family 路由不能跳过：每篇都要在内部判定一次，并且 decisions JSON 顶部必须写出 `family` 与 `family_reason`。

---

## Style Routing(family 内)

### Family `anime-pop`

| 文章 mood 关键词 | style | Reference |
|---|---|---|
| 冷峻 / 秩序 / 精密 / 技术 / 系统 / 调试 | `eva` | [references/styles/anime-pop/eva.md](references/styles/anime-pop/eva.md) |
| 反叛 / 觉醒 / 锐利 / 戳穿 / 反共识 / 心理 | `p5` | [references/styles/anime-pop/p5.md](references/styles/anime-pop/p5.md) |
| 末日 / 危机 / 赛博 / 工业 / 血色 / 都市 | `akira` | [references/styles/anime-pop/akira.md](references/styles/anime-pop/akira.md) |

### Family `mega-chinese-type`

| 文章 mood 关键词 | style | Reference |
|---|---|---|
| 热血 / 速度 / 竞技 / 对抗 / 上线 / 突破 / 数字焦虑 | `esports-key-visual` | [references/styles/mega-chinese-type/esports-key-visual.md](references/styles/mega-chinese-type/esports-key-visual.md) |
| (后续扩展) | `movie-poster` | — |
| (后续扩展) | `editorial-sports` | — |
| (后续扩展) | `fashion-campaign` | — |

每个 style reference 文件含:主色 / 字体 / 气质 / 装饰文字原则 / 图像锚点 / 边框倾向 / 角色字段摆放 / 原作 IP 黑名单 / 英文 prompt opener 推荐用语。

---

## Output Format(严格遵守)

仅在未直接调用 image generation 工具时使用以下输出格式。Codex 中有 image generation 工具时,默认跳过此文本输出并直接出图。

````
## Decisions

```json
{
  "family": "anime-pop | mega-chinese-type",
  "family_reason": "为什么这篇文章适配该 family(1 句)",
  "style": "<family 内的具体 style 名>",
  "style_reason": "为什么这篇文章适配该 style(1 句)",
  "palette_mode": "anime-pop/eva 必填; mega-chinese-type 必填(各 style 自带枚举)",
  "palette_reason": "为什么该文章适配这个 palette mode",
  "perspective_mode": "仅 mega-chinese-type 必填: 地面透视字 / 墙面透视字 / 天花板压迫字 / 斜切冲击字 / 纵深隧道字 / 巨型投影字 / 俯视透视字 / 仰视巨物字 / 环绕包围字 / 断裂爆破字 (十选一)",
  "perspective_reason": "仅 mega-chinese-type 必填: 为什么选这个透视方式",
  "hero": "A 层巨型大字 (anime-pop 2-6 中文字; mega-chinese-type 2-8 中文字)",
  "title": "B 层完整标题 (与原文 100% 一致)",
  "subtitle": "C 层副标题 (可空)",
  "keywords": ["关键词1", "关键词2", "关键词3"],
  "mood": "情绪基调",
  "metaphor_anchor": "具体可视的视觉锚点",
  "metaphor_anchor_evidence": "引用文章中支持该锚点的 1-2 句原文",
  "decoration_text": [
    {
      "text": "装饰文字 1(含位置/旋转/颜色等视觉指示)",
      "source": "A. 文章 §X「原文片段」 / B. reference 中性词模板 / C. 编号-对应关系"
    }
  ],
  "field_placement": {
    "handle": "...",
    "vol_no": "...",
    "date": "..."
  },
  "border": "无 | 蓝图细线框 | 撕裂边 | 红色双线 | 顶底警示条 | ..."
}
```

## Image Prompt (paste into ChatGPT with gpt-image-2)

```
[完整英文 prompt,中文标题/角色字段/装饰汉字保留不翻译]
```
````

---

## Universal Design Spec(跨 family 通用)

> 跨 family 的真正通用规范在这一节。**每个 family 自己的差异化规范**(三层文字细节、视觉面积约束、透视/调色枚举、装饰原则、IP 黑名单)
> 在 `references/families/<family>.md` + `references/styles/<family>/<style>.md`,
> 加载所选那一对,**不要同时加载多个 family / 多个 style**。

### 1. 角色字段(强制)

`handle` / `vol` / `no` / `date` 四项**必须全部出现在画面中**,任一项遗漏即视为错误输出。
**具体摆放位置见所选 style reference 文件**。

### 2. 装饰文字溯源(强制)

除角色字段外的所有 ticker / caution / system 文字,按所选 family + style 的"装饰文字原则"生成,并**为每一条装饰填写 source(来源)**写进 decisions JSON。

每一条 decoration_text 的 source 必须落在以下三类之一：

- **A. 文章导出**:从文章关键词、命题、章节数、术语、引文里提炼
- **B. 中性系统词模板**:family / style reference 中明列的中性词
- **C. 数据/编号/坐标/倒计时**:必须能与文章内容形成对应

**严禁**:

- ❌ "看起来够风格"凭空填的字符串
- ❌ 自己也写不出 source 的装饰
- ❌ 任何 decoration 以"大尺寸 / 中央 / 全屏 / huge / large"出现 —— **巨型字位永远只属于 hero 一个**
- ❌ 数字/编号写得"像那么回事"但其实没对应文章

### 3. 边框(跨 family 通用)

不强制有无;**具体倾向见所选 style reference 文件**。
若加,必须服务于版式秩序,不能装饰化。

### 4. 原作 IP / 商业品牌文字硬约束(跨 family 通用)

风格(色彩 / 笔触 / 构图 / 角色 / 载具 / 场景)可以参考三部作品或现实场景,**文字必须避开**:

- 原作 IP 名 / logo / 角色名 / 专有词(anime-pop family 重点防)
- 现实电竞联赛 / 俱乐部 / 选手脸部肖像 / 运动品牌名(mega-chinese-type family 重点防)
- 通用未授权品牌(Canon / Honda / OpenAI / Anthropic / Adidas / Nike 等)

**英文 prompt 撰写时同样回避作品名 / 品牌名 / 联赛名,改用 family + style reference 推荐的通用描述**。

### 5. 图文结构关系(跨 family 通用)

图像不能独立漂浮,必须和文字发生**结构关系**:

- anime-pop family:嵌入字内 / 从字里长出 / 穿过字形 / 作为字的地面或阴影 / 微缩人物站在巨字前形成尺度反差 / 文字像建筑或屏幕
- mega-chinese-type family:文字作为道路 / 墙面 / 隧道 / 天花板 / 投影 / 包围结构本身;人物站在文字上、跑过文字、被文字压迫、从文字里冲出

### 6. 严格禁止(跨 family 通用)

- 长标题整句粗暴放大
- 只有大字没有图像隐喻 / 图文割裂 / 图像只是装饰
- 普通商业插画感 / 电商封面感 / 信息图模板感
- 廉价科技蓝紫霓虹(除非该色明确属于所选 style)
- 错别字 / 漏字 / 字体严重变形
- 角色字段任一遗漏
- 风格混搭(family 不能跨,style 也不能跨)
- 未授权品牌 logo
- 大段解释文字、花哨边框、过度 3D
- 多个小图标 / 双区对称构图

---

## English Image Prompt 撰写规范

最终输出的英文 prompt 必须:

1. **首句**:声明 aspect ratio + family + style opener,**用所选 family + style reference 推荐的通用描述,不写作品名 / 品牌名 / 联赛名**
2. **MANDATORY LAYOUT**:显式列出画面只允许的视觉层,禁止图标墙 / 双区对称;写出所选 family 的标题面积约束(anime-pop ≥ 70%;mega-chinese-type 50%–80%)
3. **核心视觉**:描述 metaphor_anchor 与图文结构关系;若 family 为 mega-chinese-type 必须显式声明 perspective_mode 与文字-人物关系
4. **A/B/C 文字**:明确指出三层文字内容、字号层级、字体倾向
5. **角色字段**:逐一列出 handle/vol/no/date 的具体内容与位置
6. **装饰文字**:列出 decoration_text 与摆放位置
7. **色彩**:明确 hex(让模型不漂)
8. **边框**:有则描述,无则不提
9. **质感**:halftone / film grain / blueprint hairlines / cel shadow / poster grain 等(按所选 family + style)
10. **STRICTLY FORBIDDEN 段**:列出所选 family + style 的 IP/品牌黑名单字样 + "no icon walls / no symmetric halves"
11. **尾句**:`text must be rendered exactly as specified, no spelling errors, no extra characters`

中文标题、角色字段、汉字装饰文字在英文 prompt 中**保留原中文**,加引号包裹。

---

## Style Anchor(可选,推荐)

如果用户在 `~/.config/anime-cover-prompt/anchors/` 放了 `eva.png` / `p5.png` / `akira.png` / `esports-key-visual.png` 等,
在最终输出的 prompt 末尾追加一行提示用户:

> 💡 出图时建议把 `~/.config/anime-cover-prompt/anchors/<family>/<style>.png` 作为 reference image 一并上传给 ChatGPT,可显著提升风格一致性。

---

## Self-check Before Output

输出前自查:

- [ ] `decisions.family` 已写出且符合 Family Routing 表
- [ ] `decisions.style` 落在所选 family 下挂的 style 列表内（不允许跨 family）
- [ ] `decisions.title` 与原文标题逐字相同
- [ ] `decisions.hero` 在原标题中可找到来源,语义未偏离
- [ ] handle / vol / no / date 四项齐全
- [ ] `metaphor_anchor_evidence` 真实引用了原文
- [ ] **只读了一份 family + 一份 style** reference,未混搭
- [ ] 若 family=anime-pop 且 style=eva,已填写 `palette_mode` / `palette_reason`,且没有无理由默认橙色
- [ ] 若 family=mega-chinese-type,已填写 `palette_mode` 与 `perspective_mode`（透视方式从 family 10 种里挑且在 style 允许范围内）
- [ ] **`decoration_text` 中无任何原作 IP / 现实联赛 / 现实俱乐部 / 现实运动品牌专有名词**
- [ ] **英文 prompt 中也未直接写作品名 / 品牌名 / 联赛名**（用 reference 推荐通用描述）
- [ ] 英文 prompt 末尾有 "text must be rendered exactly as specified"
- [ ] **标题块满足所选 family 的面积约束**（anime-pop ≥ 70%;mega-chinese-type 50–80%），英文 prompt 已显式写出
- [ ] 隐喻是**单一锚点 + 单一氛围**,不是多元素堆叠;不是左右对称双区构图
- [ ] **每条 decoration_text 都填了 source**
- [ ] hero 是画面里**唯一**的"巨型"字位

---

## References

### Families（每篇必读其一）

- [families/anime-pop.md](references/families/anime-pop.md) — 日式动漫流行视觉系（三层文字、面积约束、装饰溯源）
- [families/mega-chinese-type.md](references/families/mega-chinese-type.md) — 巨型中文透视标题视觉系（透视 10 种、配色撞色原则、字体倾向）

### Styles

#### Family `anime-pop`

- [styles/anime-pop/eva.md](references/styles/anime-pop/eva.md) — EVA 风格完整规范
- [styles/anime-pop/p5.md](references/styles/anime-pop/p5.md) — Persona 5 风格完整规范
- [styles/anime-pop/akira.md](references/styles/anime-pop/akira.md) — Akira 风格完整规范

#### Family `mega-chinese-type`

- [styles/mega-chinese-type/esports-key-visual.md](references/styles/mega-chinese-type/esports-key-visual.md) — 电竞赛事主视觉风格完整规范

加载时机：Workflow 步骤 6-7 决定 family + style 后，**只读对应那对**，不要同时加载多个。
