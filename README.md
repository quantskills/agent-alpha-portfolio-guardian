# Alpha 多因子组合健康度守卫 Agent

对已在用的多因子组合做持续健康度守卫：聚合单因子体检、衰减、拥挤与聪明钱旁证，输出**因子健康度矩阵 + 拥挤度警示 + 退休/重构候选 + IC 衰减曲线**。

> 组合/守护型分析 Agent · 不自动下单 · 不伪装 Alpha 生产包

## 快速开始

```powershell
py -3.10 -m pip install -r requirements.txt
py -3.10 -m runtime self-test
# 或
py -3.10 -m runtime --mock
```

打开 `reports/runtime_out/_self_test/runtime_report.md`（self-test 会同步刷新 `reports/samples/mock_run/`）。

## 依赖

| 依赖 | 作用 |
|------|------|
| [skill-factor-evaluate](../skill-factor-evaluate) | 单因子 ScoreReport |
| [skill-factor-decay](../skill-factor-decay) | DecayReport / IC 曲线 |
| [agent-crowding-risk-monitor](../agent-crowding-risk-monitor) | 拥挤 snapshot |
| [skill-smart-money-profiler](../skill-smart-money-profiler) | 合力/分歧旁证 |

## 常用命令

```text
python -m runtime --mock
python -m runtime --portfolio config/portfolio.yaml --live
python -m runtime validate reports/runtime_out/<run_id>
python -m runtime publish --from reports/runtime_out/<run_id>
python -m runtime backtest --allow-simulate
# 回测后浏览器打开 reports/backtest/<run_id>/l4.html（SCI 风格）
python scripts/export_l4_backtest.py --run-dir reports/backtest/<run_id>
python -m runtime clean
```

## 文档

- [用户使用手册.md](用户使用手册.md) — 能做什么、怎么配置、怎么读报告
- [SKILL.md](SKILL.md) — 行为入口
- [references/](references/) — 数据、规则、契约、边界

## 免责声明

仅供量化研究参考，不构成投资建议。
