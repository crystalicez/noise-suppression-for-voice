# RNNoise VST2 Refresh Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Update the vendored RNNoise snapshot to Xiph commit `70f1d256acd4b34a572f999a05c87bf00b67730d` while preserving the existing VST2 mono/stereo plugin path.

**Architecture:** Keep the existing JUCE/FST VST2 wrapper and project-specific RNNoise CMake integration intact. Replace only files changed upstream since the currently vendored RNNoise commit, update vendoring metadata, then validate using the repository's existing VST2-enabled GitHub Actions workflow.

**Tech Stack:** C, C++, CMake, JUCE, FST VST2 headers, Xiph RNNoise, GitHub Actions

## Global Constraints

- Preserve VST2 support and the existing mono/stereo plugin outputs.
- Do not upgrade JUCE or FST in this change.
- Do not redesign UI or alter plugin parameters.
- Preserve `external/rnnoise/CMakeLists.txt` unless an upstream runtime change makes a targeted integration patch necessary.
- Target RNNoise upstream commit: `70f1d256acd4b34a572f999a05c87bf00b67730d`.

---

### Task 1: Refresh vendored RNNoise files

**Files:**
- Modify upstream-changed files under `external/rnnoise/`
- Delete: `external/rnnoise/TRAINING-README`
- Create: `external/rnnoise/.gitlab-ci.yml`
- Modify: `external/rnnoise/.gitrepo`

**Interfaces:**
- Consumes: existing `RnNoise` CMake target and public `rnnoise.h` API
- Produces: vendored RNNoise tree corresponding to upstream commit `70f1d256...`

- [ ] **Step 1:** Replace every file reported by the upstream comparison `372f7b4...70f1d256` with the content from `xiph/rnnoise@70f1d256`.
- [ ] **Step 2:** Remove `TRAINING-README`, which upstream deleted.
- [ ] **Step 3:** Add upstream `.gitlab-ci.yml`.
- [ ] **Step 4:** Change `.gitrepo` `commit` to `70f1d256acd4b34a572f999a05c87bf00b67730d` while retaining this repository's subrepo metadata structure.
- [ ] **Step 5:** Compare the branch against master and confirm there are no unrelated JUCE/FST/plugin changes.

### Task 2: Check integration compatibility

**Files:**
- Inspect: `external/rnnoise/CMakeLists.txt`
- Inspect: `src/common/`
- Inspect: `src/juce_plugin/`
- Modify only if required by compiler/API errors.

**Interfaces:**
- Consumes: updated RNNoise public API and project-specific static-library target
- Produces: unchanged plugin-facing behavior with a buildable updated RNNoise engine

- [ ] **Step 1:** Verify RNNoise public symbols used by the project remain available.
- [ ] **Step 2:** Verify changed runtime sources are already part of the existing `RN_NOISE_SRC` list or require no new runtime source files.
- [ ] **Step 3:** Avoid compatibility edits unless CI identifies a concrete compile/link failure.

### Task 3: Verify VST2 builds and tests

**Files:**
- Use: `.github/workflows/githubci.yml`

**Interfaces:**
- Consumes: updated branch
- Produces: CI evidence for Windows VST2 and cross-platform compilation/tests

- [ ] **Step 1:** Open a draft pull request into `master` so the existing `pull_request` workflow runs.
- [ ] **Step 2:** Confirm Windows config passes with `BUILD_VST_PLUGIN=1`.
- [ ] **Step 3:** Confirm common tests pass.
- [ ] **Step 4:** Inspect any CI failure and make only the minimal targeted compatibility change required.
- [ ] **Step 5:** Re-run verification until required checks pass or document an external CI infrastructure blocker.
