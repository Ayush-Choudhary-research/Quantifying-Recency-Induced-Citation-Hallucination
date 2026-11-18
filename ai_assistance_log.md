## Code Verification: Data Loading (Prompt 6 & 8)
*   **Task:** Verifying dataset loading logic.
*   **AI Proposal:** The AI initially suggested checking `json.load()` in the independent verification checklist.
*   **Human Decision:** I manually reviewed the generated Python script and identified that `json.load()` was unnecessary because the dataset was embedded directly as a Python dictionary. I approved the embedded dictionary approach for script portability and bypassed the redundant JSON file check.

## Debugging Log: API Rate Limiting (Prompt 11)
*   **Task:** Executing baseline script for citation generation and API verification.
*   **Observed Error:** The Colab console returned dozens of `429 Client Error` responses from the Semantic Scholar API, and the final hallucination rate was calculated incorrectly (4%) because it ignored the `API_ERROR` flags.
*   **AI Proposal:** The AI diagnosed that the 1.5-second `time.sleep()` was insufficient for Semantic Scholar's strict limits. It provided an updated `verify_citation` function containing an exponential backoff loop (retrying on 429 with escalating 5s, 10s, 15s delays) and increased the base sleep to 3 seconds.
*   **Human Decision:** Verified the logic of the exponential backoff, replaced the function in the Colab notebook, and re-ran the experiment to ensure valid baseline data.


## PROMPT 18: Final Human-AI Accountability Audit

### 1. Phase-by-Phase Human-AI Collaboration Summary
| Phase | Prompts | AI Role (Drafting & Scaffolding) | Human Role (Verification, Debugging & Decisions) |
| :--- | :---: | :--- | :--- |
| **I. Scoping & Brief** | 1–5 | Synthesized gap matrix, proposed Pathway B comparative framing, and drafted Gate G1 brief structure. | Defined temporal boundary (`pre_2022` vs. `post_2025`), selected `gemini-3.1-flash-lite`, verified all 22 BibTeX references, and excluded 7 incomplete citations. |
| **II. Protocol Freeze** | 6–7 | Drafted validation checklist, standardized prompt template, and regex extraction pattern. | Approved deterministic decoding (`temperature=0.0`), audited data-loading logic, and froze the 10-topic benchmark prior to data collection. |
| **III. Execution & Debug** | 8–11 | Generated Colab Python script, S2AG verification wrapper, and baseline validation template. | Executed live API runs, diagnosed HTTP 429 rate limits and `None` retry dropouts, executed the negative fake-paper sanity check, and froze `experiment_results.csv`. |
| **IV. Evaluation & Plots** | 12–14 | Provided matplotlib/seaborn visualization scripts and interpretation structure. | Audited CSV counts after runtime disconnect, resolved `pandas` 3.1.1 import conflict via pure-Python CSV aggregation, and aligned `research_decisions.md` to `10.0%` vs. `24.0%`. |
| **V. Manuscript & Repo** | 15–18 | Drafted ACM LaTeX manuscript, adversarial peer review, GitHub `README.md`, and audit templates. | Verified every claim in `main.tex` against `experiment_results.csv`, checked BibTeX integrity, compiled Overleaf PDF, and signed off on final deliverables. |

---

### 2. Concrete Real Examples of AI Errors Caught & Corrected by the Researcher
1. **Insufficient Rate-Limit Buffering (HTTP 429 Failure):** The initial AI-generated `verify_citation()` function used a static `1.5s` delay without retry logic, causing Semantic Scholar to reject nearly all requests with HTTP `429 Client Error` and producing a misleading `4.0%` hallucination rate. *Human Action:* Diagnosed the `API_ERROR` masking in the logs and enforced a `4.0s` delay with exponential backoff.
2. **Redundant `json.load()` Verification Check:** The AI's Prompt 6 checklist instructed verifying `json.load()` even though the Colab implementation embedded `topics_dataset` directly as a Python dictionary. *Human Action:* Caught the mismatch during code review, documented the embedded dictionary adaptation in `baseline_validation.md`, and executed the negative fake-paper oracle test instead.
3. **Implicit `None` Return on Retry Exhaustion:** When all retries hit HTTP 429, the loop in `verify_citation()` exited without a fallback `return` statement, writing blank (`NaN`) cells into `experiment_results.csv` and shifting percentages across runs. *Human Action:* Audited the raw CSV row-by-row, added an explicit `return "API_ERROR"` fallback and top-3 candidate search, and froze the final 50-row CSV.
4. **Mid-Session `pandas` 3.1.1 Dependency Conflict:** Running `!pip install -U pandas` inside Colab upgraded `pandas` mid-session and triggered `ImportError: cannot import name 'Pandas4Warning'` during `pd.crosstab()`. *Human Action:* Restarted the runtime cleanly, removed the unnecessary `pip` upgrade, migrated to the supported `google-genai` SDK, and computed final counts with Python's built-in `csv` module.

---

### 3. Final Integrity & Submission Gate Checklist (G1–G3)
- [x] **Gate G1 (Research Brief):** Problem, RQ1/RQ2, Pathway B comparative design, and $>15\%$ decision threshold documented in `research_decisions.md`.
- [x] **Gate G2 (Baseline Validation):** Negative sanity check (`HALLUCINATED_TITLE` on fake paper), regex parser completeness (`50/50`), and adaptations documented in `baseline_validation.md`.
- [x] **Gate G3 (Frozen Results & Manuscript):** `experiment_results.csv` ($N=50$), `hallucination_comparison.pdf`, `research_decisions.md`, `README.md`, and `paper/main.tex` all report identical frozen figures (`10.0%` `pre_2022` vs. `24.0%` `post_2025`).
- [x] **Citation & Claim Verification:** All 22 BibTeX entries in `paper/references.bib` have verified authors, venues, years, and DOIs/arXiv URLs; zero unverified statistics appear in the manuscript.

**Final Sign-Off:** I, Ayush Choudhary, take full academic responsibility for the experimental execution, frozen dataset, statistical interpretations, and final manuscript in this repository.
