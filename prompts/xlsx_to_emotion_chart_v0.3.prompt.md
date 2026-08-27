# XLSX 转情绪变化图 Prompt v0.3

> 日常使用只修改“人工控制面板”。后面的执行契约负责保证评分不被改写、节点可追溯、图表可复现。

## 修改索引

| 想修改什么 | 修改位置 |
|---|---|
| 输入 XLSX、输出目录、游戏名 | `job` |
| 标题、阶段总结和重点节点 | `content` |
| 折线/阶梯线/散点、平滑和面积 | `chart` |
| 分值上下限和零线 | `score` |
| 画布、密度和注释轨道 | `layout` |
| 正向/中性/负向配色 | `colors` |
| 标签显示与密集模式 | `labels` |
| 字体和字号 | `typography` |
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
  title: "{game_name} · 玩家情绪变化"       # 保留{game_name}可自动替换游戏名
  subtitle: "节点为已记录事件，不代表事件之间持续测量" # 图标题下方的方法说明
  show_stage_summaries: true                  # 是否在图表上方显示阶段总结
  stage_summary_max_count: 6                  # 阶段总结最多数量，过多会挤压绘图区
  highlight_positive_peaks: true              # 是否突出正向最高点
  highlight_negative_troughs: true            # 是否突出负向最低点
  highlight_turning_points: true              # 是否突出趋势转折点

chart:
  type: "line_with_nodes"                    # line_with_nodes折线 / step_line阶梯 / scatter散点
  smooth_curve: false                         # true使用平滑曲线；可能弱化离散事件边界
  show_area_fill: false                       # true填充折线下方区域
  line_width_px: 3                            # 主线宽度px
  node_radius_px: 7                           # 普通事件节点半径px
  highlighted_node_radius_px: 10              # 重点节点半径px，应不小于普通节点
  show_vertical_event_guides: true            # 是否显示节点到时间轴的垂直辅助线
  show_zero_baseline: true                    # 是否显示情绪分值0基准线

score:
  minimum: -2                                 # 情绪轴最低分，需与评分表口径一致
  maximum: 3                                  # 情绪轴最高分，需与评分表口径一致
  baseline: 0                                 # 中性基准分，通常保持0
  clamp_out_of_range: false                   # false时越界分值不修正并进入报告
  missing_score_policy: "exclude_and_report" # 缺分事件处理：排除绘图并在报告列出

layout:
  canvas_background: "#FAFBFC"               # 整张图画布背景色
  plot_background: "#FFFFFF"                 # 情绪折线绘图区背景色
  minimum_canvas_width_px: 1800              # 短时间线最小宽度px
  maximum_canvas_width_px: 16000             # 长时间线最大宽度px，过大会增加文件体积
  canvas_height_px: 980                      # 固定画布高度px
  horizontal_padding_px: 90                  # 画布左右留白px
  top_padding_px: 110                        # 标题、图例和阶段总结顶部空间px
  bottom_padding_px: 130                     # 时间轴标签和注释底部空间px
  minimum_pixels_per_event: 95               # 每个事件期望的最小横向空间px
  minimum_pixels_per_minute: 5               # 每分钟期望的最小横向空间px
  annotation_lane_count: 4                   # 标签错层轨道数，事件密集时可增大
  annotation_lane_gap_px: 14                 # 相邻标签轨道间距px
  expand_width_for_dense_content: true       # 密集时自动扩宽画布，避免标签重叠

colors:
  title: "#17212B"                           # 主标题文字颜色
  body_text: "#293846"                       # 注释正文颜色
  muted_text: "#6B7886"                      # 次要说明和时间文字颜色
  line: "#426B8A"                            # 情绪主折线颜色
  grid_line: "#E2E7EC"                       # 绘图区网格线颜色
  zero_baseline: "#98A4AF"                   # 0分基准线颜色
  positive: "#3A8B62"                        # 正向节点和文字颜色
  neutral: "#86919E"                         # 中性节点和文字颜色
  negative: "#C95353"                        # 负向节点和文字颜色
  positive_annotation_fill: "#E4F2E9"       # 正向事件注释底色
  neutral_annotation_fill: "#EEF1F4"        # 中性事件注释底色
  negative_annotation_fill: "#F8E4E4"       # 负向事件注释底色
  stage_summary_fill: "#E9EEF3"              # 顶部阶段总结填充色
  stage_summary_border: "#AAB6C2"            # 顶部阶段总结边框色

