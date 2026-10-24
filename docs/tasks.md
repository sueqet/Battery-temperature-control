# 任务清单

> 先读 `AGENTS.md` 和 `docs/research_plan.md`，再读本文件。
>
> **执行者**：Agent = 执行代理可独立完成；Human = 必须由人类操作（硬件、实验、决策）；Agent+Human = 代理写代码或文档，人类在硬件上执行或验证。
>
> **状态取值**：未开始 / 进行中 / 阻塞 / 完成。每个执行者只更新自己那一行。

## 状态表

| ID | 名称 | 执行者 | 依赖 | 状态 |
|---|---|---|---|---|
| T0.1 | 仓库脚手架 | Agent | — | 未开始 |
| T0.2 | PD 规范与标准事实核实 | Agent | — | 未开始 |
| T0.3 | 相关工作检索 | Agent | — | 未开始 |
| T0.4 | 人类实验规程编写 | Agent | T0.2 | 未开始 |
| T0.5 | 商用设备热管理行为普查 | Human | T0.4 | 未开始 |
| T0.6 | 回答待决策问题 Q1–Q10 | Human | — | 未开始 |
| T0.7 | PD Source 硬件平台调研 | Agent | T0.2 | 未开始 |
| T1.1 | Sink 与 Source 仿真器 | Agent | T0.1 | 未开始 |
| T1.2 | 虚拟设备种群生成 | Agent | T1.1 | 未开始 |
| T1.3 | PDO 工具库与合规校验器 | Agent | T0.1, T0.2 | 未开始 |
| T1.4 | 仿真数据集生成与划分 | Agent | T1.2, T1.3 | 未开始 |
| T1.5 | 风险预测器 M0/M1/M2 | Agent | T1.4 | 未开始 |
| T1.6 | Conformal 校准模块 | Agent | T1.5 | 未开始 |
| T1.7 | 游标控制器 C 与安全兜底层 | Agent | T1.3, T1.6 | 未开始 |
| T1.8 | 对比控制器 B0–B7 | Agent | T1.3, T1.5 | 未开始 |
| T1.9 | 仿真闭环评估与 Pareto 曲线 | Agent | T1.7, T1.8 | 未开始 |
| T1.10 | 仿真消融实验 | Agent | T1.9 | 未开始 |
| T1.11 | （可选）MPC 与贪心截断对比 | Agent | T1.9 | 未开始 |
| T2.1 | 最小可编程 Source 固件 | Agent+Human | T0.7, T0.6（Q2） | 未开始 |
| T2.2 | 搭建参考 Sink | Human | T0.4, T0.6（Q10） | 未开始 |
| T2.3 | 实测数据采集与对齐工具 | Agent | T0.1 | 未开始 |
| T2.4 | 同功率不同电压实验与重新协商兼容性实验 | Human | T2.1, T2.3 | 未开始 |
| T2.5 | 实测数据采集（随机游标调度） | Human | T2.1, T2.2, T2.3 | 未开始 |
| T2.6 | Sim-to-real 训练与评估 | Agent | T1.6, T2.5 | 未开始 |
| T2.7 | 闭环控制固件（PC 在环 → MCU） | Agent+Human | T1.7, T2.1, T2.6 | 未开始 |
| T2.8 | 实测闭环实验 | Human | T2.7 | 未开始 |
| T2.9 | 实测结果分析 | Agent | T2.8 | 未开始 |
| T2.10 | 部署开销测量 | Agent+Human | T2.7 | 未开始 |
| T3.1 | 论文图表复现脚本 | Agent | T1.10, T2.9 | 未开始 |
| T3.2 | （可选）ICCE 短文初稿 | Agent | T1.9, T0.6（Q7） | 未开始 |
| T3.3 | TCE 全文初稿 | Agent | T3.1, T0.3 | 未开始 |
| T3.4 | 审阅与投稿 | Human | T3.3 | 未开始 |

## 里程碑

| 里程碑 | 完成条件 |
|---|---|
| M0 预研完成 | T0.1–T0.7 完成 |
| M1 仿真结果 | T1.9 产出 Pareto 曲线 |
| M2 实测数据 | T2.5、T2.6 完成 |
| M3 实测闭环 | T2.8、T2.9 完成 |
| M4 论文初稿 | T3.3 完成 |

**可并行的工作**：T0.1、T0.2、T0.3 可以同时开始。阶段 1（仿真）只依赖代理任务，可以和人类的阶段 0、阶段 2 硬件准备并行推进。

---

# 阶段 0：基础与预研

## T0.1 仓库脚手架（Agent）

**目标**：建立 Python 项目骨架，让后续任务有统一的结构。

