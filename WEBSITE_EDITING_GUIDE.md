# WAM Survey 网站内容编辑与维护指南

> 适用仓库：`wam-survey-site`  
> 适用对象：希望长期维护网站内容、但不一定熟悉前端开发的编辑者  
> 网站类型：纯静态 HTML + CSS + JavaScript + JSON，无数据库、无构建步骤

---

## 1. 先理解这个网站是怎样工作的

这个项目的内容有两种来源：

1. **直接写在 HTML 里的内容**：页面标题、介绍文字、观点卡片、表格、按钮、图片等。
2. **写在 JSON 里的结构化内容**：论文卡片、数据集卡片、发展时间线、参考文献。浏览器加载页面后，`assets/js/main.js` 会读取 JSON 并自动生成卡片。

因此，维护时先判断内容属于哪一类：

| 想修改的内容 | 应修改的文件 |
|---|---|
| 首页标题、介绍、统计数字、模块卡片 | `index.html` |
| WAM 定义、概念比较、研究问题 | `overview.html` |
| 路线图页面的固定文字和图片 | `roadmap.html` |
| 路线图中的年份节点 | `data/timeline.json` |
| 论文卡片、论文摘要、标签 | `data/papers.json` |
| 论文页的筛选按钮和页面说明 | `papers.html` |
| 数据综述正文、固定表格和观点 | `data.html` |
| 数据集卡片 | `data/datasets.json` |
| 参考文献卡片 | `data/references.json` |
| 全站颜色、间距、卡片尺寸、响应式布局 | `assets/css/style.css` |
| JSON 如何变成卡片、搜索和筛选行为 | `assets/js/main.js` |
| 首页及路线图中的图示 | `assets/img/` |

### 当前页面与数据流

```text
index.html       ← papers.json 中 featured=true 的前 6 篇论文
overview.html    ← 页面内容全部直接写在 HTML
roadmap.html     ← timeline.json + 页面固定内容
papers.html      ← papers.json + 搜索框 + 筛选按钮
data.html        ← datasets.json + 页面固定内容
references.html  ← references.json

所有页面 → assets/css/style.css（统一外观）
所有页面 → assets/js/main.js（导航、加载 JSON、生成卡片）
```

---

## 2. 每次编辑的推荐流程

1. 找到内容来源，不要看到卡片就直接去 HTML 中找。动态卡片通常在 JSON 中。
2. 修改前保留原有 HTML 标签、JSON 标点和字段名。
3. 启动本地服务器预览，不能只双击打开 HTML。
4. 同时检查桌面宽度和手机宽度。
5. 检查链接、图片、筛选、搜索和中文显示。
6. 提交前查看变更，避免误删相邻内容。

在仓库根目录启动预览：

```powershell
python -m http.server 8000
```

然后访问：

```text
http://localhost:8000
```

结束服务时，在终端按 `Ctrl + C`。

> 为什么不能直接双击 `index.html`？因为浏览器通常不允许本地 `file://` 页面读取 JSON 文件。结果会是页面能打开，但论文、数据集、时间线和参考文献卡片为空。

---

## 3. 编辑 HTML 时需要认识的最少语法

HTML 使用成对标签包住内容：

```html
<h2>这是二级标题</h2>
<p>这是正文段落。</p>
```

常用标签：

| 标签 | 用途 |
|---|---|
| `<h1>` | 每个页面的主标题，通常只用一次 |
| `<h2>` | 一个大章节的标题 |
| `<h3>` | 卡片或小节标题 |
| `<p>` | 段落 |
| `<strong>` | 重点文字 |
| `<a>` | 链接或按钮 |
| `<img>` | 图片 |
| `<section>` | 页面章节 |
| `<article>` | 独立卡片或条目 |
| `<div>` | 布局容器 |
| `<span>` | 行内文字或标签 |
| `<table>` | 表格 |

当前 HTML 文件被压缩成很长的一行。这不会影响网站，但手工编辑时要格外小心。可以在编辑器里使用“格式化文档”，让标签换行；如果这样做，整个文件会产生较大的格式变更，建议一次只格式化一个页面并单独提交。

### 修改一段普通文字

例如在 `overview.html` 中找到：

```html
<p class="lead">本页回答一个核心问题……</p>
```

只替换标签之间的文字：

```html
<p class="lead">本页梳理 WAM 的定义、技术边界与核心研究问题。</p>
```

