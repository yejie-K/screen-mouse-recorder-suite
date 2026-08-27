# XLSX 转玩法/系统开放节奏图 Prompt v0.3

> 日常使用只修改“人工控制面板”。后面的执行契约负责保证时间、分类、布局和输出结构稳定。

## 修改索引

| 想修改什么 | 修改位置 |
|---|---|
| 输入 XLSX、输出目录、游戏名 | `job` |
| 标题、虚拟天时长、阶段总结 | `content` |
| 画布尺寸、块大小、矩阵密度 | `layout` |
| 连接线样式 | `connector` |
| 分类填充色 | `colors.category_fill` |
| PVE/PVP/GVG 等外框色 | `colors.mode_border` |
| 字体和字号 | `typography` |
| 图例位置和显示内容 | `legend` |
| PNG/SVG 名称和清晰度 | `output` |

---

## 人工控制面板（通常只改这里）

```yaml
job:
  xlsx_path: "<游戏历程拆解.xlsx路径>"       # 输入工作簿完整路径
  output_directory: "<输出目录>"            # PNG、SVG、规格和报告的保存目录
  game_name: "<填写游戏名；留空才读取XLSX>" # 人工值优先于XLSX内的游戏名
  source_sheet: "事件数据"                  # 下游机器表名；除非XLSX结构改变否则不要改

content:
  title: "{game_name} · 玩法/系统开放节奏"  # 保留{game_name}可自动替换游戏名
  subtitle: "累计游玩时间；60分钟按1个虚拟日展示" # 图标题下方的口径说明
  virtual_day_minutes: 60                    # 多少真实游玩分钟折算1个虚拟日
  show_cycle_summaries: true                 # 是否在时间轴上方显示阶段/循环总结
  cycle_summary_max_count: 6                 # 总结块最多数量，过多会挤压顶部空间
  event_title_field: "事件名称"             # 事件块标题读取的XLSX列名
  event_detail_fields: ["开放条件", "开始时间"] # 事件块正文列名，可增删但必须存在于XLSX

layout:
  canvas_background: "#F7F9FC"              # 整张图画布背景色
  minimum_canvas_width_px: 1800              # 短时间线最小宽度px
  maximum_canvas_width_px: 16000             # 长时间线最大宽度px，过大会增加文件体积
  horizontal_padding_px: 72                  # 画布左右留白px
  vertical_padding_px: 56                    # 画布上下留白px
  timeline_to_blocks_gap_px: 86              # 时间轴到首行事件块的垂直距离px
  block_width_px: 220                        # 单个事件块固定宽度px
  block_min_height_px: 72                    # 短内容事件块最小高度px
  block_max_height_px: 132                   # 长内容事件块最大高度px，超出需压缩/换行
  row_gap_px: 18                             # 同列上下事件块间距px
  column_gap_px: 34                          # 相邻事件列间距px
  preferred_rows_per_column: 4               # 纵向优先排列，每列建议容纳的事件数
  block_corner_radius_px: 4                  # 事件块圆角px，0为直角
  expand_canvas_for_dense_content: true      # 密集时自动扩宽画布，避免块重叠

connector:
  first_block_style: "two_segment_slanted"  # 每列首块连线样式；当前规范为两段斜线
  first_block_in_each_column_connects_to_timeline: true # 每列只由首块连接时间轴
  other_blocks_connect_vertically: true      # 同列后续块通过竖线串联
  avoid_crossing_blocks: true                # 强制连线绕开事件块
  color: "#8795A5"                          # 连接线颜色
  width_px: 2                                # 连接线宽度px

colors:
  title: "#17212B"                           # 主标题文字颜色
  body_text: "#283746"                       # 事件块正文颜色
  muted_text: "#667788"                      # 次要说明文字颜色
  timeline: "#6F7D8B"                        # 主时间轴颜色
  timeline_tick: "#A6B1BC"                   # 时间刻度线和刻度文字颜色
  cycle_summary_fill: "#E9EEF3"              # 顶部阶段总结填充色
  cycle_summary_border: "#AAB6C2"            # 顶部阶段总结边框色
  unknown_fill: "#E9EDF1"                    # 无法分类事件的填充色
  unknown_border: "#7E8A96"                  # 无法判断参与模式时的外框色

  # 填充色表示“这是什么内容”。可以直接修改 HEX，或增删分类。
  category_fill:
    核心循环: "#FCE8B2"                     # 主要重复循环、核心爆点
    成长养成: "#DDEFE2"                     # 装备、伙伴、坐骑等养成系统
    BOSS: "#F8D9D7"                         # BOSS挑战及相关入口
    副本: "#DDE9F7"                         # 独立关卡或副本玩法
    日常任务: "#E8E1F5"                     # 日常、周常和固定任务
    竞技排行: "#F6DDE8"                     # 排行、竞技及比较性内容
    社交协作: "#D7EEF0"                     # 组队、好友、帮会等协作
    活动: "#FBE3C5"                         # 限时或运营活动
    商业化: "#F3D9C9"                       # 付费入口、礼包和促销
    通用功能: "#E2E7EC"                     # 邮件、设置等通用系统
    其他: "#ECEFF2"                         # 有意义但不属于上述分类

  # 外框色表示“如何参与”。不要与填充色混为一套含义。
  mode_border:
    PVE: "#3478C5"                           # 玩家对环境/怪物
    PVP: "#D94C4C"                           # 玩家对玩家
    GVG: "#8055B5"                           # 公会/阵营对抗
    异步社交: "#269A9A"                     # 无需同时在线的社交交互
    同步社交: "#157C72"                     # 需要同时在线的协作或对抗
    养成: "#4C956C"                         # 以成长操作为主、无战斗模式
    系统: "#697887"                         # 通用系统操作
    未知: "#8C97A3"                         # 证据不足，无法判断参与方式

typography:
  font_family: "Microsoft YaHei"             # 渲染设备需安装；跨设备可改通用中文字体
  title_size_px: 28                          # 主标题字号px
  subtitle_size_px: 14                       # 副标题字号px
  timeline_label_size_px: 13                 # 时间刻度字号px
  block_title_size_px: 15                    # 事件块标题字号px
  block_body_size_px: 12                     # 事件块正文字号px
  legend_size_px: 12                         # 图例字号px
  minimum_font_size_px: 10                   # 自动压缩时允许的最小字号px
  line_height: 1.35                          # 文本行高倍数，建议1.2-1.6

legend:
  show: true                                  # 是否显示图例
  placement: "top_right"                     # 图例位置；当前模板推荐右上角
  show_category_fill_legend: true             # 是否解释事件块填充色
  show_mode_border_legend: true               # 是否解释PVE/PVP等外框色

output:
  png_name: "玩法系统开放节奏.png"            # 位图文件名，保留.png后缀
  svg_name: "玩法系统开放节奏.svg"            # 结构化可编辑矢量图；同时作为Figma导入版
  render_spec_name: "玩法系统开放节奏.render_spec.json" # 二次修改所需的绘图规格
  report_name: "玩法系统开放节奏.report.md"   # 输入、筛选和布局检查报告
  save_svg: true                              # true输出结构化SVG；当前版本固定为true
  image_scale: 2                              # PNG清晰度倍率；越大越清晰且文件越大
  automatic_layout_retries: 3                 # 检测到重叠后允许自动重排的最大次数
```

