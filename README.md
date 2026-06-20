# Triplicate

## What this is
A Windows-native Triton Inference Server with three inference paths
(GPU via TensorRT, NPU via OpenVINO, CPU fallback) behind one HTTP endpoint.

## Why it exists
The OpenVINO backend for Triton is officially "not supported on Windows."
It is C++ with CMake. It compiles. The wall is paperwork, not physics.
This repo proves the path exists and documents it so the next person
doesn't have to start from zero.

## What "not supported" means
It means: if it breaks, don't call NVIDIA. It does not mean: impossible.
It means: nobody at NVIDIA tested this. It does not mean: it won't work.

## What "Triplicate" means here
Three copies of the same truth, kept in sync:

1. `commands.txt` — every command, as run
2. `BUILD_LOG.md` — every error, every fix, every decision
3. `patches/` — every source code modification

If any of these drift from the others, something is wrong.
This is how Triplicate stays reproducible when machines fail,
sessions end, and context gets lost.

## Architecture
Three silicon paths, one Triton endpoint:

- **RTX 5050** — LLM inference via TensorRT backend (NVIDIA-provided)
- **Intel NPU** — embeddings / reranking via OpenVINO backend (built here)
- **CPU** — fallback for anything else

Model configs (`config.pbtxt`) decide where each model runs.
One HTTP endpoint at `localhost:8000` routes to the right silicon.

### Concrete models (Phase 3 target)
- **LLM (GPU, TensorRT):** Qwen3-8B or Llama 3.2 3B → `model.plan`
- **Embedder (NPU, OpenVINO):** BGE-M3 → `model.xml` + `model.bin`
- **Reranker (NPU, OpenVINO):** BGE Reranker v2 M3 → `model.xml` + `model.bin`

All pre-converted variants available on HuggingFace under the OpenVINO org.

## Status
[Phase 0 — Foundation] → [Phase 0.5 — Pre-built check] → [Phase 1 — DLL]
→ [Phase 2 — Verification] → [Phase 3 — Full stack] → [Phase 4 — Share]

See `BUILD_LOG.md` for the current phase and what's next.

## Repo layout
```
Triplicate/
├── .gitignore
├── commands.txt          # source of truth #1 — every command
├── BUILD_LOG.md          # source of truth #2 — narrative + preamble
├── README.md             # this manifest
├── patches/              # source of truth #3 — source modifications
├── vendor/               # downloaded deps (versioned in commands.txt)
├── src/                  # cloned upstream repos (rebuildable from commands.txt)
├── build/                # CMake build dir (gitignored)
└── runtime/              # extracted Triton installation (gitignored)
```

## The whole arc in six commits
```
Triplicate: bootstrap — folder, .gitignore, commands.txt, BUILD_LOG.md, README.md, patches/
Triplicate: Phase 0.5 — prebuilt DLL inspection
Triplicate: Phase 1 — triton_openvino.dll builds
Triplicate: Phase 2 — Triton loads OpenVINO backend, NPU responds to inference
Triplicate: Phase 3 — single endpoint serving GPU + NPU simultaneously
Triplicate: Phase 4 — BUILD_WINDOWS.md + GitHub Release + upstream PR
```

## Upstream
- Backend source: https://github.com/triton-inference-server/openvino_backend
- Triton core:   https://github.com/triton-inference-server/server
- This project plans to open a PR upstream:
  **"Triplicate: Windows native build support"**