---
title: "大模型面试怎么准备：从 JD 到一条可落地的路线"
date: 2026-09-14T15:00:00+08:00
tags: ["大模型", "面试", "AI Agent"]
author: "李佳悦"
---

投了一堆「大模型应用工程师」「AI Agent 开发」的岗位，简历石沉大海，好不容易拿到面试，却不知道对方到底会问什么——你翻遍面经，发现有人在背八股，有人在刷 LeetCode，还有人在 GitHub 上背题库，说法互相矛盾。到底该准备什么？

这篇文章不给你「标准答案」，因为这个领域根本没有单一权威来源。它只做三件事：用官方招聘数据告诉你这类岗位到底在招什么样的人；把能力栈逐项拆开，每项给你一个权威出处；最后给出一条可以照着动手的准备顺序。所有结论的强度，我会在文末统一校准。

## 一、这类岗位到底在招什么人：先看招聘数据

与其猜，不如直接读 JD。我抽了三家官方招聘接口，方法可复现，结果如下（截至 2026-09）：

- Anthropic 官方招聘接口（Greenhouse）在招 **596** 个职位，标题含「Applied AI」**37** 个、「Prompt」**仅 1** 个；
- OpenAI 官方招聘接口（Ashby）在招 **795** 个，标题含「Applied AI」**48**、「Agent」**22**、「Prompt」**0**；
- 腾讯招聘官方接口按关键词命中：**Agent 216** 条、**RAG 83** 条、**大模型应用 34** 条、**提示词 7** 条。

先说清楚口径：以上是「在招职位标题 / 接口命中的条数」，不是岗位占比，也不代表哪个方向更有前景。但两个结构性信号是清楚的：

第一，这类岗位的入口叫 **Applied AI / Forward Deployed / Solutions（客户侧工程）**，不叫「提示词工程师」。两家顶级实验室的在招标题里，「Prompt」合计只命中 1 条，OpenAI 为 0。

第二，中文市场同样如此——提示词只命中 7 条，而 Agent 216 条、RAG 83 条。提示词已经从「岗位名」退化为「岗位描述里的技能项」。

