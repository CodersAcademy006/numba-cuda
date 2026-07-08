# Fork CI Mirror: Coverage and Gaps

This document describes what the fork's CPU-only CI mirror includes and intentionally excludes.

## Mirrored Checks

The fork CI mirror (`fork-ci-mirror-fast.yml`) includes the following CPU-feasible checks:

### 1. Pre-commit Checks
- Standard pre-commit hooks (check-added-large-files, check-ast, check-merge-conflict, check-toml, check-yaml, debug-statements, end-of-file-fixer, trailing-whitespace, mixed-line-ending)
- Ruff linting and formatting (ruff, ruff-format)
- SPDX header validation (check-spdx)
- Sphinx documentation linting (sphinx-lint)

These checks are language-agnostic, platform-independent, and provide immediate feedback on code style, formatting, and documentation quality.

### 2. Documentation Build
- Full Sphinx documentation build using pixi environments
- Validates that documentation builds successfully without errors or warnings (in strict mode)
- Produces HTML artifacts for preview

## Acknowledged Gaps (Not Mirrored)

The following checks require CUDA runtime or specialized hardware and are **intentionally not mirrored** on the fork:

### 1. Build Workflows
- Build wheels for linux-64, linux-aarch64, win-64 (require CUDA SDK, platform-specific build tools)
- Conda package builds (require CUDA toolchain)

### 2. Functional Tests
- Test wheel (linux, windows)
- Conda tests (test-conda)
- Simulator tests (test-simulator)
- Third-party integration tests (cuDF, nvmath, awkward)
- Coverage reports

These tests require:
- CUDA 12.x or 13.x runtime
- GPU compute capability or CUDA simulator
- Build artifacts (wheels, conda packages) from upstream
- Hardware-specific or simulator-based execution

### 3. Why This Gap is Permanent

The fork is configured on `ubuntu-latest` runners, which provide only standard GitHub-hosted Linux infrastructure (no CUDA, no GPU). Full functional testing requires either:
- Self-hosted NVIDIA GPU runners (not available for this fork)
- CUDA simulator support (requires additional setup not suitable for general-purpose CI)

The pre-commit and docs-build checks provide fast, actionable feedback on code quality without requiring specialized hardware. For full validation including CUDA functional tests, changes should be tested on an NVIDIA-provided runner or self-hosted GPU infrastructure before upstream submission.

## Triggering the Mirror CI

The mirror CI can be triggered in three ways:

1. **Automatically on PR**: When a PR is opened, synchronized, or reopened
2. **Manual dispatch**: Using `gh workflow run fork-ci-mirror-fast.yml`
3. **On-demand via comment**: Comment `/run-ci-mirror` on any existing PR (requires PR edit permissions)

## Success Criteria

A passing fork mirror CI run means:
- All code follows SPDX header conventions
- All Python code passes ruff formatting and linting checks
- All documentation compiles without errors
- No trailing whitespace or mixed line endings
- No obvious syntax or merge conflicts

A failing fork mirror CI run indicates code quality issues that should be addressed before upstream submission.
