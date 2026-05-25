---
name: paper-to-report
description: Use when user provides a paper PDF and wants to generate a report/slides, or asks to "analyze this paper", "generate a report for this paper", "follow X format to write a report", or provides a paper path with a reference template folder
---

# Paper-to-Report Generator

## Overview

Given a paper PDF and a reference report folder, generate a report that faithfully
reproduces the paper's content in the reference's format. The report must contain
zero fabricated data and zero personal analysis unless explicitly requested.

Target length: around 400 lines for the content file.

## Core Rules

### 1. Extract Before Writing

Always extract the paper's full text before writing a single line:
- Use `pdftotext` first. If output is sparse, the PDF is image-based — read pages directly.
- Verify: title, authors, affiliations, venue, all table numbers, all figure data.
- If a number appears in the report, you must be able to point to its source in the paper.

### 2. No Fabrication

- Never guess paper content from the filename, directory name, or prior knowledge.
- A paper with "vision" in the filename might be about LLM math reasoning.
- Do not add evaluative words the paper doesn't use: "首次", "证明了",
  "最重要的", "核心突破".
- Simple arithmetic from paper-stated numbers is acceptable.
- Do not rank the paper's contributions or add a "深层含义" section.

### 3. No Phantom Table/Figure References

- Never write "as shown in Table X" or "see Figure Y" when the table/figure is NOT
  present in the report slides. The audience cannot look up the paper during a
  presentation.
- Either: (a) include the table/figure data directly in the report, or (b) don't
  reference it at all. Describe the finding without the phantom reference.
- Bad: "As shown in Table 3, the ablation confirms..." (Table 3 is not in the slides)
- Good: include the ablation data table, then reference it. Or: state the finding
  without naming the table number.

### 4. Limitations Must Come from the Paper

- Search the extracted text for "Limitations", "Future Work", "Appendix", "Failure".
- The limitations slide must paraphrase ONLY what the paper itself says.
- Common pattern: limitations in Appendix A, future work in Appendix A,
  failure analysis in Appendix B. Extract all of them.

### 5. Training Hyperparameters — Commented Out

- Include the paper's training hyperparameters (optimizer, LR, batch size, epochs,
  hardware, etc.) written in the content file but kept fully commented out.
- This preserves the data for future reference while keeping the presentation concise.
- Use block comments: `/* ... */` in Typst, `<!-- ... -->` in HTML, etc.

### 6. Separate Data Summary from Personal Analysis

KEEP (data observations from the paper):
- "Model X improved from A to B on dataset Y (+Z%)"
- Numbers and trends directly calculable from the paper

REMOVE or COMMENT OUT:
- Causal explanations not stated in the paper
- Value judgments ("增益递减", "effect strongest on small models")
- Future work not listed in the paper's own future work section
- Vision statements extending beyond the paper's own conclusion

If unsure: find the sentence in the paper that supports it. No sentence → comment it out.

### 7. Keep It Concise (~400 Lines)

- Focus on: motivation, method, experiments, key results, limitations.
- Do NOT create slides for: related work (unless the paper's key contribution is
  positioning against prior work), dataset details beyond basic descriptions,
  every single ablation study — pick the most important 1-2.
- Each slide should make one clear point. If a slide is crammed with 10+ bullet
  points, split it or trim it.
- Tables should fit on one slide without scrolling. If a table is too large,
  show only the most representative rows.
- Use commented-out hyperparameters instead of a dedicated hyperparameter slide.

## Workflow

### Step 1: Extract Paper Content

```
1. pdftotext paper.pdf /tmp/paper.txt
2. If extraction is sparse, Read(paper.pdf, pages: "1-N") page by page
3. Identify: title, authors, affiliations, venue, code URL
4. Identify: abstract, method, experiments, key tables/figures, limitations/appendix
```

### Step 2: Understand the Reference Format

```
1. List all files: find ref/ -type f
2. Identify: content file vs template/config files
3. Read the content file fully — understand structure, styling, conventions
4. Note markup language, template system, slide/page functions, theme settings
```

### Step 3: Set Up the Report Directory

```
1. Copy ALL template/config files from reference (NOT the content file)
2. Copy paper PDF into the report directory
3. Write only the content file (e.g., main.typ)
```

### Step 4: Write the Report

Typical structure (adapt to reference format):
1. Title/authors slide
2. Table of contents
3. Research motivation
4. Core method
5. Experimental setup
6. Main results (tables with exact numbers)
7. Key analysis (paper's own findings)
8. Why it works (paper's own analysis)
9. Limitations and future work (from paper's appendix)
10. Summary

### Step 5: Systematic Formatting Verification

After writing, verify formatting. For Typst specifically:

**@ Symbol:**
- Body text: escape as `\@` (e.g., `pass\@1`)
- String arguments (titles): `@` literal, no escape
- Math mode `$...$`: `@` literal
- `#link(...)`, `#import`, emails: `@` literal

**Line Breaks:**
- Consecutive lines without blank line → one paragraph. Add `\` for explicit break.
- List items (`-`, `+`, `1.`) auto-break.
- Blank line = new paragraph (larger gap than `\`).

**Special Characters:** `#` starts function → `\#` for literal. `$` starts math.

**Tables:** Ensure column count matches. Use `[*Header*]` for headers.

**Comments:** `//` for line, `/* */` for block. Use block comments to hide
personal analysis while preserving it in source.

## Verification Before Completion

```
1. Every number → find matching line in extracted paper text
2. Limitations → match against paper's Appendix word-for-word
3. No phantom references → every "Table X" / "Fig Y" referenced is present in slides
4. Search for overclaim keywords: "首次", "证明", "最强", "突破"
5. Search for formatting issues: unescaped @ in body, consecutive non-list lines without \
6. Hyperparameters → present but commented out
7. Total lines → ~400, not significantly more
8. Authors/affiliations/venue → match paper exactly
```
