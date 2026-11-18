# Quantifying Recency-Induced Citation Hallucination in LLM-Generated Literature Reviews

**Author:** Ayush Choudhary (`mcl2026015@iiita.ac.in`)  
**Affiliation:** Department of Information Technology, Indian Institute of Information Technology Allahabad, Prayagraj, India  
**Study Pathway:** Pathway B — Controlled Comparative Study

---

## 1. Overview
Large language models (LLMs) are increasingly used to draft scientific literature reviews, yet every parametric model operates under a fixed pretraining knowledge cutoff. This repository contains the reproducible code, frozen experimental dataset, verification logs, and ACM-formatted manuscript for a controlled comparative study measuring how **topic recency** drives citation hallucination.

Holding the model (`gemini-3.1-flash-lite`), decoding temperature (`0.0`), and structural prompt constant, we generate 50 citations evenly split across **5 historical computer science topics (`pre_2022`)** and **5 emerging topics (`post_2025`)**, verifying every reference programmatically against the **Semantic Scholar Academic Graph (S2AG) REST API**.

---

## 2. Repository Structure

    ├── README.md                        # Project overview, setup, and reproduction guide
    ├── research_decisions.md            # Phase I–V decision log, Gate G1 brief, and peer review
    ├── baseline_validation.md           # Phase III baseline audit, adaptations, and sanity checks
    ├── ai_assistance_log.md             # Human-AI collaboration, debugging, and accountability audit
    ├── notebooks/
    │   └── endsem_atr_exp.ipynb         # Google Colab experimental pipeline (Cells 1–3)
    ├── results/
    │   ├── experiment_results.csv       # Frozen dataset of 50 generated & verified citations
    │   ├── hallucination_comparison.pdf # High-resolution vector bar chart (embedded in paper)
    │   └── hallucination_comparison.png # High-resolution raster bar chart
    └── paper/
        ├── main.tex                     # ACM-formatted 6–10 page LaTeX manuscript
        └── references.bib               # Verified BibTeX bibliography

---

## 3. Frozen Experimental Results

Out of 50 target citations (25 per temporal window), 45 were successfully evaluated by the S2AG API and 5 `pre_2022` queries were excluded due to persistent HTTP 429 rate-limit timeouts (`API_ERROR`):

| Temporal Window | Target ($N$) | Evaluated ($N_{\text{eval}}$) | Verified | Hallucinated Author | Hallucinated Title | API Error | Hallucination Rate (%) |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Historical (`pre_2022`)** | 25 | 20 | 18 | 2 | 0 | 5 | **10.0%** ($2/20$) |
| **Emerging (`post_2025`)** | 25 | 25 | 19 | 5 | 1 | 0 | **24.0%** ($6/25$) |

### Key Takeaways
* **2.40× Recency Surge:** Citation hallucination rises by **+14.0 percentage points** (from $10.0\%$ to $24.0\%$) when moving from foundational pre-2022 topics to emerging post-2025 topics.
* **Author-Title Conflation Dominates ($87.5\%$ of Errors):** Seven of the eight flagged hallucinations (`HALLUCINATED_AUTHOR`) pair real, prominent researchers in a subfield with newer or altered paper titles, whereas pure title fabrication (`HALLUCINATED_TITLE`, $1/8$) occurs exclusively in the `post_2025` window.

---

## 4. Environment & Reproduction Instructions

### Prerequisites
* **Runtime:** Google Colab (Python 3) or local Python $\ge 3.10$ environment
* **Packages:** `google-genai`, `requests`, `matplotlib`
* **API Credentials:**
  1. `GEMINI_API_KEY` (Google AI Studio)
  2. `SEMANTIC_SCHOLAR_API_KEY` (Semantic Scholar Academic Graph API)

### Step-by-Step Execution
1. **Configure Secrets:** Open `notebooks/endsem_atr_exp.ipynb` in Google Colab, open the **Secrets** (key icon) sidebar, and add `GEMINI_API_KEY` and `SEMANTIC_SCHOLAR_API_KEY` with **Notebook access** enabled.
2. **Run Cell 1 (Setup & Sanity Check):** Installs `google-genai`, initializes the API clients, and runs a negative sanity check against a fabricated title (`"The Quantum LSTM Optimization of Blueberry Muffins"`) to confirm S2AG returns `HALLUCINATED_TITLE`.
3. **Run Cell 2 (Controlled Generation & Verification):** Queries `gemini-3.1-flash-lite` at `temperature=0.0` across all 10 benchmark topics, extracts citations via regex, verifies each against the top 3 S2AG search candidates with a `4.0s` delay and exponential backoff, and saves `experiment_results.csv`.
4. **Run Cell 3 (Audit & Figure Export):** Computes exact verification counts using Python's built-in `csv` module and exports `hallucination_comparison.pdf` and `hallucination_comparison.png`.
5. **Compile Manuscript:** Upload `paper/main.tex`, `paper/references.bib`, and `results/hallucination_comparison.pdf` to Overleaf (using `pdfLaTeX` + `BibTeX`) to build the final ACM-formatted PDF.

---

## 5. Citation

    @inproceedings{choudhary2026recency,
      author    = {Choudhary, Ayush},
      title     = {Quantifying Recency-Induced Citation Hallucination in LLM-Generated Literature Reviews: A Controlled Temporal Comparison},
      booktitle = {AI Tools for Research: Experimental Study (IIIT Allahabad Research Lab)},
      year      = {2026},
      address   = {Prayagraj, India}
    }