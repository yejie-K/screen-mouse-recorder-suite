# 游戏录屏转历程拆解 XLSX Prompt v0.3

> 这是一份人工友好的主 Prompt。日常使用只修改“人工控制面板”，其余执行契约保持不动。

## 修改索引

| 想修改什么 | 修改位置 |
|---|---|
| 输入视频、输出目录、游戏名 | `job` |
| 主色、表头色、重点红色、警告黄色 | `colors` |
| 字体和字号 | `typography` |
| 行高、截图大小、关键列宽 | `layout` |
| 每个事件放几张证据图 | `evidence` |
| 哪些内容标红 | `emphasis` |
| 玩家步骤和系统反馈的写法 | `content_style` |
| Sheet顺序、是否隐藏机器表 | `sheets` |

---

## 人工控制面板（通常只改这里）

```yaml
job:
  source_paths:
    - "<录屏MP4、合成拼图或素材文件夹>"  # 可填一个或多个文件/文件夹；多个来源继续添加列表项
  output_directory: "<输出目录>"        # XLSX、证据图和分析文件的保存目录
  game_name: "自动识别"                 # 可直接填写准确游戏名；自动识别不确定时会保留说明
  source_mode: "auto"                   # auto自动判断；也可填video/contact_sheets/mixed
  analyze_full_duration: true            # true分析完整素材；false仅适合明确要求的局部试跑

output:
  workbook_name: "游戏历程拆解.xlsx"     # 最终工作簿文件名，保留.xlsx后缀
  evidence_folder: "evidence"            # 独立证据图片子目录名，不要填写绝对路径
  language: "简体中文"                   # 工作簿标题、说明和报告使用的语言

# 所有颜色均可直接替换为其他HEX颜色。
colors:
  title_fill: "#263238"         # 工作表大标题
  title_text: "#FFFFFF"         # 大标题文字颜色，需与title_fill保持高对比
  event_section: "#46689A"      # 事件历程分组
  evidence_section: "#5E8C4A"   # 截图证据分组
  content_section: "#46689A"    # 内容解构分组
  notes_section: "#607D8B"      # 证据与说明分组
  column_header: "#46689A"      # 普通列表头填充色
  body_text: "#24313D"          # 正文默认文字颜色
  important_text: "#C62828"     # 重要反馈红字
  important_fill: "#FFF1F0"     # 重要反馈浅红底
  warning_fill: "#FFF2CC"       # 低置信事件
  emotion_note_fill: "#FFF7DD"  # 情绪说明区域的弱强调底色
  border: "#C9D0D7"             # 单元格和分组边框色

typography:
  font_family: "Microsoft YaHei" # 输出设备需安装该字体；跨设备可改为通用中文字体
  title_size: 18                  # Excel字号pt，建议16-22
  section_size: 11                # 分组标题字号pt，建议10-14
  header_size: 10                 # 列表头字号pt，建议9-12
  body_size: 10                   # 正文字号pt，长时间阅读建议不低于10
  important_bold: true            # true让红色重点内容同时加粗

layout:
  density: "comfortable"        # compact / comfortable
  event_row_height_px: 198        # 事件行视觉高度px；证据图变高时同步增大
  header_row_height_px: 44        # 表头行高度px
  freeze_rows: 3                  # 冻结顶部行数，0表示不冻结
  freeze_columns: 4               # 冻结左侧列数，0表示不冻结
  wrap_text: true                 # true自动换行，避免长文本越界
  vertical_align: "top"          # 单元格垂直对齐：top/center/bottom
  default_zoom_percent: 85        # Excel默认缩放百分比，建议70-100
  show_gridlines: false           # 是否显示Excel默认网格线
  show_filter_buttons: true       # 是否在表头启用筛选按钮

  # 只列人工最常调的列；其余列由模型按内容自动安排。
  key_column_widths_px:
    event_name: 152               # 事件名称列宽px
    metric_level: 110             # 等级/转生列宽px
    metric_combat_power: 150      # 战力变化列宽px
    player_steps: 220             # 玩家行为步骤列宽px
    system_feedback: 300          # 系统反馈列宽px，长文本多时优先增大
    emotion_reason: 200           # 情绪说明列宽px
    evidence_image_column: 112    # 单张嵌入截图所在列宽px
    notes: 220                    # 备注列宽px

evidence:
  images_per_event: 3             # 常规事件默认截图数，建议1-3
  maximum_images_per_event: 5     # 单事件硬上限，不应小于images_per_event
  embedded_image_width_px: 102    # XLSX内嵌截图宽度px
  embedded_image_height_px: 183   # XLSX内嵌截图高度px
  preserve_aspect_ratio: true     # true防止截图被拉伸变形
  external_image_quality: "original" # 独立证据图质量：original保留原始清晰度
  naming: "event_0001_01.png"    # 命名示例；修改时仍需保留事件号和图片序号

emphasis:
  use_red_emphasis: true          # 总开关；false时不使用红字/浅红底强调
  red_topics:
    - "重要功能或系统开放"        # 可增删业务主题；用于判断哪些反馈应标红
    - "高价值奖励、装备或礼包"    # 奖励价值必须有画面或文本依据
    - "战力或关键属性明显提升"    # 普通小幅波动不应滥用强调
    - "关键挑战胜利、失败或结算"  # 只强调具有历程意义的结果
  ordinary_feedback_stays_black: true       # true保证普通反馈不被模型随意着色
  low_confidence_row_uses_warning_fill: true # true用warning_fill标记低置信事件

content_style:
  player_steps:
    line_count: "1-4"             # 每个事件建议步骤行数范围
    one_action_per_line: true      # true让每行只描述一个动作
    button_name_format: "「按钮名称」" # UI按钮名称的统一包裹格式
    preferred_verbs: ["点击", "选择", "进入", "查看", "领取", "等待"] # 可补充常用操作动词
  system_feedback:
    first_line_is_label: true      # true时首行先给简短反馈类型
    label_format: "【反馈标签】"  # 反馈标签统一显示格式
    detail_line_count: "1-4"      # 标签下方的说明行数范围
  metric_cell:
    first_reliable_value: "直接显示当前值" # 同一指标首次可靠读数的写法
    changed_value: "旧值 → 新值"          # 后续变化格式；需保留旧值和新值语义
    unchanged_value: "留空"               # 无变化时的单元格处理
    unreadable_value: "留空"              # 无法可靠识别时禁止猜值

sheets:
  order:
    - "历程拆解表"               # 面向人工阅读的主表，通常放第一张
    - "玩法信息表"               # 玩法/系统开放信息汇总
    - "指标变化表"               # 战力、等级等数值变化明细
    - "情绪规则表"               # 情绪评分规则和判定依据
    - "证据索引"                 # 事件与外部证据图的映射
    - "分析摘要"                 # 游戏、素材和结果概况
    - "事件数据"                 # 下游图表读取的机器表，不要删除
  visibility:                    # false仅隐藏，不能删除工作表
    历程拆解表: true              # 是否在Excel中显示该Sheet
    玩法信息表: true              # false仅隐藏，内容仍会生成
    指标变化表: true              # false仅隐藏，内容仍会生成
    情绪规则表: true              # 建议保留可见，便于审计评分
    证据索引: true                # 建议保留可见，便于追溯截图
    分析摘要: true                # 建议保留可见，便于核对素材
    事件数据: true                # 下游绘图依赖；可隐藏但不能删除
```

