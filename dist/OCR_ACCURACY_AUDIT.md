# OCR accuracy audit — phase 1 documented before changes

## Status
Runtime Ubuntu [local Ubuntu GPU host] is unreachable (SSH: Host is down). No new inference, model verification, or optimization measurement has been completed for this audit. Cached-output re-evaluation is not an inference reproduction. Production code, images and annotations remain unchanged.

## Actual architecture
The 500-image baseline runs `benchmark/token_experiment.py`, not `app/ocr_service.py`. OpenCV `imread` decodes original image bytes to BGR. Sensitive pass: lang=id, GPU, MKLDNN disabled, max detector side1920, detection threshold0.2, box threshold0.35, unclip1.8. Large pass: cubic2x image, max detector side2560, other detector defaults inherited. Orientation classification, unwarping and textline orientation disabled. Boxes from large pass are divided by2. Axis aligned boxes and a coarse angle from polygons are retained, polygons discarded.

PaddleOCR model names/version/precision are NOT pinned in that runner. Historical logs identify PP-OCRv6_medium_det/rec, PaddleOCR3.7.0, Paddle3.3.1, GTX1050Ti driver550.120. These are historical observations, not current runtime verification. Backend configured as Paddle; precision/actual inference engine/default detector parameters must be inspected in the loaded pipeline when Ubuntu returns. No justified claim of FP16 or model identity from lang=id alone.

`app/token_refine.py`: combine both passes, secondary confidence>=0.75; rank recognition confidence minus0.06 for very tall boxes and0.001 for secondary. Suppress any candidate intersecting >=50% of the smaller box area. This can remove a correct larger region when a smaller wrong region has higher confidence. Confidence is model recognition score, not calibrated probability of field correctness.

`app/adapter.py`: median angle, corrected centers, group row when vertical difference<=0.5 median height. Join distant columns with pipe, paragraphs split at0.8 median height. Angle includes zero estimates from short regions. Distorted rows can be mispaired. Formatting inserts currency and colon-digit spaces, without character correction.

`app/receipt_spatial.py`: calls text rules first, then labels/amounts paired geometrically with monotone dynamic programming; distance<=1.25 text heights, skip penalty1.5. Grand total takes priority; conflicting totals may resolve using card/payment or subtotal components. `NET TOTAL` is subtotal. Label regexes hardcoded. Alternatives/DP trace are not returned. No extraction confidence. Existing subtotal/payment fallback and stale baseline warnings require review.

`app/receipt.py`: Decimal parser: final1–2 digits decimal; groups of3 thousands. No document-level locale inference: 12,500 always12500. Currency stripping lives in callers, not parser. Text labels prioritized; conflicting totals may be ambiguous. Some arithmetic warnings exist, not a comprehensive validation engine.

API differs: PIL/EXIF-aware image loading, validation limits, native PDF extraction or rasterization; optional CLAHE only in ocr_contrast. ocr_receipt uses detail then conditional cubic2x retry for missing/ambiguous/unpaired grand total. API engine can silently fallback CPU after failure; benchmark runner explicitly requires CUDA but records only device string. API output keeps primary text even if receipt uses retry. LLM optional and disabled by default; no LLM needed for benchmark.

## Baseline and metric
Cached baseline:11356/12197=93.10486% tokens; raw before formatting11190/12197=91.74388%;443/485 total=91.34021%;42 failed,15 total-unscorable;1000 passes stored on gpu:0. Median1.7455s = sum of separately measured pass times per receipt, excluding first receipt. Excludes model construction, merge/extraction and full API overhead. VRAM not measured. Execution success does not imply extraction success.

Scorer `benchmark/analyze.normalized_matches` preserves GT token multiplicity; whitespace split of annotated valid_line words. `norm` strips whitespace, casefolds, strips leading/trailing punctuation .,:;!?|()[]{} and quotes. Hyphens/internal punctuation remain. First exact normalized matches greedily consume predictions anywhere. Then fully numeric tokens compare Decimal parsing. Finally2–3 adjacent nonnumeric GT tokens in same annotation line can join into one output token (length>=5). Score=sum(matched annotation tokens)/all nonempty normalized annotation tokens. This is custom annotation-token recall, not character accuracy, CER, WER, precision or F1. Reordering incurs no penalty; hallucinated extra tokens are unpenalized; annotations cover only part of receipt. Formatting gains166 tokens, not additional character recognition. Numeric format errors may be hidden by normalization.

CER/WER on full OCR versus partial annotations would count legitimate unannotated text as errors. Additional metrics should align predictions to annotated polygons; report coverage, unmatched regions and reading-order metrics separately. Field P/R/F1 also requires distinguishing absent annotation from true absent field. No such scores are fabricated here.

## Next gates
Phase2: rerun frozen baseline on Ubuntu, fingerprint model files, packages, backend/precision and device; preserve first run. Phase3: inspect all42 images against GT polygons and both pass boxes; machine-generated hypotheses require visual confirmation. Phase4: count confirmed causes. Only then design controlled preprocessing/crop/extraction experiments. Fixed split by stable receipt-ID hash, with template leakage warning; inspect labels in development portion only for tuning. Evaluating all500 remains mandatory; report holdout separately. Do not train on42 then call same42 independent evaluation.
