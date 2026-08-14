# RNNoise VST2 Refresh Design

## Goal

Refresh the vendored Xiph RNNoise snapshot used by this project while preserving the existing VST2 plugin implementation and behavior.

## Scope

- Keep the existing JUCE and FST/VST2 wrapper unchanged unless compilation requires a targeted compatibility fix.
- Refresh `external/rnnoise` from vendored upstream commit `372f7b4b76cde4ca1ec4605353dd17898a99de38` to Xiph RNNoise `70f1d256acd4b34a572f999a05c87bf00b67730d`.
- Preserve the project-specific `external/rnnoise/CMakeLists.txt`, because it is the integration layer used by this repository and is not part of the upstream delta.
- Keep VST2 mono/stereo output enabled through the existing `BUILD_VST_PLUGIN` path.
- Do not upgrade JUCE, FST, or redesign the UI in this change.

## Update Strategy

Apply only the files changed upstream between the two RNNoise commits. This keeps the vendored tree close to Xiph while minimizing unrelated integration churn. Update `.gitrepo` metadata to record the new upstream snapshot.

## Compatibility

The existing plugin code continues to consume the public RNNoise API. Any upstream API changes will be handled with the smallest possible compatibility patch outside the vendored code. VST2 plugin identifiers, names, mono/stereo split, UI, and parameter behavior remain unchanged.

## Verification

The pull request must exercise the repository's existing GitHub Actions build. The Windows job configures `BUILD_VST_PLUGIN=1`, builds the plugin, runs common tests, and uploads build artifacts. Linux/macOS builds provide additional compile coverage. A passing Windows job is the primary acceptance criterion for this refresh.