**步骤**：
1. 创建以下目录结构（每个子包放一个空的 `__init__.py`）：
   ```
   src/pdcursor/
     pd/        # PDO 数据结构、Power Rules 配置、Source_Capabilities 生成与校验
     sim/       # 电池与设备热模型、Sink 行为、Source/线缆模型、会话仿真
     data/      # 数据格式、白名单、数据集构建与划分
     models/    # 风险预测器 M0/M1/M2、conformal 校准
     control/   # 排序轴构造、游标控制器、滞回、安全兜底、对比控制器
     eval/      # 指标、Pareto、统计
     tools/     # 实测日志解析（Android、热电偶、Source）
   configs/
   scripts/
   tests/
   reports/
   docs/notes/
   docs/proposals/
   ```
2. 编写 `pyproject.toml`：
   - 依赖：numpy、scipy、pandas、pyarrow、pyyaml、torch、matplotlib、pybamm；
   - 开发依赖：pytest、ruff。
3. 编写 `.gitignore`：忽略 `data/`、`results/raw/`、`__pycache__/`、`.venv/` 等。
4. 编写 `configs/defaults.yaml`，内容与 `docs/research_plan.md` 第 12 节完全一致。
5. 编写 `src/pdcursor/config.py`：加载 YAML，支持用实验配置覆盖默认值。
6. 编写 `src/pdcursor/data/schema.py`：
   - 定义 `research_plan.md` 第 13 节的四张表的列名与类型；
   - 定义常量 `FEATURE_WHITELIST` 和 `LABEL_ONLY`；
   - 定义函数 `assert_features_allowed(columns)`：输入列不全在白名单内时抛出异常。
7. `docs/questions.md` 和 `docs/proposals/` 已经存在，不要覆盖。

**产出**：上述文件。

**验收标准**：
- `pip install -e .[dev]` 成功。
- `pytest` 通过，至少包含：
  - 配置加载测试；
  - `assert_features_allowed` 的正例和反例测试（例如传入 `t_bat` 必须报错）。
- `ruff check` 无报错。
- `configs/defaults.yaml` 与 `research_plan.md` 第 12 节逐项一致（在报告里列出对照表）。

## T0.2 PD 规范与标准事实核实（Agent）

**目标**：把本项目依赖的规范事实逐条核实，避免论文和代码建立在错误事实上。

**步骤**：
1. 获取 USB PD 规范原文（USB-IF 官网可免费下载，至少包括 R2.0 和 R3.1 或更新版本），以及 USB Type-C 规范。
2. 对下表每一条，查找原文出处，记录：结论（已证实 / 已证伪 / 部分正确 / 无法核实）、规范版本、章节号、原文引用（简短）。

   | 编号 | 待核实的事实 |
   |---|---|
   | F1 | Source_Capabilities 最多包含 7 个 PDO |
   | F2 | 第一个 PDO 必须是 vSafe5V Fixed PDO |
   | F3 | Fixed PDO 必须按电压升序排列 |
   | F4 | 同类型、同电压只能有一个 PDO |
   | F5 | Fixed PDO 最大电流字段单位为 10 mA，以及字段位宽 |
   | F6 | Source 可在显式合约期间随时发送新的 Source_Capabilities；Sink 必须响应 Request；相关定时器 |
   | F7 | 新能力中不包含当前合约时 Sink 的行为；何种情况触发 Hard Reset；Hard Reset 时 VBUS 的行为 |
   | F8 | Request 中的 Capability Mismatch 位的含义 |
   | F9 | Power Rules：各 PDP 下必须提供的电压/电流（列出 15W、27W、45W、60W、65W、100W 的完整表） |
   | F10 | Power Rules 是否允许 Source 动态降低广播能力；与多口充电器动态分配相关的规范表述 |
   | F11 | PD 2.0 中 Get_Sink_Cap 的存在性，以及 Sink 是否必须响应 |
   | F12 | Discover Identity（SOP）对 UFP/Sink 是否可选；能取得的字段（VID、PID 等） |
   | F13 | PD 3.0 Status 消息的字段（Internal Temp、Temperature Status 等）及 Sink 是否必须支持 |
   | F14 | PD 3.0 Alert 消息中的 OTP 事件 |
   | F15 | Sink 实际电流不得超过 Request 中申请值的规范表述 |
   | F16 | Type-C 的 Rp 电流广播（Default / 1.5A / 3.0A）及 Source 动态改变 Rp 的规则 |
   | F17 | Type-C 插座/插头的温升要求 |
   | F18 | IEC 62368-1 中可触及表面温度限值（按材料、接触时长） |
   | F19 | JEITA 锂电池充电温度分段（常见的温度区间和对应动作） |

3. F17–F19 的原文可能需要付费，或无法访问。此时标"无法核实"，写明你能找到的二手来源（附链接），并在 `docs/questions.md` 中提问，请人类提供原文。**不得凭记忆填写数值。**

**产出**：`docs/notes/pd_spec_notes.md`。

**验收标准**：
- F1–F19 每一条都有结论和出处。"已证实"的条目必须有规范版本和章节号。
- 如果某条结论与 `research_plan.md` 的描述不一致，在报告中单独列出，并在 `docs/proposals/` 下提交修正建议。

