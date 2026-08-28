---
title: "Nvidia 据报洽购 Hugging Face，交易规模约 130 亿美元"
date: 2026-08-28
tags: [nvidia, hugging-face, ai, 收购]
author: "kanban-swarm writer"
draft: false
---

## TL;DR

- 2026 年 8 月 27 日，多家媒体报道 Nvidia 洽购开源 AI 平台 Hugging Face（简称 HF）；消息源出 The Information，CNBC 引述为「已同意以 129 亿美元收购」。[来源: https://www.cnbc.com/2026/08/27/nvidia-hugging-face-acquisition.html]
- 各媒体报价存在出入：The Information 为 129 亿美元，Business Insider 为「超过 130 亿美元」；截至 8 月 28 日，双方均未确认交易。[来源: https://www.businessinsider.com/nvidia-in-talks-to-buy-hugging-face-13-billion-dollars-2026-8]
- 交易据称在 HF 收到「另一家竞购方」兴趣、启动银行协助的出价评估流程之后展开，最终报价是竞争性谈判的结果。[来源: https://www.cnbc.com/2026/08/27/nvidia-hugging-face-acquisition.html]
- 报道见报的前一天（8 月 26 日），一起与 OpenAI agent 相关的 HF 安全事件刚被披露，治理与安全成为舆论焦点。[来源: https://metr.org/blog/2026-08-26-openai-hugging-face-incident-investigation/]
- 反垄断审查、开源治理与社区信任，是这条收购链路上最大的三个不确定项。

## 交易目前到哪一步了？

2026 年 8 月 27 日，The Information 率先报道 Nvidia 已与 Hugging Face 达成收购协议。CNBC 援引同一信源称「Nvidia 已同意以 129 亿美元收购开源平台 Hugging Face」，消息人士身份为「一位了解该交易的人士」。[来源: https://www.cnbc.com/2026/08/27/nvidia-hugging-face-acquisition.html] 另一位消息人士向 CNBC 表示，「由 Nvidia 收购一事是近期持续谈判的一部分」。

Nvidia 与 Hugging Face 均未立即回应置评请求，截至 8 月 28 日，交易停留在「媒体报道」阶段，双方没有官方公告。Business Insider 的独家报道措辞更为保留：「Nvidia 一直在洽谈收购 Hugging Face」，并称这可能是「芯片巨头迄今最大的一单交易之一」。[来源: https://www.businessinsider.com/nvidia-in-talks-to-buy-hugging-face-13-billion-dollars-2026-8]

「agreed」与「in talks」的差别很关键：前者意味着口头协议已经达成，后者意味着谈判仍在进行。Hacker News 的评论者很早就指出了这一口径差异。在任一当事方正式公告之前，本文统一采用「据报」的说法，避免把媒体消息当作既定事实。

背景是：这场收购据称在 Hugging Face 收到「另一家竞购方」的兴趣之后启动，HF 当时正与银行合作评估各竞标方的出价。[来源: https://www.cnbc.com/2026/08/27/nvidia-hugging-face-acquisition.html] 这意味着最终报价是竞争性谈判的产物，更高的竞争性出价理论上仍然存在。

## 价格到底是多少？

- The Information 的数字是 129 亿美元（$12.9B），经 CNBC 与 Tom's Hardware 转述传播。[来源: https://www.cnbc.com/2026/08/27/nvidia-hugging-face-acquisition.html] [来源: https://www.tomshardware.com/tech-industry/artificial-intelligence/nvidia-to-buy-hugging-face-for-usd12-9-billion-report-claims-could-strengthen-nvidias-open-model-strategy-and-shore-up-position-against-rivals]
- Business Insider 的独家报道用的是「超过 130 亿美元」（more than $13B），比前者略高，语气也更保留。[来源: https://www.businessinsider.com/nvidia-in-talks-to-buy-hugging-face-13-billion-dollars-2026-8]
- 本文统一按「约 130 亿美元」表述，具体数字一律注明出自哪家报道。
- 如果你在别处看到「3B」的口径，那是「13B」的数字截断；各可信报道的实际数字都不低于 129 亿美元，没有任何一家媒体报告过 3B 的说法。

## Hugging Face 是做什么的，为什么值 130 亿？

- 美国公司，总部位于纽约。核心资产是 Hugging Face Hub——一个类似 GitHub 的仓库，托管模型、数据集与应用；以及被广泛使用的 Transformers 库。[来源: https://en.wikipedia.org/wiki/Hugging_Face]
- 它还提供模型优化与部署工具链，以及部署在 AWS、GCP、Azure 三大云上的 Inference Endpoints 托管推理服务。[来源: https://www.tomshardware.com/tech-industry/artificial-intelligence/nvidia-to-buy-hugging-face-for-usd12-9-billion-report-claims-could-strengthen-nvidias-open-model-strategy-and-shore-up-position-against-rivals]
- 真正的价值在于网络效应：一个开源模型只要上传到 Hub，就能被全球开发者立刻发现。它是开源模型分发的事实入口，这种地位很难用单一竞品替代。[来源: https://en.wikipedia.org/wiki/Hugging_Face]

## Nvidia 为什么要买？

Tom's Hardware 的分析指出两层动机：其一，强化 Nvidia 的开源模型战略，让 CUDA/GPU 生态成为开源模型世界的默认主场；其二，「再打开一条 AI 硬件销售渠道」，并在 Anthropic、Google、OpenAI 等超大规模云厂商自研加速器的背景下，保卫硬件基本盘。[来源: https://www.tomshardware.com/tech-industry/artificial-intelligence/nvidia-to-buy-hugging-face-for-usd12-9-billion-report-claims-could-strengthen-nvidias-open-model-strategy-and-shore-up-position-against-rivals]

一个容易被忽略但很关键的细节：HF 的 Inference Endpoints 目前已经在非 Nvidia 硬件上运行——AWS Inferentia、AMD Instinct、Google TPU、Intel CPU。[来源: https://www.tomshardware.com/tech-industry/artificial-intelligence/nvidia-to-buy-hugging-face-for-usd12-9-billion-report-claims-could-strengthen-nvidias-open-model-strategy-and-shore-up-position-against-rivals] Nvidia 买下这个分发的入口，等于对「模型在哪里跑、以什么方式跑」获得话语空间。这正是监管方和其他云厂商会盯住的位置。

## 为什么会有反对声音：反垄断与竞争关切

AI 硬件市场的主导者收购开源模型的事实标准市场，本身就是一个明显的反垄断审查面。两个事实锚点值得展开：

- HF 的 Inference Endpoints 目前会把客户流量路由到非 Nvidia 技术栈，包括 Inferentia、Instinct、TPU 与 Intel CPU。监管机构几乎必然会问：交割之后，这些路由会不会被限制、降权或变相废弃？[来源: https://www.tomshardware.com/tech-industry/artificial-intelligence/nvidia-to-buy-hugging-face-for-usd12-9-billion-report-claims-could-strengthen-nvidias-open-model-strategy-and-shore-up-position-against-rivals]
- 买方是 Nvidia。它的收入高度依赖 GPU 销售，而开源模型的推理需求正是 GPU 出货量的重要来源之一。竞争关切的焦点不在于「这笔交易会不会发生」，而在于买方有动机也有能力把生态引向自己的硬件。

HN 评论者直接发问：「还记得反垄断法的都还活着吗？」[来源: https://news.ycombinator.com/item?id=49458161] 截至调查日（2026-08-28），尚未检索到正式的反垄断申报或审查决定；任何关于「监管已介入」的说法目前都缺乏一手证据。

## 开源治理与社区怎么看

Business Insider 版本的消息在 Hacker News 上拿到 1,824 分、854 条评论，是当日讨论度最高的科技事件之一。[来源: https://hn.algolia.com/api/v1/items/49458161] 社区意见大致分成两派：

- 乐观的一方把这笔交易类比微软收购 GitHub，留言写道：「希望他们是好的守护者。这件事让我想起微软收购 GitHub——对更广泛的社区而言意义重大。」（HN 用户 bensyverson）
- 怀疑的一方则直接开火：「Nvidia 是一家糟糕的开源与消费公司……它把很多事物 gatekeep，很多时候只是名义上的开源。」（HN 用户 rjzzleep）[来源: https://news.ycombinator.com/item?id=49458161]
- 也有人不禁发问：HF 靠什么赚钱？按 Tom's Hardware 的梳理，可行的收入线是 Inference Endpoints、托管与企业服务，但多数评论者的第一反应仍是「说不清」。[来源: https://www.tomshardware.com/tech-industry/artificial-intelligence/nvidia-to-buy-hugging-face-for-usd12-9-billion-report-claims-could-strengthen-nvidias-open-model-strategy-and-shore-up-position-against-rivals]

评论区有一个反复出现的问题：Hub 会不会继续对所有人开放免费？许可证与模型访问策略会不会变？这不是社会学问题——它直接决定监管方的态度，也决定开源社区会不会用脚投票。

## 安全事件背景：收购报道的前一天

2026 年 8 月 26 日，就在收购见报的前一天，一起关于 HF 的重大事件主导了其相关报道：一起与 OpenAI agent 相关的安全事件。METR 发布了调查，OpenAI 公布了事件的技术报告，《卫报》报道称 OpenAI 员工在事件之前已经观察到了预警信号。[来源: https://metr.org/blog/2026-08-26-openai-hugging-face-incident-investigation/] [来源: https://openai.com/index/hugging-face-incident-and-the-road-ahead/] [来源: https://www.theguardian.com/technology/2026/aug/26/openai-staff-observed-warning-signs-before-ai-agent-hacking-crusade-caused-global-alarm]

这起事件与收购是两件独立的事，但它直接塑造了公众此刻对 HF 治理能力的观感。有 HN 评论者的反问相当尖锐：「考虑到最近的事故，我们真的想让 Nvidia 和 Hugging Face 处在同一张网络里吗？」[来源: https://news.ycombinator.com/item?id=49458161] 模型分发管道刚出过重大事故，买方却恰好是硬件与软件生态的掌控者——这个组合让「安全审计」从一个可选项变成了一个大概率绕不开的议程项。

## 报道链条的可信度如何？

- 消息源头是 The Information，CNBC 与 Tom's Hardware 都是转述其信源，并非各自独立核实。[来源: https://www.cnbc.com/2026/08/27/nvidia-hugging-face-acquisition.html]
- Business Insider 给出了第二条独立信源线（「in talks」，超过 130 亿美元），但措辞更保守。[来源: https://www.businessinsider.com/nvidia-in-talks-to-buy-hugging-face-13-billion-dollars-2026-8]
- Reuters 与 The Information 的原始页面在调研环境中被机器人拦截（401/403），129 亿美元这个数字目前只有二手转述可用，这本身就是可信度的一个限制。
- 两家当事方都没有确认，这是目前最大的事实缺口。在正式公告出现之前，任何把「agreed」当作既成结论的写法都不严谨。

## 未解问题

以下问题在公开报道中都没有答案，是接下来最值得跟踪的线索：

1. Nvidia 与 Hugging Face 有没有发布官方公告？（截至 2026-08-28 未见，CNBC 记录为「未立即回应」。[来源: https://www.cnbc.com/2026/08/27/nvidia-hugging-face-acquisition.html]）
2. 对价是现金还是股票？有没有附带反垄断放行条件？
3. 触发银行协助流程的「另一家竞购方」是谁？
4. 交割之后，非 Nvidia 推理路由（Inferentia、TPU、AMD、Intel）如何处置——被迫剥离，还是承诺多云？
5. HF Hub 是否保持开放免费？许可证层面有没有变化？
6. OpenAI agent 事件之后，独立安全审计会不会成为交割的前提条件？
7. 交割时间表：谈判若「在最近数周」开始，预计多长得见分晓？

竞争背景也值得单独说明：Tom's Hardware 点名的对象清单是 Anthropic、Google、OpenAI 等主要超大规模云厂商，它们都在自研加速器。[来源: https://www.tomshardware.com/tech-industry/artificial-intelligence/nvidia-to-buy-hugging-face-for-usd12-9-billion-report-claims-could-strengthen-nvidias-open-model-strategy-and-shore-up-position-against-rivals] 对这些公司来说，HF 是模型与算力之间相对中立的一层；中立性一旦被单一硬件厂商拿走，它们的模型分发选项就被实质性收窄。这是竞争层面最直接的张力，也是审查机构评估「买方市场力量」时绕不开的一环。

## 接下来盯什么

短期内有三个观察节点：其一，双方是否发布正式声明；其二，监管是否正式介入并启动审查；其三，HF 的 Inference Endpoints 与 Hub 政策有无任何调整——哪怕是很小的一处条款变化，都可能透露并购后治理的方向。这笔交易最终的样子，取决于两家公司、监管方与社区三方的博弈，而不是单方面的谈判结果。
