# 每日 YouTube 摘要

每天以个性化摘要开始，了解你最喜欢的 YouTube 频道的新视频——不再错过你真正想关注的创作者的内容。

## 痛点

YouTube 通知不可靠。你订阅了频道，但他们的新视频从未出现在你的主页中。它们不在通知中。它们就是...消失了。这并不意味着你不想看到它们——而是 YouTube 的算法把它们埋了。

另外：以精选内容洞察开始一天比无脑刷推荐信息流更有趣。

## 功能说明

- 从你最喜欢的频道列表中获取最新视频
- 总结或从每个视频的字幕中提取关键见解
- 每天（或按需）向你发送摘要

## 所需技能

安装 [youtube-full](https://clawhub.ai/therohitdas/youtube-full) 技能。

只需告诉你的 OpenClaw：

```text
"安装 youtube-full 技能并为我设置"
```
或

```bash
npx clawhub@latest install youtube-full
```

就是这样。智能体处理其余部分——包括账户创建和 API 密钥设置。你在注册时获得 **100 个免费积分**，无需信用卡。

> 注意：创建账户后，技能会根据操作系统自动将 API 密钥安全地存储在正确的位置，因此 API 将在所有上下文中工作。

![youtube-full 技能安装](https://pub-15904f15a44a4ea69350737e87660b92.r2.dev/media/1770620159490-e41e7baa.png)

### 为什么选择 TranscriptAPI.com 而不是 yt-dlp？

| CLI 工具（yt-dlp 等） | TranscriptAPI |
|--------------------------|---------------|
| 冗长的日志淹没智能体上下文 | 干净的 JSON 响应 |
| 在 GCP/云 OpenClaw 上不工作 | 在任何地方都能工作，速度快 |
| 被 YouTube 随机阻止 | 为 [YouTubeToTranscript.com](https://youtubetotranscript.com) 提供支持，服务数百万用户。缓存且可靠。 |
| 需要二进制安装 | 无二进制文件，只需 HTTP |

## 设置方法

### 选项 1：基于频道的摘要

提示 OpenClaw：

```text
每天早上 8 点，从这些 YouTube 频道获取最新视频，并给我一个包含每个视频关键见解的摘要：

- @TED
- @Fireship
- @ThePrimeTimeagen
- @lexfridman

对于每个新视频（在过去 24-48 小时内上传）：
1. 获取字幕
2. 用 2-3 个要点总结要点
3. 包括视频标题、频道名称和链接

如果频道句柄无法解析，搜索它并找到正确的。
将我的频道列表保存到记忆中，以便我以后可以添加/删除频道。
```

### 选项 2：基于关键词的摘要

跟踪关于特定主题的新视频：

```text
每天在 YouTube 上搜索关于"OpenClaw"（或"Claude Code"、"AI 智能体"等）的新视频。

维护一个名为 seen-videos.txt 的文件，其中包含你已经处理过的视频 ID。
仅获取不在该文件中的视频的字幕。
处理后，将视频 ID 添加到 seen-videos.txt。

对于每个新视频：
1. 获取字幕
2. 给我一个 3 点摘要
3. 注意与我的工作相关的任何内容

每天早上 9 点运行此操作。
```

这样你就不会浪费积分重新获取你已经看过的视频。

## 提示

- `channel/latest` 和 `channel/resolve` 是 **免费的**（0 积分）——检查新上传不花费任何费用
- 只有字幕花费 1 个积分
- 要求不同的摘要风格：关键要点、值得注意的引用、有趣时刻的时间戳
- 这已经作为产品存在 - [Recapio - 每日 YouTube 回顾](https://recapio.com/features/daily-recaps)
