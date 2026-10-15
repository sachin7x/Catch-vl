# Patchset manifest

The verified patch-and-evidence bundle is provided as a ChatGPT conversation artifact, not yet as a GitHub repository asset.

- Archive SHA-256: 57723af74e70e02c166530e3318289f1ce31e4682a9c433ba99005344e8e80ad
- Archive filename: catch2-verified-hardening.tar.gz
- Scope: seven unified patches, updated regression tests, security audit, limitations, offline-suite evidence, V-Agent verifier-contract proposal, and gated GPU run manifest.
- Test command: PYTEST_DISABLE_PLUGIN_AUTOLOAD=1 python3 -m pytest hardening/tests -q --tb=short
- Recorded test result: 137 tests collected, full suite reached 100%, exit code 0.

Patch files in the archive:
1. 0001-gr-config-fields.patch
2. 0002-verl-patch-hook.patch
3. 0003-pc-swe-metadata-redaction.patch
4. 0004-pc-swe-readonly-permission-visibility.patch
5. 0005-rule-monitor-classifications.patch
6. 0006-pc-swe-subprocess-audit-isolation.patch
7. 0007-pc-swe-visible-evidence-isolation.patch

Publication status: the security audit, manifest, V-Agent contract proposal, and GPU run manifest are committed on branch verified-hardening. The patch archive itself is not committed because the available GitHub connector does not expose a direct upload operation for a local archive. This manifest deliberately does not claim that the patch files are already present in this repository.
