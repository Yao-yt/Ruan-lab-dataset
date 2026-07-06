# UI 改版方案

项目：`website_construction`  
对象：Ruan Lab zebrafish embryo development data website  
范围：信息架构、视觉设计、交互体验、组件规范、响应式与科研数据库规范  
限制：保持现有功能不变，仅优化视觉设计与交互体验

## 1. 项目现状概览

### 1.1 项目结构

当前项目是一个静态前端网站，主要由单个 HTML 文件和本地数据/图片资源组成。

```text
website_construction/
├── index.html                # 主页面、样式、交互逻辑集中在同一文件
├── data.txt                  # 轨迹网络边数据
├── cell_order.json           # 发育时间点与细胞类型顺序
├── color_config.json         # 细胞类型到胚层/组织域的配色映射
├── memory.md                 # 项目背景说明
├── singlecell_cover.png      # Single Cell 模块封面
├── browser_cover.png         # Browser 模块封面
├── network.png               # GRN 模块封面
├── Gene_expression.png       # Gene expression 图片
├── UMAP.png                  # UMAP 图片
├── RNA.png / ATAC.png / PET.png
└── 其他封面候选图片
```

### 1.2 技术与页面构成

当前网站使用原生 HTML/CSS/JavaScript，外部依赖包括：

- D3.js：用于绘制 single-cell trajectory SVG。
- Chart.js：已引入，但当前主要交互仍以 D3、图片和表格为主。
- 本地 JSON/TXT：用于细胞顺序、颜色映射和轨迹边数据。

当前页面包括：

- `Home`：实验室数据集介绍。
- `Zebrafish`：模块入口页，包含 Single Cell、Browser、Gene Regulatory Network 三张卡片。
- `Single Cell`：轨迹网络主视图，带 gene search、节点点击 modal、RNA/ATAC/PET tab、marker table。
- `GRN`：占位页，显示 Coming soon。
- `Browser`：通过按钮跳转外部 genome browser 地址。

## 2. 当前网站优点

1. 数据主题明确：项目围绕 zebrafish embryo development，数据类型包括 single-cell trajectory、genome browser、gene regulatory network，科研方向清晰。
2. 功能原型完整：已有导航、模块入口、轨迹图、节点 modal、gene search、marker table、tab 切换和图片查看。
3. 数据分层已有基础：`cell_order.json` 和 `color_config.json` 已经把时间点、细胞类型、胚层/组织域做了结构化表达，适合后续设计系统化。
4. 科研图像资产丰富：已有 UMAP、ATAC、PET、Gene expression、network 和 browser 截图，可用于首页背景、模块封面和数据说明。
5. 交互方向正确：用户可以从模块入口进入 single-cell 图谱，再点击节点查看细胞类型相关信息，符合科研数据库的探索路径。

## 3. 当前存在的问题

### 3.1 高严重程度

1. 信息架构不够像科研数据库门户  
   首页当前主要展示 “Ruan Lab Dataset” 和研究方向列表，缺少数据门户应有的核心入口：物种、发育阶段、细胞数量/细胞类型数量、数据类型、搜索入口、引用方式和数据版本。用户进入后无法立即判断这个数据库能查什么、怎么查、数据可信度如何。

2. 导航层级过浅且语义不足  
   当前导航只有 Home 和 Zebrafish，没有 Dataset、Single Cell、Browser、GRN、Documentation、Download、Citation 等科研数据库常见入口。二级页面依赖卡片和 Back 按钮，用户路径不够稳定。

3. 视觉系统缺失  
   页面大量使用内联样式，颜色、字号、间距、按钮、卡片、表格都以局部方式定义。相同类型组件在不同位置外观不完全一致，难以形成 AlphaFold Database、EMBL-EBI、Human Cell Atlas 那种可信、稳定的数据库气质。

4. 响应式布局基本缺失  
   当前未发现 viewport meta、media query 或移动端布局策略。模块卡片使用横向 flex，modal 固定宽高，single-cell 页面使用固定高度和大 SVG，移动端和窄屏下容易溢出、遮挡或难以操作。

5. 可访问性不足  
   图片缺少 `alt` 语义；按钮以普通 `div` 或内联按钮为主；modal 缺少 `role="dialog"`、焦点管理和 ESC 关闭规范；颜色作为主要区分方式但没有同时提供形状、文本或图例增强；搜索下拉缺少键盘导航和 ARIA 语义。