不要误删 `<p class="lead">` 或结尾的 `</p>`。

### 修改页面标题和搜索摘要

每个页面的 `<head>` 中有：

```html
<title>Overview · WAM Survey</title>
<meta name="description" content="What is WAM and how it differs from WM and VLA.">
```

- `<title>` 是浏览器标签页名称，也会影响搜索结果标题。
- `description` 是页面摘要，建议控制在约 80–160 个字符。
- 新增或大改页面主题时，这两项也应同步修改。

---

## 4. 修改首页

首页内容在 `index.html`。

### 修改主标题和简介

找到首页 `class="hero"` 的章节：

```html
<h1>World Action Models Survey</h1>
<p class="lead">一个长期维护的世界动作模型研究导航站……</p>
```

直接替换文字即可。

### 修改首页统计数字

首页的四个统计项目前是手工维护的，不会自动读取 JSON 数量：

```html
<div class="stat">
  <strong>14+</strong>
  <span>WAM & adjacent papers</span>
</div>
```

新增论文或数据集后，应同步调整这里的 `14+` 或 `10`。如果增加第五个统计项，桌面端默认仍可显示，但可能拥挤；更稳妥的做法是保持四项，或同时调整 `.stat-row` 的 CSS。

### 修改首页四个入口卡片

```html
<article class="card link-card">
  <h3>WAM 概述</h3>
  <p>定义 WAM，区分 WM、AC-WM 与 VLA。</p>
  <a class="stretched" href="overview.html">Open overview →</a>
</article>
```

可以改标题、说明和跳转地址。`href` 是目标页面；内部链接使用相对路径，例如 `data.html`。

### 控制首页推荐论文

首页推荐区不是写在 `index.html` 中，而是读取 `data/papers.json`。将某篇论文的：

```json
"featured": false
```

改成：

```json
"featured": true
```

它就会进入首页推荐候选。脚本目前只展示 JSON 中最靠前的 6 篇推荐论文，因此顺序也很重要。

---

## 5. 新增、修改和删除论文卡片

论文数据位于 `data/papers.json`。这是长期维护论文内容时最重要的文件。

### 完整论文范例

将下面的对象复制到数组中，并替换内容：

```json
{
  "id": "example-wam",
  "title": "Example-WAM",
  "fullTitle": "Example World Action Model for Robotic Manipulation",
  "year": 2026,
  "type": "Joint WAM",
  "architecture": [
    "Video-Action Model",
    "Diffusion",
    "Action Tokenizer"
  ],
  "data": [
    "Robot Demonstrations",
    "Human Video"
  ],
  "venue": "arXiv",
  "arxiv": "2606.12345",
  "url": "https://arxiv.org/abs/2606.12345",
  "featured": false,
  "summary": "一句话说明论文解决了什么问题以及采用了什么方法。",
  "dataSummary": "说明训练数据来源、规模、模态和采集方式。",
  "contribution": "说明相对已有工作的核心增量。",
  "limitations": "说明适用边界、未验证内容或已知限制。"
}
```

字段解释：

| 字段 | 是否必需 | 说明 |
|---|---:|---|
| `id` | 是 | 页面锚点，必须唯一；建议只用小写字母、数字和连字符 |
| `title` | 是 | 卡片短标题 |
| `fullTitle` | 是 | 论文完整标题 |
| `year` | 是 | 数字，不加引号 |
| `type` | 是 | 论文类别，也可被筛选器搜索 |
| `architecture` | 是 | 架构标签数组 |
| `data` | 是 | 数据来源标签数组 |
| `venue` | 是 | 会议、期刊或 arXiv |
| `arxiv` | 是 | arXiv 编号；没有时可填空字符串 |
| `url` | 是 | 论文链接，建议使用 DOI、arXiv 或项目主页 |
| `featured` | 是 | `true` 表示可出现在首页；布尔值不加引号 |
| `summary` | 是 | 方法摘要 |
| `dataSummary` | 是 | 数据摘要 |
| `contribution` | 是 | 核心贡献 |
| `limitations` | 是 | 局限性 |

### JSON 中最容易出错的逗号

数组中相邻对象之间必须有逗号：

```json
[
  { "id": "paper-a", "title": "Paper A" },
  { "id": "paper-b", "title": "Paper B" }
]
```

最后一个对象后面不要加逗号。对象内部最后一个字段后面也不要加逗号。

### 修改论文