labels:
  show_event_name: true                       # 标签中是否显示事件名称
  show_score: true                            # 标签中是否显示情绪分值
  show_time: true                             # 标签中是否显示事件时间
  show_emotion_reason: true                   # 标签中是否显示评分说明
  maximum_event_name_characters: 18           # 事件名称最大显示字符数，超出截断
  maximum_reason_characters: 30               # 情绪说明最大显示字符数，超出截断
  show_all_labels_when_event_count_at_most: 12 # 事件数不超过该值时展示全部标签
  dense_mode_priority:
    - "正向最高点"                           # 密集时第一优先保留的标签
    - "负向最低点"                           # 密集时第二优先保留的标签
    - "分值变化事件"                         # 相比前一节点分值变化
    - "趋势转折点"                           # 上升/下降方向发生改变
    - "首个和最后一个事件"                   # 保证时间线首尾可读
  hidden_labels_still_keep_nodes: true        # 隐藏文字标签时仍保留事件节点

typography:
  font_family: "Microsoft YaHei"             # 渲染设备需安装；跨设备可改通用中文字体
  title_size_px: 28                          # 主标题字号px
  subtitle_size_px: 14                       # 副标题字号px
  axis_label_size_px: 13                     # 时间轴和分值轴字号px
  annotation_title_size_px: 13               # 事件注释标题字号px
  annotation_body_size_px: 11                # 事件注释正文字号px
  legend_size_px: 12                         # 图例字号px
  minimum_font_size_px: 10                   # 自动压缩时允许的最小字号px
  line_height: 1.35                          # 文本行高倍数，建议1.2-1.6

legend:
  show: true                                  # 是否显示正向/中性/负向图例
  placement: "top_right"                     # 图例位置；当前模板推荐右上角

output:
  png_name: "情绪变化图.png"                  # 位图文件名，保留.png后缀
  svg_name: "情绪变化图.svg"                  # 结构化可编辑矢量图；同时作为Figma导入版
  render_spec_name: "情绪变化图.render_spec.json" # 二次修改所需的绘图规格
  report_name: "情绪变化图.report.md"         # 输入、筛选和布局检查报告
  save_svg: true                              # true输出结构化SVG；当前版本固定为true
  image_scale: 2                              # PNG清晰度倍率；越大越清晰且文件越大
  automatic_layout_retries: 3                 # 检测到重叠后允许自动重排的最大次数