### 3.2 中严重程度

6. 首页视觉记忆点不足  
   首页背景和 zebrafish 主题关联弱，缺少胚胎发育、细胞图谱、基因组轨道或斑马鱼形态的视觉线索。当前页面更像普通文本说明页，不能快速建立“现代生物信息学数据库”的第一印象。

7. 页面层级不清  
   标题、说明文字、模块入口、数据图谱、表格和 modal 的视觉权重不够明确。比如 Single Cell 主视图进入后直接铺满 SVG，缺少页面标题、数据上下文、当前筛选状态和图例说明区域。

8. 数据可视化容器缺少科研工具感  
   D3 轨迹图本身有价值，但容器、工具栏、图例、缩放提示、节点状态、hover 信息、选中状态、loading/error 状态都还偏原型。表格和图片 tab 与主图之间视觉语言不统一。

9. 表格设计偏基础  
   marker table 有分页和高亮，但缺少 sticky header、列对齐规则、数值格式规则、排序/筛选状态、空状态和下载入口。科研用户通常需要快速比较 marker gene、score、p-value、表达量等指标。

10. Search 体验不完整  
    当前 gene search 支持前缀匹配和下拉建议，但没有全站搜索层级、模糊搜索、最近搜索、无结果建议、键盘上下选择和搜索结果分类。科研数据库中搜索通常是第一核心入口。

### 3.3 低严重程度

11. 字体默认系统感不够精细  
    当前主要使用 Arial。Arial 可用但不够现代，英文科研站点更适合使用 Inter、Source Sans 3、IBM Plex Sans 或 system UI，并为数据表使用等宽数字。

12. 间距尺度不统一  
    页面中同时出现 4、6、8、10、12、15、16、20、24、32、40、48、60 等值，但没有明确 spacing token。短期可保留视觉结果，长期应统一到 4/8px 基础网格。

13. 按钮和状态反馈不统一  
    不同模块按钮分别使用绿色、蓝色、紫色作为主按钮色，容易造成“每个功能一套品牌色”的感觉。更适合统一主色，模块色只作为标签、图例或轻量 accent。

14. 科研网站规范信息不足  
    当前缺少数据来源说明、版本号、发布日期、引用方式、联系人、使用许可、下载入口、方法摘要、FAQ/Help、数据质量说明。即使这些功能后续再做，UI 上也应预留稳定位置。

## 4. 改版目标

1. 建立现代、专业、可信的科研数据库视觉形象。
2. 让用户在首页 5 秒内理解：这是 zebrafish embryo development multi-omics data portal。
3. 保持现有功能不变，优先优化入口、层级、视觉一致性和交互反馈。
4. 建立可复用的 design token 和组件库，为后续扩展 Dataset、Download、Citation、Documentation 做准备。
5. 让 single-cell trajectory、gene search、modal、marker table 更像正式科研工具，而不是临时原型。

## 5. 设计原则

1. 数据优先  
   页面先回答用户能查什么、数据规模多大、入口在哪里，再展示装饰性内容。

2. 科研可信  
   使用克制、清晰、低噪音的界面。避免过度营销化的大渐变、大阴影和无意义装饰。

3. 生物信息学特色  
   视觉元素来自真实数据：细胞轨迹、胚胎发育时间轴、基因组 tracks、UMAP 点阵、斑马鱼轮廓或显微图像纹理。

4. 一致性优先  
   按钮、卡片、表格、搜索、modal、tab、toolbar 使用同一套尺寸、状态和语义。

5. 渐进实施  
   先建立视觉系统和首页，再改模块入口和 single-cell 工作区，最后处理响应式、可访问性和文档化。

## 6. 参考方向

参考站点与规范的启发：

- AlphaFold Database：强搜索入口、简洁页面层级、可信的科学数据呈现。
- EMBL-EBI：统一视觉框架、可访问性、可复用 web patterns。
- Human Cell Atlas Data Portal：数据规模指标、Explore Data 主路径、面向 open data 的信息组织。
- Apple Human Interface Guidelines：清晰层级、系统化间距、状态反馈、交互可预期。

