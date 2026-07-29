# 论文知识图谱构建项目

## 项目背景

赵娜导师团队论文合集的知识图谱构建工程。将 37 篇学术论文（PDF）通过三阶段 Pipeline 转化为可导航、可查询的知识图谱，最终在 Obsidian 中可视化浏览论文间的关联。

## 论文概况

- **总量**：37 篇 PDF
- **研究方向**：复杂网络关键节点识别（14篇）、NLP/文本挖掘（5篇）、生物医学（5篇）、近视预测（4篇）、其他（9篇）
- **核心作者**：赵娜（导师，8篇，涵盖复杂网络和生物医学）
- **语言**：中文 7 篇，英文 30 篇

## 三阶段 Pipeline

```
PDF 论文  ──→  Full Markdown  ──→  Abstract Markdown  ──→  Knowledge Graph
 (37篇)      (Phase 1: MinerU)    (Phase 2: Claude Agent)   (Phase 3: graphify)
```

- **Phase 1** — PDF → Full Markdown：使用 MinerU 提取全文内容（公式/表格/段落），输出到 `full_markdown/`
- **Phase 2** — Full MD → Abstract MD：Claude 子 Agent 逐篇深度阅读，输出结构化摘要（YAML frontmatter + 7 章节），输出到 `abstracts/`
- **Phase 3** — Abstract MD → Knowledge Graph：graphify 生成 `[[wiki-link]]` 知识图谱，输出到 `knowledge-base/` + `graph.html`

## 关键文件

| 文件/目录 | 用途 |
|---|---|
| `论文知识图谱构建方案.md` | 完整方案文档（本体设计、Prompt模板、质量检查清单） |
| `.claude/skills/paper-kg/` | `/paper-kg` 命令的 skill 定义 |
| `full_markdown/` | Phase 1 输出：完整 MD |
| `abstracts/` | Phase 2 输出：结构化摘要 |
| `knowledge-base/` | Phase 3 输出：Obsidian vault |
| `graph.html` | 可视化知识图谱 |
| `GRAPH_REPORT.md` | 图谱分析报告 |

## 常用命令

```bash
# Python 环境
uv venv .venv --python 3.13
source .venv/Scripts/activate

# 依赖安装
uv pip install mineru
uv pip install "graphifyy[pdf,chinese]"

# 图谱构建
graphify extract ./abstracts --obsidian --obsidian-dir ./knowledge-base
```

## Skill 触发

- `/paper-kg` — 手动触发完整论文知识图谱构建流程