根据唯一的 `id` 或 `title` 搜索对象，只改对应字段。例如只更新局限性：

```json
"limitations": "目前只在单一机器人本体和桌面操作场景中验证。"
```

### 删除论文

删除从 `{` 到配对 `}` 的整个对象，并处理前后对象之间的逗号。删除后还应检查：

- `data/references.json` 中是否也要删除对应参考文献；
- 首页的论文统计数字是否要更新；
- 正文中是否有指向 `papers.html#该-id` 的链接。

### 新增论文分类筛选按钮

`papers.html` 中的按钮只是搜索匹配项，不会自动根据 JSON 生成。复制一个按钮：

```html
<button class="filter-btn" data-filter="Latent Action">Latent Action</button>
```

`data-filter` 的内容需要能在论文的标题、类型、摘要、架构标签或数据标签中匹配到。按钮显示文字可以与它相同，也可以更短。

### 链接到某一篇论文卡片

论文卡片的 `id` 会成为锚点。若论文 `id` 是 `example-wam`，可使用：

```html
<a href="papers.html#example-wam">查看 Example-WAM 摘要</a>
```

---

## 6. 新增、修改和删除数据集卡片

数据集位于 `data/datasets.json`，页面由 `data.html` 中的 `id="dataset-list"` 容器承载。

### 完整数据集范例

```json
{
  "id": "example-dataset",
  "name": "Example Robot Dataset",
  "type": "Real Robot Manipulation Data",
  "scale": "100K trajectories / 500 hours / 50 tasks",
  "collection": "Collected through teleoperation in 20 real-world environments",
  "embodiment": "Single-arm and dual-arm manipulators",
  "hasAction": true,
  "hasLanguage": true,
  "use": "WAM pretraining, action tokenization and cross-embodiment evaluation",
  "limitations": "The license is non-commercial and failure trajectories are underrepresented."
}
```

注意：

- `hasAction` 与 `hasLanguage` 必须写 `true` 或 `false`，不能写成字符串 `"true"`。
- 脚本会根据这两个值显示 `Action labels`、`No robot action`、`Language / text` 或 `No language` 标签。
- `id` 可用于站内链接：`data.html#example-dataset`。
- 当前数据集卡片没有外链字段。若需要官网按钮，参见第 13 节“修改动态卡片模板”。

---

## 7. 新增发展时间线节点

时间线数据位于 `data/timeline.json`，按数组顺序显示。它不会自动按年份排序，因此请把新节点放在正确位置。

```json
{
  "year": "2027",
  "title": "Next-generation WAM",
  "stage": "闭环部署阶段",
  "summary": "概括这一阶段的代表性进展。",
  "relation": "解释该进展为什么改变或推进了 WAM。"
}
```

这里的 `year` 使用字符串，因此也可以写成区间：

```json
"year": "2026–2027"
```

时间线页面中的分类图 `assets/img/wam-taxonomy.svg` 不会随 JSON 自动更新；如果时间线概念发生变化，需要单独编辑或替换该图。

---

## 8. 添加参考文献与正文引用

参考文献卡片来自 `data/references.json`。

### 添加参考文献

```json
{
  "id": 15,
  "title": "Example World Action Model",
  "authors": "Alice Zhang, Bob Li, et al.",
  "year": 2026,
  "tag": "Joint WAM",
  "url": "https://arxiv.org/abs/2606.12345"
}
```

- `id` 是显示在卡片上的编号 `[15]`，目前不会自动生成。
- 新增时建议使用当前最大编号加 1。
- `url` 可省略；省略后不会显示 `Open` 按钮。
- 论文卡片和参考文献是两个独立数据源。新增重要论文时，通常需要同时维护 `papers.json` 与 `references.json`。

### 在正文中引用编号

最简单的写法：

```html
<p>Dreamer 通过潜空间想象轨迹优化策略 [4]。</p>
```

更好用的写法是让编号可点击，并为目标文献增加锚点支持。目前参考文献脚本没有给卡片输出 `id`。如果暂时不改脚本，可以链接到整个参考文献页：

```html
<p>Dreamer 通过潜空间想象轨迹优化策略
  <a href="references.html">[4]</a>。
</p>
```

若希望精确跳到某条文献，按第 13 节修改参考文献模板后，可写：

```html
<a href="references.html#ref-4">[4]</a>
```

### 添加脚注式补充