这些参考不建议直接复制视觉样式，而是吸收共性：明确入口、稳定组件、可信信息、可访问交互和高质量留白。

## 7. 色彩方案

### 7.1 品牌主色

建议使用“深海青蓝 + 科研绿”作为主轴，既贴近 zebrafish/水生主题，也符合生物信息学数据库的专业感。

```text
Primary / Deep Teal     #0B5D66
Primary Hover           #084B52
Primary Soft            #E6F3F4
Accent / Bio Green      #2E7D5B
Accent Soft             #E8F4EE
Ink / Main Text         #172026
Ink / Secondary Text    #4E5D66
Ink / Muted Text        #71808A
Surface                 #FFFFFF
Surface Subtle          #F6F8FA
Border                  #D9E1E7
Warning                 #B36B00
Error                   #B42318
Focus Ring              #2D7FF9
```

### 7.2 数据可视化色彩

现有 `color_config.json` 中的胚层/组织域颜色可以保留，但建议做规范化：

- Retina：紫色系，保持识别度。
- Neuroectoderm：深绿色系。
- Surface ectoderm：洋红/玫红系。
- Mesoderm：蓝色系。
- Endoderm：橄榄/土金色系。
- Exe embryo：橙色系。
- Other：中性灰。

优化建议：

1. 保留现有分类语义，不随意改变用户对颜色的记忆。
2. 降低部分高饱和颜色在大面积区域的使用，只在点、线、标签、图例中使用。
3. 为色盲用户增加形状、线型或标签辅助，不只依赖颜色。
4. 页面品牌主色与数据色分离，避免按钮颜色和数据类别颜色混淆。

## 8. 字体方案

### 8.1 英文字体

推荐优先级：

```css
font-family: Inter, "Source Sans 3", -apple-system, BlinkMacSystemFont, "Segoe UI", Arial, sans-serif;
```

理由：

- Inter / Source Sans 3 适合现代数据产品。
- 系统字体符合 Apple HIG 的清晰、稳定、跨平台原则。
- 保留 Arial fallback，避免字体加载失败影响可读性。

### 8.2 数据与数字

表格、坐标、统计数字建议使用 tabular numbers：

```css
font-variant-numeric: tabular-nums;
```

基因名、染色体位置、ID 可使用等宽字体：

```css
font-family: "Roboto Mono", "SFMono-Regular", Consolas, monospace;
```

### 8.3 字号层级

```text
Display / Hero Title       48px / 56px
Page Title                 32px / 40px
Section Title              24px / 32px
Card Title                 18px / 26px
Body                       16px / 24px
Small Body                 14px / 20px
Caption                    12px / 16px
Table                      13px / 18px
```

## 9. 间距系统

建议采用 4px 基础网格，常用 token：

```text
space-1   4px
space-2   8px
space-3   12px
space-4   16px
space-5   20px
space-6   24px
space-8   32px
space-10  40px
space-12  48px
space-16  64px
space-20  80px
```

页面布局建议：

- 页面左右 padding：桌面 40-64px，平板 32px，移动端 20px。
- Section 间距：64-96px。
- Card 内边距：20-24px。
- Toolbar 高度：48-56px。
- 表格行高：36-44px。

## 10. Icon 方案

推荐使用线性图标体系，风格轻量、统一、语义明确。

建议图标类别：

- Search：搜索。
- Database：数据集。
- Network：GRN。
- Activity / LineChart：轨迹或表达趋势。
- Dna：基因组/browser。
- Download：下载。
- ExternalLink：外部 Browser。
- Info：方法/说明。
- Filter：筛选。
- ChevronLeft / ChevronRight：返回和分页。
- ZoomIn / ZoomOut / RotateCcw：图片和图谱查看。

规范：

1. 图标尺寸默认 16px 或 20px。
2. 图标按钮需要 tooltip 或 aria-label。
3. 图标只辅助理解，不替代关键文本。
4. 避免手写箭头符号，统一使用图标组件。

## 11. 页面布局方案

### 11.1 全站布局

建议采用固定顶部导航 + 内容区 + 页脚：

```text
Global Header
├── Logo / Database Name
├── Navigation
│   ├── Home
│   ├── Datasets
│   ├── Single Cell
│   ├── Genome Browser
│   ├── GRN
│   └── Help / Citation
└── Global Search

Main Content

Footer
├── Ruan Lab / Zhejiang University
├── Citation
├── Contact
├── Data version
└── License
```

