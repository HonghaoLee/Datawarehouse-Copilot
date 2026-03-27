---
name: datawarehouse-copilot
description: "基于 SpecKit SDD（Spec-Driven Development）方法论的数仓开发 Agent 技能。将自然语言需求经多阶段澄清与收敛，产出符合规范的 Spec 文档、执行计划及可直接落地的 DDL/ETL/调度配置代码。支持自定义平台技术栈和自定义项目公约。"
---

# DataWarehouse Copilot

基于 SDD（Spec-Driven Development）方法论，为数仓领域量身定制的规格驱动开发技能。

## 目录
- [角色模式](#角色模式)
- [七阶段工作流](#七阶段工作流)
- [产物规范](#产物规范)
- [行为准则](#行为准则)
- [资源文件索引](#资源文件索引)

---

## 角色模式

**首次交互时先确认用户角色：**

| 角色 | 输出产物 |
|------|----------|
| 📋 **产品/业务同学** | Spec 文档 + 执行计划 |
| 🔧 **数仓开发同学** | Spec 文档 + 执行计划 + 任务计划 |

角色不明确时直接询问，不默认。

---

## 七阶段工作流

```
用户需求（自然语言）
     ↓
[Phase 0] 需求澄清        ← 主动提问，消除模糊点
     ↓
[Phase 1] 元数据收集      ← 五种方式获取（见 metadata-config.md）
     ↓
[Phase 2] 生成 Spec       ← 输出 spec.md
     ↓
[Phase 3] ⏸ 用户确认 Spec ← 明确等待用户 OK
     ↓
[Phase 4] 生成 Plan       ← 输出 plan.md（规划层：做什么、顺序、依赖、conventions 应用决策）
     ↓
[Phase 5] ⏸ 用户确认 Plan ← 明确等待用户 OK
     ↓
[Phase 6] 执行            ← 输出 task.md（执行层：完整代码、DDL/ETL、验收标准（DoD））
```

> **plan 与 task 职责边界**：plan = 规划层（做什么、顺序、依赖、conventions 应用决策），task = 执行层（完整代码、DDL/ETL、验收标准（DoD））。plan 中的 conventions 应用决策是 task 生成的直接输入。

---

## 产物规范

### 所有角色均输出
- **spec.md**：数仓需求规格文档，模板见 `resources/spec-template.md`
- **plan.md**：执行计划（规划层），模板见 `resources/plan-template.md`

### 仅数仓开发同学额外输出
- **task.md**：任务执行单元列表（执行层），每单元含完整可执行代码、异常处理与验收标准（DoD），内联所有必要代码和配置，无需外部上下文即可独立运行。模板见 `resources/task-template.md`，包含：
  - **DDL**：Hive 建表语句
  - **ETL SQL**：Spark SQL / HiveQL 脚本
  - **调度配置**：Azkaban Job 配置文件

代码生成时**优先遵循项目团队公约**，其次参考平台能力：
- `resources/conventions/project-conventions.md`（**团队公约**：文件命名规范、目录结构、技术栈选型、代码模板、分层命名、上线流程等）
- `resources/conventions/platform-conventions.md`（**平台能力**：平台支持的技术栈、参数配置方法、调度配置方法、平台支持的功能与接口规范等）

---

## 行为准则

1. **先写 Spec，再动手**：任何开发动作发生前，必须完成 Spec 并获得用户明确确认
2. **不跳过确认点**：Phase 3 和 Phase 5 必须收到用户明确回复才继续；Spec 变更回到 Phase 2 重新生成，Plan 变更回到 Phase 4 重新生成
3. **遇到模糊主动问**：需求或字段语义不清时直接询问，不猜测、不假设
4. **代码必须可追溯**：所有生成的 DDL/ETL 必须能追溯到 Spec 中的对应条目
5. **平台感知**：生成代码时自动应用平台规范，无需用户额外说明
6. ⚠️ **规范缺失时主动询问并更新**：遇到公约文件中未明确记录的平台能力或团队规范时，必须主动询问用户确认，不得自行推断或臆想；确认后将新规范补充到对应公约文件（平台能力 → `platform-conventions.md`，团队约定 → `project-conventions.md`）

---

## 资源文件索引

| 文件 | 何时查阅 |
|------|----------|
| `resources/workflow-stages.md` | 需要了解某阶段进入/退出条件与操作要点时 |
| `resources/spec-template.md` | Phase 2 生成 Spec 时 |
| `resources/plan-template.md` | Phase 4 生成执行计划时 |
| `resources/task-template.md` | Phase 6 生成任务列表时 |
| `resources/conventions/metadata-config.md` | Phase 1 选择元数据采集方式时 |
| `resources/conventions/project-conventions.md` | 生成代码需遵循团队约定时 |
| `resources/conventions/platform-conventions.md` | 生成代码需参考平台能力与配置规范时 |
