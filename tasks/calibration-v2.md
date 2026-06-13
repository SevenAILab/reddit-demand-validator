# V2 校准测试记录

## 样本 1：牙刷颜色推荐（预期 weak）

- 实际结论: weak / 置信度: medium
- 证伪门触发: 是
- 结论是否符合预期: 是。正向痛点缺席，只有闲聊/娱乐弱信号；不需要修正规则。

## 样本 2：自由职业者催款（预期 >= mixed）

- 实际结论: strong / 置信度: medium
- 高共鸣线程数: 0（Reddit JSON API 在本机返回 403，无法可靠采集 score/comment/agreement 元数据；未使用高共鸣加权）
- 已付费证据: 找到。发票软件（FreshBooks/QuickBooks/Wave/Stripe）、会计/律师/collection agency、支付工具与催收流程都属于 revealed spend 或付费替代方案。
- 结论是否符合预期: 是。即使不使用高共鸣加权，也满足跨社区重复、痛点强、workaround 成本高、revealed WTP 的 strong 判定。

## 修正记录

- 无规则漏洞需要修正。
- 实施注意：本次校准发现 Reddit JSON API 可能返回 403 HTML/block 页面；V2 的 fallback 与抓取完整性说明是必要能力。
