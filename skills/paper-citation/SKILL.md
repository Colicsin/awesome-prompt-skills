---
name: paper-citation
description: 根据文本语义检索学术论文并生成规范引用。调用 Semantic Scholar 和 OpenAlex API，按 Tier 分级推荐。
---


# 论文智能引用 Skill

## 触发条件

**正向触发**（执行 skill）：
- 用户请求论文推荐、文献建议、引用查找
- 用户文本中包含 `[CITE]` 标记
- 用户要求生成 BibTeX 或参考文献

**负向触发**（不执行 skill）：
- 纯知识问答（"什么是 BERT？"）
- 解释或总结特定已知论文
- 非学术写作（创意写作、日常文档）

## 输入解析

对于 `[CITE]` 标记：提取前后各 1-2 句作为独立查询。
对于直接请求：整段文本作为查询。

**查询准备**：
1. 提炼核心学术概念，翻译为英文
2. 生成 1-2 个检索变体（同义词替换）
3. 示例：用户写"大模型幻觉问题" → 检索词 `"hallucination in large language models"` 和 `"LLM factual accuracy"`

## API 检索

**主检索 — Semantic Scholar**
```
https://api.semanticscholar.org/graph/v1/paper/search?query={QUERY}&limit=10&fields=title,authors,year,citationCount,venue,externalIds,publicationTypes
```
- 限制 10 条结果（不是 20）
- 仅请求必要字段（去掉 abstract, tldr, referenceCount, influentialCitationCount, journal）

**备选检索 — OpenAlex**（仅当 Semantic Scholar 返回 <5 条时）
```
https://api.openalex.org/works?search={QUERY}&per_page=10&select=id,doi,title,authorships,publication_year,cited_by_count,primary_location,type
```

**错误处理与降级**：
- API 返回 429/5xx：等待 3 秒后重试一次
- 两个 API 均失败或超时：基于 LLM 训练知识推荐论文，明确标注"未经 API 验证"
- 结果 <5 篇：如实呈现，建议用户调整检索词或扩大范围

## 论文评估 — Tier 分级框架

对每篇论文评估，分为三个等级：

**Tier 1（强推荐）**
- 顶级会议/期刊：venue-rankings.md 中标 ★ 的期刊/会议，或 LLM 公认的全球顶刊（Nature, Science, CVPR, NeurIPS 等）
- 被引量：>100 次
- 发表形式：正式发表（非预印本）

**Tier 2（推荐）**
- 正式索引期刊/会议：SCI/SSCI Q1-Q2, CCF-A/B, CSSCI, 北核等
- 被引量：10-100 次
- 发表形式：正式发表

**Tier 3（条件推荐）**
- 低级别索引、预印本、被引 <10 次
- 仅在 Tier 1/2 结果不足时补充
- 需明确标注"预印本"或"低级别期刊"

**评估规则**：
- 优先查 venue-rankings.md 确定期刊等级
- 未命中则用 WebSearch 查询期刊排名（搜索 `"{期刊名}" SCI SSCI CCF ranking` 或 `"{期刊名}" CSSCI 北核`）
- 同一论文既有预印本又有正式版：仅保留正式版
- 按 Tier 排序，同 Tier 内按被引量降序

## 输出格式

对每篇推荐论文输出：

```
### [序号] 论文标题

- **作者**: 作者列表（前三位 + et al.）
- **发表于**: 会议/期刊名, 年份
- **Tier**: [1/2/3]
- **被引量**: N 次
- **推荐理由**: 1-2 句说明与用户文本的语义关联及学术贡献
- **BibTeX**:
  @article{key2024title,
    author = {...},
    title = {...},
    ...
  }
```

**报告末尾**：
1. 汇总所有 BibTeX 到一个完整代码块
2. 简要说明排除了多少篇及原因（如"排除 3 篇预印本"）

## 回填引用（仅当原文含 [CITE]）

将原文中的 `[CITE]` 替换为 `\cite{key1, key2}` 格式（最多 3 个 key），输出修改后的完整正文。

## 用户可选参数

用户可在请求中附加：
- **数量**："推荐 10 篇" → 调整结果数
- **年份限制**："只要 2020 年以后的"
- **领域限定**："计算机视觉方向"
- **包含预印本**："arXiv 的也可以"

## API 限流参考

- Semantic Scholar: 100 req/5min（无 key）, 1 req/sec
- OpenAlex: 无硬限制，建议 200ms 间隔
- 遇限流时添加 1-2 秒延迟
