# QA Assets for rust-bitcoinkernel

This repository contains test assets for the [rust-bitcoinkernel](https://github.com/thecharlatan/rust-bitcoinkernel) project.

## Overview

This repository provides **fuzz corpus data** used for regression testing of rust-bitcoinkernel's fuzz targets. The corpus contains edge cases and interesting inputs discovered through fuzzing that help catch bugs and regressions.

## Usage

### Testing Locally

To verify corpus files work with your local rust-bitcoinkernel:
```bash
# Clone this repository
git clone https://github.com/alexanderwiederin/qa-assets.git

cd <path-to-rust-bitcoinkernel>

# Test all corpus files for a target
cargo fuzz run block_roundtrip <path-to-qa-assets>/fuzz_corpora/block_roundtrip -- -runs=0

# Test a specific corpus file
cargo fuzz run block_roundtrip <path-to-qa-assets>/fuzz_corpora/block_roundtrip/<hash>
```

### CI Integration

These corpus files are automatically tested in rust-bitcoinkernel's CI on every PR to catch regressions.

## Adding New Corpus Inputs
### Option 1: From Local Fuzzing

1. **Run fuzzer locally to discover new inputs:**
```bash
   cd <path-to-rust-bitcoinkernel>

   # Seed with existing corpus and fuzz for new inputs
   cargo fuzz run <target> <path-to-qa-assets>/fuzz_corpora/<target> -- -max_total_time=3600
```

2. **Minimize new inputs to reduce size:**
```bash
   cargo fuzz cmin <target> <path-to-qa-assets>/fuzz_corpora/<target>
```

4. **Checkout a new branch and push commit:**
```bash
   cd <path-to-qa-assets>
   git checkout -b update-corpus
   git add fuzz_corpora/<target>/
   git commit -m "chore(fuzz): expand corpus with new coverage"
   git push origin update-corpus
```

### Option 2: From Crash Reports
If a fuzzer finds a crash (either locally or in CI):
1. **Download the crash input** from CI artifacts or local `fuzz/artifacts/`
2. **Fix the bug** in rust-bitcoinkernel
3. **Add crash input to corpus** as a regression test:

- **If found locally:** Copy from fuzzer artifacts

```bash
   cp <path-to-rust-bitcoinkernel>/fuzz/artifacts/<target>/crash-<id> <path-to-qa-assets>/fuzz_corpora/<target>/crash-<id>
```

- **If downloaded from CI:** Copy from your downloads folder
```bash
   cp ~/Downloads/crash-<id> <path-to-qa-assets>/fuzz_corpora/<target>/crash-<id>
```
4. **Commit and push the regression test:**
```bash
   cd <path-to-qa-assets>
   git checkout -b add-regression-test
   git add fuzz_corpora/<target>/crash-<id>
   git commit -m "chore(fuzz): add regression test for <target> crash

   Adds crash input that triggered [brief description of the bug].
   Fixed in rust-bitcoinkernel commit <commit-hash>."
   git push origin add-regression-test
```
## Attribution

Some test assets in this repository are derived from or include data from [bitcoin-core/qa-assets](https://github.com/bitcoin-core/qa-assets), which is part of the Bitcoin Core project and follows the MIT license.

## License

This repository is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

Test assets are provided for testing purposes only.