## T0.3 相关工作检索（Agent）

**目标**：建立经过核实的参考文献库，明确本研究的差异点。

**步骤**：
1. 按 `research_plan.md` 第 10 节的方向逐类检索。
2. 每篇文献必须**实际打开过** DOI 或 URL，确认标题、作者、年份、期刊/会议与链接一致。
3. 每篇写 2–4 句：它做了什么，与本研究的关系或差异。
4. 每个类别末尾写一段"本研究的差异点"。
5. 商业产品（例如充电器的温控功能）单列一节，只记录官方公开资料中的描述，附链接。

**产出**：
- `docs/related_work.md`；
- `docs/references.bib`（BibTeX，与 `related_work.md` 一一对应）。

**验收标准**：
- 至少 30 篇已核实的文献。
- 其中至少 10 篇来自 IEEE TCE 或 IEEE ICCE。
- 每条都有可访问的 DOI 或 URL。
- 不存在无法核实的条目。确实找不到原文的，不要列入。

## T0.4 人类实验规程编写（Agent）

**目标**：为人类执行的实验写出可直接照做的规程和记录表。

**需要编写的规程**：
1. `docs/protocols/P1_commercial_survey.md`：商用设备热管理行为普查（供 T0.5 使用）。
   - 设备：商用充电器 + 带 PD 协议显示的 USB-C 功率计 + 被测设备。
   - 条件：35°C 或 40°C 环境（有温箱时）；息屏；从 10% 电量开始充 60 分钟。
   - 记录：协商的 PDO、输入功率曲线；Android 设备另外记录电池温度、电流。
   - 判定方法：输入功率是否随温度升高而下降、在什么温度开始下降。用来把设备归入威胁模型的 (a)、(b)、(c) 三类。
2. `docs/protocols/P2_same_power_diff_voltage.md`：同功率不同电压温升实验（供 T2.4 使用）。
   - 用可编程 Source 只广播一个高电压档，例如分别只开放 15V/3A 或只开放 20V/2.25A，比较各热节点温升。
3. `docs/protocols/P3_renegotiation_compat.md`：重新协商兼容性实验（供 T2.4 使用）。
   - 每台设备在充电中触发 N 次（默认 50 次）重新协商，覆盖"保留当前合约"和"移除当前合约"两种情况。
   - 记录成功、Hard Reset、断开、设备界面异常。
4. `docs/protocols/P4_data_collection.md`：随机游标调度的数据采集（供 T2.5 使用）。
5. `docs/protocols/P5_reference_sink.md`：参考 Sink 的搭建与使用（供 T2.2 使用）。

**每个规程必须包含以下小节**：目的、所需设备、**安全**（按 `AGENTS.md` 第 6 节）、准备步骤、执行步骤（编号）、记录表模板（CSV 列名）、数据文件命名规则、常见问题。

**验收标准**：
- 5 个规程齐全，每个都包含"安全"小节。
- 记录表的列名能映射到 `research_plan.md` 第 13 节的数据格式，并给出映射表。
- 涉及规范事实的地方只引用 T0.2 中"已证实"的条目。

## T0.5 商用设备热管理行为普查（Human）

**目标**：用数据回答"有多少设备的热管理缺失或滞后"，支撑威胁模型。

**步骤**：按 `docs/protocols/P1_commercial_survey.md` 执行，尽可能覆盖多类设备（Q6）。

**产出**：`data/survey/` 下的原始数据，以及填好的记录表。

**验收标准**：每台设备都有完整记录，并被归入威胁模型 (a)、(b)、(c) 之一，或标为"无法判断"。

## T0.6 回答待决策问题（Human）

在 `docs/research_plan.md` 第 11 节表格中，对 Q1–Q10 填写决定。需要修改默认参数的，直接修改第 12 节和 `configs/defaults.yaml`。

## T0.7 PD Source 硬件平台调研（Agent）

**目标**：为人类选型（Q2）提供对比材料。**只调研，不采购，不替人类做决定。**

**对比维度**：
1. 能否在合约期间按命令重发自定义的 Source_Capabilities。
2. 能否任意设置每个 Fixed PDO 的电压和最大电流。
3. 是否支持 PPS（用于 B7 参照）。
4. 是否内置 VBUS 电压/电流测量，精度和采样率如何。
5. 是否有 NTC 输入（Source 温度、插座温度）。
6. MCU 资源（Flash、RAM、主频），能否运行 int8 小模型。
7. 开源固件或协议栈是否可用，以及许可证。
8. 能否获取 PD 消息日志（用于 `events` 表）。
9. 价格和可购买性。

**候选方向**（需逐一核实，不要假定它们满足条件）：
- 带 UCPD 外设的 STM32G0/G4 系列开发板；
- TI 的 PD 控制器评估板；
- Infineon/Cypress 的 PD 控制器评估板；
- 开源 USB PD 协议栈（例如 Zephyr、ChromeOS EC 中的实现）。