### 11.2 导航设计

当前 Home / Zebrafish 的导航应升级为数据库型导航：

- Home：概览、数据入口、搜索。
- Dataset：物种、发育阶段、数据类型、下载。
- Single Cell：轨迹图谱入口。
- Browser：基因组轨道入口，带 external link 标识。
- GRN：若未完成，标记为 Coming soon，而不是让用户误以为功能完整。
- Help：使用说明、FAQ、Citation、Contact。

导航状态：

- 当前页面高亮。
- hover 状态轻量背景。
- focus 状态有明显 focus ring。
- 外部链接使用 external link icon。

## 12. 首页 Hero Section 设计

### 12.1 首页目标

首页应回答四个问题：

1. 这是什么数据库？
2. 覆盖哪些发育阶段和数据类型？
3. 用户能马上搜索什么？
4. 主要模块入口在哪里？

### 12.2 推荐 Hero 文案结构

标题：

```text
Zebrafish Embryo Development Data Portal
```

副标题：

```text
Explore single-cell trajectories, genomic tracks, and regulatory networks across zebrafish embryogenesis.
```

主操作：

- Search gene or cell type
- Explore Single Cell Atlas
- Open Genome Browser

数据指标：

- 11 developmental stages
- 70+ cell types
- Multi-omics modules
- Single-cell trajectory map

### 12.3 背景设计建议

首页背景应与 zebrafish 主题结合，但保持科研克制。

优先方案：

1. 使用真实数据图像做抽象化背景  
   从 `singlecell_cover.png`、`network.png` 或 `browser_cover.png` 中裁切局部，叠加低透明度网格和浅色遮罩。这样背景来自真实数据，可信且有生物信息学特色。

2. 加入斑马鱼胚胎发育线索  
   在 Hero 右侧或背景层使用半透明 zebrafish embryo silhouette / timeline，不建议使用卡通插画。视觉应该像科研图谱，而不是科普海报。

3. 建立“水域 + 细胞图谱”气质  
   背景色可用浅青灰到白色的极轻过渡，点阵/连线来自轨迹图，局部强调色来自胚层色。

不建议：

- 大面积深色背景，可能降低数据门户的可读性。
- 纯装饰渐变或抽象光斑，缺少科研可信度。
- 卡通斑马鱼插画，会削弱专业感。

### 12.4 Hero 布局

```text
Hero Section
├── Left
│   ├── Database label
│   ├── H1
│   ├── Description
│   ├── Global search bar
│   └── Primary actions
└── Right / Background
    ├── Zebrafish developmental timeline
    ├── UMAP / trajectory preview
    └── subtle data grid
```

## 13. Dashboard 设计

当前 Zebrafish 模块入口页建议升级为 Dashboard。

### 13.1 Dashboard 内容

```text
Dashboard
├── Page header
│   ├── Zebrafish Atlas
│   ├── description
│   └── data version / last updated
├── KPI strip
│   ├── stages
│   ├── cell types
│   ├── modules
│   └── available files
├── Module cards
│   ├── Single Cell Atlas
│   ├── Genome Browser
│   └── Gene Regulatory Network
├── Development timeline
└── Dataset notes / citation
```

### 13.2 Single Cell 工作区

Single Cell 页面建议改为科研工具工作区：

```text
Single Cell Atlas
├── Top toolbar
│   ├── breadcrumb
│   ├── page title
│   ├── gene/cell type search
│   └── view controls
├── Left panel
│   ├── filters
│   ├── germ layer legend
│   └── stage selector
├── Main canvas
│   └── trajectory SVG
└── Right/Modal detail
    ├── selected cell type
    ├── UMAP
    ├── marker table
    ├── ATAC
    └── PET
```

短期不必改变功能结构，可先用视觉方式模拟 toolbar、legend panel 和 detail modal。

## 14. Card Design

### 14.1 模块卡片

卡片应统一：

```text
Card
├── Media preview
├── Module label
├── Title
├── Description
├── Metadata chips
└── Action row
```

建议属性：

