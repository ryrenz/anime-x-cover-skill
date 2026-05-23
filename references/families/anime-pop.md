# Family: anime-pop — 日式动漫流行视觉系

> 当 `family` 决议为 `anime-pop` 时，先读这一份 family 规范，再去读所选具体 style（`references/styles/anime-pop/<style>.md`）。

这个 family 的统一气质：**晚 90 年代到 2010 年代的日本动漫流行视觉**——机甲控制室、青年反叛海报、手绘赛璐璐城市电影。三个 style 都共享同一套"动漫 IP 审美 + 文字与图像有强结构关系 + 单一图像锚点"的底层逻辑。

## 适用文章类型（路由依据）

| 文章 mood 关键词 | 推荐 style |
|---|---|
| 冷峻 / 秩序 / 精密 / 技术 / 系统 / 调试 | `eva` |
| 反叛 / 觉醒 / 锐利 / 戳穿 / 反共识 / 心理 | `p5` |
| 末日 / 危机 / 赛博 / 工业 / 血色 / 都市 | `akira` |

如果文章语义与上面三条都不强匹配（典型：体育、电竞、热血商业、电影感叙事、广告大片），**应路由到另一个 family `mega-chinese-type`，不要硬塞 anime-pop**。

## 三层文字系统（family 通用）

- **A 层 主视觉巨型文字**：从原标题中提炼最有视觉张力的核心词。中文 2-6 字，英文 1-3 词。承担画面骨架。
- **B 层 完整主标题**：用户原标题完整保留，中号呈现。
- **C 层 系统小字**：副标题 / 关键词 / 角色字段（handle/vol/no/date）/ 装饰文字。

A 层与原题语义必须一致；长标题严禁整句放大；功能性词（教程/指南/方法论/路线/实战）不做最大字，放 B 或 C 层。

## 视觉面积分配（family 通用硬约束）

| 元素 | 全画面占比 |
|---|---|
| 标题块（A 层 hero + B 层完整标题 + 副标题） | **≥ 70%**，推荐 70-78%，必须居中 |
| Hero 字宽度 | **≥ 50% 画面宽度**，推荐 55-65% |
| 背景 / 隐喻图像 | 15-25%（必须**单一氛围或单一锚点**，不得抢标题） |
| 装饰文字 + 角色字段 | 5-10% |

英文 prompt 撰写时必须显式声明：

- `title block is centered and occupies at least 70% of the frame area`
- `hero text occupies at least 50% of frame width and is the dominant visual element`
- `background metaphor is a single atmospheric layer, NOT multiple competing icons or a grid of small elements`
- `do not divide the canvas into symmetric halves; title block dominates the central / focal area`

## 图文结构关系（family 通用）

图像不能独立漂浮，必须和文字发生**结构关系**：嵌入字内 / 从字里长出 / 穿过字形 / 作为字的地面或阴影 / 微缩人物站在巨字前形成尺度反差 / 文字像建筑或屏幕。

一次只选一个主图像锚点（机甲剪影 / 独行者剪影 / 红色机车 / 实验舱 / 异常几何体 / ……），避免把元素堆成图标墙。

## 装饰文字溯源（family 通用）

除角色字段外的所有 ticker / caution / system 文字，按所选 style 的"装饰文字原则"生成，并**为每一条装饰填写 source（来源）**写进 decisions JSON。

source 必须落在以下三类之一：

- **A. 文章导出**：从文章关键词、命题、章节数、术语、引文里提炼
- **B. 中性系统词模板**：style reference 里明列的中性词
- **C. 数据/编号/坐标/倒计时**：必须能与文章内容形成对应

写不出 source 的条目不准放。

## 原作 IP 文字硬约束（family 通用原则）

风格（色彩 / 笔触 / 构图 / 角色 / 载具 / 场景）可以参考三部作品，**文字必须避开**。

- 视觉意象保留（机甲、机车、面具、城市） ✅
- 元素上贴的原作 logo / 名字 / 专有词 ❌
- 英文 prompt 撰写时同样回避作品名（`EVA NERV terminal aesthetic` ❌ / `Akira poster style` ❌ / `Persona 5 splash` ❌），改用所选 style reference 推荐的通用描述

**具体每家的禁止字样清单与推荐替代语，见所选 style reference 文件**。

## 严格禁止（family 通用）

- 长标题整句粗暴放大
- 只有大字没有图像隐喻 / 图文割裂 / 图像只是装饰
- 普通商业插画感 / 电商封面感 / 信息图模板感
- 廉价科技蓝紫霓虹（除非该色明确属于所选 style）
- 错别字 / 漏字 / 字体严重变形
- 风格混搭（EVA 模式不能出现 P5 半色调网点）
- 未授权品牌 logo（Canon / Honda / OpenAI / Anthropic 等）
- **原作 IP 专有文字作为装饰出现**（详见所选 style reference）
- 大段解释文字、花哨边框、过度 3D

## English Prompt Opener 选择

opener 一定用所选 style reference 里推荐的"通用描述"短语，绝不写作品名。

## family-level self-check

- [ ] 风格未混搭（只读了一份 anime-pop style reference）
- [ ] 单一主图像锚点；不是多元素堆叠；不是左右对称双区构图
- [ ] 标题块居中且占画面 ≥ 70%（hero ≥ 50% 宽度）
- [ ] 英文 prompt 已显式写出面积约束
- [ ] 每条 decoration_text 都填了 source，且 source 在 A/B/C 三类之一
- [ ] decoration 中没有凭风格惯性塞的元素
- [ ] hero 是画面里唯一的"巨型"字位

## Style References

- [styles/anime-pop/eva.md](../styles/anime-pop/eva.md) — EVA 风格完整规范
- [styles/anime-pop/p5.md](../styles/anime-pop/p5.md) — Persona 5 风格完整规范
- [styles/anime-pop/akira.md](../styles/anime-pop/akira.md) — Akira 风格完整规范

加载时机：family 路由判定后，**只读对应一份**，不要同时加载多个。