**产出**：`docs/hw_platform_survey.md`，包含对比表和每个候选的资料链接。

**验收标准**：
- 每个结论都有官方资料链接。
- 至少 4 个候选。
- 末尾写出"推荐及理由"，但明确标注"最终由人类决定"。

---

# 阶段 1：仿真闭环

## T1.1 Sink 与 Source 仿真器（Agent）

**目标**：实现一个可配置、可复现的充电会话仿真器，以 1 Hz 输出符合 `research_plan.md` 第 13 节格式的数据。

**模块与默认模型**（所有参数都放进配置）：

1. **电池**（`sim/battery.py`）：
   - 1RC 等效电路模型：`V_b = OCV(SOC) − I·R0 − V1`，`dV1/dt = −V1/(R1·C1) + I/C1`。
   - OCV 曲线：可以从 PyBaMM 的公开参数集中提取，或用文献中的多项式。在报告中写明来源。
   - R0 随温度变化：按 Arrhenius 关系，`R0(T) = R0_ref · exp(Ea/R_gas · (1/T − 1/T_ref))`，温度用开尔文。
   - 产热：`Q_b = I²·R0 + V1²/R1`（忽略可逆热，在报告中注明）。
2. **Sink 充电器**（`sim/charger.py`）：
   - CC-CV 充电：CC 电流受"PDO 功率 × 转换效率 − 系统负载"和最大充电倍率共同限制；CV 电压到达后电流按模型自然衰减，降到截止电流时停止。
   - 转换效率：`η = η_max − β·(V_in/V_b − 1)`，结果限制在 [η_min, η_max]。转换损耗 `Q_cv = P_in·(1 − η)`。
   - 自身热管理类型 `tm_type`：
     - `none`：不降流。
     - `jeita`：按配置中的温度分段降低充电电流或停充。分段默认值标注"典型值，待 T0.2 F19 核实"。
     - `aggressive`：表面温度超过阈值时，按比例降低输入电流上限。
   - 每个时间步输出 `sink_tm_active`。
3. **Sink 申请策略**（`sim/sink_policy.py`），`request_policy` 取以下之一：
   - `max_power`：选支持范围内功率最大的 PDO；
   - `max_voltage`：选支持范围内电压最高的 PDO；
   - `fixed_voltage`：总是选指定电压（例如 9V），不存在时选 5V；
   - `random`：在支持范围内随机选。
   - 每台设备有 `supported_voltages`。
4. **PD 协商**（`sim/pd_link.py`）：
   - 收到新的 Source_Capabilities 后，Sink 按申请策略重新选择 PDO。
   - 每台设备有参数 `hard_reset_prob`：每次重新协商以这个概率触发 Hard Reset。Hard Reset 时 VBUS 断开 `hard_reset_off_s` 秒（默认 3），然后从 vSafe5V 重新协商。
   - 写入 `events` 表。
5. **设备热网络**（`sim/thermal.py`）：电芯节点和外壳节点，方程见 `research_plan.md` 第 4.2 节。散热条件通过 R_sa 的倍数表示。
6. **系统负载**（`sim/load.py`）：
   - `idle`、`moderate`、`heavy` 三档，给出 P_sys(t) 曲线；
   - `heavy` 下允许电池放电（P_sys > 可用输入功率时）。
7. **Source 与接口/线缆**（`sim/source.py`）：
   - Source 转换损耗 → Source 热节点；
   - 接口/线缆产热 `I²·R_c` → 接口热节点；
   - 线缆压降。
8. **传感器**（`sim/sensors.py`）：
   - 在观测值上加噪声和量化（Source NTC 默认 σ = 0.2°C、量化 0.1°C；V/I 测量加 0.5% 相对噪声）；
   - 标签值不加噪声，但另外提供一个"模拟 Android 上报"的版本：量化到 0.1°C 或 1°C，且每 10 s 才更新一次。
9. **会话仿真**（`sim/session.py`）：
   - 函数 `run_session(device, scenario, controller, seed) -> (timeseries_df, events_df)`；
   - 积分步长 1 s，显式 Euler 即可；如果数值不稳定，改用更小的内部步长。
   - 控制器接口：`controller.step(obs_history, events_history, t) -> list[PDO]`，每个控制周期调用一次，返回要广播的 PDO 列表。

**一致性检查**（`tests/` 中实现）：
1. 能量守恒：每步 `p_in` = 电池储能变化率 + 系统负载 + 所有产热项（误差 < 1%）。
2. 恒功率输入下，热网络稳态温度与解析解一致（误差 < 0.1°C）。
3. CC-CV 电流曲线形态正确：先恒流，再衰减，最后截止。
4. `jeita` 类型在越过温度分段时，电流按配置下降。
5. 同一个 seed 运行两次，结果逐位相同。
6. 可选：选 3 个工况，与 PyBaMM（SPMe + 集总热模型）的温度轨迹对比，报告误差。不要求一致，但要报告。