- Border radius：8px。
- Border：1px solid `Border`。
- Shadow：默认不使用或极轻阴影，hover 才增加。
- Image height：180-220px，使用一致比例。
- Action：统一主按钮或 text link，不按模块随意换主按钮颜色。

### 14.2 状态标签

示例：

- Available
- External
- Coming soon
- Beta

GRN 当前应明确显示 `Coming soon`，降低用户误解。

## 15. Table Design

Marker table 应按科研数据表规范优化：

1. Header sticky，滚动时保持列名可见。
2. 数值列右对齐，文本列左对齐。
3. 使用 tabular numbers。
4. hover 行高亮轻量，不使用过饱和颜色。
5. 当前搜索命中使用左侧竖线或浅色背景，不只靠黄色底。
6. 分页按钮尺寸一致，disabled 状态明确。
7. 预留排序状态图标。
8. 空状态显示 “No marker genes found for this query.”。
9. 长基因名或细胞名使用 tooltip 或省略号。
10. 表格上方显示数据来源、当前 cell type、总行数。

## 16. Search Design

### 16.1 全站搜索

首页和导航建议提供统一搜索：

```text
Search genes, cell types, stages, or modules
```

搜索结果分类：

- Genes
- Cell types
- Development stages
- Modules

### 16.2 Gene Search

当前 gene search 可以保留，但增强体验：

1. 支持键盘上下选择和 Enter。
2. 支持无结果状态。
3. 支持最近搜索或常见 gene chips。
4. 搜索框左侧加入 search icon。
5. 输入后展示匹配类型，如 Gene。
6. 搜索结果点击后清晰显示当前查看对象。

### 16.3 Modal 内搜索

Marker table 的搜索应与全站搜索视觉一致，但范围说明要明确：

```text
Search marker genes in selected cell type
```

## 17. 数据可视化风格

### 17.1 Trajectory SVG

建议保留当前 D3 绘图逻辑，但优化视觉外壳和状态：

- 添加图标题和说明。
- 加入 zoom controls。
- hover 节点显示 tooltip：stage、cell type、germ layer、connected edges。
- selected 节点有高亮 ring。
- 线条权重映射更明确，0 weight 的虚线/弱线需要图例解释。
- 图例固定在左侧或右侧 panel，避免遮挡图。
- 胚层颜色与 `color_config.json` 保持一致。

### 17.2 UMAP / ATAC / PET / Expression 图片

当前图片以静态图方式展示，可以先优化容器：

- 图片容器使用统一 toolbar。
- zoom in/out/reset 使用图标按钮。
- 图片加载失败显示 error state。
- 添加 caption：数据类型、当前选择、单位或来源。
- 高分辨率图片使用 `object-fit: contain`，避免裁切科研信息。

## 18. Responsive Design

### 18.1 断点

```text
Mobile     < 640px
Tablet     640-1024px
Desktop    1024-1440px
Wide       > 1440px
```

### 18.2 首页

- 桌面：Hero 左文案右数据视觉。
- 平板：上下布局，搜索保持首屏可见。
- 移动端：只保留关键标题、搜索、两个主入口，背景降低复杂度。

### 18.3 模块卡片

- 桌面：3 列。
- 平板：2 列。
- 移动端：1 列。

### 18.4 Single Cell 页面

- 桌面：toolbar + side panel + canvas。
- 平板：legend/filter 可折叠。
- 移动端：默认显示搜索和当前图谱，详情以 bottom sheet 或全屏 modal 打开。

### 18.5 Modal

- 桌面：最大宽度 1100-1200px。
- 移动端：全屏 modal，tab 置顶，表格横向滚动。

## 19. Accessibility

需要补齐：

1. 添加 viewport meta。
2. 所有图片添加有意义的 alt。
3. 导航使用语义化 `<nav>`，当前项使用 `aria-current="page"`。
4. modal 使用 `role="dialog"` 和 `aria-modal="true"`。
5. 搜索下拉使用 combobox/listbox 语义。
6. 图标按钮提供 aria-label。
7. 键盘可完成导航、搜索、tab 切换和关闭 modal。
8. focus ring 不可移除，颜色对比度符合 WCAG AA。
9. 数据颜色不作为唯一编码方式。
10. 动画尊重 `prefers-reduced-motion`。

## 20. 科研数据库网站规范

建议补齐以下信息模块：