页面目前没有脚注组件，可以用普通小字卡片表达：

```html
<div class="callout">
  <strong>注：</strong> 本站将“严格 WAM”限定为未来预测与动作生成进入同一策略框架的方法。
</div>
```

---

## 9. 添加静态卡片、标签、提示框和公式

这些内容直接添加到某个 HTML 页面的 `<main>` 内。

### 添加一张普通卡片

```html
<article class="card">
  <h3>动作表示</h3>
  <p>讨论连续控制、离散动作 token 和潜动作表示之间的关系。</p>
</article>
```

### 添加两列、三列或四列卡片

```html
<div class="grid cards-3">
  <article class="card"><h3>数据</h3><p>数据说明。</p></article>
  <article class="card"><h3>模型</h3><p>模型说明。</p></article>
  <article class="card"><h3>评测</h3><p>评测说明。</p></article>
</div>
```

可把 `cards-3` 换成 `cards-2` 或 `cards-4`。在屏幕宽度小于 900px 时，这些布局会自动变成单列。

### 添加标签

```html
<div class="tag-row">
  <span class="tag">Cross-embodiment</span>
  <span class="tag">Latent Action</span>
  <span class="tag">Video Prediction</span>
</div>
```

### 添加重点提示框

```html
<div class="callout">
  <strong>核心结论：</strong>
  高质量动作—视频对齐数据比单纯扩大视频数据规模更关键。
</div>
```

### 添加公式或代码式表达

```html
<code class="formula">p(o_future, a_future | o_history, language)</code>
```

此样式不会自动渲染 LaTeX，只会以等宽字体显示文本。若需要真正的数学排版，需要额外引入 KaTeX 或 MathJax；这是功能扩展，不是单纯改文字。

### 添加一个完整章节

```html
<section class="section">
  <div class="section-head">
    <div>
      <span class="eyebrow">Evaluation</span>
      <h2>WAM 评测维度</h2>
    </div>
    <p>同时衡量未来预测质量、动作成功率和闭环鲁棒性。</p>
  </div>

  <div class="grid cards-3">
    <article class="card"><h3>预测</h3><p>视觉一致性和物理合理性。</p></article>
    <article class="card"><h3>控制</h3><p>任务成功率和动作平滑度。</p></article>
    <article class="card"><h3>闭环</h3><p>扰动恢复和长程误差累积。</p></article>
  </div>
</section>
```

建议把新章节放在 `</main>` 之前，并保持所有 `<section>`、`<div>` 和 `<article>` 正确闭合。

---

## 10. 插入图片

建议把网站图片统一放进 `assets/img/`，文件名使用小写英文、数字和连字符，例如：

```text
assets/img/action-tokenizer-overview.png
assets/img/data-pipeline.svg
assets/img/benchmark-results.webp
```

### 插入大图

```html
<section class="section">
  <div class="figure-card">
    <img
      src="assets/img/action-tokenizer-overview.png"
      alt="WAM 动作 tokenizer 的训练与推理流程图">
  </div>
</section>
```

`.figure-card img` 已有宽度、圆角和阴影样式。

### 图片加标题和来源

```html
<figure class="figure-card">
  <img src="assets/img/data-pipeline.svg" alt="WAM 数据处理流程">
  <figcaption>
    图 3：WAM 数据处理流程。根据 Example-WAM 论文整理。
    <a href="https://arxiv.org/abs/2606.12345" target="_blank" rel="noopener">来源</a>
  </figcaption>
</figure>
```

当前 CSS 没有专门设置 `figcaption`，仍可正常显示。若希望统一样式，可在 `style.css` 末尾增加：

```css
.figure-card figcaption {
  margin-top: 12px;
  color: var(--muted);
  font-size: 14px;
}
```

### 图片编辑注意事项

- `alt` 应描述图的内容，不要写“图片”或留空；纯装饰图才可写 `alt=""`。
- 优先使用 SVG 展示架构图，放大不会模糊。
- 照片或复杂截图可使用 WebP/JPEG；需要透明背景时使用 PNG/WebP。
- 尽量压缩大图，避免单张图片达到数 MB。
- 外部论文图片可能受版权约束。更稳妥的是自己重绘，并在图注中标明“根据某论文整理”。
- 外链图片可能失效，长期维护时建议在许可允许的情况下保存到仓库内。

---

## 11. 添加表格

项目已有横向滚动表格样式，必须用 `table-wrap` 包住表格，手机上才不会撑破页面。

