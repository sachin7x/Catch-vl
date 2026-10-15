# Catch-vl

## Current state

This repository currently contains the project status README only. The CATCH 2.0 hardening patch bundle has **not** been committed to this repository yet.

The verified patch bundle is available as a downloadable artifact in the associated ChatGPT session. SHA-256:

aaf22d3eb469a823de60810ffd55a882e4993ccb54edba99c867cff32cfa1975

The bundle contains all seven patch files, the updated regression tests, the security audit, limitations, and captured offline-suite evidence. It is not the full CATCH source archive.

## Verification scope

The final offline hardening suite completed in one invocation with exit code 0: 137 tests collected, 100% progress. Environment: Linux x86-64, Python 3.13.5, with pytest plugin auto-loading disabled.

This does not reproduce GPU-based RL training or the full upstream CATCH test suite. Process-group termination and prlimit are not a complete sandbox. Run generated code only in a disposable container or VM without host secrets or credentials.

The bundle still needs to be uploaded to this repository before this README can be treated as a published patch release.
