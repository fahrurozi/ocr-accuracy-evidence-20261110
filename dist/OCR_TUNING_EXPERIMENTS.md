# Controlled experiments — pending runtime availability

|Experiment|Token match|CER|Total exact|Median latency|Evidence|
|---|---|---|---|---|---|
|Historical baseline, cached re-evaluation|93.10486%|Not yet measured|443/485 (91.34021%)|1.7455s historical two-pass only|Cached scorer reproduced; no fresh inference|
|Preprocessing A|Not run|Not run|Not run|Not run|Pending baseline reproduction and visual audit|
|Numeric crop OCR|Not run|Not run|Not run|Not run|Pending baseline reproduction and visual audit|
|Extraction improvement|Not run|Not run|Not run|Not run|Pending confirmed cause analysis|
|Combined pipeline|Not run|Not run|Not run|Not run|Pending controlled experiments|

No pipeline changes implemented. All source images and annotations preserved. No downloads, cloud APIs, training, commits or pushes during this audit. Future experiments must record model file hashes, resolved parameters, precision/backend, GPU telemetry, raw results and complete per-receipt timings. Supplement partial-annotation token recall with spatially aligned CER/WER/numeric CER; do not label full-image-versus-partial-GT edit distance OCR accuracy.
