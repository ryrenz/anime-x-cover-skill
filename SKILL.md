---
name: anime-cover-prompt
description: Read an article and produce a paste-and-go gpt-image-2 prompt in EVA / Persona 5 / Akira aesthetic. Outputs decision JSON + English image prompt. Use when user wants to generate an article cover prompt with anime / cyberpunk / mecha-terminal / red-black-rebel style.
version: 0.3.6
---

# Anime Cover Prompt

文章 → 生成封面图或直接可粘贴进 ChatGPT (gpt-image-2) 的封面 prompt。
风格家族:EVA / Persona 5 / Akira。文字 100% 与原标题一致,角色字段强制出现。

每个风格的具体视觉规范(色彩 / 字体 / 装饰文字 / IP 黑名单 / 字段摆放)在 `references/styles/<style>.md`,**按需加载**,不在本文件常驻。

---

## Invocation

```
/anime-cover-prompt path/to/article.md
/anime-cover-prompt path/to/article.md --style eva|p5|akira --aspect 5:2
/anime-cover-prompt path/to/article.md --date 2026.05.03 --vol "Claude Code Vol.3"
/anime-cover-prompt path/to/article.md --reset      # 清空 config,重新问一次
/anime-cover-prompt                                  # 不带参数 → 让用户粘贴文章
```

参数:
- `--style` `eva` | `p5` | `akira`(默认 auto,根据文章 mood 判断)
- `--aspect` `5:2`(Twitter,默认) | `2.35:1`(公众号) | `3:2` | `4:5` | `1:1`
- `--lang` `zh`(默认) | `en` | `mix`
- `--handle` / `--vol` / `--no` / `--date`:单次覆盖 config 值
- `--reset`:清空 `~/.config/anime-cover-prompt/config.yaml` 并重新询问 handle

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

被调用时执行如下步骤,**步骤 1-2 内部完成不输出,步骤 3 输出 fields 一行,步骤 4-10 内部完成不输出过程**:

1. 加载持久字段(读 config.yaml)
2. 读文章
3. 解析 vol/no/date,**输出 fields 一行**
4. 判断风格模式(若 `style_default: auto` 且 CLI 未指定):见下方 Style Routing
5. **加载该风格的 reference 文件**:`references/styles/<style>.md`(EVA / P5 / Akira 三选一,**只读所选那一份**)
6. 应用通用 Design Spec(本文件下方)+ 该风格 reference,生成 decisions
   - 若风格为 `eva`,必须按 `references/styles/eva.md` 先选择 `palette_mode`,禁止无理由默认橙色。
7. 撰写英文 image prompt,opener 用风格 reference 推荐用语,绝不使用作品名直字面 cue
8. 校验:title 100% 等于原文标题;角色字段齐全;无原作 IP 文字;标题块居中且面积 ≥ 70%
9. 判断运行环境与可用能力,按下列优先级选第一个命中的分支(用户明确要求"只要 prompt"时直接跳到 9c):
   - **9a. 宿主有内置 image generation 工具(典型:Codex)**:直接调用宿主内置工具出图,不要把 prompt 当最终结果交给用户再手动跑一遍。调用前可短暂说明正在直接生成图片;调用后遵守宿主工具规则,不要再输出 prompt、解释或追问。
   - **9b. 宿主无内置 image generation 工具,但本机 `codex` CLI 可用**(典型:Claude Code 中 `command -v codex` 命中):shell 调 `codex exec` 转手出图,见下方"Codex CLI Bridge"段。**只有 9b 真的失败(codex 退出码非 0、超时、或未生成目标文件)时才降级到 9c**。
   - **9c. 既无宿主 image_gen,也没有可用的 codex CLI / 用户明确要求只要 prompt**:输出 decisions JSON + 英文 image prompt(走"Output Format"段)。
10. 如果走了 9a 或 9b,按 Image Output Policy **复制到 `~/Downloads/<basename>-cover.png`**(只复制到这一个位置,不要顺手 +1 复制到任何其它路径,除非用户在本次调用明示要求多个目标)。完成前必须 `ls` 验证目标 `*-cover.png` 存在。

---

## Codex CLI Bridge(9b 的执行细节)

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
   <步骤 7 撰写出的完整英文 image prompt>
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
4. **校验**:codex 退出码非 0、stderr 含明显错误、或 `ls "<target>"` 找不到文件 → 视为 9b 失败,降级到 9c(输出 decisions + 英文 prompt 让用户自己粘)。
5. **不要做的**:
   - 不要把 prompt body 再额外输出给用户(9b 的最终交付物就是 PNG 本身,prompt 是中间过程)
   - 不要顺手把 PNG 也复制到文章同目录 / Desktop 等额外位置 —— 默认输出位置永远只有 `~/Downloads/` 一处
   - 不要在 wrapper 里给 codex 留"多目标兜底"模板,精确写出唯一目标路径

