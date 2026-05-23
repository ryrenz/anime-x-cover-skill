# esports-key-visual — 电竞赛事主视觉

> 当 `family=mega-chinese-type` 且 `style=esports-key-visual` 时，在生成 decisions 与英文 image prompt 之前，必须读这一份（先读 family 文件再读本文件）。

## 核心判断

不是普通"红黑科技海报"，不是"游戏皮肤宣传图"，不是"广告字大图"。这是**国际级电竞赛事 key visual**——巨型中文透视标题作为画面骨架，运动员或冠军意象与文字发生结构关系，强冲突撞色配色，电影级构图。

参考视觉级别：LPL/LCK/LEC 决赛主视觉、Worlds 主视觉、KPL 顶级海报、运动品牌国际赛事 campaign（不照搬具体作品，只取气质）。

## Palette Modes（必须先选一项）

### 1. crimson_void（红黑暗夜）

适合：决赛、对决、复仇、王者、绝杀、最后一战。

- 深空黑 `#050608`
- 警示血红 `#e30613`
- 暗红血色 `#7a0a14`
- 冷白 `#f4f4f4`
- 暗金高光 `#c79a3a`

### 2. viper_strike（紫绿毒液）

适合：爆款、毒辣、暗黑系、个人英雄、非传统打法、AI 突袭。

- 深紫黑 `#0a0518`
- 暴击紫 `#7a2cff`
- 毒液绿 `#7cff4f`
- 暖白 `#eef0d5`
- 高光金 `#d4a017`

### 3. ice_strike（青红冰火）

适合：双线对抗、双子主题、东西对决、版本更替、左右脑、新旧交替。

- 深空黑 `#070a14`
- 冰川青 `#00d4ff`
- 血色红 `#ff3a3a`
- 冷白 `#f5f8fb`
- 银灰高光 `#9aa5b1`

### 4. neon_arena（霓虹竞技场）

适合：城市竞技场、街头电竞、亚文化、新生代、潮流出圈、街头篮球感。

- 深夜紫黑 `#0b0820`
- 霓虹粉 `#ff4bb8`
- 电子青 `#15b8a6`
- 暖白 `#f8efe4`
- 暗橙 `#ff8a2a`

## Palette Routing

| 文章语义 | palette_mode |
|---|---|
| 决赛 / 对决 / 王者 / 绝杀 / 最后一战 | `crimson_void` |
| 爆款 / 毒辣 / 暗黑 / 个人英雄 / 非传统打法 / AI 突袭 | `viper_strike` |
| 对抗 / 双子 / 版本更替 / 新旧交替 | `ice_strike` |
| 城市竞技 / 街头电竞 / 亚文化 / 新生代 / 潮流出圈 | `neon_arena` |

如果文章同时命中多个语义，选最能服务标题情绪的那个。

## 透视方式优先级（在 family 10 种里挑）

esports-key-visual 优先选这 5 种透视：

1. **斜切冲击字**（最常用）——hero 中文以 ±15° 至 ±35° 斜切横穿画面，配合人物爆发动作（拳击、冲刺、挥剑、扣杀等）。
2. **断裂爆破字**——hero 被冲破、撕裂、爆裂，人物或物体从字里冲出。
3. **纵深隧道字**——hero 字逐层向远处递进，形成赛道 / 通道 / 出场走廊感。
4. **仰视巨物字**——hero 像巨型纪念碑或舞台 LED，人物剪影在前景，文字产生英雄感。
5. **环绕包围字**——hero 围绕中央人物或奖杯形成包围结构。

**绝对禁用**：俯视透视字、巨型投影字（这两种气质偏沉静，不适合电竞）。

## 关键视觉元素

可用，但**一次只选一个主图像锚点**：

- 单一运动员剪影（拳击 / 跑步 / 持械 / 站立 / 持手柄 / 持鼠标键盘的轮廓，**不要细节面部，不要露出真人脸**）
- 冠军奖杯轮廓 / 队徽几何抽象（**不能是任何现实电竞俱乐部 logo**）
- 抽象竞技场结构（赛道、舞台、LED 大屏阵列、聚光灯束）
- 爆发能量线 / 速度线 / 粒子尘爆
- 持械（球拍 / 拳套 / 长剑 / 火炬）但不要具象品牌

**绝对禁用**：现实存在的电竞队伍/俱乐部 logo、现实选手脸部肖像、未授权品牌名、像素风游戏图标墙。

## 字体倾向

- 中文 hero：超粗黑体（思源黑体 Heavy / 庞门正道粗书 / 站酷酷黑），可压缩、可拉伸、可斜切；字怀必须撑满
- 英文 hero / B 层：condensed bold sans（Eurostile Extended Bold / Bebas Neue / Helvetica Inserat），通常全大写
- 小字 / 装饰：等宽字体（JetBrains Mono / IBM Plex Mono），用于时间戳、坐标、比分、版本号
- **禁用**：圆润字体、衬线宋体明朝体、手写体、华文行楷

