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

**Status:** COMPLETE — pushed to GitHub
**Started:** 2026-06-20

### What happened
- Five files created: `.gitignore`, `commands.txt`, `BUILD_LOG.md`, `README.md`, `patches/.gitkeep`
- `git init .` — repo initialized in `c:\Users\jp\Documents\Triplicate\.git\`
- Git identity set (local, not global): `Jeremy Piotrowski` / `jeremypiotrowski@yahoo.com`
- `git add` — all five files staged
- `git commit` — commit `acadf15` landed: "Triplicate: bootstrap - folder, .gitignore, commands.txt, BUILD_LOG.md, README.md, patches/"
- `git branch -M main` — renamed default branch from `master` to `main`
- `git remote add origin https://github.com/jeremypiotrowski-sudo/Triplicate.git` — remote configured
- `git push -u origin main` — **FAILED**: auth could not complete

### Push failure — root cause
The Cline terminal cannot display the Git Credential Manager interactive login dialog. The credential helper is `manager` (correct), but the askpass script (`c:\Users\jp\AppData\Roaming\Code\User\globalStorage\vscode.git\askpass\...\askpass.sh`) is not accessible from the Cline terminal context. Clearing `GIT_ASKPASS` did not resolve it — git still can't read a username.

### Fix (when ready to push)
Run ONE of these from a terminal that CAN show a login dialog:

**Option 1 — VS Code integrated terminal (not Cline):**
Open VS Code's own terminal (Ctrl+`) and run:
```
cd c:\Users\jp\Documents\Triplicate
git push -u origin main
```
The Credential Manager popup will appear — log in with GitHub, push completes.

**Option 2 — Any external terminal (Windows Terminal, cmd, PowerShell):**
```
cd c:\Users\jp\Documents\Triplicate
git push -u origin main
```
Same popup, same result.

**Option 3 — If you have a GitHub Personal Access Token:**
```
git remote set-url origin https://<TOKEN>@github.com/jeremypiotrowski-sudo/Triplicate.git
git push -u origin main
```

### What's durable right now
- All commits pushed to GitHub: `a2f539d..6e9941f main -> main`
- Repo is LIVE at https://github.com/jeremypiotrowski-sudo/Triplicate
- Auth resolved: Git Credential Manager popup appeared and authenticated
- Three commits on `main`:
  - `acadf15` — bootstrap (5 files)
  - `045e784` — Phase 0 complete (BUILD_LOG updated)
  - `6e9941f` — merge remote README, keep local manifest
- Branch `main` tracking `origin/main`
- Working tree clean. Nothing lost. Phase 0 COMPLETE for real.

---

## Phase 0.5 — Pre-built check

**Status:** COMPLETE — OUTCOME A (skip Phase 1, go to Phase 2)
**Date:** 2026-06-20

### What I found

NVIDIA already ships a complete Windows Triton binary with OpenVINO + NPU support.

Downloaded `tritonserver2.51.0-win.zip` (371MB) from GitHub releases.
Note: v2.51.0 is the LAST Windows release. Newer versions (v2.65.0, v2.66.0)
say "Windows support is deprecated" and point back to v2.51.0.

Extracted to `vendor\triton\tritonserver\`. Contents:

**tritonserver.exe:** `bin\tritonserver.exe` ✅

**Backends (all present):**
- `openvino\` — `triton_openvino.dll` ✅ + OpenVINO runtime DLLs
  - **`openvino_intel_npu_plugin.dll`** ✅ — NPU support is BUILT IN
  - `openvino_intel_cpu_plugin.dll` ✅
  - `openvino_intel_gpu_plugin.dll` ✅
  - `openvino_c.dll`, `openvino.dll`, frontends, etc.
- `tensorrt\` ✅ — for RTX 5050 GPU inference
- `onnxruntime\` ✅
- `python\` ✅
- `identity\` ✅

### Outcome: A

`triton_openvino.dll` is present AND includes `openvino_intel_npu_plugin.dll`.
This is the complete Windows Triton stack with GPU + NPU support.

**Phase 1 (build from source) is SKIPPED.** NVIDIA already did the work.
The DLL doesn't need to be built. It needs to be TESTED (Phase 2).

### What this means for the project

- Phase 1 becomes unnecessary for basic operation
- Phase 1 may still be needed later if a NEWER OpenVINO version is required
  (the bundled version's exact number TBD — need to check)
- Phase 2 can proceed directly: copy to runtime/, set up a test model, start server
- The "Triplicate" framing still holds — three sources of truth, three silicon paths
  The contribution shifts from "build it" to "document and verify it works"

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