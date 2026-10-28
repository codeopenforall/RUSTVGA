# RUST-VGA: An LLM-Generated, Test-Validated Benchmark for Rust Vulnerability Detection

Code and data for **"LLM-Based Data Generation and Augmentation for Rust Vulnerability Detection"** (IEEE COMPSAC 2026).

RUST-VGA is a large-scale, executable benchmark for binary vulnerability detection in Rust, built to train and evaluate ML- and LLM-based detectors where real-world labeled Rust data is scarce.

- **8,480 executable Rust programs** as paired vulnerable/fixed variants
- **53 CWE categories**, seeded from 122 real-world Rust CVEs
- Every pair ships with a **test that fails on the vulnerable version and passes on the fixed one**
- Generated with o3-mini and Qwen2.5-Coder; validated by compile + test, with optional AddressSanitizer checks

## Repository layout

| Path | Contents |
| --- | --- |
| `Dataset/` | The benchmark, organized by generating model → stage → CWE → pair |
| `Data_Generation/` | Prompts and scripts for LLM generation of seed vulnerable/fixed pairs |
| `Data_Augmentation/` | Augmentation strategies A1–A3, oracle runners, and cleaning scripts for fine-tuning and evaluation |
| `Dataset Curation/` | Curation of the 122 source CVE samples |
| `Asan_box/` | AddressSanitizer harness |
| `Rudra/` | Scripts for comparison against the Rudra static analyzer |
| `LLaMA-Factory/` | Fine-tuning configuration |

Each sample directory contains:

| File | Purpose |
| --- | --- |
| `vulnerable.rs` / `fixed.rs` | The paired program variants |
| `demo_test.rs` | Oracle test: fails on `vulnerable.rs`, passes on `fixed.rs` |
| `vulnerable_diff.rs` / `fixed_diff.rs` | Diff-based context used by augmentation |
| `vuldesc.txt` / `fixeddesc.txt` | Natural-language description of the vulnerability and the fix |
| `lineno.js` | Line numbers of the vulnerable region |

## How the dataset is built

**1. Data generation.** An LLM produces a vulnerable/fixed pair plus a test from a CWE description and a seed CVE. A pair is kept only if both versions compile and the test fails on the vulnerable version and passes on the fixed one.

**2. Data augmentation.** Each validated pair is expanded with three strategies, each re-validated by the same oracle:

- **A1 — test-driven bug injection and repair:** inject a new bug, then repair it
- **A2 — vulnerability-preserving paraphrase:** restructure the code while keeping the bug
- **A3 — contextual completion:** complete new code from diff-based context

## Quick start

Requirements: Rust toolchain (`rustc` 1.89 or newer) and Python 3.

```bash
git clone https://github.com/codeopenforall/RUSTVGA
cd RUSTVGA/Dataset/o3_mini/data_generation/CWE-190/pair1
cargo test          # should fail on vulnerable.rs, pass on fixed.rs
```

## Source CVE distribution

The 122 seed samples cover 53 CWEs; the largest groups are CWE-416 (9), CWE-125 (7), CWE-400 (7), CWE-787 (6), CWE-079 (5), and CWE-119 (5). The full per-CWE table is in [`CWE_DISTRIBUTION.md`](CWE_DISTRIBUTION.md).

## Responsible use

This dataset contains intentionally vulnerable code for research. Do not deploy any sample in production.

## Citation

```bibtex
@inproceedings{khan2026llm,
  title     = {LLM-Based Data Generation and Augmentation for Rust Vulnerability Detection},
  author    = {Khan, Irfan Ali and Luo, Yu and Xu, Weifeng and Xu, Dianxiang},
  booktitle = {2026 IEEE 50th Annual Computers, Software, and Applications Conference (COMPSAC)},
  pages     = {711--720},
  year      = {2026},
  organization = {IEEE}
}
```