## 气质

国际级电竞赛事 key visual 的气质——**强压迫感 + 冷静爆发力 + 工业级精度 + 巨型 LED 场馆即视感**。不是热闹、不是花哨、不是低龄向。是开赛前最后 5 秒灯光暗下来时的那种"凝固的暴力"。

## 装饰文字原则

**严禁出现下列内容作为可读文字**：

- 任何现实电竞联赛缩写（`LPL` `LCK` `LCS` `LEC` `KPL` `MSI` `Worlds` `S赛`）
- 任何现实电竞俱乐部名称或缩写
- 任何现实运动品牌（Nike / Adidas / Reebok / 红牛 / Logitech / Razer 等）
- 任何现实游戏专有名词（`英雄联盟` / `王者荣耀` / `DOTA` / `CS` / `Valorant` 等）
- 商业广告口号（"Just Do It" 等知名 slogan）

**正确做法**：从文章关键词提炼 ALL CAPS 英文 + 中性赛事系统词模板。

- 文章关键词 ALL CAPS：`AGENT WORKFLOW BREAK`、`PROMPT ENGINEERING FINAL`、`RAG STACK ONLINE`
- 中性赛事系统词：`FINAL ROUND` / `MATCH POINT` / `STAGE 03` / `ROSTER LOCKED` / `OVERTIME` / `KICK-OFF` / `SHOWDOWN`
- 比分模板：`03 : 02` / `2-1` / `BO5` / `GAME 5`
- 时间戳：`22:13:07` / `LIVE NOW` / `T-00:30`
- 坐标编号：`SECTOR-A` / `ARENA-04` / `ROW-7`

## 边框倾向

不强制完整矩形边框。优先用画面内部结构建立边界：LED 屏幕边缘、聚光灯锥光、舞台桁架、爆破粒子边缘。

如果加边框，只能是顶底警示条 + 时间戳条，不能花哨。

## 角色字段摆放惯例

- **顶部左**：`<vol>` 与 `<no>`（如有），等宽小字 + 警示色描边
- **顶部右**：`<date>`，等宽，可配 `LIVE` / `MATCH DAY` 标签
- **底部右**：`@<handle>`，等宽，配一个中性英文系统标签如 `STAGE 03` 或 `BO5`
- 字段颜色跟 palette_mode 走，但都必须保持等宽工业感

## 英文 prompt 推荐 opener 用语

- ✅ `international-tier esports finals key visual aesthetic, with giant Chinese title as architectural backbone`
- ✅ `cinematic championship poster with bold Chinese perspective typography and high-contrast color clash`
- ✅ `editorial sports campaign poster aesthetic where the Chinese title is the arena itself`
- ❌ `LPL finals key visual` / `Worlds 2026 poster` / `Riot Games style`（IP 名）
- ❌ `Nike sports campaign` / `Adidas Originals poster`（品牌名）

## Prompt 必写约束

esports-key-visual 的英文 prompt 必须显式写出：

- `palette mode: <chosen palette_mode>` + 3-5 个 hex 色值
- `perspective mode: <chosen perspective_mode>`（family 10 种之一，且必须在本 style 允许的 5 种内）
- `single main visual anchor: a stylized silhouette of [athlete/trophy/arena element], no realistic facial features, no real-world team logo, no real-world brand`
- `bold ultra-heavy Chinese sans-serif for hero text; condensed bold sans for English; monospace for system text`
- `giant Chinese title occupies 50% to 80% of visual area with strong perspective, dramatic depth, and pressure`
- `figure interacts structurally with title text (standing on, running through, breaking out of, surrounded by)`
- `no visible real-world league names, club names, sponsor brands, or game IP names`
- `text must be rendered exactly as specified, no spelling errors, no extra characters`

## 自查

- [ ] 已在 decisions 中写出 `palette_mode`（四选一），且与文章语义匹配
- [ ] 已在 decisions 中写出 `perspective_mode`（限本 style 允许的 5 种）
- [ ] 单一主图像锚点，无 icon 墙、无群像、无现实人脸
- [ ] 中文 hero 厚重粗黑体，不是圆润可爱体
- [ ] 配色是高冲突撞色，不是低饱和柔和
- [ ] 没有任何现实电竞联赛 / 俱乐部 / 游戏 / 运动品牌名作为装饰
- [ ] hero 与人物 / 物体形成结构关系，不是漂浮贴纸
- [ ] 标题块占画面 ≥ 70%（含 hero ≥ 55% 宽度）
- [ ] 角色字段 4 项齐全
- [ ] 每条 decoration_text 都有 source
