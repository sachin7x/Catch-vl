# CATCH 2.0 GPU run manifest

**Status:** prepared, not executed. No cloud GPU was provisioned and no training was started.

## Canonical recipe

Use the CATCH repository's 9k/2k example only with a local SFT checkpoint trained from the matching 9,000 non-hacking + 2,000 hacking initialization. The documented launcher configures 8 GPUs and a 32,768-token prompt-plus-response budget. Do not substitute a checkpoint or silently change the recipe.

For mitigation comparisons, use the exact launcher and GPU count documented by the repository: baseline and CoT monitor examples declare 4 GPUs; GR and chi-square examples declare 8 GPUs. These are separate experiment recipes, not a single run configuration.

## Runtime pins

- Linux x86-64 with NVIDIA CUDA-capable GPUs
- Python 3.11
- PyTorch 2.8.0
- Transformers 4.57.6
- verl 0.6.1
- vLLM 0.11.0
- flash-attn 2.8.3
- ninja, pytest, packaging, and Linux prlimit from util-linux

Use the repository's pinned pyproject and the matching launcher. Verify CUDA driver/runtime compatibility and available GPU memory for the chosen model before allocating paid compute; the recipe's GPU count is not a guarantee that an arbitrary GPU SKU has sufficient memory.

## Stage 1 preflight gate

Run in a disposable Linux container or VM with no host credentials or sensitive mounts:

1. uname -m must report x86_64.
2. python --version must report Python 3.11.
3. nvidia-smi must work and report the exact required number of visible GPUs for the selected launcher.
4. PyTorch must be CUDA-enabled, torch.cuda.is_available() must be true, and the visible GPU count must match the recipe.
5. command -v prlimit must succeed.
6. Install the pinned stack; verify installed versions rather than relying on successful pip exit alone.
7. Run the offline hardening suite and patch-application checks before training.
8. Verify dataset revisions, checkpoint path/hash, launcher hash, reward configuration, environment lock, and all run IDs in a manifest.

If any prerequisite fails, stop. Do not move to SFT/RL setup or substitute a checkpoint, dataset, Python version, or GPU count.

## Evidence to preserve

Record host image/container digest, driver/CUDA/Python/package versions, GPU model and count, dataset revisions, SFT checkpoint SHA-256, launcher SHA-256, reward/verifier/audit versions, seeds, token budget, resource limits, all checkpoints, rollout IDs, raw proxy metrics, independent-audit metrics, and test logs. Keep hidden labels and raw submitted code out of public trackers by default.

## Current blocker

The current runtime has Python 3.13.5, CPU-only PyTorch, and zero detected NVIDIA GPUs. No GPU provisioning connector is available in this session. A paid runner has not been created; provider and spend limit must be selected before external compute is allocated.
