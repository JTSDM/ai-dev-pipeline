# SPEC.md —— 规格说明模板

> 由 Spec 阶段产出，落在 `.ai-workflow/SPEC.md`。Review 后迁移到 `docs/ai-workflow/SPEC.md`（长期资产）。
> 只到接口层，不写实现。每个验收标准必须能映射 ≥1 个测试。

## 1. 背景与目标

- 要解决什么问题（一句话）：
- 成功指标（可观察）：
- 非目标（明确不做）：
- 约束（技术栈/时间/兼容性）：

## 2. 术语（统一语言）

> 引用项目专属 `docs/ai-workflow/CONTEXT.md`；此处只列本功能新增/修改的术语。

| 术语 | 定义 |
|------|------|
| 例：materialization cascade | 例：把一个课程分配到文件系统实际位置的级联过程 |

## 3. 模块边界

| 模块 | 职责 | 对外接口（简洁） | 依赖 | 被依赖 |
|------|------|----------------|------|--------|
| 例：导出服务 | 生成 CSV 并返回下载 | `export(criteria) -> file` | 数据层 | Web 层 |

## 4. 接口签名（只到接口层）

```text
// 例：导出历史净值
POST /api/export/net-value
  req: { date_from?, date_to?, format: 'csv'|'xlsx' }
  resp: 200 { download_url, row_count }
  errors: 400 参数不合法 / 403 无权限
```

## 5. 验收标准（每条映射 ≥1 个测试）

| # | 验收标准 | 对应测试 |
|---|---------|---------|
| 1 | 导出 CSV 中 date 列为 YYYY-MM-DD，且与页面展示一致 | `test_export_date_format` |
| 2 | 空数据导出返回 0 行且不报错 | `test_export_empty` |
| 3 | 无权限用户调用返回 403 | `test_export_unauthorized` |

## 6. 边界情况与错误处理

- 例：日期范围超过 10 年 → 拒绝（400）
- 例：第三方依赖失败 → 返回降级结果而非 500

## 7. 未决问题

> 从 DECISIONS.md 引用，或在此列出影响本 spec 的待定项。

| # | 未决问题 | 影响 | 验证时机 |
|---|---------|------|---------|
