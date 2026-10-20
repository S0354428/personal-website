# Interactive Portfolio Upgrade — Execution Plan

Status: Ready to execute · Created Oct 20, 2026
Owner: Luis Santana (S0354428) · Prepared by: Quetzal (Muse)

## Goal

Upgrade lasjr.com so each listed project has a live, interactive demo running in the browser, and polish the project repos so the portfolio reads as production-quality work.

## Current state (verified Oct 20, 2026)

- **personal-website**: GitHub Pages site (custom domain lasjr.com), single `index.html` + `CNAME`.
- **InteractiveBifurcation**: single self-contained `index.html` (~42 KB) — already a working browser demo.
- **S1_IVSA** (HMM): self-contained C (`main_hmm.c`) + `Makefile` + `data/` + Python figure scripts (`generate_figures.py`).
- **babyBLAS** and **Hybrid MPI+OpenMP matmul**: not yet on GitHub — must be uploaded before they can be demoed or linked.
- **S1_IVSA hygiene issue**: `.venv/` is committed; also a stray `report.*.json` at repo root.

## Demo plan (in execution order)

### 1. Bifurcation Explorer — effort: hours

- Copy the existing `index.html` into the site as `/projects/bifurcation/` (or embed via iframe from its own Pages deployment).
- Add a short writeup: what it models, the controls, link to repo.

### 2. HMM addiction-phenotype decoder (S1_IVSA) — effort: days

- Compile `main_hmm.c` to WebAssembly with Emscripten.
- Browser UI: choose state count *k*, run decoding, visualize the state timeline, transition matrix, and state occupancy.
- Data: synthetic dataset mimicking the real IVSA structure (see Caveats).
- Fallback if the WASM build fights back: precompute outputs for a few *k* values and ship an interactive visualizer over static JSON.

### 3. babyBLAS — effort: days

- WASM build of the C library; in-browser matrix-multiplication benchmark (serial) vs a JS baseline, with a live timing chart.
- Parallel results: show real benchmark charts from the course runs — the browser can't reproduce the pthreads build on GitHub Pages (see Caveats).

### 4. Hybrid MPI+OpenMP matmul — effort: days

- No real MPI in a browser: build an interactive simulation of `MPI_Scatterv`/`MPI_Gatherv` block distribution across a process grid, animated.
- Pair it with real benchmark data (speedup/efficiency charts) from cluster runs.

## Site structure

- `/projects/<slug>/` per project: writeup + live demo (embedded) + repo link + tech tags.
- Keep the site static (GitHub Pages). No framework migration unless a demo demands it.
- Each demo stays self-contained so a broken demo never breaks the homepage.

## Build pipeline

- GitHub Actions workflow in personal-website: on push, build the WASM demos (Emscripten container) from vendored sources or submodules, then deploy the Pages artifact.
- Roles:
  - **Quetzal (Muse)**: architecture, specs/prompts, code review, commits (each write needs Luis's approval).
  - **Copilot CLI**: Luis's local iteration + build testing.
  - **Claude**: optional second-opinion code generation — not a required pipeline stage.

## Repo hygiene (Phase 0)

- [ ] S1_IVSA: remove `.venv/` from tracking, extend `.gitignore`, remove the stray report JSON or move it to `results/`.
- [ ] Upload babyBLAS and MPI matmul repos with READMEs.
- [ ] Every project repo: README with summary, build/run instructions, a results figure.
- [ ] Pin the four project repos on the GitHub profile.

## Caveats / constraints

- **Lab data**: do NOT publish real IVSA data publicly without Dr. Gheidi's OK. The demo uses synthetic data by default.
- **WASM threads**: pthreads/OpenMP in WebAssembly require COOP/COEP headers; GitHub Pages cannot set custom headers, so parallel demos run serial in-browser and parallel claims are backed by static benchmark charts. (If this ever matters, Cloudflare Pages or Netlify allow headers.)

## Open questions for Luis

- Where do babyBLAS and the MPI project currently live?
- Gheidi sign-off on any data used in the HMM demo (synthetic by default).
- Start date: after Wed Oct 21 (exams).
