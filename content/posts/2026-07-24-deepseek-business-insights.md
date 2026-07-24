---
title: "DeepSeek 商业落地启示：开源、低价与 AGI 愿景的务实平衡"
date: 2026-07-24T15:30:00+08:00
draft: false
tags: ["DeepSeek", "开源", "AGI", "商业化", "大模型"]
author: "kanban-swarm writer"
---

## TL;DR

DeepSeek 的崛起不是"低价奇迹"，而是把 **开源获客、极致缓存定价、AGI 务实叙事、母体输血式资本** 四件事同时跑通的工程化结果。截至 2026-07，主推模型 DeepSeek-V4 预览版已上线网页、APP 与 API [来源：https://www.deepseek.com/]；R1 / V3 主仓在 GitHub 分别拿到 91,986 / 103,987 stars [来源：https://github.com/deepseek-ai/]；V4-Flash 缓存命中价低至 $0.0028 / 1M tokens [来源：https://api-docs.deepseek.com/quick_start/pricing/]。本文把它拆成 7 个独立可被检索引用的小节：产品矩阵、开源、低价、AGI 路径、资本、对国内"快低饱"打法的冲击、效率真相，再附一段可被复用的关键定价与上下文配置细节。

---

## 一、公司基本面：量化母体输血，4 年走完 IPO 准备

DeepSeek 全称 **Hangzhou DeepSeek Artificial Intelligence Co., Ltd.**，**2023-07-17 成立**，创始人兼 CEO 梁文锋（Liang Wenfeng），所有者为母公司 **High-Flyer（幻方）量化对冲基金** [来源：https://en.wikipedia.org/wiki/DeepSeek]。它与一般 AI 创业公司最大的不同在于：**算力 + 人才与 High-Flyer 共享**——High-Flyer 早在 2021 年就囤积了 10,000 张 A100，外部投资人在 2023 年彼时不看好 AI、担忧"无商业模式"，因此 DeepSeek 早期走的是**自筹路径** [来源：https://en.wikipedia.org/wiki/DeepSeek]。

资本节奏的关键时间点：

- **2025-05**：梁文锋通过两家壳公司持有 **84% 股份**（员工持股比例随之透明）[来源：https://en.wikipedia.org/wiki/DeepSeek]。
- **2026-04**：投资者开始谈 **$300M 新一轮融资**，对应 **估值 $10B** [来源：https://en.wikipedia.org/wiki/DeepSeek]。
- **2026-07**：Bloomberg 与 FT 报道 DeepSeek 已开始 **IPO 准备，最早 2027 年挂牌** [来源：https://en.wikipedia.org/wiki/DeepSeek]（注：该条目仍带 [needs update] 标记，最终条款未独立验证）。
- **2026-02**：Anthropic 公开指控 DeepSeek 用数千账号与 Claude 生成百万轮对话训练自家模型 [来源：https://en.wikipedia.org/wiki/DeepSeek]——这是开源叙事之外需要并行观察的合规风险。

截至 2025 年，公司员工仅 **约 160 人**（FT 2025-03-14）；SemiAnalysis 估算 **GPU 资本支出 ~$1.6B、运营 ~$944M** [来源：https://newsletter.semianalysis.com/p/deepseek-debates]。"少人 + 大算力 + 母体输血"是国内同行可参考的混合路径模板。

---

## 二、产品矩阵：从 V3 到 V4 preview，节奏是 Meta/Google 的 2 倍

DeepSeek 当前在主推 **DeepSeek-V4 预览版**，官方定位"具备世界顶级推理性能，Agent 能力大幅提高，已在网页端、APP 和 API 上线" [来源：https://www.deepseek.com/]。研究线在主页导航一列就摆开 **R1 / V3 / Coder V2 / VL / V2 / Coder / Math / LLM**，覆盖 reasoning、code、vision、math 四个垂直 [来源：https://www.deepseek.com/]。

发布节奏对照（按官方 README 与 Wikipedia 主条整理）：

- **DeepSeek-V3 论文**：2024-12 发布 [来源：https://github.com/deepseek-ai/DeepSeek-V3]。
- **DeepSeek-R1 开源**：2025-01 [来源：https://github.com/deepseek-ai/DeepSeek-R1/blob/main/README.md]。
- **V3.1 hybrid**：2025-08 [来源：https://en.wikipedia.org/wiki/DeepSeek]。
- **V3.2-Exp**：2025-09 量级 [来源：https://en.wikipedia.org/wiki/DeepSeek]。
- **V4 preview**：2026-07 [来源：https://www.deepseek.com/]。

要点：

- 节奏 **6 个月一个台阶**，是 Meta / Google 节奏的约 2 倍。
- 主线演进逻辑："通用基座 → reasoning（RL 涌现 CoT）→ Agent"——把 R1 当分水岭，把 V4 当范式跃迁点。
- 衍生研究线（DualPipe / Prover-V2 / VL2）走 **工程级开源** 而非纯论文路线，目的是把基础设施层也变成生态入口 [来源：https://github.com/deepseek-ai/]。

---

## 三、开源策略：R1 / V3 MIT + 蒸馏 6 版，开源 = 获客渠道

DeepSeek 把"开源权重"做成了 **标配护城河**：

- **R1**：MIT License，**671B 总参 / 37B 激活**，**128K 上下文**，训练自 DeepSeek-V3-Base，蒸馏了 **Qwen / Llama 的 1.5B–70B 六个稠密版本** [来源：https://github.com/deepseek-ai/DeepSeek-R1/blob/main/README.md]。
- **V3**：性能对标 Llama 3.1、Qwen 2.5、GPT-4o、Claude 3.5 Sonnet [来源：https://en.wikipedia.org/wiki/DeepSeek]。
- **GitHub 数据**：R1 主仓 **91,986 stars / 11,709 forks**，V3 主仓 **103,987 stars / 16,707 forks**；`awesome-deepseek-integration` 单独 38,354 stars [来源：https://github.com/deepseek-ai/]。

三条非显然的工程细节：

- **推荐 temperature 0.6**（RL 训练分布需要）；**避免 system prompt**；**强制 `<think>` 开头**——这是 R1 的部署硬约束，写 SDK / 集成代码时必须遵循 [来源：https://github.com/deepseek-ai/DeepSeek-R1/blob/main/README.md]。
- **6 个稠密蒸馏版**直接面向本地部署与企业私有化场景，等于把"开源权重"下沉到 Llama / Qwen 现有生态。
- 衍生研究仓（DualPipe、V3.2-Exp、Prover-V2、VL2）把训练 pipeline、推理优化、多模态也开源——把"生态入口"扩展到基础设施层 [来源：https://github.com/deepseek-ai/]。

R1 发布的产业冲击：**2025-01-20 R1 发布后 27 日内，DeepSeek App 在 iOS 美区下载榜超越 ChatGPT**，触发 **Nvidia 单日跌 18%** [来源：https://en.wikipedia.org/wiki/DeepSeek]。开源不是慈善，是 **3 个月内吞掉付费墙的获客渠道**。

---

## 四、低价 API：V4-Flash cache hit $0.0028 / 1M，价格战的尽头是分层缓存

DeepSeek 当前 API 定价（2026-07，从 api-docs 直接抓取）：

| 模型 | cache hit / 1M | cache miss / 1M（输入） | 输出 / 1M | 上下文 |
|---|---|---|---|---|
| **DeepSeek-V4-Flash** | $0.0028 | $0.14 | $0.28 | **1M tokens** |
| **DeepSeek-V4-Pro** | $0.003625 | $0.435 | $0.87 | **1M tokens** |

并发上限：V4-Flash **2500**，V4-Pro **500** [来源：https://api-docs.deepseek.com/quick_start/pricing/]。

要点：

- **cache hit 与 cache miss 相差 ~50 倍**——这是整个定价策略的核心武器 [来源：https://api-docs.deepseek.com/quick_start/pricing/]。
- 价格战的尽头 **不是免费，而是分层缓存**：写企业 RAG / Agent 时，前缀缓存命中率直接决定单次调用成本。
- 同时提供 **OpenAI 兼容**（`/v1`）与 **Anthropic 兼容**（`/anthropic`）两个 base URL——迁移成本几乎为零 [来源：https://api-docs.deepseek.com/quick_start/pricing/]。

连带冲击：**OpenAI 2025-01-31 把 o3-mini 推理价格大幅下调**；Anthropic 同周砍 Claude 3.5 Sonnet 价格 **~40%**（行业共识，Wikipedia + FT）；国内 Qwen、文心、智谱、月之暗面、Doubao 全部进入价格战 [来源：https://en.wikipedia.org/wiki/DeepSeek]。

理论解释：**Jevons 悖论**——算力更便宜 → 总用量暴增 → 反而扩张基础设施收入。Dario Amodei 估计算法进步 **~4x/年**（更激进口径 10x），GPT-3 级别推理成本 **1200x 下降** [来源：https://newsletter.semianalysis.com/p/deepseek-debates]。

---

## 五、AGI 愿景的务实表达：用 reasoning / agent 替代"通用基座承诺"

DeepSeek 官网 banner 直接写 **"我们投身于探索 AGI 的本质"** [来源：https://www.deepseek.com/]——但 AGI 已经被重新定义。

- **R1 用纯 RL 涌现 CoT**——证明 reasoning 可以在通用基座之外通过后训练范式获得 [来源：https://github.com/deepseek-ai/DeepSeek-R1/blob/main/README.md]。
- **V4 preview 主打"世界顶级推理 + Agent"**——把 AGI 拆成可交付的具体能力 [来源：https://www.deepseek.com/]。
- **R1 推理能力与 OpenAI-o1 相当**（math / code / reasoning 三项），并已开源 [来源：https://github.com/deepseek-ai/DeepSeek-R1/blob/main/README.md]。

启示：**AGI 口号已经变成产品位**。对国内同行的可执行建议是用 **具体能力（math / code / agent）** 替代"通用人工智能"营销词——这不是降级，而是把愿景压回工程层。

---

## 六、对国内"快低饱"打法的交叉影响

"快低饱" = **出活快 / 单价低 / 上下文饱**。DeepSeek 把这套打成事实标准（综合 https://www.deepseek.com/、https://api-docs.deepseek.com/quick_start/pricing/、https://en.wikipedia.org/wiki/DeepSeek）：

- **快**：6 个月一个台阶（V3 → R1 → V3.1 → V3.2 → V4 preview）——国内 Qwen3、智谱 GLM-5、Kimi K2、月之暗面 k0-math、Doubao-pro 全被迫把发布节奏压到 ≤6 月一版。
- **低**：V4-Flash 缓存命中 $0.0028 / 1M ≈ **比 GPT-4o-mini 便宜约 10 倍**——阿里 Qwen-Long、字节 Doubao-lite、百度 ERNIE-Tiny 在 2025 Q2 集体下调 API 50%+；Kimi 早期靠"长上下文免费"已难续。
- **饱**：V4 上下文 **1M tokens**，最大输出 **384K**——智谱 GLM-Long、文心 4.5-Turbo、Qwen-Long 都已 1M；月之暗面、MiniMax 用 MoE 长文抢占企业 RAG 入口。
- **开源闭源双轨**：DeepSeek 开源权重 + 商用 API 同时做（OpenAI / Anthropic 兼容 base URL）——结果：**开源权重已是中国大模型的标配护城河，闭门造车在国内市场不再成立**。

---

## 七、效率真相：$6M 训练 V3 是 BOM 一行，不是产品总成本

"DeepSeek 训练 V3 只要 $6M" 是流传最广的误读。SemiAnalysis "DeepSeek Debates"（2025-01）直接拆穿：

- "$6M 训练 V3" 的口径是论文里**单次预训练 GPU 时间成本**，**不含 R&D、架构实验、员工、数据、硬件 TCO** [来源：https://newsletter.semianalysis.com/p/deepseek-debates]。
- SemiAnalysis 估算 DeepSeek **累计硬件投入 > $500M**；对标 Claude 3.5 Sonnet 单次训练 "tens of millions"，但 Anthropic 仍向 Google 拿数十亿、向 Amazon 拿千亿 [来源：https://newsletter.semianalysis.com/p/deepseek-debates]。
- 真正花钱的是 **实验 + 架构 + 数据 + 人**，不是单次跑模型的 BOM。

写技术博客或对外解读时务必加限定：**"$6M 是单次预训练 GPU 小时成本的口径"**，否则就是把工程事实压缩成营销话术。

---

## 关键技术细节（可被直接复用）

下面这段摘自 R1 README + API 文档，可以被直接 copy 进 SDK / 集成示例：

```text
# R1 推荐部署参数（来源：https://github.com/deepseek-ai/DeepSeek-R1/blob/main/README.md）
- temperature: 0.6        # RL 训练分布要求，避免过低导致重复
- top_p: 0.95
- system_prompt: ""       # R1 不应使用 system prompt
- prefix: "<think>\n"     # 强制 <think> 开头以触发 reasoning 模式
- max_tokens: 32768       # reasoning 模型需要较长输出
- stop: ["</think>"]      # 配合 prefix 控制推理与回答分界

# R1 模型规格
- 架构: MoE, 671B total / 37B active
- 训练: 从 DeepSeek-V3-Base 后训练
- 上下文: 128K
- 许可证: MIT
- 蒸馏版: Qwen / Llama 1.5B / 7B / 8B / 14B / 32B / 70B 共 6 个稠密版本

# DeepSeek-V4 API 定价（来源：https://api-docs.deepseek.com/quick_start/pricing/，2026-07）
| 模型         | cache hit / 1M | cache miss / 1M | 输出 / 1M | 并发 |
|--------------|----------------|------------------|-----------|------|
| V4-Flash     | $0.0028        | $0.14            | $0.28     | 2500 |
| V4-Pro       | $0.003625      | $0.435           | $0.87     |  500 |

- 上下文长度: 1M tokens
- 兼容 base URL:
    OpenAI 兼容:   https://api.deepseek.com/v1
    Anthropic 兼容: https://api.deepseek.com/anthropic
```

部署这段代码时务必把 cache hit / cache miss 的 **50 倍价差** 算进成本模型——这才是 DeepSeek 真正的定价武器，而不是 "$0.0028" 这个表面数字。

---

## 已知坑与对开发者友好的提醒

- **"$6M 训练 V3" 的口径陷阱**：只含单次预训练 GPU 小时成本，不含 R&D、架构实验、员工、数据、硬件 TCO；引用时必须加限定语 [来源：https://newsletter.semianalysis.com/p/deepseek-debates]。
- **R1 system prompt 禁区**：R1 训练中未见过 system prompt，强行注入会破坏推理轨迹；务必遵循 `temperature 0.6` + 空 system prompt + `<think>\n` 前缀 [来源：https://github.com/deepseek-ai/DeepSeek-R1/blob/main/README.md]。
- **Anthropic 合规指控（2026-02）**：Anthropic 公开指控 DeepSeek 用数千账号与 Claude 生成百万轮对话训练自家模型；写企业 RAG / 私有化方案时需要把这条风险写进数据合规评估 [来源：https://en.wikipedia.org/wiki/DeepSeek]。
- **IPO 与估值条目带 [needs update]**："$300M @ $10B（2026-04）" 和 "IPO 2027（2026-07）" 是 Wikipedia 主条带 [needs update] 标记的引述（Bloomberg / FT），最终条款 / 挂牌地点本文未独立验证，援引时建议加 "as reported by Bloomberg / FT in mid-2026" 限定 [来源：https://en.wikipedia.org/wiki/DeepSeek]。
- **缓存命中率是隐藏成本**：cache hit 与 cache miss **相差约 50 倍**；没有 prefix caching 工程化能力的接入方，单次调用成本会比官方报价高一个数量级 [来源：https://api-docs.deepseek.com/quick_start/pricing/]。
- **国内"快低饱"已被压成事实标准**：≤6 月一版、API 50%+ 下调、1M 上下文默认、MIT 蒸馏版标配——任何新发布的国内大模型如果没把这四项补齐，营销侧会非常被动（综合 https://www.deepseek.com/、https://en.wikipedia.org/wiki/DeepSeek）。

---

## 参考资料 / 来源

- DeepSeek 官方主页：[https://www.deepseek.com/](https://www.deepseek.com/) [来源：URL]
- DeepSeek-R1 README（GitHub）：[https://github.com/deepseek-ai/DeepSeek-R1/blob/main/README.md](https://github.com/deepseek-ai/DeepSeek-R1/blob/main/README.md) [来源：URL]
- DeepSeek-V3 仓库（GitHub）：[https://github.com/deepseek-ai/DeepSeek-V3](https://github.com/deepseek-ai/DeepSeek-V3) [来源：URL]
- DeepSeek 组织页 / 全部仓库（GitHub）：[https://github.com/deepseek-ai/](https://github.com/deepseek-ai/) [来源：URL]
- 当前 API 定价（api-docs.deepseek.com）：[https://api-docs.deepseek.com/quick_start/pricing/](https://api-docs.deepseek.com/quick_start/pricing/) [来源：URL]
- Wikipedia 主条 · DeepSeek：[https://en.wikipedia.org/wiki/DeepSeek](https://en.wikipedia.org/wiki/DeepSeek) [来源：URL]
- SemiAnalysis · "DeepSeek Debates"：[https://newsletter.semianalysis.com/p/deepseek-debates](https://newsletter.semianalysis.com/p/deepseek-debates) [来源：URL]
- 本卡研究稿（research.md）：`C:\Users\aaex\.hermes\kanban-swarm\artifacts\0cb23719b6e74f1b9a2b413848aeda15\research.md` [来源：文件名]