**产出**：`src/pdcursor/sim/`、对应测试、`configs/sim_default.yaml`。

**验收标准**：
- 以上一致性检查 1–5 全部作为自动化测试通过。
- 能跑一个 45W Source、手机类设备、B0 控制器的 60 分钟会话，并画出温度、功率、SOC 曲线，图放入报告。

## T1.2 虚拟设备种群生成（Agent）

**目标**：生成多样的虚拟 Sink 种群，使实验既有区分度，又不偏向本文方法。

**步骤**：
1. 定义四个设备类别 `phone`、`tablet`、`laptop`、`iot` 的参数分布：容量、串联节数、R0、热参数、最大输入功率、`supported_voltages`、`request_policy` 的分布、`tm_type` 的分布、`hard_reset_prob`。
   - 参数范围要参考公开资料（数据手册、拆解报告、文献），在报告中逐项写明来源；找不到来源的标注"假设"。
2. `tm_type` 的比例默认为：`none` 1/3、`jeita` 1/3、`aggressive` 1/3。T0.5 有结果后由人类调整。
3. 实现 `generate_population(n, seed, config) -> devices_df`。
4. **种群标定（只做一次，事先进行）**：用一个**专门的标定种群**（独立 seed，记录在配置中，此后任何任务都不得使用该 seed 生成的设备）运行 B0（不加限制），统计最严苛场景（40°C、`insulated`、重负载）下的过温会话比例。目标是让该比例落在 20%–60% 之间，否则实验没有区分度。允许调整热参数的分布，但每一轮调整都要记录在报告里。
5. **种群冻结**：标定完成后，把配置写入 `configs/population_v1.yaml` 并提交。**此后任何任务都不得修改该文件。** 确需修改时，按 `AGENTS.md` 第 2 节提交提案。

**产出**：`src/pdcursor/sim/population.py`、`configs/population_v1.yaml`、种群统计图（各参数的分布直方图）。

**验收标准**：
- 同一 seed 生成的种群完全相同。
- 报告中有参数来源表和标定过程记录。
- 标定只使用标定种群，它的 seed 与后续数据集使用的 seed 不同。

## T1.3 PDO 工具库与合规校验器（Agent）

**目标**：实现 PDO 数据结构、Power Rules 配置，以及 Source_Capabilities 的合规校验。

**步骤**：
1. `pd/pdo.py`：实现 `FixedPDO(voltage_v, max_current_a)`，电流按 10 mA 取整；以及 `power_w` 属性。PPS APDO 留接口，供 B7 使用。
2. `pd/power_rules.py`：按 T0.2 中 F9 已证实的表格，提供 `source_profile(pdp_w) -> list[FixedPDO]`。T0.2 未完成时，先用 `research_plan.md` 中的描述，并标注 `TODO(T0.2)`。
3. `pd/validate.py`：实现 `validate_source_caps(pdos) -> list[str]`（返回违规列表，空列表表示合规），检查：
   - 数量 ≤ 7（F1）；
   - 第一个是 vSafe5V（F2）；
   - 按电压升序（F3）；
   - 不存在同电压的 Fixed PDO（F4）；
   - 电流是 10 mA 的整数倍，且在字段范围内（F5）。
4. `pd/caps.py`：实现 `make_source_caps(selected_pdos) -> list[FixedPDO]`，把任意子集整理成合规顺序；无法整理时抛出异常。

**验收标准**：每条校验规则都有正例和反例测试；45W 配置通过校验；故意构造的 5V1A + 5V2A 列表被拒绝。

## T1.4 仿真数据集生成与划分（Agent）

**目标**：生成训练、校准、测试数据，覆盖足够多样的游标动作。

**步骤**：
1. **划分设备**：用种群 v1 生成设备，按 `device_id` 划分为 train 60%、cal 20%、test_iid 20%。
2. **生成 OOD 设备**：额外生成一组 `test_ood` 设备，热参数超出训练分布（例如 R_sa 取训练范围上限的 1.2–1.5 倍），数量与 test_iid 相同。OOD 种群配置写入 `configs/population_ood_v1.yaml`，同样冻结。
3. **探索策略**（`control/exploration.py`）：
   - 60% 的会话用随机游标调度：等待时间服从指数分布（均值 180 s），到时后从当前可用游标中均匀随机选一个；
   - 20% 的会话使用固定游标（随机选定后整个会话不变）；
   - 20% 的会话使用一个简单的反应式策略，在本文件中实现：`t_src` 或 `t_con` 超过阈值就降一档，低于"阈值 − 3°C"并持续 60 s 就升一档。不要依赖 T1.8 的 B2。
