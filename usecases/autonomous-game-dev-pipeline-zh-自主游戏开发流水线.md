# 自主教育游戏开发流水线

## 痛点
**起源故事：** 一位"老派 LANero"爸爸想为他的女儿们 Susana（3 岁）和 Julieta（即将出生）创建一个安全、无广告、高质量的游戏门户。现有网站充斥着垃圾信息、激进广告和欺骗性按钮（暗黑模式），让他的幼儿感到沮丧。

**挑战：** 构建一个"干净、快速、简单"的门户是容易的部分。真正的挑战是填充 **40+ 个教育游戏**，针对特定的发展阶段（0-15 岁），而没有开发团队。手动开发对于单独的父母开发者来说太慢了，而且在数十个游戏中保持一致性正在变成噩梦。

## 功能说明
这个用例定义了一个"游戏开发智能体"，自主管理游戏创建和维护的整个生命周期。工作流强制执行 **"Bug 优先"** 策略，智能体必须在实现新功能之前检查并解决报告的 bug。

**效率：** 这个流水线能够 **每 7 分钟生产 1 个新游戏或 bug 修复**。智能体不知疲倦地遍历 41+ 个计划游戏的待办事项，在创建新内容和纠正先前周期中检测到的问题之间交替。

当路径清晰时，智能体：
1.  **选择**：根据"轮询"策略从队列（`development-queue.md`）中识别下一个游戏，以平衡各年龄组的内容。
2.  **实现**：为游戏编写 HTML5/CSS3/JS 代码，严格遵循 `game-design-rules.md`（无框架、移动优先、离线可用）。
3.  **注册**：自动将游戏元数据添加到中央注册表（`games-list.json`）。
4.  **文档化**：更新 `CHANGELOG.md` 和 `master-game-plan.md` 状态。
5.  **部署**：处理 Git 工作流：获取 master、创建功能分支、使用常规提交提交更改，并合并回去。

## 提示词

这个工作流的核心是给智能体的 **系统指令**。这个提示将 LLM 变成一个遵守项目严格结构的有纪律的开发者。

*（**注意：** 生产中使用的实际提示是 **西班牙语**（`es-419`），以与项目的目标受众（拉丁美洲儿童）和该地区的潜在未来贡献者保持一致。下面的版本是为本文档翻译的。）*

```text
作为 Web 游戏开发和儿童 UX 专家。
你的目标是开发生产队列中的下一个游戏。

在开始之前，请阅读并分析以下上下文文件：

1.  BUG 上下文（最高优先级 - 关键）：
    @[bugs/]
    （检查此文件夹。如果有文件，你的任务是修复 **仅第一个文件**（按字母顺序）。暂时忽略其余的 bug 和游戏队列）。

2.  队列上下文（下一个游戏是什么）：
    @[development-queue.md]
    （识别"下一个游戏"部分中标记为 [NEXT] 的游戏。仅在没有 bug 时）。

3.  设计规则（技术标准）：
    @[game-design-rules.md]
    （严格遵循这些规则：纯 HTML/CSS/JS、文件夹结构、移动响应）

4.  游戏规格（机制和资源）：
    （根据游戏 ID 识别 games-backlog/ 中的相应文件）

5.  中央注册表（集成）：
    @[public/js/games-list.json]
    （你必须在此文件中注册新游戏，以便它出现在主页上）

任务：
0. **BUG 优先！**：如果 `bugs/` 文件夹有内容，你的唯一优先级是修复 **按字母顺序排列的第一个 bug**。创建一个 `fix/...` 分支，解决 **那个** bug，更新状态，并合并。**不要尝试一次修复多个 bug。**
   - 如果没有 BUG，继续下一个游戏：

1. **同步**：`git fetch && git pull origin master`（关键）。
2. 创建新分支：`git checkout -b feature/[game-id]`。
3. 在 'public/games/[game-id]/' 中创建文件夹和文件。
4. 根据待办事项和设计规则实现逻辑和设计。
5. 在 'games-list.json' 中注册游戏（关键）。
6. 完成后：
    - 更新 `CHANGELOG.md` 提升版本。
    - 更新 `master-game-plan.md` 和 `development-queue.md`。
    - 记录更改：`git commit -m "feat: add [game-id]"`。
7. **交付**：
    - 推送：`git push origin feature/[game-id]`。
    - 请求合并到 master。
    - 一旦在 master 中，推送更改（`git push origin master`）。
```

## 所需技能
-   **Git**：管理分支、提交和合并。

## 相关链接
-   [项目起源故事（LinkedIn）](https://www.linkedin.com/feed/update/urn:li:activity:7429739200301772800/) - 配置 OpenClaw 后这个项目如何出现。
-   [El Bebe Games 仓库](https://github.com/duberblockito/elbebe/tree/master) - 源代码。
-   [El Bebe Games 在线网站](https://elbebe.co/) - 这个流水线的结果。
-   [HTML5 游戏开发最佳实践](https://developer.mozilla.org/en-US/docs/Games)
