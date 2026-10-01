# ai-dev-pipeline

> AI 辅助开发的完整编排流水线：**Grill（拷问）→ Spec（规格）→ Tickets（拆票）→ Implement（实现）→ Review（审查）**

核心思想：**交接物是文件，不是聊天记录**；流水线里没有一步叫"写代码"，规划质量决定 AI 产出质量。

---

## 致谢与来源

本项目的**方法论内核来自公开成果，工程化落地为原创贡献**，两者边界如下，以便引用与追责：

| 部分 | 来源 |
|------|------|
| 5 个核心技巧（Grill / 统一语言 / TDD / Deep Module / 接口设计委托实现） | **Matt Pocock**，AI Engineer Summit 演讲 *Software Fundamentals Matter More Than Ever* |
| 流水线形态（grill → spec → tickets → implement → review） | 社区项目 `mattpocock/skills`（v1.1） |
| "长上下文让回退重来近乎免费，研究与规划才是最高杠杆" | 「瀑布流 2.0」洞见 |
| 底层设计原则 | Brooks《设计的设计》、Evans《领域驱动设计》、Ousterhout《软件设计的哲学》、《程序员修炼之道》 |
| **分级裁剪矩阵、双区落盘、文档↔skill 单向依赖、双向回退与 mini-Grill、Review 逐条处置表、真实生产验证** | **本项目原创** |

如果你要引用其中的方法论思想，请引用 Matt Pocock 的原始分享；引用编排与工程化设计，请引用本项目。

## 解决的问题

AI 写代码越写越烂，不是 AI 的问题，是丢了软件基础。典型症状：

- 需求一句话就开写 → AI 用"貌似合理的猜测"填坑
- 出 bug 就改 prompt 重新生成 → 软件熵，每循环一次更烂
- 关键决策只存在于聊天记录 → 换窗口/换模型就全丢
- 没有测试约束 → AI 生成的测试只会同意它自己产出的东西

## 核心机制

| 机制 | 说明 |
|------|------|
| 5 步流水线 | Grill → Spec → Tickets → Implement → Review，没有一步叫"写代码" |
| 分级裁剪 L/M/S | 按"出错成本"选档，S 档必须轻，防流程过重被架空 |
| 交接物 = 文件 | DECISIONS / SPEC / TICKETS 全部落盘，抗换模型、清上下文 |
| TDD 控制回路 | 先写失败的测试，再写实现；测试是最便宜的 oracle |
| 统一语言 | 生成项目专属 CONTEXT.md 术语表，AI 不再啰嗦、不再猜 |
| 双向回退 | 规格级错误回 Spec、实现级错误回 Implement；M 档歧义触发 mini-Grill |
| 逐条处置表 | 引入外部评审时，必须产出「采纳 / 有据反驳 / 不在本编号」三分类表，禁只改不答 |

## 安装

把 `ai-dev-pipeline/` 目录放到你的 skills 目录：

- **WorkBuddy / Claude Code 等**：`~/.workbuddy/skills/` 或 `~/.claude/skills/`
- **项目级**：`{workspace}/.workbuddy/skills/`

然后在对话中调度（如 `@ai-dev-pipeline`，或由你的 skill 选择器决策表路由）。

## 使用：三档裁剪

| 档位 | 适用 | 流程 |
|------|------|------|
| **L 大任务** | 新项目/新模块/架构级决策 | 完整 Grill → Spec → Tickets → Implement → Review |
| **M 中任务** | 单功能/跨文件改动 | 10 问自检（可默认则跳过）→ Spec → Tickets → Implement → Review |
| **S 小改动** | 修 bug/改文案 | 一行最小任务描述（含验收标准+测试）→ Implement → Review |

判定原则：**按"出错成本"选档**。需求不明确、跨多文件、有数据模型或接口设计 → 至少 M 档。拿不准时往高一档走。

## 工作区约定

- **过程工作区**：项目根目录 `.ai-workflow/`（建议 gitignore），过程文件先写这里
- **长期资产区**：项目 `docs/ai-workflow/`（纳入 Git），Review 后迁移长期价值内容

## 文件结构

```
ai-dev-pipeline/
├── SKILL.md                          # 操作事实源（唯一）
└── references/
    ├── docs/
    │   └── workflow-ai-dev-pipeline.md  # 方法论总纲（原则/陷阱/何时用）
    ├── grill-questions.md             # 三层问题库（核心 10 问/领域扩展/深度拷问）
    └── templates/
        ├── CONTEXT.md                 # 项目术语表通用模板
        ├── DECISIONS.md               # 决策记录模板
        ├── SPEC.md                    # 规格说明模板
        └── TICKETS.md                 # 任务清单模板
```

## 文档↔skill 单向依赖

SKILL.md 是唯一操作事实源，只改 SKILL.md 就能改流程。总纲文档只写原则（"为什么、何时用、陷阱"），不复制步骤/模板/路径——防止双写漂移。仅当**流程原则、裁剪阈值、生命周期规则、方法论来源**变化时才同步更新总纲。

## 验证情况

已在运营中的真实生产项目（教育类在线答题平台，非 demo/玩具项目）完成双档试点：

- **M 档**（数据导出类功能，跨前后端）：13/13 冒烟测试通过 + 前端构建通过 + 回归测试通过
- **S 档**（前端路由边界修复）：6/6 逻辑测试通过 + Review 环节 2 分钟完成

两点值得注意：S 档能压缩到"一行任务描述 + 直接实现"，说明轻量裁剪没有把流程架空；M 档在无 SPEC 的情况下靠 10 问自检仍产出了可验收的验收标准。

## License

MIT License — 详见 [LICENSE](LICENSE)。