4. **场景**：每台设备按 `research_plan.md` 第 6.3 节的场景矩阵随机抽取若干场景运行。会话时长 90 分钟，或充满即停。
5. **构造训练样本**（`data/samples.py`）：
   - 在每个控制周期时刻 k，取过去 W 秒的白名单特征作为输入，取未来 H 秒的真实 `p_in` 序列作为 u；
   - 目标是未来 H 秒内各节点的最高温度。
   - **必须调用 `assert_features_allowed` 检查输入列。**
6. **防泄漏测试**：
   - 检查各子集的 `device_id` 没有交集；
   - 检查输入张量中没有任何仅标签字段（例如逐列与 `t_bat` 比对，不得完全相同）。

**产出**：`data/sim_v1/`（不进 git）；生成脚本 `scripts/gen_sim_dataset.py`；数据集说明 `docs/notes/dataset_sim_v1.md`，记录规模、各子集会话数、各类设备占比、过温比例。

**验收标准**：
- 一条命令能从头复现整个数据集。
- 防泄漏测试通过。
- 数据集说明齐全。

## T1.5 风险预测器 M0/M1/M2（Agent）

**目标**：按 `research_plan.md` 第 4.2 节实现三种模型，并离线评估预测质量。

**统一接口**：

```python
class RiskModel:
    def fit(self, train_ds, val_ds): ...
    def predict_quantiles(self, history, u_future) -> dict[node, dict[q, float]]: ...
```

**步骤**：
1. **M0 纯 RC**：每个设备类别用训练数据拟合一组 RC 参数（最小二乘）。隐状态从插入时刻起开环仿真，初始温度取环境温度（环境温度不可得时取 `t_src` 的初值）。
2. **M1 纯黑盒**：GRU 编码历史，另一个 GRU 或 MLP 编码 u，输出各节点、各分位数。
3. **M2 灰箱**：按 4.2 节实现：编码器输出隐状态估计和正参数；产热项对 u 单调；ODE 前向积分；高分位数 = 中位数 + 非负增量。
4. **训练**：pinball loss；早停只看验证集，验证集从 train 设备中再按 `device_id` 切出 10%。
5. **离线评估**（暂不做 conformal）：在 cal、test_iid、test_ood 上报告 pinball loss、最高温度 MAE、各分位数的实际覆盖率。

**必须的单元测试**：
- M2 单调性：随机抽 100 个样本，把 u 整体乘以 1.1，所有节点、所有分位数的预测值都不减。
- 加载数据时调用了 `assert_features_allowed`。
- 用固定 seed 训练 2 个 epoch，结果可复现。

**产出**：`src/pdcursor/models/`、训练脚本 `scripts/train_risk.py`、`configs/model_m{0,1,2}.yaml`、`results/summary/T1.5_offline.csv`。

**验收标准**：三种模型都能训练并完成评估；指标表齐全。**不要求 M2 胜出**，如实报告即可。

## T1.6 Conformal 校准模块（Agent）

**目标**：把模型的 (1−α) 分位数校准为有覆盖率保证的上界。

**步骤**：
1. 实现单侧 CQR：
   - 在 cal 集上计算分数 `s_i = y_i − q̂_{1−α}(x_i)`，共 n 个；
   - 把分数从小到大排序，取第 `ceil((n+1)(1−α))` 个值作为 Q（若该序号 > n，Q 取 +∞）；
   - 校准后的上界 = `q̂_{1−α} + Q`。
2. 每个节点分别校准；同时支持按设备类别分组校准（Mondrian）。
3. 合成数据测试：生成已知分布的数据，校准后在大样本上的覆盖率应在 [1−α−0.01, 1−α+0.02] 内。
4. 对 M0/M1/M2 都做校准，报告 test_iid 与 test_ood 上的实际覆盖率。

**产出**：`src/pdcursor/models/conformal.py`、测试、`results/summary/T1.6_coverage.csv`、校准图。

**验收标准**：合成数据测试通过；真实覆盖率表齐全，OOD 上覆盖不足时如实报告。

## T1.7 游标控制器 C 与安全兜底层（Agent）

**目标**：按 `research_plan.md` 第 4.3、4.4 节实现本文控制器。

**需要实现的函数**（`control/`）：

```python
build_axis(candidate_pdos, risk_fn, demand_est) -> list[FixedPDO]
select_cursor(axis, risk_upper_fn, limits, margins) -> int     # 返回 k*
apply_hysteresis(state, k_star, now, cfg) -> int
class SafetyLayer:
    def check(self, obs, model_ok) -> Optional[int]            # 返回覆盖后的游标，或 None
class CursorController:                                         # 组合以上组件，实现仿真器的控制器接口
    def step(self, obs_history, events_history, t) -> list[FixedPDO]
```

可选：功率预算变体 `CursorControllerPB`。

