# EVA Style — Cold Mecha Terminal Aesthetic

> 当 `style` 决议为 `eva` 时，在生成 decisions 与英文 image prompt 之前，必须读这一份。

## 核心判断

EVA 风格不是“黑底 + 橙色警示 UI”。橙色只是一种警示／攻击性子模式，不能作为默认色无脑套用。

EVA 的有效视觉范围分两类：**角色／机体色**（绫波丽蓝白、初号机紫绿、二号机橙红、真希波粉绿）和**世界观系统色**（机构白红黑、使徒冷光异常）。生成前必须先根据文章语义选择一个 **palette mode**，并在 decisions 里说明理由。

## Palette Modes（必须先选一项）

### 1. Rei Blue / Clinical Isolation

适合：冷静分析、孤独个体、被系统观察、低温实验、失语、疏离、医疗／实验感。

- 深蓝黑：`#020712`
- 冷蓝：`#1b6fbf`
- 浅蓝白：`#d8f2ff`
- 医疗白：`#f2f7f8`
- 少量红点：`#d71920`

视觉元素：白色实验舱、冷蓝屏幕、透明管线、低饱和蓝色 HUD、大片留白和孤立小人剪影。

### 2. Unit-01 Purple / Green Awakening

适合：系统突破、个体觉醒、暴走、技术跃迁、工具变成主体、压抑后爆发。

- 深紫黑：`#080611`
- 初号机紫：`#3b1d69`
- 荧光绿：`#7cff4f`
- 暗红点缀：`#b0152a`
- 骨白：`#e8e6dc`

视觉元素：紫色巨型机械剪影、荧光绿网格、断裂约束带、神经连接线、斜向发散的能量标记。

### 3. Unit-02 Orange / Red Alert

适合：事故、裁撤、紧急决策、警报、组织切断、倒计时、风险暴露、攻击性行动。

- 深空黑：`#050505`
- 警示橙：`#ff6a00`
- 红橙：`#d71920`
- 暖白：`#e8e8e8`
- 暗灰：`#222222`

视觉元素：橙红机体色块、警示条、倒计时、切断线、红橙状态灯、控制室紧急 UI。不要把所有 EVA 图都做成这个模式。

### 4. Institutional White / Red / Black Command

这是世界观里的机构／命令系统色，不是初号机、二号机或绫波丽角色色。

适合：企业组织、命令链、协议、审批、总部、权力结构、冷酷制度。

- 机构白：`#f4f1e8`
- 深黑：`#050505`
- 血红线：`#c8102e`
- 暗灰：`#2b2b2b`
- 少量冷蓝灰：`#66737f`

视觉元素：白色面板、红色细线、黑色压缩字、组织结构蓝图、命令卡片、审批准入框。可以有“机构感”，但画面内绝不能出现原作机构名称或 logo。

统一背景规则：优先选 **全画面机构白底**（`#f4f1e8` 覆盖 70%+ 画面），用黑字、红线、暗灰蓝图做结构；也可选全画面深黑底，但不能同时做“外黑内白”的大面板拼贴。禁止白色大面板漂在黑色背景里。

### 5. Angelic Anomaly / Luminous Threat

适合：不可理解的外部冲击、非人系统、威胁、异质智能、异常信号。

- 黑：`#010101`
- 冷白：`#f7fbff`
- 异常青：`#00f0ff`
- 酸性绿：`#b6ff00`
- 少量血红：`#d71920`

视觉元素：几何发光体、十字形冷光、异常波形、漂浮多边形、被观测的未知对象。不要画成宗教插画，也不要生成原作使徒名字。

### 6. Mari Pink / Green Experimental Bypass

适合：外部介入、实验打法、轻微戏谑、非传统操作者、危险但灵活的工具使用、从旁路切入系统。

- 深黑：`#050505`
- 战术粉：`#e64aa0`
- 荧光绿：`#7cff4f`
- 暖白：`#f2efe8`
- 暗紫灰：`#292033`

视觉元素：粉色细线标记、绿色状态光、非对称 HUD、旁路接入箭头、斜切实验标签、轻微不守规矩但仍然工程化的排版。

## Palette Routing

选择 EVA 子模式时按文章核心语义路由：

| 文章语义 | palette_mode |
|---|---|
| 冷静、孤独、被观察、临床、疏离 | `rei_blue_clinical` |
| 觉醒、突破、暴走、技术跃迁、主体性增强 | `unit01_purple_green` |
| 裁撤、警报、事故、风险、倒计时、切断、攻击性行动 | `unit02_orange_red_alert` |
| 企业、组织、审批、命令链、制度、总部控制 | `white_red_black_command` |
| 外部冲击、未知智能、异常、不可解释威胁 | `angelic_anomaly` |
| 外部介入、实验打法、旁路、非传统操作者、轻微戏谑 | `mari_pink_green_experimental` |

如果文章同时命中多个语义，选最能服务标题的那个。比如“企业 AI 转型第一步——裁员”可以选 `white_red_black_command`（企业组织和命令链）或 `unit02_orange_red_alert`（裁撤和警报），但不能无理由固定橙色；若标题焦点是“企业为什么必须裁员”，机构白红黑通常比纯二号机橙红更贴合。

## 关键视觉元素

警示带 / 蓝图坐标网格 / 十字准星 / HUD 仪表盘 / 数据流 / 倒计时 / 极细蓝图发丝线 / 白色机构面板 / 紫绿机械剪影 / 冷蓝实验舱 / 粉绿旁路接入面板 / 异常几何光体。

一次只选一个主图像锚点，避免把所有 EVA 元素堆成图标墙。

## 统一背景硬约束

每张 EVA 图必须先选择一个 dominant background color,并让它覆盖全画面 70% 以上。不要出现“里面一种背景、外面另一种背景”的双层画布感。