- Dataset overview。
- Data version / last updated。
- Methods summary。
- Citation。
- Data download。
- Contact。
- License。
- Help / FAQ。
- External browser 说明。
- Module availability 状态。
- 数据质量或限制说明。

首页和页脚至少应显示：

```text
Ruan Lab, Life Sciences Institute, Zhejiang University
Data version
Contact
Citation
```

## 21. 用户浏览路径

### 21.1 新用户路径

```text
Home
→ Understand dataset scope
→ Search gene / choose module
→ Open Single Cell Atlas
→ Click cell type node
→ Inspect UMAP / marker table / ATAC / PET
```

### 21.2 熟练用户路径

```text
Home or Header Search
→ Type gene
→ Open gene expression modal
→ Compare cell type or stage
```

### 21.3 Genome Browser 用户路径

```text
Home
→ Genome Browser card
→ External browser opens
→ User understands it is an external tool
```

### 21.4 GRN 用户路径

```text
Dashboard
→ GRN card shows Coming soon
→ User can read planned scope or subscribe/contact
```

## 22. 动画规范

动画应服务于状态反馈，不做装饰性表演。

```text
Duration fast       120ms
Duration normal     180ms
Duration slow       240ms
Easing              cubic-bezier(0.2, 0, 0, 1)
```

应用场景：

- Card hover：轻微上移 2px 或边框变色。
- Dropdown：淡入 + 轻微位移。
- Modal：淡入，不做大幅缩放。
- Tab 切换：内容轻微 fade。
- Loading：使用 skeleton 或 progress indicator。

禁用：

- 大面积背景漂移动画。
- 持续闪烁。
- 影响阅读的数据图动画。

## 23. Design Token

建议第一阶段建立以下 token。

```text
color.background.page       #F6F8FA
color.background.surface    #FFFFFF
color.background.soft       #E6F3F4
color.text.primary          #172026
color.text.secondary        #4E5D66
color.text.muted            #71808A
color.border.default        #D9E1E7
color.brand.primary         #0B5D66
color.brand.primaryHover    #084B52
color.brand.accent          #2E7D5B
color.state.focus           #2D7FF9
color.state.warning         #B36B00
color.state.error           #B42318

font.family.sans            Inter, Source Sans 3, system-ui, sans-serif
font.family.mono            Roboto Mono, SFMono-Regular, Consolas, monospace

radius.sm                   4px
radius.md                   8px
radius.lg                   12px

shadow.card                 0 1px 2px rgba(16, 24, 40, 0.06)
shadow.popover              0 12px 32px rgba(16, 24, 40, 0.14)

space.1                     4px
space.2                     8px
space.3                     12px
space.4                     16px
space.6                     24px
space.8                     32px
space.10                    40px
space.12                    48px
space.16                    64px
```

## 24. Component Library

建议逐步抽象以下组件，即使继续使用静态 HTML，也应按组件思维统一样式。

### 24.1 基础组件

- Button：primary、secondary、ghost、icon。
- Input：search、default、compact。
- Select / Filter chip。
- Badge：Available、External、Coming soon、Beta。
- Card：module card、metric card、info card。
- Tabs：RNA、ATAC、PET。
- Table：data table、pagination。
- Modal：detail modal、image modal。
- Tooltip。
- Breadcrumb。
- Toolbar。

### 24.2 科研专用组件

- Dataset metric strip。
- Development stage timeline。
- Germ layer legend。
- Gene search combobox。
- Cell type detail panel。
- Visualization canvas container。
- External browser launch card。
- Citation block。

## 25. 分阶段实施计划

### Phase 0：设计审计与备份

目标：确保功能不变，建立改版基线。

任务：

1. 记录当前所有页面和交互路径。
2. 截图保存 Home、Zebrafish、Single Cell、modal、gene expression modal。
3. 确认当前外部 browser 链接、data.txt、JSON 配置均可用。
4. 建立视觉回归检查清单。

交付：

- 当前状态截图。
- 页面/组件清单。
- 风险清单。

### Phase 1：Design Token 与基础 CSS

目标：不改变页面结构，先统一视觉语言。

任务：

1. 建立颜色、字体、间距、圆角、阴影 token。
2. 替换散落的颜色与字号。
3. 定义全局 body、heading、link、button、input、table 基础样式。
4. 加入 viewport meta。
5. 保留所有现有 JS 功能。