**必须的单元测试**：
1. 排序轴第一个元素永远是 vSafe5V。
2. 输出的 PDO 列表永远通过 `validate_source_caps`。
3. 构造风险随 c 单调的情形，验证 k* 等于最后一个安全位置。
4. 构造某 PDO 被支配的情形，验证它被移除。
5. 滞回：下调立即生效；上调必须满足 δ 裕量和 T_dwell，且每次最多上调 1 档。
6. 兜底层：S1、S2、S3、S5 各至少一个触发用例和一个不触发用例。S4 默认关闭（Q4）。
7. 广播内容没有变化时，不产生 `src_caps_sent` 事件。

**验收标准**：测试全部通过；能在仿真器中跑完整会话；报告中附一个典型会话的温度、游标、功率时间序列图。

## T1.8 对比控制器 B0–B7（Agent）

**目标**：按 `research_plan.md` 第 6.4 节实现全部对比方法，接口与 `CursorController` 相同。

**要点**：
- **B1**：在训练设备上，对每个 Source 能力配置，找到使最差场景过温率 ≤ α 的最大固定游标。
- **B2**：阈值在训练设备上调，调参目标与本文方法相同（过温率 ≤ α 的前提下速度最大）。
- **B3**：实现，但默认关闭（Q4）。
- **B4/B5**：分别用 M0、M1，加上同样的 C 和兜底层。
- **B6**：直接读取仿真器的真实状态和真实模型，**仅用于仿真**。代码中加断言，防止用于实测。
- **B7**：只对 `supports_pps` 的设备运行；按真实温度连续调节功率，使温度不超过 T_lim − m。

**验收标准**：每个控制器都有至少一个"能跑通会话"的测试；B6、B7 的仅限仿真断言有测试。

## T1.9 仿真闭环评估与 Pareto 曲线（Agent）

**目标**：在 test_iid 和 test_ood 上对比所有方法，产出论文主结果。

**步骤**：
1. 每个控制器在测试设备 × 场景矩阵上运行，固定 seed 列表（写在配置中）。
2. 按 `research_plan.md` 第 6.5 节计算每个会话的指标，保存为 `results/raw/T1.9_sessions.parquet`。
3. 汇总：均值 ± 95% CI（按设备 bootstrap，1000 次）。
4. 画 Pareto 曲线：
   - 本文方法：扫 α ∈ {0.01, 0.02, 0.05, 0.1, 0.2}；
   - B1：扫固定游标；
   - B2：扫阈值。
5. 画典型时间序列图（本文方法对 B2，各选 2 个会话：1 个普通场景，1 个严苛场景）。
6. 单独统计 `heavy` 负载场景（能量上界失效的情况）的结果。

**产出**：`scripts/eval_closed_loop.py`、`results/summary/T1.9_*.csv`、`results/summary/figures/T1.9_*.png`、报告。

**验收标准**：
- 一条命令可复现全部结果。
- 所有方法在相同的设备、场景、seed 上运行。
- 报告中如实给出结果，包括本文方法不如对比方法的情况。

## T1.10 仿真消融实验（Agent）

按 `research_plan.md` 第 6.7 节的 a–h 逐项进行。每项只改一个因素，其余与 T1.9 保持一致。

**产出**：`results/summary/T1.10_ablation.csv`、柱状图、报告。

**验收标准**：8 项消融齐全；每项都写明改了什么、结果如何。

## T1.11 （可选）MPC 与贪心截断对比（Agent）

实现一个带切换代价的短时域 MPC：在未来若干个控制周期内选择游标序列，目标是"交付能量 − λ × 切换次数"，约束与 C 相同。与贪心截断（C）对比速度和协商次数。

**验收标准**：结果表与图齐全；报告中写明 MPC 的计算开销。

---

# 阶段 2：硬件与实测

> 阶段 2 中所有涉及电池和硬件的操作都由人类执行，开始前必须完成 Q10（安全审批）。执行代理只写代码、脚本和文档。

## T2.1 最小可编程 Source 固件（Agent+Human）

**目标**：在人类选定的平台（Q2）上实现：
1. 按配置广播 PDO 列表，并能按串口命令重发新的 Source_Capabilities；
2. 以 1 Hz 通过串口输出 VBUS 电压、电流、温度和 PD 事件日志；
3. 过流保护和限流。

**分工**：代理写固件和主机端串口工具 `tools/source_link.py`；人类烧录并按检查清单验证。

**验收标准**（人类填写检查清单）：
- 商用手机能按广播内容协商；
- 串口命令能触发重新协商；
- 日志能被 `tools/` 解析成 `events`/`timeseries` 格式；
- 过流保护经过测试。

## T2.2 搭建参考 Sink（Human）

按 `docs/protocols/P5_reference_sink.md` 执行。完成条件：申请策略可编程（`max_power`、`fixed_voltage` 等）；热管理可开关；电芯和 IC 上贴好热电偶并能记录数据；安全措施到位。

## T2.3 实测数据采集与对齐工具（Agent）

