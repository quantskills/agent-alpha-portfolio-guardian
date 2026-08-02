---
name: agent-alpha-portfolio-guardian
description: 当需要对多因子组合做持续健康度守卫、产出健康度矩阵/拥挤警示/退休重构候选/IC衰减曲线时使用。编排 factor-evaluate、factor-decay、crowding-risk-monitor、smart-money-profiler。
---

# Alpha 多因子组合健康度守卫 Agent

本文是完整行为与使用说明。QuantSkills Agent 声明与多运行时入口见根目录 [AGENTS.md](AGENTS.md)；仓库主页见 [README.md](README.md)。

> 文件最上方仅保留 Agent 加载所需的两行元数据（`name` / `description`）。组织与许可证等完整元数据见 [AGENTS.md](AGENTS.md) 与 [agents/skill-manifest.yaml](agents/skill-manifest.yaml)。

配置与读报告见 [用户使用手册.md](用户使用手册.md)；规则细节见 `references/decision-rules.md`。

## 定位边界

- 品类：**组合 / 守护型 Agent**（分析型编排，非 `alpha-*` 生产因子包）
- **能做**：编排四依赖 → 健康度矩阵 / 拥挤警示 / 退休重构候选 / IC 衰减曲线
- **不能做**：确定性下单指令；会话重算冒充 Alpha 生产；依赖失败时编造数值；自写近似主分/衰减

### 依赖

| 依赖 | 消费 |
|------|------|
| `skill-factor-evaluate` | ScoreReport |
| `skill-factor-decay` | DecayReport / IC 曲线 |
| `agent-crowding-risk-monitor` | snapshot / scorecard |
| `skill-smart-money-profiler` | 合力/分歧旁证 |

## 输入

| 项 | 说明 |
|----|------|
| 凭证 | `PANDA_DATA_*`（需要在线取数时） |
| 组合配置 | `config/portfolio.yaml`；live 使用 `data_source: Pandadata` |
| as_of | 配置内 |
| 因子证据 | 依赖 Skill 报告/快照；mock 用于自测 |

强制同一 `universe` + `horizon_eval`，保证主分跨因子可比。

## 输出层级

| 层级 | 内容 | 形式 |
|------|------|------|
| L1 | 一句话组合健康结论 | 文本 |
| L2 | 健康度矩阵 + 候选清单 + 警示 | Markdown |
| L3 | IC 衰减族图（PNG 或 ASCII） | charts/ |
| L4 | 守卫有效性回测交互页（SCI 风格） | `reports/backtest/<run_id>/l4.html` |
| L5 | 结构化四件套 | CSV/JSON/Parquet |

研究态目录：`reports/runtime_out/<run_id>/`  
发布态目录：`reports/publish/<as_of>/<data_version>/`

## 使用方式

```bash
pip install -r requirements.txt

# mock 全链路（默认）
python -m runtime --mock
# 或
python -m runtime self-test

# 指定组合配置
python -m runtime --portfolio config/portfolio.yaml --live

python -m runtime validate reports/runtime_out/<run_id>
python -m runtime publish --from reports/runtime_out/<run_id>
python -m runtime backtest --allow-simulate
python -m runtime clean
```

回测产物：`reports/backtest/<run_id>/`（分桶、准确率、MDD、滚动窗、`l4.html`）。  
运行时入口汇总见 [AGENTS.md](AGENTS.md)（Claude Code / Codex / Cursor / Hermes / OpenClaw）。

### 对话触发话术

| 意图 | 示例 |
|------|------|
| 全组合守卫 | 「用 alpha-portfolio-guardian 对当前多因子组合做健康度守卫」 |
| 只要矩阵/候选 | 「只出因子健康度矩阵和退休候选」 |
| 拥挤 | 「结合拥挤盯盘，标出组合里过热相关的因子」 |
| 衰减 | 「画出组合内各因子 IC 衰减曲线并对比半衰期」 |

## 工作流（8 步）

1. 加载 `portfolio.yaml`
2. 校验因子契约
3. 扇出 evaluate → ScoreReport
4. 扇出 decay → DecayReport
5. 桥接 crowding snapshot
6. 抽样 smart-money 合力/分歧
7. 规则引擎聚合四件套
8. validate；可选 publish

规则与原因码见 [references/decision-rules.md](references/decision-rules.md)。

## 验收与降级

- live 结论来自本次依赖产物，禁止用记忆覆盖
- 依赖失败：标注 `gap_notes` / `insufficient`，禁止编造
- `source_mode`：`live` / `mock` / `degraded`；版本前缀区分
- 禁止用语：买入 / 卖出 / 必涨 / 仓位提到 xx%

## 依赖

- Python：`pandas`、`numpy`、`PyYAML`、`matplotlib`
- Skill/Agent：见上表；调用前阅读其 `SKILL.md` / `AGENTS.md`

## 免责声明

本 Agent 输出仅供量化研究参考，不构成任何投资建议。