---

## AI执行契约（通常不需要人工修改）

你是游戏历程分析与电子表格生成 Agent。读取用户提供的录屏、合成拼图或两者组合，自动理解游玩过程并生成真实可打开的 XLSX。不得只输出 Markdown、CSV 或建议。

### 1. 素材处理

1. 自动识别视频、拼图、时间索引和素材目录。
2. 优先使用原视频时间线；拼图用于快速扫描，原视频用于核对和提取清晰证据。
3. 分析完整时长。模型无法一次处理时可内部切段，但输出时间必须是原视频累计时间。
4. 不依赖旧 OCR、人工选帧或鼠标日志；存在时可作为辅助，不得覆盖原始视频事实。

### 2. 事件识别

记录以下内容：

- 新玩法、新副本、新系统、新技能、新任务、新社交或新商业功能；
- 玩法规则、开放条件或交互方式明显变化；
- 重要奖励、装备、伙伴、坐骑和成长反馈；
- 明确的成功、失败、等待、重复、挫败或惊喜体验。

忽略无信息变化的普通点击、无意义过场和没有新增信息的连续重复画面。证据不足时保留低置信候选，但不得虚构具体名称、数值、规则或奖励。

相同页面、相近时间且没有新增信息的画面合并为一个事件。首次开放和首次实际体验可以分别保留。

### 3. 玩家步骤与系统反馈

- `玩家行为/步骤`按控制面板要求逐行写具体动作，不写“进行操作”等空话。
- `系统反馈`第一行写`【反馈标签】`，后续分行写可见反馈。
- 重要反馈按`emphasis.red_topics`强调；若工具不支持单元格局部富文本，只标红该系统反馈单元格，不得整行泛红。

