# Polymarket 自动驾驶：自动化模拟交易

手动监控预测市场的套利机会并执行交易既耗时又需要持续关注。你想在不冒真实资本风险的情况下测试和完善交易策略。

这个工作流使用自定义策略自动化 Polymarket 上的模拟交易：

• 通过 API 监控市场数据（价格、交易量、价差）
• 使用 TAIL（趋势跟踪）和 BONDING（逆向）策略执行模拟交易
• 跟踪投资组合表现、盈亏和胜率
• 每天向 Discord 提供交易日志和见解摘要
• 从模式中学习：根据回测结果调整策略参数

## 痛点

预测市场变化很快。手动交易意味着错过机会、情绪化决策以及难以跟踪什么有效。用真钱测试策略会在你了解市场行为之前冒损失风险。

## 功能说明

自动驾驶持续扫描 Polymarket 寻找机会，使用可配置策略模拟交易，并记录所有内容以供分析。你醒来时会看到它夜间"交易"了什么、什么有效、什么无效的摘要。

示例策略：
- **TAIL**：当交易量激增且动量明确时跟随趋势
- **BONDING**：当市场对新闻反应过度时买入逆向头寸
- **SPREAD**：识别具有套利潜力的错误定价市场

## 所需技能

- `web_search` 或 `web_fetch`（用于 Polymarket API 数据）
- `postgres` 或 SQLite 用于交易日志和投资组合跟踪
- Discord 集成用于每日报告
- Cron 作业用于持续监控
- 子智能体生成用于并行市场分析

## 设置方法

1. 为模拟交易设置数据库：
```sql
CREATE TABLE paper_trades (
  id SERIAL PRIMARY KEY,
  market_id TEXT,
  market_name TEXT,
  strategy TEXT,
  direction TEXT,
  entry_price DECIMAL,
  exit_price DECIMAL,
  quantity DECIMAL,
  pnl DECIMAL,
  timestamp TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE portfolio (
  id SERIAL PRIMARY KEY,
  total_value DECIMAL,
  cash DECIMAL,
  positions JSONB,
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

2. 创建 Discord 频道用于更新（例如 #polymarket-autopilot）。

3. 提示 OpenClaw：
```text
你是 Polymarket 模拟交易自动驾驶。持续运行（通过 cron 每 15 分钟）：

1. 从 Polymarket API 获取当前市场数据
2. 使用这些策略分析机会：
   - TAIL：跟随强劲趋势（>60% 概率 + 交易量激增）
   - BONDING：对过度反应的逆向操作（新闻导致突然下跌 >10%）
   - SPREAD：当 YES+NO > 1.05 时套利
3. 在数据库中执行模拟交易（起始资本：$10,000）
4. 跟踪投资组合状态并更新头寸

每天早上 8 点，向 Discord #polymarket-autopilot 发布摘要：
- 昨天的交易（入场/出场价格、盈亏）
- 当前投资组合价值和未平仓头寸
- 胜率和策略表现
- 市场见解和建议

在高交易量期间使用子智能体并行分析多个市场。

永远不要使用真钱。这仅是模拟交易。
```

4. 根据表现迭代策略。调整阈值、添加新策略、回测历史数据。

## 相关链接

- [Polymarket API](https://docs.polymarket.com/)
- [模拟交易最佳实践](https://www.investopedia.com/articles/trading/11/paper-trading.asp)
