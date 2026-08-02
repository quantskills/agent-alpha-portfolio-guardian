# Portable Loader

无原生 Skill 机制时：

1. 将本仓库放入工作区，并确保四个依赖目录可访问
2. `pip install -r requirements.txt`
3. 复制 `.env.example` → `.env`（live 时填凭证）
4. 运行：

```bash
python -m runtime --mock
python -m runtime --portfolio config/portfolio.yaml --live
```

5. 阅读 `reports/runtime_out/<run_id>/runtime_report.md` 与 `agent_snapshot.json`
6. 宿主 Prompt 应注入：先读 `SKILL.md`；禁止编造；禁止下单指令