```html
<div class="table-wrap">
  <table>
    <thead>
      <tr>
        <th>方法</th>
        <th>未来预测</th>
        <th>动作输出</th>
        <th>数据需求</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>World Model</td>
        <td>是</td>
        <td>不一定</td>
        <td>视频或状态转移</td>
      </tr>
      <tr>
        <td>WAM</td>
        <td>是</td>
        <td>是</td>
        <td>对齐的视频—动作数据</td>
      </tr>
    </tbody>
  </table>
</div>
```

新增列时注意：当前表格最小宽度为 `760px`，列很多时可在 `style.css` 中针对特定表格增加更大的 `min-width`，不要把全站所有表格一并放大。

例如：

```html
<div class="table-wrap wide-table">...</div>
```

```css
.wide-table table { min-width: 1100px; }
```

---

## 12. 添加列表和外部链接

### 无序列表

```html
<ul>
  <li>真实机器人遥操作数据</li>
  <li>无动作标签的人类视频</li>
  <li>仿真与合成轨迹</li>
</ul>
```

### 有序列表

```html
<ol>
  <li>统一时间戳和采样频率</li>
  <li>映射动作空间</li>
  <li>过滤失败或低质量片段</li>
</ol>
```

### 外部链接

```html
<a href="https://arxiv.org/abs/2606.12345" target="_blank" rel="noopener">
  阅读论文
</a>
```

`target="_blank"` 会在新标签页打开；配合 `rel="noopener"` 更安全。站内链接通常不需要新标签页：

```html
<a href="data.html#droid">查看 DROID 数据集</a>
```

---

## 13. 修改动态卡片展示字段

当 JSON 已经有某个字段，但页面没有显示它时，需要修改 `assets/js/main.js` 中对应的渲染函数：

| 内容 | 函数 |
|---|---|
| 首页推荐论文 | `home()` |
| 论文页卡片 | `renderPapers()` |
| 论文搜索匹配范围 | `pm()` |
| 论文筛选交互 | `filters()` |
| 数据集卡片 | `renderDatasets()` |
| 时间线 | `renderTimeline()` |
| 参考文献 | `renderRefs()` |

该文件当前经过压缩，几乎全部在一行中。修改前建议先格式化 JavaScript，并单独检查变更。

### 例：让参考文献支持精确锚点

在 `renderRefs()` 输出模板的 `<article>` 上加入：

```js
id="ref-${esc(r.id)}"
```

目标结构应类似：

```js
<article class="reference-card" id="ref-${esc(r.id)}">
```

之后正文中的 `references.html#ref-4` 就能准确跳转。

### 例：给数据集增加官网链接

第一步，在 `data/datasets.json` 的每个需要外链的数据集中添加：

```json
"url": "https://example.org/dataset"
```

第二步，在 `renderDatasets()` 的卡片模板中加入条件输出：

```js
${d.url ? `<div class="paper-actions"><a href="${esc(d.url)}" target="_blank" rel="noopener">Website</a></div>` : ''}
```

第三步，给搜索或其他功能使用该字段时，也应通过 `esc()` 转义后再放进 HTML。

> `esc()` 用于防止标题、摘要或链接中的特殊字符破坏页面结构。新增动态字段时不要直接把用户可编辑文本插入 HTML。

### 例：让论文搜索也搜索 venue

当前 `pm()` 已把多个字段合并成可搜索文本。需要增加字段时，将它加入数组，例如：

```js
const h = [
  p.title,
  p.fullTitle,
  p.year,
  p.type,
  p.venue,
  p.arxiv,
  p.summary
].join(' ').toLowerCase();
```

修改脚本属于功能开发，完成后应重点测试：空搜索、中文搜索、英文大小写、所有筛选按钮和无结果状态。

---

## 14. 调整颜色、间距和卡片外观

全站样式位于 `assets/css/style.css`。优先修改开头的 CSS 变量，而不是到处替换颜色值：

```css
:root {
  --bg: #fff8f3;
  --card: #fffefa;
  --ink: #30231f;
  --muted: #7c675e;
  --line: #ead8cc;
  --primary: #9a5c47;
  --primary2: #c97961;
  --accent: #dca08a;
  --radius: 24px;
  --max: 1160px;
}
```

变量用途：