---

## Style Routing

**只是路由,完整风格规范在 reference 文件**。

| 文章 mood 关键词 | 风格 | Reference |
|---|---|---|
| 冷峻 / 秩序 / 精密 / 技术 / 系统 / 调试 | **eva** | [references/styles/eva.md](references/styles/eva.md) |
| 反叛 / 觉醒 / 锐利 / 戳穿 / 反共识 / 心理 | **p5** | [references/styles/p5.md](references/styles/p5.md) |
| 末日 / 危机 / 赛博 / 工业 / 血色 / 都市 | **akira** | [references/styles/akira.md](references/styles/akira.md) |

每个 reference 文件含:主色 / 字体 / 气质 / 装饰文字原则 / 图像锚点 / 边框倾向 / 角色字段摆放 / 原作 IP 黑名单 / 英文 prompt opener 推荐用语。

---

## Output Format(严格遵守)

仅在未直接调用 image generation 工具时使用以下输出格式。Codex 中有 image generation 工具时,默认跳过此文本输出并直接出图。

````
## Decisions

```json
{
  "style": "eva | p5 | akira",
  "style_reason": "为什么这篇文章适配该风格(1 句)",
  "palette_mode": "仅 EVA 必填: rei_blue_clinical | unit01_purple_green | unit02_orange_red_alert | white_red_black_command | angelic_anomaly | mari_pink_green_experimental",
  "palette_reason": "仅 EVA 必填: 为什么该文章适配这个 EVA 子色彩模式",
  "hero": "A 层巨型大字 (2-6 中文字 / 1-3 英文词)",
  "title": "B 层完整标题 (与原文 100% 一致)",
  "subtitle": "C 层副标题 (可空)",
  "keywords": ["关键词1", "关键词2", "关键词3"],
  "mood": "情绪基调",
  "metaphor_anchor": "具体可视的视觉锚点",
  "metaphor_anchor_evidence": "引用文章中支持该锚点的 1-2 句原文",
  "decoration_text": [
    {
      "text": "装饰文字 1(含位置/旋转/颜色等视觉指示)",
      "source": "A. 文章 §X「原文片段」 / B. reference 中性词模板 / C. 编号-对应关系(如 13 章 → CHK 01-13)"
    }
  ],
  "field_placement": {
    "handle": "...",
    "vol_no": "...",
    "date": "..."
  },
  "border": "无 | 蓝图细线框 | 撕裂边 | 红色双线 | ..."
}
```

## Image Prompt (paste into ChatGPT with gpt-image-2)

```
[完整英文 prompt,中文标题/角色字段/装饰汉字保留不翻译]
```
````

---

## Universal Design Spec

> 通用规范在此。每个风格的差异化规范(色彩 / 字体 / 装饰原则 / IP 黑名单 / 字段摆放 / 边框倾向)
> 在 `references/styles/<style>.md`,加载所选那一份,**不要同时加载多个**。

### 1. 三层文字系统(必须)

- **A 层 主视觉巨型文字**:从原标题中提炼最有视觉张力的核心词。中文 2-6 字,英文 1-3 词。承担骨架。
- **B 层 完整主标题**:用户原标题完整保留,中号呈现。
- **C 层 系统小字**:副标题 / 关键词 / 角色字段(handle/vol/no/date) / 装饰文字。

### 2. 长标题提炼规则

- 短标题:整句作为大字
- 中等长度:提炼核心词做大字,完整标题缩为 B 层
- 长标题:严禁整句放大
- 功能性词(教程/指南/方法论/路线/实战)不做最大字,放 B 或 C 层
- A 层与原题语义必须一致

### 3. 角色字段(强制)

`handle` / `vol` / `no` / `date` 四项**必须全部出现在画面中**,任一项遗漏即视为错误输出。
**具体摆放位置见所选风格 reference 文件**。

### 4. 装饰文字(自动生成 + 强制溯源)

除角色字段外的所有 ticker / caution / system 文字,按所选风格的"装饰文字原则"生成,并**为每一条装饰填写 source(来源)**写进 decisions JSON。

**每一条 decoration_text 的 source 必须落在以下三类之一**:

- **A. 文章导出**:从文章关键词、命题、章节数、术语、引文里提炼 → source 写出对应原文位置或词
- **B. 中性系统词模板**:风格 reference 中明列的中性词(如 EVA `STATUS: ACTIVE` / Akira `SECTOR-7` / P5 `BREAK`) → source 写"reference 中性词模板"
- **C. 数据/编号/坐标/倒计时**:必须能与文章内容形成对应(如 13 章 → `CHK 01-13` / `T-013`,5 月 9 日 → `IDX-0509`) → source 写出对应关系

