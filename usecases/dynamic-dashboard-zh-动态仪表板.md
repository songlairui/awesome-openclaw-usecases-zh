# 使用子智能体生成的动态仪表板

静态仪表板显示过时数据，需要不断手动更新。你想要跨多个数据源的实时可见性，而无需构建自定义前端或触及 API 速率限制。

这个工作流创建一个实时仪表板，生成子智能体并行获取和处理数据：

• 同时监控多个数据源（API、数据库、GitHub、社交媒体）
• 为每个数据源生成子智能体以避免阻塞并分配 API 负载
• 将结果聚合到统一仪表板（文本、HTML 或 Canvas）
• 每 N 分钟更新一次新数据
• 当指标超过阈值时发送警报
• 在数据库中维护历史趋势以进行可视化

## 痛点

构建自定义仪表板需要数周时间。等它完成时，需求已经改变了。顺序轮询多个 API 很慢并且会触及速率限制。你现在就需要洞察，而不是在编码一个周末之后。

## 功能说明

你通过对话定义要监控的内容："跟踪 GitHub 星标、Twitter 提及、Polymarket 交易量和系统健康状况。" OpenClaw 生成子智能体并行获取每个数据源，聚合结果，并将格式化的仪表板发送到 Discord 或作为 HTML 文件。更新按 cron 计划自动运行。

示例仪表板部分：
- **GitHub**：星标、分叉、未解决问题、最近提交
- **社交媒体**：Twitter 提及、Reddit 讨论、Discord 活动
- **市场**：Polymarket 交易量、预测趋势
- **系统健康**：CPU、内存、磁盘使用率、服务状态

## 所需技能

- 子智能体生成用于并行执行
- `github`（gh CLI）用于 GitHub 指标
- `bird`（Twitter）用于社交数据
- `web_search` 或 `web_fetch` 用于外部 API
- `postgres` 用于存储历史指标
- Discord 或 Canvas 用于渲染仪表板
- Cron 作业用于计划更新

## 设置方法

1. 设置指标数据库：
```sql
CREATE TABLE metrics (
  id SERIAL PRIMARY KEY,
  source TEXT, -- 例如 "github"、"twitter"、"polymarket"
  metric_name TEXT,
  metric_value NUMERIC,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE alerts (
  id SERIAL PRIMARY KEY,
  source TEXT,
  condition TEXT,
  threshold NUMERIC,
  last_triggered TIMESTAMPTZ
);
```

2. 为仪表板更新创建 Discord 频道（例如 #dashboard）。

3. 提示 OpenClaw：
```text
你是我的动态仪表板管理器。每 15 分钟运行一次 cron 作业：

1. 并行生成子智能体从以下位置获取数据：
   - GitHub：星标、分叉、未解决问题、提交（过去 24 小时）
   - Twitter：提及 "@username"、情感分析
   - Polymarket：跟踪市场的交易量
   - 系统：通过 shell 命令获取 CPU、内存、磁盘使用率

2. 每个子智能体将结果写入指标数据库。

3. 聚合所有结果并格式化仪表板：

📊 **仪表板更新** — [时间戳]

**GitHub**
- ⭐ 星标：[数量]（+[变化]）
- 🍴 分叉：[数量]
- 🐛 未解决问题：[数量]
- 💻 提交（24 小时）：[数量]

**社交媒体**
- 🐦 Twitter 提及：[数量]
- 📈 情感：[积极/消极/中性]

**市场**
- 📊 Polymarket 交易量：$[金额]
- 🔥 热门：[市场名称]

**系统健康**
- 💻 CPU：[使用率]%
- 🧠 内存：[使用率]%
- 💾 磁盘：[使用率]%

4. 发布到 Discord #dashboard。

5. 检查警报条件：
   - 如果 GitHub 星标在 1 小时内变化 > 50 → ping 我
   - 如果系统 CPU > 90% → 警报
   - 如果 Twitter 上出现负面情感激增 → 通知

将所有指标存储在数据库中以进行历史分析。
```

4. 可选：使用 Canvas 渲染带有图表和图形的 HTML 仪表板。

5. 查询历史数据："显示过去 30 天的 GitHub 星标增长。"

## 相关链接

- [使用子智能体进行并行处理](https://docs.openclaw.ai/subagents)
- [仪表板设计原则](https://www.nngroup.com/articles/dashboard-design/)