| 变量 | 控制内容 |
|---|---|
| `--bg` | 页面背景 |
| `--card` | 卡片和输入框底色 |
| `--ink` | 主文字颜色 |
| `--muted` | 次要文字颜色 |
| `--line` | 边框和分隔线 |
| `--primary` | 主品牌色、链接、时间线节点 |
| `--primary2` | 渐变辅助色 |
| `--accent` | 强调色 |
| `--radius` | 普通卡片圆角 |
| `--max` | 页面主体最大宽度 |

### 例：让页面更宽

```css
--max: 1280px;
```

宽屏会显示更多横向内容，但正文行长也会增加。研究型网站通常不建议无限加宽。

### 例：减小卡片圆角

```css
--radius: 16px;
```

注意 `.paper-card`、`.dataset-card` 等部分组件直接使用 `22px`，不会随 `--radius` 一起变化；如需完全统一，也要修改这些规则。

### 例：新增一种强调卡片，不影响原卡片

HTML：

```html
<article class="card warning-card">...</article>
```

CSS 末尾：

```css
.warning-card {
  border-color: #df9b53;
  background: #fff8e8;
}
```

推荐通过新增类实现局部变化，不要为了一个卡片直接改 `.card`，否则全站卡片都会变化。

---

## 15. 修改布局和移动端表现

当前主要布局类：

- `.hero`：首页首屏两列布局。
- `.grid`：通用网格。
- `.cards-2` / `.cards-3` / `.cards-4`：2、3、4 列卡片。
- `.paper-grid` / `.dataset-grid` / `.reference-grid`：动态内容的两列网格。
- `.stat-row`：首页四项统计。

响应式断点在 `style.css` 末尾：

```css
@media (max-width: 900px) { ... }
@media (max-width: 560px) { ... }
```

在 900px 以下：

- 首页首屏变为单列；
- 卡片网格变为单列；
- 导航变成折叠菜单；
- 页脚变为纵向布局。

在 560px 以下：

- 页面左右边距减小；
- 首页统计变为单列；
- 章节标题区变为上下排列。

每次改布局至少检查约 1440px、768px 和 375px 三种宽度。特别注意：

- 标题是否溢出；
- 按钮是否被挤出屏幕；
- 表格能否横向滚动；
- 图片是否变形；
- 手机导航能否打开并点击；
- 卡片中的长 URL 或长英文是否撑破容器。

---

## 16. 新增一个完整页面

假设要新增 `benchmarks.html`：

1. 复制一个结构相近的页面，例如 `overview.html`。
2. 修改 `<title>`、`description`、面包屑、`<h1>` 和正文。
3. 导航中加入新链接。
4. 将当前页对应链接设置为 `class="nav-link active"`，其他链接只保留 `class="nav-link"`。
5. 把导航同步到所有现有 HTML 页面。
6. 视需要在首页增加入口卡片。
7. 在页脚加入链接（如果它是重要一级页面）。

导航链接范例：

```html
<a class="nav-link" href="benchmarks.html">Benchmarks</a>
```

在 `benchmarks.html` 自己的导航中：

```html
<a class="nav-link active" href="benchmarks.html">Benchmarks</a>
```

最小页面主体：

```html
<main>
  <section class="page-hero">
    <div class="breadcrumb">Home / Benchmarks</div>
    <h1>WAM 评测与基准</h1>
    <p class="lead">整理世界预测、动作生成与闭环控制的评测方法。</p>
  </section>

  <section class="section">
    <div class="section-head">
      <div><span class="eyebrow">Benchmarks</span><h2>评测维度</h2></div>
    </div>
    <div class="grid cards-3">...</div>
  </section>
</main>
```

> 这个项目没有模板系统，所以页头和页脚重复写在每个 HTML 文件里。修改全站导航、品牌名或页脚时，需要同步修改 `index.html`、`overview.html`、`roadmap.html`、`papers.html`、`data.html`、`references.html` 和 `404.html`。漏改某页是最常见的问题之一。

---

## 17. 修改导航、品牌名和页脚

### 导航

每页都有：

```html
<nav class="site-nav" id="site-nav">...</nav>
```

当前页的链接带 `active`。复制导航到其他页面时，不要把同一个 `active` 状态带到所有页面。

### 品牌名

```html
<span class="brand-mark">W</span>
<span>
  <strong>WAM Survey</strong>
  <small>World Action Models</small>
</span>
```

品牌标记、名称和副标题也在每个页面重复存在，需全站同步。

