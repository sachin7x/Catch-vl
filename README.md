# CATCH 2.0 verified hardening patch set

This repository publishes the offline evaluator-hardening patch bundle for CATCH 2.0.

The bundle contains the seven unified patches, the relevant regression tests, and security limitations. It includes fixes for process-group cleanup on timeout, keeping the audit driver outside the model-writable repository, and redacting nested hidden-test metadata.

**Verification scope:** the offline hardening suite is run in a Linux x86-64 container with Python 3.13.5. This is not a reproduction of GPU-based RL training or the upstream CATCH test suite. Timeout/process-group handling is not a complete sandbox; use a disposable container or VM without host secrets for generated code.

See the published bundle and its README for exact commands, patch application instructions, and limitations.
