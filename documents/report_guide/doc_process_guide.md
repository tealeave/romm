# Document Processing Guide for Research Papers

This guide outlines the standard operating procedure for processing research PDF files into the project's knowledge base.

## 1. Filename Harmonization

**Objective:** Ensure filenames are descriptive, consistent, and machine-friendly.

*   **Rule:** Use `snake_case` (lowercase words separated by underscores). Remove special characters and spaces.
*   **Action:** If a file has a cryptic name (e.g., `s41416-021.pdf`, `1-s2.0...pdf`, or `Review Article.pdf`), rename it to reflect its full title or main subject.
*   **Example:**
    *   *Original:* `s41598-025-08986-0.pdf`
    *   *Renamed:* `validation_of_an_ai_enabled_exome_transcriptome_liquid_biopsy_platform.pdf`

## 2. Content Extraction & Summarization

**Objective:** Create a structured text representation of the paper for easy retrieval, analysis, and context injection.

*   **Action:** Create a Markdown (`.md`) file in the *same directory* as the PDF, sharing the same base filename.
*   **Tooling:** Use the `read_file` tool to perform OCR or text extraction on the PDF.

### Markdown File Structure

The Markdown file must contain two main sections:

#### Section 1: Detailed Summarization
Focus on the technical and strategic value of the paper. This section is not just a summary of the *paper*, but a summary of its *utility* to us.
*   **Highlights:** What are the key features, algorithms, or biological insights?
*   **Methodology:** How are these features generated? (e.g., specific sequencing depth, bioinformatic tools like HATCHet or Griffin, mathematical formulas).
*   **Data Type Specifications:** Document the experimental design and sample characteristics:
    *   **Sequencing Platform:** (e.g., Illumina NovaSeq, PacBio, Oxford Nanopore)
    *   **Library Prep:** (e.g., PCR-free, KAPA, custom enrichment, whole genome vs. targeted)
    *   **Sequencing Depth:** (e.g., 30X, 100X, 240X coverage)
    *   **Tumor Type:** (e.g., colorectal cancer, breast cancer, non-small cell lung cancer)
    *   **Sample Type:** (e.g., tissue, plasma, serum, circulating tumor DNA)
    *   **For Liquid Biopsy/Somatic Samples:** Tumor fraction (%), variant allele frequency (VAF), or circulating tumor DNA concentration (if reported)
*   **Relevance:** Explicitly connect the findings to the **240X WGS Liquid Biopsy Panel**. Why does this matter for our specific project context?
    *   *Example prompts:* "Does this feature require deep sequencing?" "Can this replace a matched normal?" "Does this help with Stage I sensitivity?"

#### Section 2: Formatted Extraction of Text
A comprehensive, near-verbatim extraction of the paper's content.
*   **Metadata:** Title, Authors, Journal, Date, DOI.
*   **Body:** Extract the text from key sections (Abstract, Introduction, Methods, Results, Discussion).
*   **Fidelity:** This section must be **almost verbatim**. Do not summarize, paraphrase, or condense this section. The goal is to make the full content of the paper accessible to the LLM for future queries (e.g., "What specific primer sequence was mentioned in the Methods?").
*   **Formatting:** Use Markdown headers (`###`) to mirror the paper's structure. Keep paragraphs intact.

## 3. Organization

*   **Co-location:** Keep the PDF and its corresponding `.md` file in the same specific subdirectory (e.g., `documents/fragmentomic/`, `documents/liquid-biopsy/`).
*   **Cleanup:** Do not leave processed files in the root `research/` folder unless they are synthesis documents (e.g., "Gemini - The Evolutionary Leap..."). Move specific paper summaries to their relevant topic folders in `documents/`.