交付：

- 统一基础样式。
- 不改变功能的视觉统一版本。

### Phase 2：首页改版

目标：首页从实验室说明页升级为科研数据门户首页。

任务：

1. 新增 zebrafish/data-themed Hero。
2. 加入全站搜索入口。
3. 加入数据指标 strip。
4. 加入模块入口卡片。
5. 加入数据来源、版本、引用提示。
6. 使用真实数据图像作为背景或视觉元素。

交付：

- 现代数据库首页。
- 清晰主路径：Search / Explore Single Cell / Open Browser。

### Phase 3：Zebrafish Dashboard 与模块卡片

目标：把当前 Zebrafish 入口页升级为模块 dashboard。

任务：

1. 重构页面标题、说明和 KPI。
2. 统一三张模块卡片视觉。
3. Browser 卡片标记 External。
4. GRN 卡片标记 Coming soon。
5. 加入 development timeline 预览。

交付：

- 结构清晰的 zebrafish atlas dashboard。

### Phase 4：Single Cell 工作区视觉优化

目标：保持 D3 功能不变，提升科研工具感。

任务：

1. 增加 top toolbar 和 breadcrumb。
2. 统一 gene search 样式。
3. 增强 SVG 容器、图例、hover/selected 状态。
4. 优化 modal 视觉结构。
5. 优化 UMAP、RNA/ATAC/PET tab 区域。
6. 优化 marker table。

交付：

- 更专业的 single-cell trajectory explorer。

### Phase 5：响应式与可访问性

目标：让网站在桌面、平板和移动端可用，并符合基础可访问性规范。

任务：

1. 加入响应式断点。
2. 模块卡片从 3 列适配到 1 列。
3. modal 移动端全屏。
4. 搜索和 tab 支持键盘。
5. 图片补充 alt。
6. modal 增加 dialog 语义。
7. focus ring、颜色对比、reduced motion 检查。

交付：

- 可访问性基础合格版本。
- 移动端可浏览版本。

### Phase 6：科研规范补齐

目标：提升数据库可信度和可引用性。

任务：

1. 增加 Citation 区域。
2. 增加 Data version / last updated。
3. 增加 Methods summary。
4. 增加 Contact / Help。
5. 增加 Download 入口占位或真实链接。
6. 明确数据限制和模块状态。

交付：

- 更完整的科研数据库信息闭环。

## 26. 优先级建议

最高优先级：

1. 首页 Hero + 数据门户化。
2. Design token 和统一组件视觉。
3. 响应式基础。
4. Search 和 modal 体验。
5. 科研可信信息：版本、引用、联系、方法摘要。

可以延后：

1. 完整组件化重构。
2. 高级筛选系统。
3. GRN 功能开发。
4. 全站真实后端搜索。
5. 数据下载管理系统。

## 27. 验收标准

视觉验收：

- 首页第一屏能明确传达 zebrafish embryo development data portal。
- 模块入口、按钮、卡片、表格、modal 视觉一致。
- 主色、数据色和状态色不混淆。
- 页面不再呈现临时原型感。

交互验收：

- 现有 Home、Zebrafish、Single Cell、Browser、GRN 路径保持可用。
- Gene search、节点点击、modal、tab、marker table、分页保持功能不变。
- 移动端不会出现严重内容遮挡或不可点击。

科研规范验收：

- 用户能找到数据范围、模块状态、引用方式、联系方式和数据版本。
- 数据图例和颜色语义清楚。
- 外部 browser 链接明确标识。

## 28. 结论

当前网站已经具备科研数据门户的核心功能雏形，但视觉和信息架构仍停留在实验原型阶段。改版不应优先追求装饰，而应把真实数据、清晰入口、统一组件和科研可信信息组织起来。

推荐的整体方向是：以 zebrafish embryo development 为首页第一视觉信号，以 gene/cell type search 为核心入口，以 Single Cell、Genome Browser、GRN 为主模块，以统一 design token 和组件库支撑后续扩展。这样可以在不破坏现有功能的前提下，让网站逐步接近 AlphaFold Database、EMBL-EBI 和 Human Cell Atlas 这类现代科研数据库的专业质感。