**需要实现的工具**：
1. `tools/android_logger.py`：通过 adb（含无线调试）在设备端运行采集循环，1 Hz 读取电池温度、电流、电压、电量；sysfs 读不到时回退到 `dumpsys battery`。处理不同厂商的单位和符号差异。
2. `tools/thermocouple_parser.py`：解析多通道记录仪导出的 CSV（列名可配置）。
3. `tools/source_log_parser.py`：解析 T2.1 固件的串口日志。
4. `tools/align.py`：
   - 统一到主机时间戳；
   - 再用功率阶跃事件做互相关微调；
   - 输出第 13 节格式的四张表。
5. `scripts/build_real_dataset.py`：把一次实验的原始文件夹转换为数据集。

**验收标准**：
- 每个解析器都有使用合成样例文件的测试。
- 对齐工具在人为加入已知时间偏移的合成数据上，能把偏移恢复到 ±1 s 内。

## T2.4 同功率不同电压实验与重新协商兼容性实验（Human）

按规程 P2、P3 执行。代理随后分析数据，写入 `reports/T2.4_analysis.md`：
- 功率顺序是否与风险顺序一致；不一致的设备有哪些；
- 各设备的重新协商成功率、Hard Reset 率。

**注意**：如果功率顺序与风险顺序有显著不一致，说明帕累托链是必要的；如果没有，论文中把功率序写成"经实验验证的假设"。两种结果都要如实报告。

## T2.5 实测数据采集（Human）

按规程 P4 执行，在参考 Sink 和商用设备上采集随机游标调度的数据。用 T2.3 的工具转换成 `data/real_v1/`。

## T2.6 Sim-to-real 训练与评估（Agent）

**步骤**：
1. 实测数据按 `device_id` 划分为 train/cal/test。
2. 对比三种训练方式：只用仿真数据；只用实测数据；仿真预训练 + 实测微调。
3. 对 M0/M1/M2 都做。
4. 重新做 conformal 校准，报告实测 test 集上的覆盖率和 pinball loss。

**验收标准**：结果表齐全；最终用于 T2.7 的模型和校准参数已导出，并写明版本。

## T2.7 闭环控制固件（Agent+Human）

**分两步**：
1. **PC 在环**：主机运行 B + C + 兜底层，通过 `tools/source_link.py` 控制 Source。先在参考 Sink 上验证。
2. **MCU 部署**：
   - 把 M2 量化为 int8 导出；
   - 在 MCU 上实现 B 的推理、C 和兜底层；
   - 兜底层必须在 MCU 上独立运行，与主机是否在线无关。

**验收标准**：
- PC 在环和 MCU 两种模式下，在相同的回放输入上，游标决策一致率 ≥ 99%（用记录的数据离线比对）。
- 人类确认兜底层在主机断线时仍然工作。

## T2.8 实测闭环实验（Human）

按场景矩阵（受设备与温箱条件限制，可由人类裁剪）运行本文方法和可在实测中运行的对比方法（B0、B1、B2、B3（若 Q4 允许）、B4、B5、B7（仅 PPS 设备））。每个配置重复 ≥ 3 次。

## T2.9 实测结果分析（Agent）

与 T1.9 相同的分析流程，应用于实测数据。额外输出商用设备兼容性表（重新协商次数、Hard Reset、异常）。

## T2.10 部署开销测量（Agent+Human）

代理输出模型参数量、int8 大小、理论计算量；人类在 MCU 上实测推理时间、RAM 占用和功耗开销。结果汇总成论文用的表。

---

# 阶段 3：论文

## T3.1 论文图表复现脚本（Agent）

**要求**：
- `scripts/make_figures.py` 从 `results/` 生成 `research_plan.md` 第 6.6 节的全部图表，输出到 `paper/figures/`。
- 论文中出现的每个数字，都必须能在 `results/summary/` 的某个文件中找到。在 `paper/numbers.md` 中列出"数字 → 来源文件 → 行/列"。

## T3.2 （可选）ICCE 短文初稿（Agent）

Q7 决定投 ICCE 时执行。篇幅与格式以当年征稿要求为准。内容取 T1.9 的仿真结果和最核心的实测结果。

## T3.3 TCE 全文初稿（Agent）

**要求**：
- 结构按 `research_plan.md` 第 7 节。
- 表述遵守第 8 节。
- 参考文献只能来自 `docs/references.bib`（T0.3 已核实的条目）。
- 第 5 节的引理和命题要给出证明。
- 局限一节必须包括：能量上界在重负载下失效；OOD 设备上的覆盖率；兼容性风险；温度阈值的选取依据。
- 格式以 IEEE TCE 当前作者指南为准；在报告中写明查阅指南的日期和链接。

**产出**：`paper/` 目录下的 LaTeX 源文件。

**验收标准**：
- 能编译通过；
- `paper/numbers.md` 覆盖正文中的所有数字；
- 表述合规检查：全文搜索第 8 节的禁用词，结果为 0。

## T3.4 审阅与投稿（Human）

人类审阅全文，确认作者、致谢、利益冲突声明，然后投稿。
