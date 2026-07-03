# Markdown & Microsoft Word Conversion Tools Comparison

This report compares CLI tools and APIs for high-fidelity conversion between Markdown (.md) and Microsoft Word (.docx). This information is intended to support the development of a Gemini CLI skill that automates document conversion based on file characteristics.

## 1. Pandoc (The Industry Standard)
- **Strengths:** Most powerful bi-directional conversion. Supports reference documents for consistent styling.
- **Bi-directional:** Yes (MD ↔ DOCX).
- **Key Commands:**
  - **MD to DOCX:** `pandoc input.md --reference-doc=template.docx -o output.docx`
  - **DOCX to MD:** `pandoc input.docx -t gfm --extract-media=./images -o output.md`
- **Best For:** Complex formatting, math equations, tables, and when a specific Word style (template) is required.

## 2. Microsoft MarkItDown
- **Strengths:** Optimized for clean, LLM-ready Markdown. Strips unnecessary Word formatting.
- **Bi-directional:** No (DOCX → MD only).
- **Key Command:** `markitdown input.docx > output.md`
- **Best For:** Converting legacy Word docs into clean, semantic Markdown for AI analysis or documentation.

## 3. Mammoth
- **Strengths:** Extremely strict semantic conversion. Ignores inline styles (fonts, colors) to ensure structural integrity.
- **Bi-directional:** No (DOCX → MD only).
- **Key Command:** `mammoth input.docx --output-format=markdown > output.md`
- **Best For:** Converting messy Word documents where only headers, lists, and tables matter.

---

## Strategy for Automation Skill

To automate the selection of these tools, a skill should follow this logic:

### Phase 1: File Analysis
- **Identify Direction:** Is it MD → DOCX or DOCX → MD?
- **MD → DOCX:** Default to **Pandoc**.
- **DOCX → MD:**
  - Check for images: If media extraction is needed, use **Pandoc**.
  - Check for "messiness": If the document has heavy inline formatting (found by scanning for complex XML styles), prefer **Mammoth** or **MarkItDown** for cleaner output.
  - LLM Context: If the output is intended for an AI's context window, use **MarkItDown**.

### Phase 2: Execution
1. Install dependencies (pip install markitdown, npm install -g mammoth, or apt/brew install pandoc).
2. Run the selected command based on Phase 1 analysis.
3. Verify output integrity.