- `rei_blue_clinical`：冷蓝黑或医疗白二选一做全画面主背景。
- `unit01_purple_green`：深紫黑做全画面主背景,荧光绿只做线条和状态光。
- `unit02_orange_red_alert`：深空黑或红橙警报底二选一做全画面主背景。
- `white_red_black_command`：机构白优先做全画面主背景；黑色只能作为文字、状态条、细边界,不能变成外层背景。
- `angelic_anomaly`：黑或冷白二选一做全画面主背景。
- `mari_pink_green_experimental`：深黑或暗紫灰二选一做全画面主背景,粉绿只做旁路线条。

英文 prompt 必须写出：`single dominant background color covers over 70% of the canvas; no inner white panel on an outer black background; no two-background composition`.

## 字体

- 中文 hero：Source Han Sans Heavy（厚重几何黑体）
- 英文 hero / B 层：压缩无衬线（Eurostile 风格）
- 小字 / 装饰系统词：JetBrains Mono（等宽）

字体颜色必须跟 palette mode 匹配。不要所有 EVA 图都用 `#ff6a00`。

## 气质

晚 90 年代日式机甲控制室扫过来的画面。紧张、克制、几何精密。可以是冷蓝实验室、紫绿觉醒、白红机构命令、橙红警报、粉绿实验旁路或异常冷光；不要自动收缩成同一种橙色控制台。

## 图像锚点（可用）

巨型机甲剪影 / 实验舱 / 操作台 / 终端 / 倒计时屏 / 工业蓝图剖面图 / HUD 全息投影 / 企业组织架构蓝图 / 断裂约束带 / 旁路接入面板 / 异常几何体。

**任何器械、面板、墙面、徽章上都不要贴下方 IP 黑名单中的任何文字或 logo。**

## 装饰文字原则

**严禁出现下列原作 IP 专有名词作为可读文字：**

- `NERV`
- `SEELE`
- `MAGI`
- `AT FIELD`
- `PATTERN BLUE` / `PATTERN RED`
- `第三新东京`
- `EVA-01` / `EVA-02` / `EVA-03` 等
- `WILLE`
- 角色名、机体名、使徒名、原作组织 logo

**正确做法：**从文章自身关键词提炼为 ALL CAPS 英文 + 中性系统词模板。

- 文章关键词转 ALL CAPS：`ORG UNIT RESET`、`AGENT WORKFLOW ONLINE`、`PROGRESSIVE DISCLOSURE`、`EVAL PASS`、`TRIGGER`
- 中性系统词模板：`SYSTEM READY` / `STATUS: ACTIVE` / `CAUTION` / `ACCESS LV.03` / `SIGNAL LOST`
- 坐标：`X+1284 Y-0463` / `Z-0921`
- 编号：`PTN-01` / `REF-9821` / `ID: 0473-IDX`

注意：`-NRV` 后缀太像原作机构名，禁止使用。

## 边框倾向

- `rei_blue_clinical`：冷蓝白实验舱边界、细蓝线框、大片留白。
- `unit01_purple_green`：紫黑底 + 荧光绿网格，断裂约束线作为边界。
- `unit02_orange_red_alert`：顶部警示条 + 底部状态条，红橙切断线。
- `white_red_black_command`：机构白面板、红色细线、黑色命令卡片、干净内缩边界。
- `angelic_anomaly`：黑底冷白几何框、异常波形和十字冷光，不做完整装饰边框。
- `mari_pink_green_experimental`：粉色斜切细线 + 绿色状态灯，非对称边界，像旁路接入面板。

边框必须服务版式秩序，不做花边。

## 角色字段摆放惯例

- 顶栏左：`<vol>` 与 `<no>`（如有），压缩无衬线。
- 顶栏右：`<date>`，等宽。
- 底栏右：`@<handle>`，等宽。

字段颜色跟 palette mode 走：蓝白模式用冷蓝／白，紫绿模式用荧光绿／骨白，机构模式用黑／红／白，粉绿实验模式用战术粉／荧光绿，橙红模式才用警示橙。

## 英文 prompt 推荐 opener 用语（回避 IP 词）

- ✅ `late-90s Japanese mecha control-room poster aesthetic`
- ✅ `cold industrial mecha-engineering blueprint aesthetic`
- ✅ `clinical blue-white experimental anime terminal aesthetic`
- ✅ `purple-and-green restrained mecha awakening poster aesthetic`
- ✅ `white-red-black institutional command-interface poster aesthetic`
- ✅ `pink-and-green experimental mecha-terminal interface aesthetic`
- ✅ `luminous geometric anomaly in a cold mecha-terminal composition`
- ❌ `EVA NERV terminal aesthetic`
- ❌ `Evangelion-style poster`

## Prompt 必写约束

EVA 模式的英文 prompt 必须明确写出：

- `palette mode: <chosen palette_mode>`
- 所选 palette mode 的 3-5 个 hex 色值。
- `single dominant background color covers over 70% of the canvas; no inner panel / outer background split`
- `do not default to orange unless the article explicitly calls for alarm, severance, emergency, or countdown`
- `no visible franchise names, unit names, organization logos, or character names`

## 自查

- [ ] 已在 decisions 中写出 `palette_mode`，且不是无理由默认橙色。
- [ ] 已选择单一主背景色,且主背景覆盖全画面 70% 以上；没有内外两套背景。
- [ ] 色彩与文章语义匹配；如果用了橙色，文章必须明确有警报、切断、事故、裁撤、倒计时等理由。
- [ ] 没有出现原作 IP 文字、logo、角色名、机体名、使徒名。
- [ ] 画面不是“所有 EVA 都橙黑控制台”的模板化输出。