**严禁(都是模板惯性翻车点)**:

- ❌ "看起来够 EVA / 够 Akira"凭空填的字符串(典型:`T-005` 与文章无关、`危険` 与 Akira 海报视觉绑定过紧、随机 `REF-9821` 没对应任何东西)
- ❌ 自己也写不出 source 的装饰 —— 写不出来就不要放
- ❌ 任何 decoration 以"大尺寸 / 中央 / 全屏 / huge / large"出现 —— **巨型字位永远只属于 hero 一个**(详见 §7.5)
- ❌ 数字/编号写得"像那么回事"但其实没对应文章(`PTN-13` 必须明示与 13 章对应,否则换掉)

必须看起来像版式系统的一部分,不像后期贴上。**具体每家的原则与 IP 黑名单见所选风格 reference 文件**。

### 5. 边框

不强制有无;**具体倾向见所选风格 reference 文件**。
若加,必须服务于版式秩序,不能装饰化。

### 6. 原作 IP 文字硬约束(关键)

风格(色彩 / 笔触 / 构图 / 角色 / 载具 / 场景)可以参考三部作品,**文字必须避开**。
理由:用户的标题才是画面焦点,任何原作 IP 文字都会抢戏、稀释主题。

**通用原则**:

- 视觉意象保留(机甲、机车、面具、城市) ✅
- 元素上贴的原作 logo / 名字 / 专有词 ❌
- 英文 prompt 撰写时同样回避作品名(`EVA NERV terminal aesthetic` ❌ / `Akira poster style` ❌ / `Persona 5 splash` ❌),改用风格 reference 推荐的通用描述

**具体每家的禁止字样清单与推荐替代语,见所选风格 reference 文件**。

### 7. 图文结构关系

图像不能独立漂浮,必须和文字发生**结构关系**:嵌入字内 / 从字里长出 / 穿过字形 / 作为字的地面或阴影 / 微缩人物站在巨字前形成尺度反差 / 文字像建筑或屏幕。

### 7.5 视觉面积分配(硬约束 ⚠️)

**标题永远是画面焦点,任何隐喻图像都不得抢占标题应有的视觉面积**。
违反即视为输出失败。

**强制比例**:

| 元素 | 全画面占比 |
|---|---|
| 标题块(A 层 hero + B 层完整标题 + 副标题) | **≥ 70%**,推荐 70-78%,必须居中 |
| Hero 字宽度 | **≥ 50% 画面宽度**,推荐 55-65% |
| 背景 / 隐喻图像 | 15-25%(必须**单一氛围或单一锚点**,不得抢标题) |
| 装饰文字 + 角色字段 | 5-10% |

**严禁(全部都是常见 gpt-image 翻车点)**:

- ❌ **多个小图标/剪影并列堆成"内容墙"**(几十个 pitch deck、demo 屏、icon 堆叠)—— 视觉上就是垃圾
- ❌ **左右双区对称构图**(如"左半 metaphor / 右半 title")—— 标题永远是主,metaphor 是背景或单点
- ❌ Hero 字小于全画面 35% 宽度
- ❌ 标题块被挤到画面单侧的小子区域
- ❌ 标题块不居中,或标题块低于全画面 70%
- ❌ **任何装饰元素(印章 / 徽章 / 大数字 / 倒计时 / 副标语)的视觉权重 ≥ hero** —— hero 必须始终是画面里"最响"的一处。若有印章 / 徽章式装饰(如 P5 大红印章),其尺寸必须**明显小于 hero 一档以上**,英文 prompt 里必须显式写"smaller than hero" 或 "subordinate to hero in size"
- ❌ 在 hero 之外再生成第二个"巨型"字位(典型翻车:EVA HUD 中央放一个 huge 倒计时数字 / Akira 街景里又一块大 LED 字幕与 hero 抢焦点)

**正确做法**:

- 隐喻应该是**一个主要图像锚点 + 一片统一氛围背景**,而不是多个并列元素拼起来
- 标题块必须居中,用背景结构托住标题,而不是让标题绕开背景
- 例:✅ "撕口背景上一个独行者剪影 + 红色半色调氛围"(单一锚点 + 单一氛围)
- 反例:❌ "左侧一面 startup 表演物的墙 + 右侧露出真实"(多元素堆 + 双区对称)

**英文 prompt 撰写时必须显式声明**:

- `title block is centered and occupies at least 70% of the frame area`
- `hero text occupies at least 50% of frame width and is the dominant visual element`
- `background metaphor is a single atmospheric layer, NOT multiple competing icons or a grid of small elements`
- `do not divide the canvas into symmetric halves; title block dominates the central / focal area`

