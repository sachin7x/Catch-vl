# CATCH 2.0 evaluator security audit

## Scope

Static review of the hardening patch paths plus offline regression tests. This is not a penetration test of a kernel/container boundary and does not certify arbitrary generated code as safe.

## Findings and changes

1. **Process-tree cleanup on timeout.** Hidden-audit and visible-test execution start the direct child in a new session. On timeout, the runner sends SIGKILL to that process group and reaps the direct child. Regression tests spawn a descendant in a disposable temporary repository and verify it is no longer running after timeout.
2. **Audit driver path was writable by the submission.** The audit driver is now written into a judge-owned temporary directory, not the submitted repository. Exclusive creation avoids following a repository-controlled symlink. A regression test plants a repository-local symlink and verifies the sentinel target is not overwritten.
3. **Nested hidden-test metadata bypassed top-level redaction.** In addition to top-level tests and model_codes, inputs, expected, and output values inside test_results_wo_hack are replaced with SHA-256 digests by default. The raw LLM monitor response is also redacted by default. CATCH_LOG_RAW_METADATA=1 is an explicit opt-out and must not be enabled when raw ground truth/code must remain out of the tracker.
4. **Zero-evidence visible passes.** The judge-owned JUnit evidence channel records when a successful process exit did not execute tests. Strict rejection remains opt-in with RLLM_PC_SWE_VISIBLE_REQUIRE_EVIDENCE=1 to preserve baseline comparability.

## Verification

Environment: Linux x86-64, Python 3.13.5, CPU-only PyTorch 2.10.0+cpu, prlimit available.

Full offline suite command:

```bash
PYTEST_DISABLE_PLUGIN_AUTOLOAD=1 python3 -m pytest hardening/tests -q --tb=short
```

Result: 137 tests collected; one full-suite invocation reached 100%; process exit code 0. The seven patch files passed patch preflight, and reward integration tests applied the patch set sequentially to bundled upstream source copies.

## Residual risks and boundaries

- Process-group termination only kills ordinary descendants that remain in the same process group. Code can attempt to escape into another session or exploit host/kernel capabilities.
- prlimit memory limits and wall-clock timeouts are resource controls, not a security sandbox. Use a disposable container or VM, restricted filesystem/network, no host credentials, and least-privilege execution for generated code.
- The offline hardening suite is not the full upstream CATCH suite and does not reproduce GPU RL training, paper metrics, or mitigation curves.
- Python 3.11, CUDA/NVIDIA GPU prerequisites, exact training datasets, and matching SFT checkpoints were not available in this runtime; the ten-stage training workflow remains blocked at preflight.