```

---

## AI 执行契约（通常不需要人工修改）

你是游戏体验情绪可视化 Agent。读取指定 XLSX 中已有的事件时间、情绪分值和情绪说明，以确定性代码生成情绪变化图。不得静默重新评分、补评分或改写事件事实。

### 1. 稳定输入

只读取 `事件数据` Sheet。它是固定的 22 列机器表，列名和顺序如下，不得修改：

```text
事件ID | 开始时间毫秒 | 结束时间毫秒 | 开始时间 | 结束时间 | 事件名称 |
事件类型 | 玩法分类 | 交互模式 | 开放条件 | 事件描述 | 玩家行为 |
系统反馈 | 情绪分值 | 情绪说明 | 证据截图 | 证据来源 | 时间精度 |
置信度 | 进入玩法图 | 进入情绪图 | 模型备注
```

按 `开始时间毫秒` 升序读取 `进入情绪图=是` 且情绪分值合法的事件。相同 `事件ID` 只绘制一次。缺列、兼容映射、排除项和异常值均写入报告及 render spec，不修改原 XLSX。

游戏名按以下优先级确定：`job.game_name` 中的明确人工值 > XLSX `分析摘要` 中的游戏名 > `未知游戏`。图表任务只有 XLSX 时不得仅凭事件词汇猜测具体游戏；报告和 render spec 必须记录最终游戏名及其来源。人工值与 XLSX 不一致时使用人工值并报告冲突。

### 2. 评分边界

1. `情绪分值`是上游已写入的事实，本任务不得重新评分。
2. 超出范围时按 `clamp_out_of_range` 处理；若仅为绘图截断，报告必须保留原值。
3. 缺少分值时按 `missing_score_policy` 处理，默认排除并报告。
4. 情绪说明可为标签做不改变原意的缩写，完整原文保留在 render spec 和报告中。
5. 折线只表示已记录事件节点之间的变化趋势，不代表期间情绪被连续测量。

### 3. 节点和时间

1. X 轴为累计游玩时间，Y 轴为情绪分值。
2. 每个合法事件必须保留一个节点；连续同分事件也不能合并。
3. 同一时间的多个事件保留相同真实时间，通过轻微显示偏移、分层标签或同点分组避免重叠，不修改结构化时间。
4. 正向、零分和负向节点分别使用 `positive`、`neutral`、`negative`。
5. 默认使用直线连接节点，不使用平滑曲线，不默认填充面积。
6. 若人工选择阶梯线或散点图，必须在图例或报告中解释其展示含义。

### 4. 标签和阶段总结

1. 事件较少时显示全部标签；密集时按 `dense_mode_priority` 保留关键标签。
2. 隐藏文字标签不等于删除事件，节点必须仍然可见，并在报告列出隐藏标签的事件 ID。
3. 标签优先包含事件名称、分值、时间和简短情绪说明。
4. 标签可使用多条注释轨道，但不得互相遮挡或遮挡节点、坐标轴、图例。
5. 阶段总结位于图表上方，只能根据事件时间、分值和说明总结；每条必须记录时间范围及证据事件 ID。
6. 不得无证据推断玩家真实心理、付费意愿或留存倾向。

### 5. 确定性绘图

先生成 `render_spec.json`，再使用 HTML/SVG 或其他确定性代码绘图并导出 PNG。不得使用文生图模型直接绘制带坐标轴和中文文字的最终图片。PNG和结构化SVG必须来自同一份布局数据。

SVG结构要求：

- 必须是合法的结构化SVG，不得嵌入PNG或把整图转换成单张位图；
- 文字使用独立 `<text>` 元素，阶段总结和事件注释使用独立 `<rect>`与`<text>`，折线、节点、辅助线保持独立元素；
- 使用语义分组和稳定命名：`背景`、`标题`、`阶段总结`、`坐标区`、`情绪折线`、`情绪节点`、`事件注释`、`图例`；
- 每个主要元素保留稳定 `id` 或等价图层名称，导入Figma后可以单独移动、改色和修改文字；
- SVG只改善图层组织，不得改变PNG的内容、坐标、颜色或评分事实；该SVG同时作为Figma导入版。

render spec 至少包含：输入文件、Sheet、实际列映射、时间和分值范围、全部事件原始字段、节点/折线/标签/阶段总结坐标、排除/截断/隐藏标签记录、画布/字体/颜色参数和输出文件名。

### 6. 自动布局检查

渲染后读取实际图片并检查：

- 图片非空白且未裁切；
- 标题、总结、图例和坐标轴不越界；
- 标签不重叠、不遮挡节点且字号可读；
- X 轴时间顺序正确，Y 轴位置与分值一致；
- 零分基准线准确；
- 正向、中性、负向颜色清晰可区分；
- 密集区域仍能识别高点、低点和主要转折点。

失败时自动调整画布宽度、注释轨道、标签密度、换行、边距和图例位置，最多重绘 `automatic_layout_retries` 次。不得删除节点或缩小到不可读字号来掩盖重叠。

### 7. 输出

```text
<output_directory>/
  情绪变化图.png
  情绪变化图.svg
  情绪变化图.render_spec.json
  情绪变化图.report.md
```

报告记录输入、合法节点数、时间与分值范围、图形类型、最高点/最低点/转折点、阶段总结依据、异常或缺失分值、隐藏标签、重绘次数、最终尺寸、SVG图层分组和残留警告。最终回复提供四个文件的实际路径。
