# 全球热点新闻简要 · global-news-digest

> 每日全球 TOP20 官方媒体的热点新闻摘要 —— 自动抓取、五类分类、TOP10 精选、精致 HTML 呈现。

从 [GDELT](https://www.gdeltproject.org/) 聚合的 **TOP20 全球主流媒体**抓取「前一天」热点，按 **经济 / 股票 / 科技 / 政治 / 军事** 五类各筛 **TOP10**，渲染为单文件 HTML，并可通过邮件推送。

## ✨ 特性

- **5 类 × TOP10**：每条含 序号、新闻名称、新闻简要、来源机构、火爆度、来源链接。
- **透明热度**：`火爆度 = 0.6 × GDELT 排名热度 + 0.4 × AI 推理热度`，页面明确标注「含 AI 推理，非官方阅读量」。
- **单文件 HTML**：内联 CSS、响应式、5 类分区配色，双击即可打开。
- **零密钥**：数据源 GDELT Doc 2.0 免费公开 API，无需申请 key。
- **真实来源**：所有条目均来自 API 实际返回，严禁编造。

## 📦 安装

### 方式一：SkillHub 安装（推荐）

1. 打开 WorkBuddy → 左侧 **专家·技能·连接器** → **技能** 页签；
2. 在 **技能市场** 中切到 **SkillHub** 分类，搜索 `global-news-digest` 或「全球热点新闻简要」；
3. 点卡片右上角 **+** 安装。

或在对话里直接说：「安装 global-news-digest 技能」。

### 方式二：从本仓库手动安装

```bash
git clone https://github.com/<你的用户名>/global-news-digest.git

# macOS / Linux
cp -r global-news-digest ~/.workbuddy/skills/

# Windows (PowerShell)
# Copy-Item -Recurse global-news-digest "$env:USERPROFILE\.workbuddy\skills\"
```

重启 WorkBuddy 后生效。

### 方式三：上传技能包

下载本仓库 → 压缩为 zip（压缩包第一层为 `SKILL.md` 所在目录）→ WorkBuddy 技能板块 **添加技能 > 上传技能**。

## 1. 依赖

- **Python 3.8+**（脚本使用标准库 + `requests`）。
- 第三方库：`requests`（**仅 `fetch_news.py` 使用**）。未安装时运行 `pip install requests` 即可；`render_html.py` 仅用标准库，无需额外依赖。脚本已对缺失依赖做友好提示。
- 实时联网访问 GDELT Doc 2.0 API（免费、无需密钥）；不上传任何用户隐私数据。

## 2. 用法

### 手动触发

对 WorkBuddy 说，例如：
> "生成昨日全球热点新闻" / "今日新闻简报" / "经济 股票 科技 政治 军事 新闻汇总"

智能体会按 `SKILL.md` 流程执行：抓取 → AI 后处理 → 渲染 → 预览。

### 自动每日推送（需自行配置）

本技能提供「每日 北京时间 08:00 自动生成并 agent-mail 推送」的能力，但该自动化**不会随技能安装自动创建**。安装后请在 WorkBuddy 自动化中按 `SKILL.md` 第 ⑤ 步的流程自行新建每日递归任务（需 agent-mail 已配置收件人）。

## 3. 数据流与产物

```
fetch_news.py <YYYY-MM-DD>   →  outputs/news-digest/<日期>.raw.news.json   （原始候选，每类≤40）
   [AI 后处理：去重/筛选/TOP10/中文简要/ai_heat/机构映射]
                              →  outputs/news-digest/<日期>.news.json      （最终结构化）
render_html.py <YYYY-MM-DD>   →  outputs/news-digest/<日期>.html           （呈现页）
```

- 时间窗：北京时间目标日 00:00–24:00，换算 UTC 作为 GDELT 起止。
- 域名过滤：查询仅用类别关键词（短、合规），抓回后客户端按 TOP20 域名过滤。

## 4. 火爆度说明

```
hotness = round( 0.6 × gdelt_score + 0.4 × ai_heat )
gdelt_score = (N - gdelt_rank + 1) / N × 100      # N 为该类别候选总数
ai_heat    = AI 按事件重要性打的 0–100 分
```

**页面已明确标注：含 AI 推理，非官方阅读量。** GDELT 不返回逐篇阅读量，故以「排名热度 + AI 推理」合成。

## 5. TOP20 媒体名单（均衡版）

路透社 / 美联社 / BBC / 彭博社 / CNBC / 金融时报 / 华尔街日报 / NHK / 德国之声 / 半岛电视台 / 新华社 / CGTN / 日本经济新闻 / 世界报 / 卫报 / 纽约时报 / 华盛顿邮报 / CNN / 法新社 / 南华早报(SCMP)。

**增删媒体**：编辑 `scripts/fetch_news.py` 顶部的 `TOP_MEDIA`（`(key, 中文名, 英文名, 域名)` 四元组）。域名按「后缀匹配」，子域（如 arabic.cnn.com）自动归入母媒体。详见 `references/media_list.md`。

## 6. 已知限制 / 注意

- **GDELT 限流严格**（约 1 次/5 秒）。脚本已内置 6 秒间隔 + 429 退避；每日 5 次查询在免费额度内。共享 IP 高频场景可能偶发 429，单类失败不中断整体。
- **需实时联网**：新闻来自 API，绝不使用模型记忆。
- **空态**：某类当日无相关报道时，页面显示「暂无数据」而非报错。
- **NewsAPI 增强（可选）**：如需更干净的中文源，可在 `fetch_news.py` 增加 NewsAPI 适配层（需 API key），当前未启用。

## 7. 仓库结构

```
.
├── SKILL.md                 # 技能入口与流程编排
├── README.md                # 本文件
├── LICENSE                  # MIT
├── references/
│   └── media_list.md        # TOP20 名单与增删指引
└── scripts/
    ├── fetch_news.py        # 抓取层（GDELT）
    └── render_html.py       # 渲染层（HTML）
```

> 运行后会在 `outputs/news-digest/` 生成产物，该目录已在 `.gitignore` 中忽略，不会污染仓库。

## License

[MIT](./LICENSE) © 2026 fellcon68

## 免责声明

新闻内容版权归原媒体所有；本技能仅做聚合与摘要呈现，热度值为合成估算，非官方阅读量。
