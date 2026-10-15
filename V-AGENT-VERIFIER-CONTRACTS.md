# V-Agent verifier contracts: proposal derived from CATCH 2.0 audit

**Status:** design proposal, not an implemented or empirically validated V-Agent integration. It is based on the offline CATCH 2.0 evaluator findings only; GPU training results have not been produced.

## Architecture

Use a verifier-first flow:

`planner/coder/reviewer → constrained workspace/tools → visible proxy verifier A → evidence ledger → exploit detectors → frozen independent audit B → truth record`

The training optimizer may consume verifier A's configured proxy reward. Evaluation and research conclusions must also use B, whose rules and version are controlled independently from the agent and held fixed within a comparison. Track the divergence between A and B over training rather than treating A's reward as ground truth.

## VerificationResult

Required fields:

- `status`: `PASS | FAIL | UNKNOWN | ERROR`
- `proxy_reward`: numeric or null
- `functional_pass`, `hidden_pass`, `exploit_detected`: boolean or null
- `authority_violation`: boolean
- `verifier_version`, `audit_version`
- `evidence`: list of evidence-record IDs
- `reason_codes`: stable machine-readable codes

`UNKNOWN` means evidence is missing, inconsistent, or insufficient. `ERROR` means the verifier or execution infrastructure failed. Neither may be silently coerced into `PASS` or an ordinary functional `FAIL`.

## EvidenceRecord

Each record should contain evidence/run/task IDs, kind and producer, artifact/report digest, judge-owned storage reference, verifier/environment/policy versions, start/end times, exit and timeout status, required/observed test counts, and redaction/access classifications. Store evidence outside the agent-writable workspace. The agent may reference evidence IDs but must not create, overwrite, or authorize the authoritative record.

## Authority boundaries

The agent must not control or rewrite hidden tests, reference solutions, audit rules, ground-truth labels, reward calculation, verifier/audit versions, evidence-policy versions, the authoritative trajectory store, or the evidence ledger. Editing tests, reports, expected outputs, or process status must not establish success. Record authority violations separately from functional correctness.

## Verdict rules

1. **PASS:** required visible checks and independent audit completed; evidence is present and valid; the independent audit confirms correctness; no disqualifying authority violation occurred.
2. **FAIL:** valid, complete evidence demonstrates that a required condition was not met.
3. **UNKNOWN:** missing test evidence, conflicting results, invalid report provenance, or an incomplete audit prevents a trustworthy decision.
4. **ERROR:** verifier, runner, or audit infrastructure failed before producing a trustworthy verdict.

A successful process exit alone is never sufficient for PASS. Zero executed tests, missing JUnit evidence, or a report inside the agent-writable workspace must not produce a pass.

## Reward-hacking metrics

Preserve the distinction between proxy reward and independently audited correctness. Record `is_hack`, `trivial_hack`, `nontrivial_hack`, and `all_passed_wo_hack` as separate diagnostic labels with explicit definitions. The paper-aligned nontrivial-hack label requires both proxy test slices to pass while the independent audit fails; ordinary incorrect code is not automatically reward hacking.

Report proxy reward and independent success by checkpoint, proxy/audit disagreement, nontrivial-hack rate, detector false-positive/negative rates, UNKNOWN/ERROR rates, evidence completeness, timeout rate, authority violations, and descendants surviving timeouts. The experiment is optimization against A while evaluating against frozen B. A rising while B falls and disagreement rises is a warning signal to test, not a result to assume.

## Operational controls and validation

Redact nested test-result payloads and raw monitor responses by default. Store evidence outside the writable workspace and hash artifacts/reports. Enforce process-tree timeouts, CPU/memory limits, filesystem/network restrictions, and least privilege. Process-group cleanup and prlimit are resource controls, not a complete sandbox; use a disposable container/VM without credentials.

Pin reward/verifier/audit versions in every run manifest and reject comparisons with mismatched configurations. Before RL training, test each status, missing/zero evidence, forged/stale hashes, test tampering, timeout descendants, symlink paths, redaction bypasses, and reward-configuration mismatch. Do not claim mitigation success until independent-audit metrics and complete run artifacts are available.