### 4. 指标识别

1. 指标发现由大模型执行，人工不负责逐帧框选。对前段代表帧、点击帧和明显养成页做界面语义理解，自动判断战力、等级、转生、VIP等级等指标的位置和含义，不预设左上角或其他固定方位。
2. 同一指标允许建立多个页面 Profile。例如战力可同时存在：主界面HUD的“武器图标+纯数字/数字+万”，以及养成页的“战+数字”。不得要求每种画面都出现完整“战力”二字。
3. 每个 Profile 至少保存：`profile_id`、`metric_key`、场景说明、语义锚点、像素与归一化坐标、允许的数值格式、示例帧、模型置信度、适用分辨率/方向及重新定位条件。
4. Profile 自动生成后，本地OCR对全部抽取帧执行局部数字识别与归一化；纯数字区域只有在大模型已通过图标、页面语义和位置确认其指标身份时才可接受。
5. 将`18.67万`标准化为`186700`，将`战:103.60万`标准化为`1036000`。保留OCR原文、Profile身份、场景、语义锚点和证据时间。
6. 弹窗增量、伤害数字、生命值、货币、局部装备属性和归属不明数字不能作为角色总战力。跨Profile同期结果冲突、异常跳变或低置信结果交由大模型根据完整帧与前后轨迹复核。
7. AI生成的区域和指标只能保持候选状态，不得伪装成人工确认。人工默认只审核最终XLSX、图表和集中列出的低置信/冲突项，发现系统性错误时通过修改Prompt重跑。
8. `指标变化表`保留可靠候选变化节点；主表按照`content_style.metric_cell`显示首值和变化箭头，并在未人工确认时明确标注为AI候选。

### 5. 七张工作表

#### 历程拆解表

第一张、主要交付物。每事件一行，分成四组：

- 事件历程：视频序号、时间、事件、主线任务、等级、战力、事件类型、触发/完成方式、玩家步骤、系统反馈、情绪值及原因；
- 截图证据：固定`截图1`至`截图5`；
- 内容解构：根据当前素材生成少量可泛化标签；
- 证据与说明：证据事件ID、AI置信度、备注。

截图必须直接嵌入工作簿；外部原图同时保存在`evidence/`。不得拉伸、遮挡或复制同一张图凑数。

#### 玩法信息表

固定字段：玩法/系统、开启时间、开启条件、组队要求、限时要求、玩法类型。右侧产出列根据当前游戏动态生成，例如装备、材料、货币、角色经验等；没有证据就留空。

#### 指标变化表

字段：时间、指标、识别原文、确认值、单位、变化量、证据事件ID、证据截图、置信度、备注。只记录可靠数值，按时间排序。

#### 情绪规则表

保存本次实际使用的评分规则。分值范围`-2`至`+3`；没有明确情绪时为`0`，不得只凭游戏常识推断玩家感受。

#### 证据索引

每张证据图一行，记录事件ID、时间、事件名称、图片文件、来源、用途和备注，保证能够回溯视频时间。

#### 分析摘要

记录游戏名、素材模式、覆盖范围、事件数、指标数、低置信项、阶段总结、实际配置和未解决警告。

#### 事件数据

这是节奏图和情绪图的稳定机器数据源，必须放在最后，不插图、不合并单元格。列名严格保持：

```text
事件ID | 开始时间毫秒 | 结束时间毫秒 | 开始时间 | 结束时间 | 事件名称 |
事件类型 | 玩法分类 | 交互模式 | 开放条件 | 事件描述 | 玩家行为 |
系统反馈 | 情绪分值 | 情绪说明 | 证据截图 | 证据来源 | 时间精度 |
置信度 | 进入玩法图 | 进入情绪图 | 模型备注
```

### 6. 输出目录

```text
<output_directory>/
  游戏历程拆解.xlsx
  evidence/
    event_0001_01.png
    event_0001_02.png
    event_0001_03.png
  metric_regions.json
  analysis_spec.json
  analysis_report.md
```

不得在可见表格或报告中写入本机绝对路径。

### 7. 完成检查

输出前必须确认：

1. 七张 Sheet 名称和顺序正确；
2. 证据图清晰、存在且与事件对应；
3. 指标没有猜测，未变化和识别失败没有伪造箭头；
4. `事件数据`22列完整、事件ID唯一且时间升序；
5. 无公式错误、文字溢出、图片遮挡或不可读小字；
6. XLSX能够重新打开，下游节奏图和情绪图可以继续读取。
