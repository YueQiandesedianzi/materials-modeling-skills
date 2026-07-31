# Materials Modeling Skills V0.2

面向聚合物、材料小样本建模、波形数据、文献数据融合和主动学习的可审计 skills 工作流。

它不是算法大全，也不是一键论文生成器。核心目标是固定五件容易失控的事：

1. 什么才算独立样本；
2. 模型究竟要泛化到什么对象；
3. 特征、切分、预处理和增强是否发生泄露；
4. 小样本性能是否超过可信 baseline，并具有不确定性边界；
5. 论文中的机理和性能 claim 是否超过现有证据。

## 设计定位

本仓库适合作为三类现成 skills 的连接层：

- **MathModeling-skills**：沿用 method card、human decision ledger、风险探针、代码审查、数字冻结和终审；
- **AI Research Skills**：用于研究空白、研究设计、文献比较和 paper memory；
- **Scientific package skills**：scikit-learn、PyMC、Aeon、SHAP、PyMOO、RDKit、PySR 等，只负责正确使用工具，不拥有研究判断权。

本仓库拥有材料科研中最关键、通用 skills 常常缺失的部分：数据谱系、验证协议、小样本 benchmark、材料特征、外部验证、实验闭环和机理 claim 分级。

## 使用模式

本套件分两档使用，避免把早期探索做成过重的投稿审计。

| 模式 | 适用场景 | 必跑边界 |
|---|---|---|
| `lean` | 日常探索、快速判断、组内迭代 | R2 数据谱系、R3 验证协议、R7 小样本 benchmark |
| `submission` | 论文、答辩、对外交付、版本冻结 | R0-R11 全流程 |

## Skills

### 核心流程

1. `materials-research-orchestrator`
2. `project-state-orienter`
3. `materials-data-lineage-auditor`
4. `validation-protocol-freezer`
5. `physics-informed-feature-builder`
6. `model-execution-contract-builder`
7. `small-sample-model-benchmarker`
8. `uncertainty-external-validation-auditor`
9. `active-learning-doe-planner`
10. `mechanism-claim-auditor`

### 领域扩展

11. `polymer-formulation-featurizer`
12. `waveform-cycle-curator`

## 推荐工作流

```text
R0  研究空白与可行性确认                    [外部：gap-to-topic]
R1  科学问题、机制假设与验证设计锁定          [外部：research-design-helper]
R2  数据谱系和独立样本定义通过                [materials-data-lineage-auditor]
R3  泛化目标、分组切分与盲测协议冻结          [validation-protocol-freezer]
R4  特征定义、单位、来源和折内计算规则通过     [physics-informed-feature-builder]
R4.5 模型执行合同生成                         [model-execution-contract-builder]
R5  主方法 + usable baseline + fallback 筛选   [外部：MathModeling method-selector + package skills]
R5.5 人类记录方法选择                        [外部：decision ledger]
R6  代码运行、输出合同和代码审查通过           [外部：generator/reviewer]
R7  小样本内部 benchmark 通过                 [small-sample-model-benchmarker]
R8  外部验证、不确定性和适用域判定             [uncertainty-external-validation-auditor]
R9  下一批实验经过约束和信息价值审查           [active-learning-doe-planner]
R10 机制与性能 claim 分级并冻结                [mechanism-claim-auditor + freeze]
R11 跨文件一致性、完整性和最终 QA 通过          [外部：auditors]
```

`project-state-orienter` 可在任意新会话先运行，只读 canonical artifacts，返回当前 gate、阻塞项和一个安全下一步。

## 安装

### Claude Code

将 `.claude/skills/` 中需要的 skill 目录复制到项目的 `.claude/skills/`，并把根目录 `CLAUDE.md` 与 `AGENTS.md` 放入项目根目录。

### Codex

将 `.codex/skills/` 中需要的 skill 目录复制到项目的 `.codex/skills/`，并把 `AGENTS.md` 放入项目根目录。

两棵 skill tree 是完整副本。维护时运行：

```bash
python tests/validate_skill_tree.py
```

## 项目目录约定

```text
project/
├── AGENTS.md
├── planning/
│   ├── session_config.json
│   ├── research_manifest.json
│   ├── decisions.jsonl
│   └── validation/
│       ├── split_protocol.json
│       ├── split_assignments.csv
│       └── protocol_change_log.jsonl
├── data/
│   ├── raw/                       # 只读
│   ├── clean/
│   ├── registry/
│   │   ├── sample_registry.csv
│   │   └── lineage_graph.json
│   └── features/
│       ├── feature_manifest.json
│       └── feature_matrix.csv
├── methods/
│   └── Qx/                        # 可兼容 MathModeling-skills
├── code/
├── results/
│   ├── experiments/
│   ├── validation/
│   ├── external_validation/
│   └── frozen/
├── experiments/
│   └── next_batch/
├── claims/
└── paper/
```

## 第一次使用

复制 `planning/session_config.example.json` 为项目中的 `planning/session_config.json`，然后发送：

```text
Read AGENTS.md, then run materials-research-orchestrator.
Do not train any model until data lineage and validation protocol pass.
```

也可直接使用 `Initial Prompt-zh.md`。

## V0.2 的边界

- 当前提供的是技能契约、schema、示例和工作流校验器，不内置具体模型训练框架；
- 模型代码应调用现成 package skills，并受本仓库冻结的 split/feature 合同约束；
- 所有自动化建议必须保留人工判断点，特别是方法选择、盲测用途、实验批次和论文 claim。

## V0.2 新增

- 增加 `lean/submission` 两档运行模式；
- 增加 `project-state-orienter`，用于新会话快速读项目状态；
- 增加 `model-execution-contract-builder`，把样本谱系、冻结切分和特征契约转换为训练代码必须遵守的合同；
- 增加 `research_manifest.schema.json` 和 `model_execution_contract.schema.json`；
- 增加 `validate_research_manifest.py`、`check_group_leakage.py`、`validate_model_execution_contract.py` 三个基础校验脚本。
