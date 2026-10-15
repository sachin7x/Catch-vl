# Catch-vl

## Current state

The default branch contains this project status README. The CATCH 2.0 hardening patch bundle has **not** been committed to the repository yet.

The verified patch bundle is available as a downloadable artifact in the associated ChatGPT session. SHA-256:

57723af74e70e02c166530e3318289f1ce31e4682a9c433ba99005344e8e80ad

The bundle contains all seven patch files, updated regression tests, the security audit, limitations, offline-suite evidence, the V-Agent verifier-contract proposal, and the gated GPU run manifest. It is not the full CATCH source archive.

## Verification scope

The offline hardening suite completed in one invocation with exit code 0: 137 tests collected, 100% progress. Environment: Linux x86-64, Python 3.13.5, with pytest plugin auto-loading disabled.

This does not reproduce GPU-based RL training or the full upstream CATCH test suite. Process-group termination and prlimit are not a complete sandbox. Run generated code only in a disposable container or VM without host secrets or credentials.

The bundle still needs to be uploaded to this repository before this README can be treated as a published patch release.
