# 活动嘉宾确认

你正在举办活动——晚宴、婚礼、公司外出——你需要确认嘉宾名单的出席情况。手动打电话给 20 多人很繁琐：你玩电话标签，忘记谁说了什么，并且无法跟踪饮食限制或加一。发短信有时有效，但人们会忽略消息。真正的电话获得更高的响应率。

这个用例让 OpenClaw 使用 [SuperCall](https://clawhub.ai/xonder/supercall) 插件呼叫你名单上的每位嘉宾，确认他们是否出席，收集任何备注，并为你编译所有内容的摘要。

## 功能说明

- 遍历嘉宾名单（姓名 + 电话号码）并呼叫每个人
- AI 以友好的角色介绍自己为你的活动协调员
- 与嘉宾确认活动日期、时间和地点
- 询问他们是否出席，并收集任何备注（饮食需求、加一、到达时间等）
- 所有呼叫完成后，编译摘要：谁确认了、谁拒绝了、谁没有接听，以及任何备注

## 为什么选择 SuperCall

这个用例专门与 [SuperCall](https://clawhub.ai/xonder/supercall) 插件一起使用——而不是内置的 `voice_call` 插件。关键区别：SuperCall 是一个完全独立的语音智能体。呼叫中的 AI 角色 **只能访问你提供的上下文**（角色名称、目标和开场白）。它无法访问你的网关智能体、你的文件、你的其他工具或其他任何东西。

这对于嘉宾确认很重要，因为：

- **安全性**：电话另一端的人无法通过对话操纵或访问你的智能体。没有提示注入或数据泄漏的风险。
- **更好的对话**：因为 AI 的范围限定在单个专注的任务（确认出席），它保持主题并比通用语音智能体更自然地处理呼叫。
- **批处理友好**：你正在向不同的人拨打许多电话。每次呼叫重置的沙盒角色正是你想要的——对话之间没有渗透。

## 所需技能

- [SuperCall](https://clawhub.ai/xonder/supercall) — 通过 `openclaw plugins install @xonder/supercall` 安装
- 带有电话号码的 Twilio 账户（用于拨打出站电话）
- OpenAI API 密钥（用于 GPT-4o Realtime 语音 AI）
- ngrok（用于 webhook 隧道——免费层有效）

有关完整配置说明，请参阅 [SuperCall README](https://github.com/xonder/supercall)。

## 设置方法

1. 按照 [设置指南](https://github.com/xonder/supercall#configuration) 安装和配置 SuperCall。确保在你的 OpenClaw 配置中启用了钩子。

2. 准备你的嘉宾名单。你可以直接在聊天中粘贴它或将其保存在文件中：

```text
嘉宾名单 — 夏季烧烤，6 月 14 日星期六，下午 4 点，橡树街 23 号

- Sarah Johnson: +15551234567
- Mike Chen: +15559876543
- Rachel Torres: +15555551234
- David Kim: +15558887777
```

3. 提示 OpenClaw：

```text
我需要你确认我的活动的出席情况。以下是详细信息：

活动：夏季烧烤
日期：6 月 14 日星期六下午 4 点
地点：橡树街 23 号

这是我的嘉宾名单：
<在此粘贴你的嘉宾名单>

对于每位嘉宾，使用 supercall 呼叫他们。使用角色"Jamie，[你的名字]的活动协调员"。每次呼叫的目标是确认他们是否出席，
并记录任何饮食限制、加一或其他评论。

每次呼叫后，记录结果。所有呼叫完成后，给我一个完整的摘要：
- 谁确认了
- 谁拒绝了
- 谁没有接听
- 每位嘉宾的任何备注或特殊要求
```

4. OpenClaw 将使用 SuperCall 逐个呼叫每位嘉宾，然后编译结果。你可以随时询问状态更新以检查进度。

## 关键见解

- **从小测试开始**：首先尝试 2-3 位嘉宾，以确保角色和开场白听起来正确。在呼叫完整列表之前，你可以调整语气。
- **注意呼叫时间**：不要安排太早或太晚的呼叫。你可以告诉 OpenClaw 只在某些时间之间呼叫。
- **审查记录**：SuperCall 将记录记录到 `~/clawd/supercall-logs`。在第一批之后浏览它们，看看对话进行得如何。
- **无应答处理**：如果有人没有接听，OpenClaw 可以记录它，你可以决定是否稍后重试或通过短信跟进。
- **真实电话需要花钱**：每次呼叫都使用 Twilio 分钟数。在你的 Twilio 账户中设置适当的限制，特别是对于大型嘉宾名单。

## 相关链接

- [ClawHub 上的 SuperCall](https://clawhub.ai/xonder/supercall)
- [GitHub 上的 SuperCall](https://github.com/xonder/supercall)
- [Twilio 控制台](https://console.twilio.com)
- [OpenAI Realtime API](https://platform.openai.com/docs/guides/realtime)
- [ngrok](https://ngrok.com)
