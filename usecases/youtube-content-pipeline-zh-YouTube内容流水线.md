# YouTube 内容流水线

作为每日 YouTube 创作者，在网络和 X/Twitter 上寻找新鲜、及时的视频创意很耗时。跟踪你已经涵盖的内容可以防止重复并帮助你保持领先趋势。

这个工作流自动化整个内容侦察和研究流水线：

• 每小时 cron 作业扫描突发 AI 新闻（网络 + X/Twitter）并向 Telegram 推荐视频创意
• 维护 90 天视频目录，包含观看次数和主题分析以避免重复涵盖主题
• 将所有推荐存储在 SQLite 数据库中，并带有向量嵌入以进行语义去重（因此你永远不会被推荐两次相同的想法）
• 当你在 Slack 中分享链接时，OpenClaw 研究主题，搜索 X 以查找相关帖子，查询你的知识库，并创建带有完整大纲的 Asana 卡片

## 所需技能

- `web_search`（内置）
- [x-research-v2](https://clawhub.ai) 或自定义 X/Twitter 搜索技能
- [knowledge-base](https://clawhub.ai) 技能用于 RAG
- Asana 集成（或 Todoist）
- `gog` CLI 用于 YouTube Analytics
- Telegram 主题用于接收推荐

## 设置方法

1. 为视频创意设置 Telegram 主题并在 OpenClaw 中配置它。
2. 安装 knowledge-base 技能和 x-research 技能。
3. 创建 SQLite 数据库用于推荐跟踪：
```sql
CREATE TABLE pitches (
  id INTEGER PRIMARY KEY,
  timestamp TEXT,
  topic TEXT,
  embedding BLOB,
  sources TEXT
);
```
4. 提示 OpenClaw：
```text
每小时运行 cron 作业：
1. 搜索网络和 X/Twitter 以查找突发 AI 新闻
2. 检查我的 90 天 YouTube 目录（通过 gog 从 YouTube Analytics 获取）
3. 检查数据库中所有过去推荐的语义相似性
4. 如果是新颖的，向我的 Telegram "视频创意"主题推荐想法并附带来源

另外：当我在 Slack #ai_trends 中分享链接时，自动：
1. 研究主题
2. 搜索 X 以查找相关帖子
3. 查询我的知识库
4. 在视频流水线中创建带有完整大纲的 Asana 卡片
```
