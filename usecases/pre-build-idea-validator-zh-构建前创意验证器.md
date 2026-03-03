# 构建前创意验证器

在 OpenClaw 开始构建任何新内容之前，它会自动检查该创意是否已经存在于 GitHub、Hacker News、npm、PyPI 和 Product Hunt 中——并根据发现的内容调整其方法。

## 功能说明

- 在编写任何代码之前扫描 5 个真实数据源（GitHub、Hacker News、npm、PyPI、Product Hunt）
- 返回 `reality_signal` 分数（0-100），指示空间有多拥挤
- 显示带有星标数和描述的顶级竞争对手
- 当空间饱和时建议转向方向
- 作为构建前门：高信号 = 停止并讨论，低信号 = 继续

## 痛点

你告诉你的智能体"为我构建一个 AI 代码审查工具"，它愉快地花了 6 个小时编码。与此同时，GitHub 上已经存在 143,000+ 个仓库——顶级仓库有 53,000 个星标。智能体从不检查，因为你从未要求，它不知道要查看。你只在投入大量时间后才发现竞争对手。这种模式在每个新项目创意中重复。

## 所需技能

- [idea-reality-mcp](https://github.com/mnemox-ai/idea-reality-mcp) — 扫描真实数据源并返回竞争分数的 MCP 服务器

## 设置方法

1. 安装 idea-reality-mcp：

```bash
uvx idea-reality-mcp
```

2. 将 MCP 服务器添加到你的 OpenClaw 配置：

```json
{
  "mcpServers": {
    "idea-reality": {
      "command": "uvx",
      "args": ["idea-reality-mcp"]
    }
  }
}
```

3. 将此添加到你的 OpenClaw 智能体指令：

```text
在开始任何新项目、功能或工具之前，始终先运行 idea_check。

规则：
- 如果 reality_signal > 70：停止。报告带有星标数的前 3 个竞争对手。
  询问我是否想继续、转向或放弃。
- 如果 reality_signal 30-70：向我展示结果和 pivot_hints。
  建议现有项目未涵盖的利基角度。
- 如果 reality_signal < 30：继续构建。
  提及空间是开放的。
- 在编写任何代码之前，始终显示 reality_signal 分数和顶级竞争对手。
```
