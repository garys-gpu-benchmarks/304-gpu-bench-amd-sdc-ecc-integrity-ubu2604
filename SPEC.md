# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Runs a PyTorch GPU (or CPU) int32 buffer check that writes 1 to even-index elements and verifies them, then reports a 0-100 pass rate in loop_verification_pass_pct. duration: wall-clock cap (values >=10000 treated as milliseconds). num_iterations: verify loops. memory_size: allocation size in GB (clamped to at least 0.125). Sweep dimensions: device_id, memory_size, pattern_type, ecc_check, num_iterations, output_format, duration, count.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| device_id | `--device-id` | smoke=0, baseline=0, extended=0 | 0 | From Parameter list; see Execution Description With Parameters. |
| memory_size | `--memory-size` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| pattern_type | `--pattern-type` | smoke=walking-1s, baseline=walking-1s, extended=walking-1s | walking-1s | From Parameter list; see Execution Description With Parameters. |
| ecc_check | `--ecc-check` | smoke=true, baseline=true, extended=true | true | From Parameter list; see Execution Description With Parameters. |
| num_iterations | `--num-iterations` | smoke=2, baseline=3, extended=9 | 3 | From Parameter list; see Execution Description With Parameters. |
| output_format | `--output-format` | smoke=csv, baseline=csv, extended=csv | csv | From Parameter list; see Execution Description With Parameters. |
| duration | `--duration` | smoke=20000, baseline=183000, extended=730000 | 183000 | From Parameter list; see Execution Description With Parameters. |
| count | `--count` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| seed | `--seed` | smoke=0, baseline=0, extended=0 | 0 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Run an in-process PyTorch even-index int32 write/verify on cuda:0 when available, otherwise CPU. Does not run rvs or a HIP ecc_walk binary
```

## Raw Output Format

CSV aggregate of the verify run. A single row is duplicated to two samples

sample_index,status,uncorrected_ecc_errors_count,corrected_ecc_errors_count,hbm3_coverage_gb,loop_verification_pass_pct,test_wall_clock_completion_time_s,error_message
0,ok,0,0,0.125,100,1.2,

## Metrics

- **#1: RAS uncorrectable counter delta** — stored as `uncorrected_ecc_errors_count`.
- **#2: RAS correctable counter delta** — stored as `corrected_ecc_errors_count`.
- **#3: Wall-clock time, s** — stored as `test_wall_clock_completion_time_s`.
- **#4: Allocated verification buffer, GB** — stored as `allocated_verification_buffer_gb`.
- **#5: Loop verification pass rate, %** — stored as `loop_verification_pass_pct`.

## Framework

Runs a PyTorch GPU (or CPU) int32 buffer check that writes 1 to even-index elements and verifies them, then reports a 0-100 pass rate in loop_verification_pass_pct. duration: wall-clock cap (values >=10000 treated as milliseconds). num_iterations: verify loops.

## Installation and Execution Summary

Run an in-process PyTorch check that writes 1 to even-index elements of a GPU or CPU int32 buffer for yaml duration and num_iterations, then emit hbm3_coverage_gb and a 0-100 loop_verification_pass_pct, to measure buffer integrity. RAS counter deltas come from amd-smi metric. RVS is not used

## Platform Portability

- **AMD (primary):** ```bash
Run an in-process PyTorch even-index int32 write/verify on cuda:0 when available, otherwise CPU. Does not run rvs or a HIP ecc_walk binary
```
- **NVIDIA:** Primary target is AMD ROCm. NVIDIA notes in this section are reference only and are not the execution path.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

CSV aggregate of the verify run. A single row is duplicated to two samples

sample_index,status,uncorrected_ecc_errors_count,corrected_ecc_errors_count,hbm3_coverage_gb,loop_verification_pass_pct,test_wall_clock_completion_time_s,error_message
0,ok,0,0,0.125,100,1.2,

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Runs a PyTorch GPU (or CPU) int32 buffer check that writes 1 to even-index elements and verifies them, then reports a 0-100 pass rate in loop_verification_pass_pct. duration: wall-clock cap (values >=10000 treated as milliseconds). num_iterations: verify loops.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Runs a PyTorch GPU (or CPU) int32 buffer check that writes 1 to even-index elements and verifies them, then reports a 0-100 pass rate in loop_verification_pass_pct. duration: wall-clock cap (values >=10000 treated as milliseconds). num_iterations: verify loops.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