### 页脚

```html
<footer class="site-footer">...</footer>
```

修改维护者、项目说明、版权年份或链接时，也要同步所有页面。

---

## 18. 内容写作与资料维护建议

为了让网站长期可信、可扩展，建议建立一致的编辑标准：

### 论文摘要

- `summary`：说明问题、方法和结论，不复述标题。
- `dataSummary`：尽量写清规模、模态、来源和是否公开。
- `contribution`：说明相对于前人增加了什么。
- `limitations`：区分作者明确承认的限制和本站判断；必要时写“本站判断”。

### 数据集描述

- 规模尽量保留单位和统计口径，例如轨迹数、小时数、任务数。
- 区分真实机器人、人类视频、仿真数据和合成数据。
- 说明是否含动作、语言、失败轨迹、力觉或深度等关键模态。
- 记录许可证、下载条件和商业使用限制。
- 数据规模可能更新时注明版本或访问日期。

### 引用

- 优先链接 DOI、arXiv、官方项目页或数据集官网。
- 不以搜索结果页、二次转载或不稳定网盘作为主要来源。
- 对快速变化的 2026 年工作，记录版本号或更新时间。
- 避免把宣传性表述直接写成已被独立验证的事实。

### 中英文和符号

- 中文正文使用中文全角标点。
- 模型名、数据集名保持官方拼写。
- 年份区间统一使用 `2024–2026` 或 `2024–26`，不要混用连字符样式。
- HTML 文件和 JSON 文件都应保存为 UTF-8，避免中文乱码。

---

## 19. 检查 JSON 是否有效

修改 JSON 后，如果整个动态区域突然为空，第一怀疑对象应是 JSON 语法。

使用 PowerShell 检查四个文件：

```powershell
Get-Content -Raw -Encoding UTF8 data/papers.json | ConvertFrom-Json | Out-Null
Get-Content -Raw -Encoding UTF8 data/datasets.json | ConvertFrom-Json | Out-Null
Get-Content -Raw -Encoding UTF8 data/timeline.json | ConvertFrom-Json | Out-Null
Get-Content -Raw -Encoding UTF8 data/references.json | ConvertFrom-Json | Out-Null
```

没有输出通常表示解析成功；出现错误时，根据提示的行列检查：

- 是否漏了逗号；
- 是否多了结尾逗号；
- 字符串是否漏写双引号；
- 正文中是否直接出现未转义的英文双引号；
- 是否误用了中文引号 `“”` 代替 JSON 双引号 `"`；
- `true`、`false` 和数字是否被错误写成其他格式。

正文中需要包含英文双引号时，应写成：

```json
"summary": "作者将该方法称为 \"joint prediction\"。"
```

---

## 20. 常见故障排查

### 页面打开了，但论文/数据集/时间线/参考文献为空

依次检查：

1. 是否通过 `http://localhost:8000` 访问，而不是双击 HTML；
2. JSON 是否能成功解析；
3. 文件路径和文件名大小写是否正确；
4. 浏览器开发者工具 Console 是否显示 `JSON loading failed`；
5. `main.js` 是否仍在页面底部正确引用。

### 新论文没有显示在首页

- 检查 `featured` 是否为布尔值 `true`；
- 检查它是否排在前 6 个 featured 对象之内；
- 检查 `papers.json` 是否整体有效；
- 强制刷新浏览器，排除缓存。

### 筛选按钮点了没有结果

`data-filter` 是文本匹配，不是固定枚举。确保该文字实际出现在论文的类型、架构、数据、标题或摘要中。

### 图片不显示

