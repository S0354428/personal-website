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

## Fits archive page — "LOGBOOK" (added Oct 21, 2026, Luis's idea; name chosen by Luis Oct 21)

A playful corner of the site, deliberately separate from the MD/PhD showcase: an archive of Luis's daily western-aesthetic outfits. This follows his adopted "archive, not channel" rule — document, don't produce. It is NOT a personal-brand play. The name fuses the fashion term *lookbook* with a terminal *log file* — fitting the site's phosphor-green CRT aesthetic (#33ff33 on #020a02, VT323).

- **Route**: `/fits/` — a single static page; link it from the site footer/nav as a minor item, not a headline.
- **Title**: "LOGBOOK".
- **Content model**: entries rendered as terminal log output. Each entry: a date header (e.g., `[2026-10-24] fit_041`), the pieces as key = value lines (`boots = …`, `denim = …`, `shirt = …`, `hat = …`), and one photo. No essays, no audience-facing captions, no comments, no like counts, no posting schedule.
- **Build**: plain static HTML/CSS in keeping with the existing single-page site; no framework, no JS required beyond optionally lazy-loading images. Photos live in `/fits/img/` with date-based filenames (e.g., `2026-10-24.jpg`), compressed for web.
- **Upload ritual (the whole point)**: wear something good → phone photo → drop the file in, add one grid entry → done in under 2 minutes. If a week has no entry, nothing is owed.
- **Guardrail**: the page catalogs what Luis actually wore. The day outfits get chosen *for* the page, it has become content production — out of scope by design.
- **Sequencing**: rides along in the same build pass as the interactive upgrade (work starts after Fri Oct 23); lowest priority item — cut first if the demos run long.
- **Spec owner**: Quetzal (planning); implementation by Claude per Luis's standing workflow split.

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
- Start date: after Fri Oct 23, 2026 (Luis, Oct 21 — exam gauntlet first).

## Blog — "FIELD NOTES" (added Oct 21, 2026, Luis's idea; name/format approved by Luis Oct 21)

A research & build log on lasjr.com — NOT a diary. Luis's own line: a personal diary doesn't belong on the public site. Like LOGBOOK, it follows his adopted "archive, not channel" rule — document, don't produce.

- **Route**: `/notes/` — the index lists entries as a terminal-style listing; entries are individual static pages or one scrolling log page (implementer's choice, keep it static either way).
- **Title**: "FIELD NOTES". Aesthetic: same phosphor-green CRT theme as the rest of the site (#33ff33 on #020a02, VT323); each entry rendered like `cat`-ing a note file.
- **Content types** (in priority order):
  1. **Research notes** from the Gheidi lab at a public-appropriate level: methods, ideas, conference recaps (ASBMB, SEMSS).
  2. **Build logs**: the hackathon decoder, the AP Calc tools, the site demos — what was built, what broke, what's next.
  3. **Study explainers**: one concept per entry, written as teaching notes.
- **Governing rule**: the blog is a byproduct of work, never the work. Entries are written in ~15 minutes from notes that already exist — a lab notebook page, a commit, a result. No note = no entry. No schedule, no cadence promises; gaps are normal for a log.
- **Starter backlog** (already-real material, no new work needed to launch): the MURS Summer 2026 HMM result; Dr. Nepal sharing the bifurcation explorer with colleagues; the Meta hackathon build (Oct 26–Nov 4, 2026); SEMSS 2026 (Nov 14–15, 2026).
- **Boundary**: lab content limited to what Dr. Gheidi is comfortable having public — methods and milestones yes, unpublished data no (same caveat as the HMM demo).
- **Sequencing**: rides along in the same build pass as the interactive upgrade (work starts after Fri Oct 23, 2026); priority below the demos, alongside LOGBOOK.
- **Spec owner**: Quetzal (planning); implementation by Claude per Luis's standing workflow split.
