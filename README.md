# Paper-to-Report Skill

## 功能描述

将学术论文 PDF 自动转化为演示报告（slides/report），严格基于论文原文内容，不编造数据、不添加个人分析。

### 核心能力

- 从论文 PDF 提取完整文本并验证所有数据点
- 按照用户提供的参考报告格式（Typst typslides / LaTeX Beamer / Markdown 等）生成报告
- 自动处理目标格式的排版陷阱（如 Typst 中的 `@` 转义、换行、表格语法等）
- 将个人分析与论文原文严格分离，分析内容默认注释保留
- 训练超参数写入但默认注释，保持汇报简洁
- 控制报告长度（~400 行），聚焦核心内容

### 适用场景

- 论文组会报告准备
- 论文阅读笔记结构化
- 学术分享 slides 快速生成

## 前置依赖

### 必需工具

| 工具 | 用途 |
|------|------|
| `pdftotext` (poppler-utils) | 提取论文 PDF 文本 |
| Typst compiler (可选) | 编译 `.typ` 文件为 PDF |

### 依赖的其他 Skill

本 skill 独立运行，无强制性前置 skill 依赖。但在实际工作流中，以下 skill 可配合使用：

| Skill | 使用时机 |
|-------|---------|
| `superpowers:brainstorming` | 撰写报告前，理清论文结构与分析思路 |
| `superpowers:verification-before-completion` | 报告完成后，系统性验证数据准确性和格式正确性 |
| `superpowers:using-git-worktrees` | 需要在隔离环境中生成报告时 |

## 使用方法

```
用户: 仿照 /path/to/reference_template 的格式，
      深入分析论文 /path/to/paper.pdf，
      并完成 report 的撰写
```

Skill 会自动触发，执行以下流程：

1. 提取论文全文（pdftotext / 直接读取 PDF）
2. 分析参考模板的结构和样式
3. 复制模板文件，仅修改内容文件（如 `main.typ`）
4. 按论文结构撰写报告：动机 → 方法 → 实验 → 结果 → 分析 → 局限
5. 系统性验证格式和数据准确性

## 关键规则

- **不编造**: 报告中每个数字必须能在论文原文中找到出处
- **不虚引**: 不引用报告中不存在的表格或图片编号
- **不越界**: 局限性/未来工作部分仅使用论文 Appendix 原文
- **可溯源**: 个人分析用注释保留，与论文原文明确区分

## 安装

```bash
# 克隆到 Claude Code skills 目录
git clone git@github.com:CarterYin/paper_report_skill.git \
  ~/.claude/skills/paper-to-report
```

或在 Claude Code 会话中通过 `/plugin` 安装。

## 示例模板

`example/` 目录包含一个完整的 Typst typslides 参考模板：

```
example/
├── ttrv_report/               ← Typst 项目模板
│   ├── template/main.typ      ← 报告内容文件（skill 仅修改此文件）
│   ├── lib.typ                ← 库入口
│   ├── utils.typ              ← 主题/幻灯片/进度条等工具函数
│   ├── typslides.typ          ← typslides 包主文件
│   ├── typst.toml             ← 包配置
│   ├── LICENSE / README.md
│   └── (论文PDF存放于此，示例中未包含)
└── ttrv-recovery-analysis.md  ← 论文深度分析笔记（额外参考）
```

**重要说明**：

- 此示例基于 [typslides](https://github.com/manjavacas/typslides) 包（`@preview/typslides:1.3.2`），主题为 `"bluey"`
- **这只是一个示例模板，并非唯一选择**。用户可以使用任意 Typst 模板（如 touying、polylux 等）、LaTeX Beamer、Markdown 等任何格式作为参考
- Skill 的核心逻辑是：分析用户提供的参考格式 → 复制模板文件 → 仅修改内容文件。无论参考格式是什么，流程一致
- 如果需要使用其他模板，只需将模板文件放在参考文件夹中，skill 会自动适配

## License

MIT
