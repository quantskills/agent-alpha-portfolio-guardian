---
name: agent-alpha-portfolio-guardian
description: "Multi-factor portfolio health guardian. Orchestrates factor evaluate/decay, crowding risk, and smart-money profiling into a health matrix, crowding alerts, retire/rebuild candidates, and IC decay curves. Use when a research AI agent needs continuous portfolio-level factor monitoring without automated order placement."
metadata:
  organization: QuantSkills
  organization_url: https://github.com/quantskills
  repository: quantskills/agent-alpha-portfolio-guardian
  repository_url: https://github.com/quantskills/agent-alpha-portfolio-guardian
  project_type: agent
  collection: factor-portfolio-guardian
  license: GPL-3.0-only
  category: guardian
  tags: [quant, factor, portfolio, guardian, crowding, smart-money, pandadata]
  platforms: [claude-code, codex, hermes, openclaw, cursor]
  language: zh-en
  status: draft
  validation_level: runnable
  maintainer_type: community
  maintainer: frocir
  requires:
    - skill-factor-evaluate
    - skill-factor-decay
    - agent-crowding-risk-monitor
    - skill-smart-money-profiler
  summary_zh: 多因子组合健康度守卫：健康度矩阵 + 拥挤警示 + 退休/重构候选 + IC 衰减曲线，含守卫规则有效性回测 L4。
  summary_en: Multi-factor portfolio health guardian producing a health matrix, crowding alerts, retire/rebuild candidates, IC decay curves, and a research-only effectiveness backtest L4 page.
quantSkills:
  organization: QuantSkills
  organization_url: https://github.com/quantskills
  repository: quantskills/agent-alpha-portfolio-guardian
  repository_url: https://github.com/quantskills/agent-alpha-portfolio-guardian
  project_type: agent
  collection: factor-portfolio-guardian
  license: GPL-3.0-only
  category: guardian
  tags: [quant, factor, portfolio, guardian, crowding, smart-money, pandadata]
  platforms: [claude-code, codex, hermes, openclaw, cursor]
  language: zh-en
  status: draft
  validation_level: runnable
  maintainer_type: community
  maintainer: frocir
  requires:
    - skill-factor-evaluate
    - skill-factor-decay
    - agent-crowding-risk-monitor
    - skill-smart-money-profiler
  summary_zh: 多因子组合健康度守卫：健康度矩阵 + 拥挤警示 + 退休/重构候选 + IC 衰减曲线，含守卫规则有效性回测 L4。
  summary_en: Multi-factor portfolio health guardian producing a health matrix, crowding alerts, retire/rebuild candidates, IC decay curves, and a research-only effectiveness backtest L4 page.
---

# Alpha Portfolio Guardian Agent

Use this Agent when a user needs a Pandadata-backed research answer for:

> Given my current multi-factor book, which factors stay healthy, which are crowded or decaying, and which deserve retire / deweight / rebuild research?

This is a **Community Project** under QuantSkills. It is a portfolio / guardian analysis Agent (orchestration + report validation + optional effectiveness backtest), **not** an `alpha-*` production factor package and **not** an automated trading system.

Detailed behavior rules live in [`SKILL.md`](SKILL.md). This file is the QuantSkills Agent declaration and multi-runtime entry.

## What It Does

1. **Factor health matrix** — consume ScoreReport / DecayReport evidence into portfolio-level scores, half-life, and coarse signals.
2. **Crowding alerts** — bridge `agent-crowding-risk-monitor` snapshots onto factor exposures.
3. **Retire / rebuild candidates** — rule engine with reason codes (`SIGN_REVERSAL`, noise, high turnover, critical crowd, etc.).
4. **IC decay curves** — family chart + JSON for rebalance horizon comparison.
5. **Guardian-rule effectiveness backtest** — bucket / accuracy / keep-path MDD / rolling OOS + SCI-style `l4.html`.

## How To Use

### Local CLI

```bash
pip install -r requirements.txt
# set PANDA_DATA_USERNAME / PANDA_DATA_PASSWORD when live fetch is required (never commit credentials)

python -m runtime self-test
python -m runtime --mock
python -m runtime --portfolio config/portfolio.yaml --live
python -m runtime validate reports/runtime_out/<run_id>
python -m runtime publish --from reports/runtime_out/<run_id>
python -m runtime backtest --allow-simulate
python -m runtime clean
```

Research artifacts: `reports/runtime_out/`. Publish snapshots: `reports/publish/`. Backtest pack + L4: `reports/backtest/<run_id>/` (sample: `reports/samples/backtest_mock/l4.html`).

### Runtime entry points

| Runtime | Entry |
| --- | --- |
| Claude Code / Codex | Load this folder; follow `SKILL.md` + four dependencies |
| Cursor | `.cursor/skills/agent-alpha-portfolio-guardian` + `agents/cursor-rule.mdc` |
| Hermes / OpenClaw / portable | Paste `agents/portable-loader.md` and replace the root path |
| OpenAI-compatible | `agents/openai.yaml` |

Publish metadata: [`agents/skill-manifest.yaml`](agents/skill-manifest.yaml).

## Maintainer

Maintained by `frocir` as a QuantSkills community project.

## Supported Scenarios

- Weekly / daily portfolio-level factor health check.
- Retire / deweight / rebuild candidate screening with reason codes.
- Crowding and smart-money side evidence attached to factor exposures.
- IC decay comparison across the book.
- Offline mock / self-test of package layout and validators.
- Research-only guardian-rule effectiveness backtest (simulate or metrics/fwd panels).

## Limitations And Boundaries

- Research and education only. Outputs are **not** investment advice and do **not** promise returns.
- Live conclusions must come from this session's dependency artifacts; memory must not override matrix decisions.
- Do not invent ScoreReport / DecayReport numbers when dependencies fail — label `gap_notes` / `insufficient`.
- No broker connectivity, no order placement, no unconditional buy/sell / position-sizing commands.
- Not a substitute for an Alpha production package; trading-link delivery must follow a separate Alpha production process.
- Backtest validates **guardian-rule** historical statistics, not a full Alpha production IC backtest.
- This repository is a Community Project; listing, recommendation, or official recognition still requires maintainer review.

## Data Source And Assumptions

- Formal data origin: Pandadata (via dependency Skills / cached artifacts with Pandadata provenance).
- Credentials via environment variables only (`PANDA_DATA_*`); see `.env.example`.
- Factors in one run must share the same `universe` and `horizon_eval` for comparable primary scores.
- Thresholds and reason codes: `config/portfolio*.yaml` and `references/decision-rules.md`.

## Reference Documents

- [`SKILL.md`](SKILL.md) — full behavior, workflow, output levels, and hard constraints
- [`用户使用手册.md`](用户使用手册.md) — configuration and how to read reports
- [`references/decision-rules.md`](references/decision-rules.md) — rules and reason codes
- [`references/dependency-contracts.md`](references/dependency-contracts.md) — dependency contracts
- [`agents/portable-loader.md`](agents/portable-loader.md) — portable runtime loader prompt

## Disclaimer

本报告与仓库材料基于规则化分析与依赖 Skill 证据生成，仅供研究参考，不构成任何投资建议。  
Materials in this repository are for research reference only and do not constitute investment advice.