### 8. 严格禁止

- 长标题整句粗暴放大
- 只有大字没有图像隐喻 / 图文割裂 / 图像只是装饰
- 普通商业插画感 / 电商封面感 / 信息图模板感
- 廉价科技蓝紫霓虹(除非该色明确属于所选风格)
- 错别字 / 漏字 / 字体严重变形
- 角色字段任一遗漏
- 风格混搭(EVA 模式不能出现 P5 半色调网点)
- 未授权品牌 logo(Canon / Honda / OpenAI / Anthropic 等)
- **原作 IP 专有文字作为装饰出现**(详见所选风格 reference)
- 大段解释文字、花哨边框、过度 3D

---

## English Image Prompt 撰写规范

最终输出的英文 prompt 必须:

1. **首句**:声明 aspect ratio + 风格 opener,**用所选风格 reference 推荐的通用描述,不写作品名**
2. **MANDATORY LAYOUT**:显式列出画面只允许的视觉层(参考 §7.5),禁止图标墙 / 双区对称
3. **核心视觉**:描述 metaphor_anchor 与图文结构关系
4. **A/B/C 文字**:明确指出三层文字内容、字号层级、字体倾向
5. **角色字段**:逐一列出 handle/vol/no/date 的具体内容与位置
6. **装饰文字**:列出 decoration_text 与摆放位置
7. **色彩**:明确 hex(让模型不漂)
8. **边框**:有则描述,无则不提
9. **质感**:halftone / film grain / blueprint hairlines 等
10. **STRICTLY FORBIDDEN 段**:列出所选风格的 IP 黑名单字样 + "no icon walls / no symmetric halves"
11. **尾句**:`text must be rendered exactly as specified, no spelling errors, no extra characters`

中文标题、角色字段、汉字装饰文字在英文 prompt 中**保留原中文**,加引号包裹。

---

## Style Anchor(可选,推荐)

如果用户在 `~/.config/anime-cover-prompt/anchors/` 放了 `eva.png` / `p5.png` / `akira.png`,
在最终输出的 prompt 末尾追加一行提示用户:

> 💡 出图时建议把 `~/.config/anime-cover-prompt/anchors/{style}.png` 作为 reference image 一并上传给 ChatGPT,可显著提升风格一致性。

---

## Self-check Before Output

输出前自查:

- [ ] `decisions.title` 与原文标题逐字相同
- [ ] `decisions.hero` 在原标题中可找到来源,语义未偏离
- [ ] handle / vol / no / date 四项齐全
- [ ] `metaphor_anchor_evidence` 真实引用了原文
- [ ] 风格未混搭(只读了一份 style reference)
- [ ] 若 `style=eva`,已填写 `palette_mode` / `palette_reason`,且没有无理由默认橙色；`unit02_orange_red_alert` 仅用于警报、切断、事故、裁撤、倒计时、攻击性行动等语义
- [ ] **`decoration_text` 中无任何原作 IP 专有名词**(按所选风格 reference 的黑名单核对)
- [ ] **英文 prompt 中也未直接写 NERV / Akira / Persona 等字面词**(用 reference 推荐通用描述)
- [ ] 英文 prompt 末尾有"text must be rendered exactly as specified"
- [ ] **标题块居中且占画面 ≥ 70%**(hero ≥ 50% 宽度),英文 prompt 已显式写出此面积约束(见 §7.5)
- [ ] 隐喻是**单一锚点 + 单一氛围**,不是多元素堆叠;不是左右对称双区构图
- [ ] **每条 decoration_text 都填写了 source**,且 source 落在 A/B/C 三类之一(文章导出 / reference 中性词 / 与文章对应的编号)。**写不出 source 的条目已删除**
- [ ] decoration 中**没有凭风格惯性塞入的元素**(逐条检查:典型翻车 `T-005` 类无源倒计时、`危険` 类 IP 联想词、随机 `REF-XXXX` 无对应关系)
- [ ] hero 是画面里**唯一**的"巨型"字位;任何印章 / 徽章 / 大字装饰都明确小于 hero,英文 prompt 已写出 `smaller than hero` 或 `subordinate to hero in size`

---

## References

- [styles/eva.md](references/styles/eva.md) — EVA 风格完整规范(色彩 / 字体 / IP 黑名单 / 字段摆放 / opener 用语)
- [styles/p5.md](references/styles/p5.md) — Persona 5 风格完整规范
- [styles/akira.md](references/styles/akira.md) — Akira 风格完整规范

加载时机:Workflow 步骤 5 决定风格后,**只读对应那一份**,不要同时加载多个。
