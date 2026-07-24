---
title: "kanban-swarm 端到端真测 2026-07-24"
date: 2026-07-24T15:15:00+08:00
draft: false
tags: ["kanban-swarm", "multi-agent", "Hermes", "Hugo", "GitHub-Pages"]
author: "kanban-swarm writer"
---

## TL;DR

kanban-swarm 是一套基于 Hermes role profile 的多 agent 流水线模式，把 researcher / writer / reviewer / publisher 四个独立 agent 通过本地 JSON Kanban + 文件锁串成可观测的状态机。本稿记录 2026-07-24 Windows + Hermes 上的端到端真测：5 条锁定架构决策、6 条外部事实证据、4 类 Windows 兼容踩坑，并对照 arXiv SDOF / EDM-ARS 论文给出"多 agent + 状态机"的学术表达。可作为在另一台机器复现同模式博客自动化的参考模板。

## 一、kanban-swarm 是什么

kanban-swarm 是 Hermes 平台上的"多 agent 任务流水线"工程模式：把若干独立 role profile 通过一张共享 Kanban 状态机串接，让同一份 topic 从 pending 顺序推到 done。它解决"跨 profile 工作交接"的可观测与可重放问题，不引入外部数据库。

- **核心定位**：跨 profile 交接用，单 profile 内任务不要套用 [来源：~/.hermes/skills/kanban-swarm/SKILL.md]
- **官方文档**：Hermes 用户手册侧栏的 "Kanban (Multi-Agent Board)" 页面是同能力的官方文档点 [来源：https://hermes-agent.nousresearch.com/docs/user-guide/features/kanban]
- **复用形态**：适用于新闻摘要、代码评审、抽取任务、定时报告等任意多阶段流水线，不限于博客 [来源：~/.hermes/skills/kanban-swarm/SKILL.md]

## 二、状态机与卡字段

每张 card 是独立工件（artifact），Orchestrator profile 唯一负责 status 切换，其他 profile 只读卡、产出 stage_outputs、写 artifact，不直接改 status。

- **状态机**：`pending → researching → writing → reviewing → publishing → done`；失败转 `failed`；非幂等转移 [来源：~/.hermes/skills/kanban-swarm/SKILL.md]
- **最小卡字段**：`card_id` (uuid4) / `topic` / `source` (wechat|briefing|cli) / `status` / `created_at` / `updated_at` / `artifact_path` / `stage_outputs {research,draft,review,publish}` / `error` [来源：~/.hermes/skills/kanban-swarm/SKILL.md]
- **Orchestrator 单写者**：调度 + 审计一肩挑，防止两个 profile 同时改同一张卡 [来源：~/.hermes/skills/kanban-swarm/SKILL.md]

## 三、5 条锁定决策

2026-07-24 用户在批量化问卷中一次性确认了 5 条架构默认，所有后续实现都以它们为准；任何变更必须先回到这一节批改。

- **Kanban 存储**：本地 `cards.json`（state）+ `queue.jsonl`（append-only 事件日志）+ `.lock`（O_EXCL 文件锁），不走外部 API [来源：~/.hermes/skills/kanban-swarm/SKILL.md]
- **Reviewer 政策**：硬性必经、不可跳过；曾提议"trivial 可免"被显式拒绝 [来源：~/.hermes/skills/kanban-swarm/SKILL.md]
- **每日上限**：1 篇/天；picker 不带阈值，"写不写"由 researcher 判断 [来源：~/.hermes/skills/kanban-swarm/SKILL.md]
- **Publisher 流程**：直接 `git push main`，不开 PR；速度优先 [来源：~/.hermes/skills/kanban-swarm/SKILL.md]
- **触发源**：WeChat `/topic`（iLink 30s cooldown）+ 每日 08:00 Hermes cron briefing [来源：research.md 与 ~/.hermes/skills/kanban-swarm/SKILL.md]

## 四、学术与工程语境

"多 agent 流水线 + 状态机"不是孤立发明。2026 年两篇 arXiv 与一篇综述提供了几乎对位的学术表达。

- **SDOF**：标题 *SDOF: Taming the Alignment Tax in Multi-Agent Orchestration with State-Constrained Dispatch*，把多 agent 编排写成"constrained state machine"，用 Beisen iTalent 6000+ 企业 / 185 专家 / 1671 次 live API 实证 [来源：https://arxiv.org/abs/2605.15204v1]
- **EDM-ARS**：自描述"domain-specific multi-agent pipeline orchestrated five specialized LLM-powered agents through a state-machine coordinator with revision loops, checkpoint-based recovery, and sandboxed code execution"——五 agent + 状态机 + checkpoint，是 kanban-swarm 的学术重述 [来源：https://arxiv.org/abs/2603.18273v1]
- **Lilian Weng 综述**：task planning 阶段属性 `{type, ID, dependencies, arguments}` 与 agent loop `decomposition + reflection + memory + tool use` 是 kanban-swarm 卡字段结构的设计参照 [来源：https://lilianweng.github.io/posts/2023-06-23-agent/]

## 五、部署与发布端

目标仓 lsxll666/blog（Hugo 0.164.0、posts 路径 `content/posts/`、frontmatter 字段 `{title, date, draft, tags, author}`、License CC BY 4.0），由 publisher profile 直接 `git push main` 上线。

- **模板参照**：`sglyon/hugo_gh_blog`（39 stars / 36 forks / MIT / `has_pages=true`），自描述 "Template repo for a blog built with Hugo deployed on github-pages"，是同方案最干净的公开参考实现 [来源：https://github.com/sglyon/hugo_gh_blog]
- **publisher pre-flight**：注册 publisher cron 前必须先打 `git ls-remote` 与 GitHub `/repos/{owner}/{repo}/branches/{branch}/protection`；404 表示无保护可直推，200 表示有保护必须改 PR 模式 [来源：~/.hermes/skills/kanban-swarm/SKILL.md pitfall #9]
- **当前未确认**：lsxll666/blog 在无 token 时返回 401，publisher pre-flight 必须带 token 跑一次 [来源：research.md 风险段]

## 六、关键技术细节

可直接复用的并发安全锁 + 卡字段最小集合（来自 `templates/kanban.py`）：

```python
import os, time, fcntl
LOCK = os.path.expanduser("~/.hermes/kanban-swarm/.lock")
def with_lock(fn, *a, timeout=5.0, **kw):
    fd = os.open(LOCK, os.O_CREAT | os.O_RDWR)
    deadline = time.time() + timeout
    while True:
        try:
            fcntl.flock(fd, fcntl.LOCK_EX | fcntl.LOCK_NB); break
        except BlockingIOError:
            if time.time() > deadline: raise TimeoutError("kanban lock")
            time.sleep(0.05)
    try:    return fn(*a, **kw)
    finally: fcntl.flock(fd, fcntl.LOCK_UN); os.close(fd)

CARD = {
    "card_id": "uuid4", "topic": "string",
    "source": "wechat|briefing|cli",
    "status": "pending|researching|writing|reviewing|publishing|done|failed",
    "created_at": "ISO8601", "updated_at": "ISO8601",
    "artifact_path": "/abs/path",
    "stage_outputs": {"research": None, "draft": None, "review": None, "publish": None},
    "error": None,
}
```

要点：锁只保护 status 转移，不保护阶段内算力工作（参见 pitfall "Don't hold the file lock across the agent turn"）[来源：~/.hermes/skills/kanban-swarm/SKILL.md]。

## 七、已知坑与对开发者友好的提醒

本节列出真测期撞到、对下一位工程师最有价值的踩坑记录。

- **路径翻译**：bash `$HOME` 在 Windows 上 = `/c/Users/<user>`，故 `~/.hermes/kanban-swarm/` 落在用户主目录而非 `$LOCALAPPDATA/hermes/`；所有传给原生 Windows Python 的路径用 `C:\\Users\\<user>\\...` 形式 [来源：~/.hermes/skills/kanban-swarm/SKILL.md env-quirks #1-2]
- **python3 是 MS Store stub**：本机执行 `python3 file.py` 会拉起应用商店，必须用 `python` (cpython 3.11.15) 或 `uv run` [来源：~/.hermes/skills/kanban-swarm/SKILL.md env-quirks #3]
- **架构未锁不注册 cron**：未锁架构就注册 cron job 会被打回重建；五项决策必须先一次性问完 [来源：~/.hermes/skills/kanban-swarm/SKILL.md pitfalls 段]
- **状态转移必须非幂等**：禁止阶段自我翻到 done 给下游使用，每个 profile 只在 Orchestrator 授权下进入下一阶段 [来源：~/.hermes/skills/kanban-swarm/SKILL.md]
- **iLink cooldown**：`deliver='wechat'` 是错的，正确键名是 `weixin`；中间阶段 `deliver='local'`，只在最终阶段推微信 [来源：~/.hermes/skills/kanban-swarm/SKILL.md]
- **真测带 scratch branch**：`scripts/dry-run.sh` 不动 git；当用户要"端到端真测"时默认开 scratch branch、不开 force-push、合并一次再暴露 main [来源：~/.hermes/skills/kanban-swarm/SKILL.md]
- **arXiv 编号核查**：2605.15204v1 / 2603.18273v1 是 2026 年编号，正式发布稿应再用正式标题核对一次，避免幻觉 [来源：research.md 风险段]

## 参考资料 / 来源

- [Hermes cron 文档](https://hermes-agent.nousresearch.com/docs/user-guide/features/cron)
- [Hermes Kanban 文档](https://hermes-agent.nousresearch.com/docs/user-guide/features/kanban)
- [Hermes README（cron 段）](https://github.com/NousResearch/hermes-agent)
- [Hugo + GitHub Pages 模板：sglyon/hugo_gh_blog](https://github.com/sglyon/hugo_gh_blog)
- [用户目标仓：lsxll666/blog](https://github.com/lsxll666/blog)
- [SDOF：arXiv 2605.15204v1](https://arxiv.org/abs/2605.15204v1)
- [EDM-ARS：arXiv 2603.18273v1](https://arxiv.org/abs/2603.18273v1)
- [Lilian Weng：LLM Powered Autonomous Agents](https://lilianweng.github.io/posts/2023-06-23-agent/)
- [本地 kanban-swarm SKILL.md](C:\Users\aaex\AppData\Local\hermes\skills\kanban-swarm\SKILL.md)
- [本次调研稿：research.md](C:\Users\aaex\.hermes\kanban-swarm\artifacts\19035e4e3d984a2bad5d908b292c8a07\research.md)
