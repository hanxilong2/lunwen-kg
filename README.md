# paper-read-kg

学术论文深度阅读与知识图谱构建技能（Claude Code Skill）。

## 简介

`paper-read-kg` 是一个统一流水线技能，用于处理学术论文：从 PDF → 结构化摘要 → Obsidian 知识图谱。支持单篇精读和任意批量的全量处理。

## 处理流水线

```
PDF 论文  ──→  阶段一：发现与组织  ──→  阶段二：深度阅读 + 图谱构建  ──→  阶段三：组内关联
                                                                     阶段四：跨领域分析
                                                                     阶段五：终验与导航
```

| 阶段 | 说明 |
|------|------|
| **阶段一：发现与组织** | 扫描所有 PDF，快速提取元数据（标题/作者/年份/领域），按主题自动分组，分配统一 ID |
| **阶段二：深度阅读** | 使用 pdfplumber（+ pdftoppm 渲染公式/表格）提取全文，按统一规则产出 YAML 元数据 + 7 章节结构化摘要，同步写入知识库 vault 文件（含 `[[wiki-link]]` 双向链接） |
| **阶段三：组内关联** | 生成各组的方法演进链、共享要素分析、方法对比矩阵、作者网络图谱 |
| **阶段四：跨领域分析** | 追踪跨领域方法迁移路径，识别共享方法论，发现潜在合作桥梁 |
| **阶段五：终验与导航** | 双向链接完整性验证，生成 MOC（内容地图）导航中枢，统计仪表盘 |

## 输出产物

- **`abstracts/`** — 结构化 Markdown 摘要（YAML frontmatter + 7 章节中文正文）
- **`knowledge-base/`** — Obsidian 兼容 vault，含 `[[wiki-link]]` 双向导航
- **`abstracts/_MANIFEST.md`** — 论文清单（ID、分组、元数据）
- **`abstracts/_CROSS_DOMAIN.md`** — 跨领域关联分析报告
- **`knowledge-base/MOC-Master.md`** — 四维主导航索引
- **`knowledge-base/_STATS.md`** — 统计仪表盘

## 结构化摘要模板

每篇论文产出一个包含以下 7 个章节的结构化摘要（正文使用中文，技术术语保留英文）：

1. **研究目标** — 1-2 句话回答"论文要解决什么核心问题"
2. **方法** — 核心技术/算法名称 + 方法类型 + 关键公式（LaTeX）
3. **创新点** — 2-3 条，每条需与现有工作对比
4. **数据集** — 名称 + 规模 + 来源
5. **评估指标** — 指标列表 + 基线方法
6. **关键结论** — 2-3 条发现 + 1 条局限性
7. **合集内关联** — 引用文献 + 合集内 `#N` 关联标注

## 参考文件

| 文件 | 用途 |
|------|------|
| `skill.md` | 主 Skill 定义，含完整 5 阶段流水线 |
| `references/abstract-prompt.md` | 核心提取规则：角色设定、YAML 模板、7 章节模板、行为规则、12 项自检清单 |
| `references/tag-vocabulary.md` | 标准化标签词汇表（13 个领域约 80 个标签） |
| `references/entity-extraction-rules.md` | 实体规范化规则：方法名、数据集、评估指标、作者消歧 |
| `references/group-association-prompt.md` | 组内关联发现提示模板 |
| `references/cross-domain-prompt.md` | 跨领域关联发现提示模板 |

## 依赖

| 依赖 | 用途 |
|------|------|
| `pdf` skill（Claude Code） | 提供 `pdfplumber`（文本提取）和 `pdftoppm`（页面渲染） |
| Python: `pdfplumber`, `pypdf` | PDF 文本提取 |
| 系统: Poppler | `pdftoppm` 渲染公式密集页/扫描页 |
| 系统: Tesseract（可选） | 无嵌入文本的纯扫描 PDF 的 OCR 后备方案 |

## 安装

将 `paper-read-kg/` 目录复制到你的 Claude Code 项目的 `.claude/skills/` 下：

```
your-project/
└── .claude/
    └── skills/
        └── paper-read-kg/
            ├── skill.md
            └── references/
                ├── abstract-prompt.md
                ├── tag-vocabulary.md
                ├── entity-extraction-rules.md
                ├── group-association-prompt.md
                └── cross-domain-prompt.md
```

然后在 Claude Code 中输入 `/paper-read-kg` 即可调用。

## 许可证

MIT
