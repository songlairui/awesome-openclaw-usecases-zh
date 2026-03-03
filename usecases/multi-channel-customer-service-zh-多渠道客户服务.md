# 多渠道 AI 客户服务平台

小企业在多个应用中处理 WhatsApp、Instagram DM、电子邮件和 Google 评论。客户期望 24/7 即时响应，但雇用全天候覆盖的员工很昂贵。

这个用例将所有客户接触点整合到一个 AI 驱动的收件箱中，代表你智能响应。

## 功能说明

- **统一收件箱**：WhatsApp Business、Instagram DM、Gmail 和 Google 评论在一个地方
- **AI 自动响应**：自动处理常见问题、预约请求和常见查询
- **人工交接**：升级复杂问题或标记它们以供审查
- **测试模式**：向客户演示系统而不影响真实客户
- **业务上下文**：根据你的服务、定价和政策进行训练

## 真实业务示例

在 Futurist Systems，我们为本地服务企业（餐厅、诊所、沙龙）部署此功能。一家餐厅将响应时间从 4+ 小时减少到 2 分钟以内，自动处理 80% 的查询。

## 所需技能

- WhatsApp Business API 集成
- Instagram Graph API（通过 Meta Business）
- `gog` CLI 用于 Gmail
- Google Business Profile API 用于评论
- AGENTS.md 中的消息路由逻辑

## 设置方法

1. **通过 OpenClaw 配置连接渠道**：
   - WhatsApp Business API（通过 360dialog 或官方 API）
   - 通过 Meta Business Suite 的 Instagram
   - 通过 `gog` OAuth 的 Gmail
   - Google Business Profile API token

2. **创建业务知识库**：
   - 服务和定价
   - 营业时间和位置
   - 常见问题解答
   - 升级触发器（例如投诉、退款请求）

3. **使用路由逻辑配置 AGENTS.md**：

```text
## 客户服务模式
```
