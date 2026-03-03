# Todoist 任务管理器：智能体任务可见性
通过将内部推理和进度日志直接同步到 Todoist，最大化长时间运行的智能体工作流的透明度。

## 痛点
当智能体运行复杂的多步骤任务（如构建全栈应用或执行深度研究）时，用户通常会失去对智能体当前正在做什么、已完成哪些步骤以及智能体可能卡在哪里的跟踪。手动检查聊天日志对于后台任务来说很繁琐。

## 功能说明
这个用例使用 `todoist-task-manager` 技能来：
1.  **可视化状态**：在特定部分创建任务，如 `🟡 进行中` 或 `🟠 等待中`。
2.  **外化推理**：将智能体的内部"计划"发布到任务描述中。
3.  **流式日志**：实时将子步骤完成情况作为评论添加到任务中。
4.  **自动协调**：心跳脚本检查停滞的任务并通知用户。

## 所需技能
你不需要预构建的技能。只需提示你的 OpenClaw 智能体创建下面 **设置指南** 中描述的 bash 脚本。由于 OpenClaw 可以管理自己的文件系统并执行 shell 命令，它将根据请求有效地为你"构建"技能。

## 详细设置指南

### 1. 配置 Todoist
创建一个项目（例如"OpenClaw 工作区"）并获取其 ID。为不同状态创建部分：
- `🟡 进行中`
- `🟠 等待中`
- `🟢 完成`

### 2. 实现："智能体构建"技能
你可以要求 OpenClaw 为你创建这些脚本，而不是安装技能。每个脚本处理与 Todoist API 通信的不同部分。

**`scripts/todoist_api.sh`**（核心包装器）：
```bash
#!/bin/bash
# 用法: ./todoist_api.sh <endpoint> <method> [data_json]
ENDPOINT=$1
METHOD=$2
DATA=$3
TOKEN="YOUR_TODOIST_API_TOKEN"

if [ -z "$DATA" ]; then
  curl -s -X "$METHOD" "https://api.todoist.com/rest/v2/$ENDPOINT" \
    -H "Authorization: Bearer $TOKEN"
else
  curl -s -X "$METHOD" "https://api.todoist.com/rest/v2/$ENDPOINT" \
    -H "Authorization: Bearer $TOKEN" \
    -H "Content-Type: application/json" \
    -d "$DATA"
fi
```

**`scripts/sync_task.sh`**（任务和状态管理）：
```bash
#!/bin/bash
# 任务同步脚本
```
