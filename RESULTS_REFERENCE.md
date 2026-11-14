# Results: 304 Silent Data Corruption (SDC)

**Main goal:** Analyze GPU buffer write/verify integrity via a PyTorch pattern verifier  
**Tools:** Run an in-process PyTorch int32 write/verify on the GPU, or on CPU if no GPU is present

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_140 · recorded 2026-11-13). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× AMD Instinct MI300X VF (196288 MB) · INTEL(R) XEON(R) PLATINUM 8568Y+ · Ubuntu 26.04.1 LTS · kernel 7.0.0-38-generic · driver 7.1.9.31600000
- **Run:** 2026-11-13T20:19:48Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 01:14 · benchmark 00:22 · whole run 01:45
- **Source:** run 304_20261113_201948
- **Command:** `bash run_benchmark.sh --smoke --device-id 0 --memory-size 1 --pattern-type walking-1s --num-iterations 2 --duration 20000`

| # | Metric | Result |
|---|---|---|
| 1 | RAS uncorrectable counter delta (uncorrected_ecc_errors_count) | 0.0 |
| 2 | RAS correctable counter delta (corrected_ecc_errors_count) | 0.0 |
| 3 | Wall-clock time, s (test_wall_clock_completion_time_s) | 20.005399227142334 |
| 4 | Allocated verification buffer, GB (allocated_verification_buffer_gb) | 1.073741824 |
| 5 | Loop verification pass rate, % (loop_verification_pass_pct) | 100.0 |
<!-- /profile:smoke -->