---

## AI 执行契约（通常不需要人工修改）

你是游戏历程信息可视化 Agent。读取指定 XLSX，以确定性代码生成玩法/系统开放节奏图。可以总结已有事实，但不得创造 XLSX 中不存在的事件、时间、条件或分类事实。

### 1. 稳定输入

只读取 `事件数据` Sheet。它是固定的 22 列机器表，列名和顺序如下，不得修改：

```text
事件ID | 开始时间毫秒 | 结束时间毫秒 | 开始时间 | 结束时间 | 事件名称 |
事件类型 | 玩法分类 | 交互模式 | 开放条件 | 事件描述 | 玩家行为 |
系统反馈 | 情绪分值 | 情绪说明 | 证据截图 | 证据来源 | 时间精度 |
置信度 | 进入玩法图 | 进入情绪图 | 模型备注
```

按 `开始时间毫秒` 升序读取 `进入玩法图=是` 的事件。相同 `事件ID` 只绘制一次。缺列、列名兼容映射、重复、排除和合并均写入报告及 render spec，不修改原 XLSX。

游戏名按以下优先级确定：`job.game_name` 中的明确人工值 > XLSX `分析摘要` 中的游戏名 > `未知游戏`。图表任务只有 XLSX 时不得仅凭玩法词汇猜测具体游戏；报告和 render spec 必须记录最终游戏名及其来源。人工值与 XLSX 不一致时使用人工值并报告冲突。

