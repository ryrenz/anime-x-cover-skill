# Akira Style — 1980s Hand-Painted Cyberpunk Anime Poster

> 当 `style` 决议为 `akira` 时，在生成 decisions 与英文 image prompt 之前，必须读这一份。

## 核心判断

这个风格不是“红黑赛博朋克海报”，而是 1980s 日本手绘赛璐璐动画的电影感封面：复杂都市建筑、硬边赛璐璐阴影、厚黑轮廓、霓虹在雾和玻璃上的漫反射、机械近景与人物或载具形成强烈透视。

如果 prompt 只写深红背景、雨夜、摩托、警告条，大概率会滑向普通 cyberpunk 或游戏海报，判为失败。

## 风格依据

- 本地参考图：`~/Downloads/Akira/`。重点观察低机位街景、玻璃橱窗反光、霓虹粉绿黄、红色夹克或车体的大面积色块、黑场中的仪表光、粗轮廓与硬边阴影。
- 公开资料显示，原片依赖大量手绘 production backgrounds、layout drawings、concept designs 与 imageboards，城市空间本身是画面力量来源；制作也以传统 cel animation、手绘背景、丰富夜景暗色与光源层次著称。

## 主色

不要把画面压成单一红黑。Akira 感来自“深暗基底 + 高饱和局部霓虹 + 手绘暗部层次”。

- 深暗基底：`#050606`、`#101318`、`#17110f`
- 标志性红：`#b40f18`、`#d71920`、`#f04a2f`
- 霓虹粉与洋红：`#ff4bb8`、`#d3318a`
- 霓虹青绿：`#00d69a`、`#15b8a6`
- 脏黄与橙光：`#f2c94c`、`#ff8a2a`
- 冷蓝夜色：`#1c3146`、`#2a4f68`
- 灰白高光：`#e8e2d8`

## 线条与上色

- 使用 hand-painted cel animation look，而不是 glossy 3D、AI game concept art、vector poster 或 smooth digital painting。
- 轮廓线要粗、黑、略有手绘抖动，机械和建筑线条可更细更密。
- 阴影是硬边分区：base color、shadow、deep shadow，少用柔滑渐变。
- 光晕可以有，但必须像赛璐璐摄影叠加出的 bloom 和雾化反射，不要变成现代霓虹赛博朋克的过度发光。
- 保留轻微 film grain、cel texture、painted background texture、gate-weave imperfection。

## 构图语法

优先选择电影分镜式构图，而不是平面 graphic design 拼贴。

- 低机位仰拍：人物、车体、管线或广告牌压迫画面。
- 宽银幕横向运动：机车、光轨或街景从画面一侧切入，但不得抢走 hero 字。
- 仪表或机械近景：前景是红黑机械和发光读数，背景是黑场或道路光束。
- 城市峡谷：高楼、招牌、管线和玻璃反射形成深度，不要画成简化 skyline。
- 单一人物或载具剪影：只允许一个主要角色锚点，避免多人物群像。

标题块仍然是主角：hero text 必须占画面宽度至少 40%，并与图像发生结构关系。可让文字像街区广告牌、道路标线、仪表读数、挡风玻璃反射或建筑立面的一部分，但不能只是贴在图上。

## 关键视觉元素

可用，但一次只选一个主锚点：

- 红色机车或流线型机械近景。
- 黑夜道路、车头灯光束、红绿仪表读数。
- 密集手绘街区：玻璃、招牌、管线、空调外机、天桥、电缆。
- 巨大实验舱、军用设备、控制台、扫描屏。
- 狭窄小巷、混凝土墙、涂鸦、蒸汽、雨后反光。
- 单一少年或骑手剪影，红色外套或红色车体作为最大色块。

不要用：现代摩天楼 skyline、蓝紫霓虹雨夜模板、赛博忍者、过度机甲化角色、3D 摩托渲染、干净矢量图标。

## 字体