再看一条最能代表能力栈的中文 JD：腾讯 WeTalk 的「大模型应用研发工程师（Agent Harness）」（北京，2026-09-11 发布，[链接](https://careers.tencent.com/jobdesc.html?postId=1934984932162117632)）。原文要点是「Agent Loop、编排和运行环境」；「Context Engineering 能力，包括上下文压缩、Skill、跨会话 Memory、检索和长任务状态管理」；「Agent 评测体系，通过评测数据、运行 Trace 和用户反馈定位问题，推动模型、Prompt、Context、Tool 等策略持续优化」。这一条几乎就是全文能力栈的中文原始出处：Prompt → Context（压缩 / 记忆 / 检索 / 状态）→ Tool → 评测闭环。

英文 JD 交叉印证，Anthropic 的「Applied AI Engineer, Enterprise Tech」（页面更新 2026-09-09，[链接](https://job-boards.greenhouse.io/anthropic/jobs/5057647008)）要求「4+ years」技术岗经验，以及「Production experience with LLMs, including advanced prompt engineering, agent development and frameworks, evaluation frameworks, transcript analysis, MCP, and deployment at scale」，并明确「proficiency in Python or TypeScript」。这条 JD 的官方页面同时给出年薪 **$200,000–$320,000**。

但注意一个反例：同为「Applied AI Engineer」，OpenAI 的 Startups 岗（2026-06-01，[链接](https://jobs.ashbyhq.com/openai/71e7252f-abb1-4b74-8e69-318413042357)）写 5+ 年，Delhi 岗（2026-08-03，[链接](https://jobs.ashbyhq.com/openai/bf036b23-cd23-46d0-a02f-4b1483f4698a)）不写年限、改写成「有把 AI/ML 系统从原型做到生产的记录」，Partner 岗要 8+ 年技术咨询。所以**年限不是稳定信号，职责描述（evals / retrieval / observability）才是**。至于中文岗位的年限、学历要求，腾讯接口的 `Requirement` 字段返回为空，我无法核实，本文不妄断。

一句话总结岗位画像：这是一类「把 LLM 从 demo 做到生产」的工程师，考核重心是 prompt/context、agent、工具调用、评测与可观测性——而不是背八股。

## 二、能力栈逐项拆开：面试围绕这五件事

### 1. Prompt 与 Context Engineering

为什么重要：这是应用的地基。Anthropic 官方 prompt 文档（[链接](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview)）开头就要求先做两件事——定义清楚用例的成功标准、准备可实证检验的测试方法。它其实在说：prompt 不是写一段话，而是先定义「什么叫好」，再迭代。

Context engineering（Anthropic 2025-09-29《Effective context engineering for AI agents》，[链接](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)）把 context 定性为「关键但有限的资源」，目标是「最小的高信号 token 集」；原文还引了一个现象：context 里 token 越多，模型精确召回能力反而下降。面试被问「上下文怎么管」，能答出「它是有限资源，要压缩到最小高信号集合」就比「我把 context window 加大」高一个层次。

### 2. RAG 与 Tool Calling

RAG（Lewis et al. 2020, NeurIPS，[论文](https://arxiv.org/abs/2005.11401)）把预训练参数记忆与非参数记忆（检索）结合，解决知识时效与幻觉问题。Tool calling（OpenAI 官方文档，[链接](https://platform.openai.com/docs/guides/function-calling)）是一个多步对话，五步循环：请求 → 模型返回工具调用 → 应用侧执行 → 回填结果 → 再请求。面试问「tool calling 怎么回事」，答这五步循环比答「就是调用函数」完整得多。MCP（[链接](https://modelcontextprotocol.io/docs/getting-started/intro)）则是把 AI 应用连到外部系统的开源标准。

### 3. Agent 形态与取舍

Anthropic《Building effective agents》（2024-12-19，[链接](https://www.anthropic.com/engineering/building-effective-agents)）区分了两者：Workflow 是通过预定义代码路径编排 LLM 与工具，Agent 则动态决定自己的流程和工具使用。注意：该文写于 2024 年底，页面顶部已声明文中工具生态在 2024 年 12 月后发生了很大变化。但两条建议现在依然成立——能用最简方案就用最简方案，从直接调用 LLM API 开始，而不是上来就上框架。面试里「什么时候不该用 Agent」是高频追问，答案就藏在这两句里。

### 4. 评测

Anthropic《Demystifying evals for AI agents》（2026-01-09，[链接](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)）给出 grader 三分类：code-based / model-based / human，并定义了 task、trial、grader、transcript、outcome、harness 六个要素。

面试里怎么答「你怎么验证一个 Agent 做得好」：先说用什么 grader——能代码判定的用 code-based，要语义判断的用 model-based，兜底用 human；再说从线上真实失败里取材建评测集；最后补一句「越早开始越好，别等完美评测集」。这样就把评测从玄学落到了工程。RAG 场景还可以提 Ragas（2023，[论文](https://arxiv.org/abs/2309.15217)）这类无参考评测框架。至于「评测会不会被刷分」这类奖励机制细节，不在本文范围，需要时直接读 Anthropic 官方评测系列。

### 5. 微调与训练侧（区分度项）

LoRA（2021，[论文](https://arxiv.org/abs/2106.09685)）冻结预训练权重、注入可训练的低秩分解矩阵，能把可训练参数量降几个数量级。应用岗不一定考这个，但知道它和 prompt、检索是「解决问题频谱」上的不同位置，能体现你的边界感——这也是把应用岗和算法岗区分开的标志：评测岗（如 Anthropic 的 Model Evaluations，[链接](https://job-boards.greenhouse.io/anthropic/jobs/5198255008)）的门槛是分布式系统 + 训练侧知识，不是「会写评测题」，两者不是同一条准备路线。

## 三、面试形式：只说到官方口径

Anthropic 官方《How we hire》（[链接](https://www.anthropic.com/careers)）明确：面试都用 Google Meet；技术岗用 Colab / CodeSignal 这类 live coding 工具，且「可以查资料，只要熟悉基本语法别浪费时间」；「一半的技术员工没有 ML 背景，一半有 PhD，但很多出色的同事没上过大学」，并鼓励把独立研究、博客、开源贡献放在简历最上面；不提供简历/面试反馈，投了没中要等 12 个月再投。

Anthropic 官方《候选人 AI 使用规范》（2025-07-10，[链接](https://www.anthropic.com/candidate-ai-guidance)）：take-home 作业默认「不用 Claude 完成，除非明确说明」；live interview「全程无 AI 辅助，除非明确说明」；简历「先自己写初稿，再用 Claude 润色」。

第三方调研（The Pragmatic Engineer，2024-12-03，汇总 49 位从业者、其中 65% 是 hiring manager，[链接](https://newsletter.pragmaticengineer.com/p/how-genai-changes-tech-hiring)）提示：code review / system design 这类新形式可能比纯 coding 更被看重，LeetCode 式算法题在减少；有 CEO 说客户里只有不到 5% 在 live coding 环节允许用 GenAI 工具。注意：该文是付费内容，我仅核实到前半部分，且距今近两年，只作参考。

至于「中文市场 = 八股 + 手撕 + 项目深挖三件套」这类说法，我只在公开面经里见过，未见权威出处，本文不作断言。

## 四、一条可行的准备顺序（动手部分）

阶段一：先把 prompt 和 context 当回事——读官方 prompt 文档，按「先定义成功标准，再迭代」的方式做一个小任务。

阶段二：动手做一个最小 Agent + 评测——哪怕只是「检索 → 生成 → 打分」三段式。Hugging Face 的免费 Agents Course（[链接](https://huggingface.co/learn/agents-course/unit0/introduction)）含实操与证书，适合起步。

阶段三：把东西写出来。Anthropic 官方明确鼓励把博客和开源贡献放简历最上面，这是唯一被官方盖章的「作品集」策略。

这里给一段零依赖的检索基线——纯标准库 BM25（中文用字符 bigram 兜底分词），直接跑：

```python
import re, math, collections

def tok(s):
    s = s.lower()
    out = re.findall(r"[a-z0-9]+", s)
    for run in re.findall(r"[\u4e00-\u9fff]+", s):
        out += [run[i:i+2] for i in range(len(run) - 1)]
    return out

docs = [
    "RAG 通过检索增强生成，把知识库接入大模型",
    "Agent 会编排模型与工具调用，自主完成多步任务",
    "MCP 是连接 AI 应用与外部系统的开放标准",
]
corpus = {f"doc{i}": tok(d) for i, d in enumerate(docs)}
df = collections.Counter()
for t in corpus.values():
    df.update(set(t))
N = len(corpus)
avg = sum(len(t) for t in corpus.values()) / N
k1, b = 1.5, 0.75

def bm25(q, topk=2):
    scores = {}
    for d, toks in corpus.items():
        tf = collections.Counter(toks)
        s = 0.0
        for w in tok(q):
            if w in tf:
                idf = math.log(1 + (N - df[w] + 0.5) / (df[w] + 0.5))
                s += idf * tf[w] * (k1 + 1) / (tf[w] + k1 * (1 - b + b * len(toks) / avg))
        scores[d] = s
    return sorted(scores.items(), key=lambda x: -x[1])[:topk]

print(bm25("RAG 检索"))
```

为什么要写这个：面试被问「RAG 你做过吗」，与其背概念，不如能说「我写过一版零依赖的 BM25 检索，中文分词用 bigram 兜底；想上神经检索就换 sentence-transformers，想本地跑、不花云 key 就用 Ollama 或 llama.cpp」。这比任何背诵都有说服力。一个诚实的提醒：在只有几篇文档的语料上算命中率没有意义——这正是「先攒语料、再做评测」的理由，也顺带解释一个真实现象：中文问题打英文语料时，纯关键词检索会明显漏召回，所以中文场景要么上中文分词、要么上向量检索。

## 五、把话说回来：这只是「一条」可行路线

两个信号值得放进你的预期管理。据媒体报道的招聘平台报告（具体口径以报告原文为准）：

- 脉脉高聘《2026 春招职场洞察报告》（2026-05 报道，[链接](https://wap.eastmoney.com/a/202605143736787097.html)）：AI 领域岗位量同比增 8.7 倍，但人才供需比也从 1.02 升到 1.23——机会和竞争同时变多。
- 脉脉《2026 年 1-2 月中高端人才求职招聘洞察》（[报告页](https://www.fxbaogao.com/detail/5305710)）：新发岗位中 3 年以上经验要求占比已达 73.34%，1 年以内经验岗位量同比减少约 20%（仅社招），34% 的岗位明确要求 AI 能力——入门门槛在往上走。

权威研究也在提醒结构性变化：Stanford Digital Economy Lab《Canaries in the Coal Mine?》（2026-08 修订，[链接](https://digitaleconomy.stanford.edu/news/canariesaug26/)）发现，22–25 岁、高 AI 暴露职业的就业水平比低暴露同龄人低约 19%，缺口从 2025-07 口径的 15% 扩大到 2026-06 的 19%，机制主要是「减少招聘年轻人」而非裁员。但必须转述作者的自我限定：这是描述性模式，不是因果估计；作者也不把这篇论文（或任何单篇研究）当作 AI 劳动力市场影响的定论。

所以，这篇文章不是「正确路线」，更不是「照着走就能上岸」的承诺。它只是我从官方 JD、官方工程博客和可复现的招聘数据里，整理出来的一条自洽、可动手的路径。你能做的最有价值的事，不是背下这些名词，而是把它们变成一个能拿给面试官看的东西——一个写出来的 agent、一篇讲你踩坑的博客、一段开源贡献。