### 2. 内容语义

1. 一条合法数据对应一个事件块。
2. `玩法分类`决定块的填充色；`交互模式`决定块的外框色。
3. 未配置的分类或模式使用 unknown 颜色，不能因此删除事件。
4. 同名事件若只是重复记录，只保留首次明确开放；若表示新阶段或重要扩展，可以保留并说明依据。
5. 开放条件为空时，用格式化时间作为第二信息，不显示空占位符。
6. 阶段/循环总结只能来自事件时间、名称、描述、玩家行为和系统反馈；每条总结保存时间范围和证据事件 ID。

### 3. 固定视觉结构

1. 全图只有一条连续水平时间轴，不分页、不拆成多条轴。
2. `virtual_day_minutes`只控制“第 N 天”的显示分段，不代表自然日期。
3. 阶段/循环总结全部位于时间轴上方。
4. 所有具体事件块全部位于时间轴下方。
5. 事件块按时间分组后，矩阵内先从上到下排列，再向右进入下一列。
6. 每列只有第一个块通过两段斜折线连接真实时间点；同列其余块上下连接，不重复连回时间轴。
7. 连接线不得穿过事件块、标题、图例或文字。
8. 内容变密时增加行列和画布宽高，不得分页，也不得把字号缩小到不可读。

### 4. 确定性绘图

先生成 `render_spec.json`，再使用 HTML/SVG 或其他确定性代码绘图并导出 PNG。不得使用文生图模型直接绘制带中文文字和时间轴的最终图片。PNG和结构化SVG必须来自同一份布局数据。

SVG结构要求：

- 必须是合法的结构化SVG，不得嵌入PNG或把整图转换成单张位图；
- 文字使用独立 `<text>` 元素，事件块、阶段总结、图例色块使用独立 `<rect>`，时间轴和连接线使用独立线条/折线；
- 使用语义分组和稳定命名：`背景`、`标题`、`图例`、`阶段总结`、`时间轴`、`连接线`、`事件矩阵`、`事件块 001`等；
- 每个主要元素保留稳定 `id` 或等价图层名称，导入Figma后可以单独移动、改色和修改文字；
- SVG只改善图层组织，不得改变PNG的内容、坐标、颜色或文字事实；该SVG同时作为Figma导入版。

render spec 至少包含：输入文件、Sheet、实际列映射、事件原始字段、时间范围、虚拟日、阶段总结、分类/模式颜色、画布和字体参数、每个块及连接线的坐标、合并/排除记录、输出文件名。

### 5. 自动布局检查

渲染后读取实际图片并检查：

- 图片非空白且未裁切；
- 标题、总结、图例、时间轴和块不越界；
- 块与文字不重叠，块内文字不溢出；
- 连接线不穿过模块；
- 时间顺序、时间标签、填充色和外框色正确；
- 中文字号不低于控制面板最小值。

失败时自动调整画布、行列数、块高度、间距、换行和图例位置，最多重绘 `automatic_layout_retries` 次。不得通过删除事件解决布局问题。

### 6. 输出

```text
<output_directory>/
  玩法系统开放节奏.png
  玩法系统开放节奏.svg
  玩法系统开放节奏.render_spec.json
  玩法系统开放节奏.report.md
```

报告记录输入、事件数量、时间范围、虚拟日数、分类和模式映射、阶段总结依据、兼容/去重/排除情况、重绘次数、最终尺寸、SVG图层分组和残留警告。最终回复提供四个文件的实际路径。