- 中文 hero：厚重黑体或硬朗明朝体，边缘清楚，适合被处理成招牌、仪表读数或建筑标识。
- 英文 hero：condensed bold sans、industrial block lettering。
- 小字：monospace 或工业铭牌感窄体。
- 文字不能拟真成原作 logo；不要使用圆润可爱字体、科技蓝 HUD 字体、未来感过强的 sci-fi font。

## 气质

高密度、压迫、暴力前夜、城市过载、青春暴走、机械失控。画面应有“手绘电影帧被重新设计成封面”的感觉，而不是商业插画、游戏 loading screen 或普通科技封面。

## 装饰文字原则

**严禁出现下列原作 IP 专有名词作为可读文字：**

- `AKIRA`
- `NEO-TOKYO`
- `金田`、`KANEDA`
- `鉄雄`、`TETSUO`
- 作为致敬式装饰的 `危険`、`神経`。这两个虽然是常见日文词，但与原作海报视觉绑定过紧，看到会立刻读作致敬，抢走用户标题。

**正确做法：**从文章关键词提炼 + 中性工业系统词。优先英文，慎用日文装饰。

- 中性工业词：`RESTRICTED AREA`、`SECTOR-7`、`DISTRICT 04`、`CLASSIFIED LV.02`、`CONTROL ROOM`、`SIGNAL LOST`
- 时间戳：`2026.05.09 22:13:07`
- 坐标或编号：`GRID-407`、`BLOCK-A7`、`REF: 0509`
- 仪表读数：`RPM 7530`、`TEMP 041`、`LV 02`，但必须能在 source 中解释来源或对应关系。

装饰文字要像城市标牌、仪表读数、印刷在金属铭牌上的小字，不能像模板贴纸。任何没有 source 的编号或倒计时都删除。

## 边框倾向

通常不需要完整矩形边框。优先用画面内部结构建立边界：车体曲线、道路透视线、广告牌边缘、控制台面板、玻璃窗框。

如果加边框，只能是很克制的顶底红线、仪表面板边缘或街区招牌框，不能做装饰性花边。

## 角色字段摆放惯例

- 顶部：细红线或招牌边缘附近放 `<vol>`、`<no>`、`<date>`，用小号等宽红字或灰白字。
- 底部：`@<handle>` 与一个中性英文系统标签，例如 `SECTOR-7` 或 `CONTROL ROOM`。
- 字段应像 production slate、仪表标签或街区标识的一部分，不要做成现代 UI chip。

## 英文 prompt 推荐 opener 用语（回避 IP 词）

- ✅ `1980s hand-painted Japanese cyberpunk cel-animation poster aesthetic`
- ✅ `dark industrial Japanese animated film frame redesigned as a title-dominant poster`
- ✅ `hand-painted urban sci-fi anime background with bold cel-shaded foreground forms`
- ❌ `Akira poster style`
- ❌ `Neo-Tokyo cyberpunk aesthetic`
- ❌ `Katsuhiro Otomo style`

## Prompt 必写约束

生成英文 image prompt 时，Akira 模式必须显式写出：

- `hand-painted cel animation, hard-edged cel shadows, thick black ink outlines, painted background texture`
- `complex urban architecture with layered signs, pipes, glass reflections, and cinematic low-angle perspective`
- `not a generic neon cyberpunk poster, not glossy 3D, not vector art, not game concept art`
- `hero text occupies at least 40% of frame width and remains the dominant visual element`
- `use one main visual anchor only; no icon wall, no character lineup, no symmetric split composition`

## 自查

- [ ] 画面不是单一红黑，而有霓虹粉、青绿、脏黄、橙红、冷蓝暗部的层次。
- [ ] 有手绘赛璐璐痕迹：粗黑轮廓、硬边阴影、painted background texture。
- [ ] 有复杂城市或机械空间，不是干净 skyline 或抽象科技背景。
- [ ] 构图像电影帧：低机位、宽银幕、前景遮挡、纵深透视至少满足两项。
- [ ] 只有一个主图像锚点，标题块仍然最大。
- [ ] 没有原作 IP 字样，也没有会被读成原作致敬的日文装饰。
