# OpenClaw + n8n 工作流编排

让你的 AI 智能体直接管理 API 密钥并调用外部服务是安全事件的配方。每个新集成意味着 `.env.local` 中的另一个凭据，智能体意外泄漏或滥用的另一个表面。

这个用例描述了一种模式，其中 OpenClaw 通过 webhook 将所有外部 API 交互委托给 n8n 工作流——智能体永远不会触及凭据，每个集成都是可视化可检查和可锁定的。

## 痛点

当 OpenClaw 直接处理所有内容时，你会遇到三个复合问题：

- **无可见性**：当它埋在 JavaScript 技能文件或 shell 脚本中时，很难检查智能体实际构建了什么
- **凭据蔓延**：每个 API 密钥都存在于智能体的环境中，距离暴露只有一次错误提交
- **浪费 token**：确定性子任务（发送电子邮件、更新电子表格）在可以作为简单工作流运行时会消耗 LLM 推理 token

## 功能说明

- **代理模式**：OpenClaw 使用传入 webhook 编写 n8n 工作流，然后为所有未来的 API 交互调用这些 webhook
- **凭据隔离**：API 密钥存在于 n8n 的凭据存储中——智能体只知道 webhook URL
- **可视化调试**：每个工作流都可以在 n8n 的拖放 UI 中检查
- **可锁定工作流**：一旦工作流构建和测试完成，你就锁定它，这样智能体就无法修改它与 API 的交互方式
- **保护步骤**：你可以在 n8n 中添加验证、速率限制和批准门，然后再执行任何外部调用

## 工作原理

1. **智能体设计工作流**：告诉 OpenClaw 你需要什么（例如"创建一个工作流，当新的 GitHub 问题被标记为 `urgent` 时发送 Slack 消息"）
2. **智能体在 n8n 中构建它**：OpenClaw 通过 n8n 的 API 创建工作流，包括传入 webhook 触发器
3. **你添加凭据**：打开 n8n 的 UI，手动添加你的 Slack token / GitHub token
4. **你锁定工作流**：防止智能体进一步修改
5. **智能体调用 webhook**：从现在开始，OpenClaw 使用 JSON 负载调用 `http://n8n:5678/webhook/my-workflow`——它永远看不到 API 密钥

```text
┌──────────────┐     webhook 调用      ┌─────────────────┐     API 调用     ┌──────────────┐
│   OpenClaw   │ ───────────────────→  │   n8n 工作流     │ ─────────────→  │  外部服务    │
│   (智能体)   │   (无凭据)            │  (锁定，带有     │  (凭据保留在    │  (Slack 等)  │
│              │                       │   API 密钥)      │   这里)         │              │
└──────────────┘                       └─────────────────┘                  └──────────────┘
```

## 所需技能

- `n8n` API 访问（用于创建/触发工作流）
- `fetch` 或 `curl` 用于 webhook 调用
- Docker（如果使用预配置堆栈）
- n8n 凭据管理（每个集成一次性手动设置）

## 设置方法

### 选项 1：预配置 Docker 堆栈

社区维护的 Docker Compose 设置（[openclaw-n8n-stack](https://github.com/caprihan/openclaw-n8n-stack)）在共享 Docker 网络上预连接所有内容：

```bash
git clone https://github.com/caprihan/openclaw-n8n-stack.git
cd openclaw-n8n-stack
cp .env.template .env
```
