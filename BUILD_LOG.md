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

**Status:** COMPLETE (local) — push to GitHub pending
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
- Commit `acadf15` is on disk, on branch `main`, working tree clean
- The remote is configured — just needs the push to complete
- Nothing is lost. The push can happen from any terminal at any time.

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