# Battery-temperature-control

**Source 侧物理引导的动态 PDO 游标机制**：在 USB PD 充电中，由供电端（Source）单端预测热风险，动态限制受电端（Sink）可申请的 PDO 上界。目标是在机会约束下控制过温风险，同时尽量减少充电速度损失。兼容不支持 PPS 的存量设备，不改硬件，不依赖 Sink 配合。

## 文档

| 文件 | 内容 |
|---|---|
| [`AGENTS.md`](AGENTS.md) | 执行代理工作守则（必读） |
| [`docs/research_plan.md`](docs/research_plan.md) | 研究设计：问题设定、机制、理论、实验、默认参数、数据格式 |
| [`docs/tasks.md`](docs/tasks.md) | 任务清单与状态表 |
| [`docs/questions.md`](docs/questions.md) | 执行中产生的待回答问题 |
| [`docs/proposals/`](docs/proposals/) | 修改已定决策的提案 |

## 当前状态

研究计划 v1 已完成，代码尚未开始。下一步：T0.1（脚手架）、T0.2（规范核实）、T0.3（相关工作）可并行启动；人类同时处理 T0.6（回答 Q1–Q10）。
