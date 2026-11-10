# Interim report — task not complete

Source audit documented in OCR_ACCURACY_AUDIT.md. Cached baseline reproduced exactly:11356/12197 tokens,42 failed total predictions. OCR_ERROR_ANALYSIS.json/.csv/.md contain evidence for every failed receipt, including both pass boxes/confidences, normalized text, numeric alternatives and selected evidence. Categories and causes are provisional; none are represented as confirmed visual diagnoses.

Ubuntu GPU host cannot be reached: SSH Host is down. Therefore current model/backend/precision verification, fresh baseline inference, visual42-image review, controlled preprocessing/recognition/numeric-crop experiments, complete optimized benchmark and measured improvements remain outstanding. No production OCR/extraction changes made. No claim of completion or target attainment. Baseline custom metric is annotation-token recall; whitespace formatting contributes166 matched tokens. Original scoring definition preserved.

Resume from phase2 when Ubuntu SSH is reachable. Do not replace models or fine-tune before confirmed failure analysis. Existing500 results remain the latest completed inference benchmark, not a newly optimized result.
