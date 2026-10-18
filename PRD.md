# PRD.md:  "The Why"; Product requirements, benchmark metadata table, high-level requirements, etc.

Product Requirements Document

"The Why"; Product requirements, benchmark metadata table, high-level requirements, etc. Defines the benchmark goal, validation objective, test name, benchmark number, category, and high-level success criteria.

## Benchmark Matrix Document Metadata (via benchmark_specification.json)

This PRD.md section is populated from benchmark_specification.json, which is the structured source of benchmark-specific product requirements.

## Workload Number
304

## Workload Name
Silent Data Corruption (SDC)

## Execution Summary (Run and Measure)
Run an in-process PyTorch check that writes 1 to even-index elements of a GPU or CPU int32 buffer for yaml duration and num_iterations, then emit hbm3_coverage_gb and a 0-100 loop_verification_pass_pct, to measure buffer integrity. RAS counter deltas come from amd-smi metric. RVS is not used

## Main Goal
Analyze GPU buffer even-index write/verify integrity via a PyTorch SDC stub

## Validation Objective
Validates that the even-index int32 write/verify loop completes and reports the allocated GPU buffer size plus a 0-100 pass rate. RAS counter deltas come from amd-smi metric. RVS is not used

## Workload Category
System Validation & Reliability

## Validation Requirement

The benchmark must include an automated SQLite-integrated validation layer that verifies persisted results from `results/benchmark.db`. Validation must confirm:

1. The benchmark run completed successfully with no tool errors.
2. Required samples and aggregate metrics were persisted for every swept shape.
3. Metrics are finite and physically sensible (positive, within plausible bounds).
4. Measured values satisfy configured thresholds when the workload defines pass/fail gates.
5. The benchmark fails validation when required data is missing, invalid, or outside bounds.

## Non-Functional Requirements

| Requirement | Target |
|---|---|
| Automation | Runs to completion without manual intervention after `bash run_benchmark.sh` |
| Idempotency | Re-running `run_benchmark.sh` appends a new run; never corrupts existing rows |
| Persistence | All metrics survive script exit; `results/benchmark.db` is the durable record |
