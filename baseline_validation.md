# Baseline Validation Report (Prompt 10)

## 1. Implementation Audit
*   **Original Methodology Reference:** Automated citation verification via API, inspired by CheckIfExist (Abbonato, 2026).
*   **Dataset / Data Split:** Embedded Python dictionary (`topics_dataset`) split evenly between 5 Pre-2022 historical topics and 5 Post-2025 emerging topics.
*   **Hyperparameters & Seeds:** Gemini API set to `temperature=0.0` for deterministic, reproducible generation (greedy decoding).
*   **Evaluation Metric:** Hallucination Rate (%) calculated as `(Unverified Citations / Total Generated Citations) * 100`.

## 2. Discrepancies and Acceptable Adaptations
*   **Adaptation 1 (Data Loading):** Instead of parsing an external `topics_dataset.json` file, the JSON structure was embedded directly into the Colab notebook as a Python dictionary. *Justification:* This is an acceptable adaptation that improves script portability and prevents file-path errors in Colab without altering the underlying data.
*   **Adaptation 2 (Rate Limiting):** The original CheckIfExist methodology assumes uninterrupted API access. Our implementation was modified to include an exponential backoff (retries at 5s, 10s, 15s) after encountering HTTP 429 Too Many Requests errors. *Justification:* Essential adaptation for execution; does not invalidate the comparison as the verification logic itself remains unchanged.

## 3. Validation Status
*   **[VERIFIED] Deterministic Output:** `temperature=0.0` successfully forced the LLM to output consistent structures.
*   **[VERIFIED] Parsing Logic:** The Regex `r"Title:\s*(.*?)\s*\|\s*Author:\s*(.*?)\s*\|\s*Year:\s*(\d{4})"` correctly extracted 50/50 generated citations without dropping data.
*   **[VERIFIED] Verification Oracle:** The Semantic Scholar API correctly identified a known fake paper ("The Quantum LSTM Optimization of Blueberry Muffins") as a hallucination (0 results) during the sanity check.
*   **[UNCERTAIN] Oracle Completeness:** It is uncertain if the Semantic Scholar Academic Graph has 100% real-time coverage of all papers published in late 2026. A small fraction of "hallucinations" in the post-2025 category could theoretically be real but unindexed papers. 
*   **[NEEDS CORRECTION]** None at this time. The previous API rate-limiting error was corrected prior to final evaluation.

## 4. Additional Checks Required (Future Work)
*   Cross-reference a random subset of the flagged "Hallucinated" papers against a secondary database (like Google Scholar or CrossRef) to measure the false-positive rate of the Semantic Scholar API.