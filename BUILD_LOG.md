# BUILD LOG — Triplicate

## Why this exists
The OpenVINO backend for Triton is officially "not supported on Windows."
It is C++ with CMake. It compiles. The wall is paperwork, not physics.

## What "not supported" means
It means: if it breaks, don't call NVIDIA. It does not mean: impossible.
It means: nobody at NVIDIA tested this. It does not mean: it won't work.

## Rules
1. Every command goes in commands.txt as it's run, not after
2. Every error goes in BUILD_LOG.md with the fix
3. Every source modification goes in patches/ as a .patch file
4. Every phase ends with a git commit
5. If you stop, you lose nothing. The git log is your memory.

---

## Phase 0 — Triplicate bootstrap

**Status:** IN PROGRESS
**Started:** 2026-06-20

Bootstrap files created: `.gitignore`, `commands.txt`, `BUILD_LOG.md`,
`README.md`, `patches/.gitkeep`. First commit pending.

### What happened
- Initialized this BUILD_LOG with the Triplicate contract (above).
- `commands.txt` seeded with the full six-phase command skeleton.
- `.gitignore` configured: ignores `build/`, `runtime/`, vendor zips,
  intermediate MSVC artifacts.

### Next
- Create `README.md` (Triplicate manifest, stranger-facing framing).
- Create `patches/.gitkeep`.
- `git init` + first commit.

---

## Phase 0.5 — Pre-built check

**Status:** NOT STARTED

### Outcome (to be recorded here)
One of:
- **A)** `triton_openvino.dll` present + NPU works at OpenVINO 2024.5.0
       → skip Phase 1, go to Phase 2
- **B)** DLL present + NPU works + want 2025.4.0 for a specific model
       → Phase 1 runs as *upgrade* (old DLL is fallback)
- **C)** DLL present + NPU broken / too old
       → Phase 1 runs as *swap-in*
- **D)** No DLL in zip
       → Phase 1 runs as *full build*

### What I found
_(fill in after running the Phase 0.5 commands in commands.txt)_

---

## Phase 1 — Build (or swap) the OpenVINO backend DLL

**Status:** NOT STARTED (conditional on Phase 0.5 outcome)

### Patches applied
_(numbered list, updated as each patch is created in patches/)_

---

## Phase 2 — Verify the backend works inside Triton

**Status:** NOT STARTED

---

## Phase 3 — Full stack: GPU + NPU behind one endpoint

**Status:** NOT STARTED

### Concrete models (chosen in advance)
- **LLM (GPU, TensorRT):** Qwen3-8B or Llama 3.2 3B → `model.plan`
- **Embedder (NPU, OpenVINO):** BGE-M3 → `model.xml` + `model.bin`
- **Reranker (NPU, OpenVINO):** BGE Reranker v2 M3 → `model.xml` + `model.bin`

All pre-converted variants available on HuggingFace under the OpenVINO org.

---

## Phase 4 — Package for sharing

**Status:** NOT STARTED