- 检查路径相对于当前 HTML 页面是否正确；
- 本项目所有 HTML 都在根目录，因此通常写 `assets/img/文件名`；
- 检查文件名大小写和扩展名；
- 不要在网页路径中写 Windows 反斜杠 `\`；应使用 `/`。

### 中文变成乱码

- 确认文件保存编码为 UTF-8；
- 不要删除 `<meta charset="utf-8">`；
- 命令行查看文件时显式指定 UTF-8；
- 避免用会以系统默认编码覆写文件的旧工具。

### 修改 CSS 后全站都变了

`.card`、`p`、`h2`、`table` 这类选择器影响范围很大。局部效果应新增专用类，例如 `.benchmark-card`，并在 HTML 中只给目标元素添加该类。

### 手机页面横向溢出

常见原因是：

- 表格没用 `.table-wrap`；
- 图片写死了像素宽度；
- 很长的 URL 或英文单词无法换行；
- 新增网格没有在 900px 断点改为单列。

---

## 21. 发布到 GitHub Pages

当前项目无需打包。提交并推送 HTML、CSS、JavaScript、JSON 和图片即可。

发布前建议确认：

- GitHub Pages 的来源是 `main` 分支根目录；
- 仓库中不存在只在本机有效的绝对路径；
- 文件名大小写与链接完全一致。Windows 本地通常不区分大小写，但 GitHub Pages 区分；
- 所有内部链接都使用相对路径；
- 新增文件已经被 Git 跟踪；
- 线上页面加载后再抽查动态卡片与图片。

典型 Git 流程：

```powershell
git status
git diff
git add WEBSITE_EDITING_GUIDE.md index.html data/papers.json
git commit -m "Update WAM survey content"
git push
```

`git add` 后只提交本次确实修改过的文件，不必照抄示例中的文件列表。

---

## 22. 一次内容更新的完整范例

假设要新增一篇论文，并在概述正文中引用它：

1. 在 `data/papers.json` 添加论文对象，分配唯一 `id`。
2. 若希望出现在首页，设置 `"featured": true`，并确认排序在前 6 项内。
3. 在 `data/references.json` 添加参考文献，分配新编号。
4. 在 `overview.html` 中添加一句分析和引用链接。
5. 如果论文引入新类型，在 `papers.html` 增加筛选按钮。
6. 如果首页统计仍写 `14+`，视需要更新数字。
7. 运行四个 JSON 解析检查。
8. 启动本地服务器。
9. 检查首页、论文页、概述页和参考文献页。
10. 在手机宽度检查卡片、标题和导航。
11. 查看变更后再提交。

正文引用例子：

```html
<p>
  Example-WAM 将动作评价与未来视频预测纳入同一训练目标
  <a href="papers.html#example-wam">[15]</a>。
</p>
```

---

## 23. 长期演进建议

当前架构非常适合小型研究门户：简单、透明、部署便宜。但内容继续增长后，可以按需求逐步升级：

1. **先格式化源文件**：把单行 HTML 和 JavaScript 格式化，提高可维护性。
2. **减少重复页头页脚**：当页面明显增多时，引入轻量静态站点生成器或模板机制。
3. **统一数据模型**：让论文和参考文献共享唯一标识，减少重复录入。
4. **自动生成统计数字**：让首页论文数和数据集数直接来自 JSON。
5. **添加数据校验**：为 JSON 增加 Schema，自动检查必需字段和重复 `id`。
6. **支持 BibTeX**：参考文献增长后，可从 BibTeX 生成 `references.json`。
7. **添加更新时间**：在论文、数据集或页面中展示最后核验日期。
8. **添加无障碍检查**：持续检查图片替代文字、颜色对比度、键盘导航和标题层级。

这些都不是日常改内容的前置条件。现阶段最稳妥的原则仍是：**正文改 HTML，重复增长的条目改 JSON，视觉改 CSS，生成逻辑改 JavaScript。**

---

## 24. 编辑速查表

| 任务 | 文件 | 是否需要改代码 |
|---|---|---:|
| 改一段介绍文字 | 对应 `.html` | 否 |
| 增加普通卡片 | 对应 `.html` | 否 |
| 增加论文 | `data/papers.json` | 否 |
| 增加数据集 | `data/datasets.json` | 否 |
| 增加时间线节点 | `data/timeline.json` | 否 |
| 增加参考文献 | `data/references.json` | 否 |
| 改首页推荐论文 | `data/papers.json` | 否 |
| 增加论文筛选按钮 | `papers.html` | 否 |
| 增加图片 | `assets/img/` + 对应 `.html` | 否 |
| 增加表格 | 对应 `.html` | 否 |
| 改颜色和圆角 | `assets/css/style.css` | 少量 CSS |
| 改动态卡片显示字段 | `assets/js/main.js` | 是 |
| 新增一级页面 | 新 `.html` + 所有页面导航 | 少量 HTML |
| 自动统计内容数量 | `assets/js/main.js` + `index.html` | 是 |

维护时如果不确定从哪里开始，就先回到第 1 节的文件映射表。这个项目最重要的边界只有一句话：**页面结构在 HTML，结构化研究条目在 JSON，样式在 CSS，自动生成规则在 JavaScript。**
