# 多渠道个人助手

在应用之间切换上下文以管理任务、安排事件、发送消息和跟踪工作很累人。你想要一个路由到所有工具的界面。

这个工作流将所有内容整合到一个 AI 助手中：

• Telegram 作为主要界面，基于主题的路由（视频创意、CRM、财报、配置等的不同主题）
• Slack 集成用于团队协作（任务分配、知识库保存、视频创意触发）
• Google Workspace：创建日历事件、管理电子邮件、上传到 Drive——全部来自聊天
• Todoist 用于快速任务捕获
• Asana 用于项目管理
• 自动提醒：垃圾日、每周公司信函等

## 所需技能

- `gog` CLI（Google Workspace）
- Slack 集成（bot + 用户 token）
- Todoist API 或技能
- Asana API 或技能
- 配置了多个主题的 Telegram 频道

## 设置方法

1. 为不同上下文设置 Telegram 主题：
   - `config` — 机器人设置和调试
   - `updates` — 状态和通知
   - `video-ideas` — 内容流水线
   - `personal-crm` — 联系人管理
   - `earnings` — 财务跟踪
   - `knowledge-base` — RAG 摄取和查询

2. 通过 OpenClaw 配置连接所有工具：
   - Google OAuth（Gmail、Calendar、Drive）
   - Slack（app + 用户 token）
   - Todoist API token
   - Asana API token

3. 提示 OpenClaw：
```text
你是我的多渠道助手。根据上下文路由请求：
```
