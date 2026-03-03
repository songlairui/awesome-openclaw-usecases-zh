# 使用子智能体的自主项目管理

管理具有多个并行工作流的复杂项目很累人。你最终会不断切换上下文，跨工具跟踪状态，并手动协调交接。

这个用例实现了一个去中心化的项目管理模式，子智能体通过共享状态文件自主工作，而不是通过中央编排器协调。

## 痛点

传统的编排器模式会产生瓶颈——主智能体变成了交通警察。对于复杂项目（多仓库重构、研究冲刺、内容流水线），你需要能够并行工作而无需持续监督的智能体。

## 功能说明

- **去中心化协调**：智能体读写共享的 `STATE.yaml` 文件
- **并行执行**：多个子智能体同时处理独立任务
- **无编排器开销**：主会话保持精简（CEO 模式——仅策略）
- **自文档化**：所有任务状态持久化在版本控制文件中

## 核心模式：STATE.yaml

每个项目维护一个 `STATE.yaml` 文件作为单一事实来源：

```yaml
# STATE.yaml - 项目协调文件
project: website-redesign
updated: 2026-02-10T14:30:00Z

tasks:
  - id: homepage-hero
    status: in_progress
    owner: pm-frontend
    started: 2026-02-10T12:00:00Z
    notes: "正在处理响应式布局"

  - id: api-auth
    status: done
    owner: pm-backend
    completed: 2026-02-10T14:00:00Z
    output: "src/api/auth.ts"

  - id: content-migration
    status: blocked
    owner: pm-content
    blocked_by: api-auth
    notes: "等待新端点架构"

next_actions:
  - "pm-content: api-auth 完成后恢复迁移"
  - "pm-frontend: 与设计团队审查 hero"
```

## 工作原理

1. **主智能体接收任务** → 生成具有特定范围的子智能体
2. **子智能体读取 STATE.yaml** → 找到分配的任务
3. **子智能体自主工作** → 更新 STATE.yaml 进度
4. **其他智能体轮询 STATE.yaml** → 接手未阻塞的工作
5. **主智能体定期检查** → 审查状态，调整优先级

## 所需技能

- `sessions_spawn` / `sessions_send` 用于子智能体管理
- 文件系统访问 STATE.yaml
- Git 用于状态版本控制（可选但推荐）

## 设置：AGENTS.md 配置

```text
## PM 委托模式

主会话 = 仅协调器。所有执行都交给子智能体。

工作流：
1. 新任务到达
2. 检查 PROJECT_REGISTRY.md 是否有现有 PM
3. 如果 PM 存在 → sessions_send(label="pm-xxx", message="[task]")
4. 如果是新项目 → sessions_spawn(label="pm-xxx", task="[task]")
5. PM 执行，更新 STATE.yaml，报告回来
6. 主智能体向用户总结

规则：
- 主会话：最多 0-2 次工具调用（仅 spawn/send）
- PM 拥有自己的 STATE.yaml 文件
- PM 可以为并行子任务生成子子智能体
- 所有状态更改提交到 git
```

## 示例：生成 PM

```text
用户："重构认证模块并更新文档"

主智能体：
1. 检查注册表 → 没有活动的 pm-auth
2. 生成：sessions_spawn(
     label="pm-auth-refactor",
     task="重构认证模块，更新文档。在 STATE.yaml 中跟踪"
   )
3. 响应："已生成 pm-auth-refactor。完成后我会报告。"

PM 子智能体：
1. 创建 STATE.yaml 并分解任务
2. 完成任务，更新状态
3. 提交更改
4. 向主智能体报告完成
```

## 关键见解

- **STATE.yaml > 编排器**：基于文件的协调比消息传递扩展性更好
- **Git 作为审计日志**：提交 STATE.yaml 更改以获得完整历史
- **标签约定很重要**：使用 `pm-{project}-{scope}` 便于跟踪
- **精简主会话**：主智能体做得越少，响应越快

## 灵感来源

这个模式受 [Nicholas Carlini 的方法](https://nicholas.carlini.com/) 启发，让智能体自组织而不是微观管理它们。

## 相关链接

- [OpenClaw 子智能体文档](https://github.com/openclaw/openclaw)
- [Anthropic：构建有效的智能体](https://www.anthropic.com/research/building-effective-agents)
