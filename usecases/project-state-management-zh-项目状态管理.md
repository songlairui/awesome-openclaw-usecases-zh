# 项目状态管理系统：事件驱动的看板替代方案

传统的看板面板是静态的，需要手动更新。你忘记移动卡片，在会话之间失去上下文，无法跟踪状态变化背后的"原因"。项目在没有明确可见性的情况下漂移。

这个工作流用自动跟踪项目状态的事件驱动系统替代看板：

• 在数据库中存储项目状态并保留完整历史
• 捕获上下文：决策、阻碍、下一步、关键见解
• 事件驱动更新："刚完成 X，被 Y 阻碍" → 自动状态转换
• 自然语言查询："[项目] 的状态是什么？"、"我们为什么在 [功能] 上转向？"
• 每日站会摘要：昨天发生了什么、今天计划什么、什么被阻碍
• Git 集成：将提交链接到项目事件以实现可追溯性

## 痛点

看板面板变得陈旧。你浪费时间更新卡片而不是做工作。上下文丢失——三个月后，你记不起为什么做出关键决策。代码更改和项目进度之间没有自动链接。

## 功能说明

你不是拖动卡片，而是与你的助手聊天："完成了认证流程，开始仪表板。"系统记录事件，更新项目状态，并保留上下文。当你问"我们在仪表板上的进度如何？"时，它会给你完整的故事：完成了什么、接下来是什么、什么阻碍了你，以及为什么。

Git 提交会自动扫描并链接到项目。你的每日站会摘要会自己写。

## 所需技能

- `postgres` 或 SQLite 用于项目状态数据库
- `github`（gh CLI）用于提交跟踪
- Discord 或 Telegram 用于更新和查询
- Cron 作业用于每日摘要
- 子智能体用于并行项目分析

## 设置方法

1. 设置项目状态数据库：
```sql
CREATE TABLE projects (
  id SERIAL PRIMARY KEY,
  name TEXT UNIQUE,
  status TEXT, -- 例如 "active"、"blocked"、"completed"
  current_phase TEXT,
  last_update TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE events (
  id SERIAL PRIMARY KEY,
  project_id INTEGER REFERENCES projects(id),
  event_type TEXT, -- 例如 "progress"、"blocker"、"decision"、"pivot"
  description TEXT,
  context TEXT,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE blockers (
  id SERIAL PRIMARY KEY,
  project_id INTEGER REFERENCES projects(id),
  blocker_text TEXT,
  status TEXT DEFAULT 'open', -- "open"、"resolved"
  created_at TIMESTAMPTZ DEFAULT NOW(),
  resolved_at TIMESTAMPTZ
);
```

2. 创建 Discord 频道用于项目更新（例如 #project-state）。

3. 提示 OpenClaw：
```text
你是我的项目状态管理器。我会以对话方式告诉你我正在做什么，而不是使用看板。

当我说这样的话时：
- "完成了 [任务]" → 记录"进度"事件，更新项目状态
- "被 [问题] 阻碍" → 创建阻碍条目，将项目状态更新为"blocked"
- "开始 [新任务]" → 记录"进度"事件，更新当前阶段
- "决定 [决策]" → 记录带有完整上下文的"决策"事件
- "转向 [新方法]" → 记录带有推理的"转向"事件

当我问：
- "[项目] 的状态是什么？" → 获取最新事件、阻碍和当前阶段
- "我们为什么决定 [X]？" → 搜索事件以获取决策上下文
- "什么阻碍了我们？" → 列出所有项目中的所有未解决阻碍

每天早上 9 点运行 cron 作业：
1. 扫描过去 24 小时的 git 提交（通过 gh CLI）
2. 根据分支名称或提交消息将提交链接到项目
3. 向 Discord #project-state 发布每日站会摘要：
   - 昨天发生了什么（事件 + 提交）
   - 今天计划什么（基于当前阶段和最近的对话）
   - 什么被阻碍（未解决的阻碍）

当我计划冲刺时，生成子智能体来分析每个项目的状态并建议优先级。
```

4. 与你的工作流集成：只需自然地与你的助手谈论你正在做什么。系统会捕获一切。

## 相关链接

- [事件溯源模式](https://martinfowler.com/eaaDev/EventSourcing.html)
- [为什么看板对独立开发者失败](https://blog.nuclino.com/why-kanban-doesnt-work-for-me)
