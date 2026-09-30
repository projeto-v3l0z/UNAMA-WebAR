# UNAMA WebAR — Agent Notes

Static site, no build/test/lint/CI. No `package.json`. Serve the files directly.

## Structure

- `index.html` — landing page. Asset paths are root-relative (`assets/…`, `css/style.css`). Links to the AR experience at `pages/page_AR.html` (2 occurrences).
- `pages/page_AR.html` — the only AR logic in the repo (inline `<script>`, not `js/main.js`, which is empty). All its asset paths must be `../assets/…` since it lives in `pages/`.
- `css/style.css` — landing styles only. `pages/page_AR.html` styles are inline.
- `assets/marker/pattern-marker.{fset,fset3,iset}` — AR.js NFT descriptors. Referenced **without extension**: `url="../assets/marker/pattern-marker"`.
- `assets/audio/` — narration/audio files. Naming convention per `assets/Assets.md`: `personagem-unama.glb`, `sala-101.mp3`, `fachada-unama.jpg` (note: doc says `audios/`/`models/` but actual dirs are `audio/` + CDN — follow the real tree).

## Stack (do not migrate)

A-Frame 1.5.0 + AR.js NFT (`aframe-ar-nft.js` 3.4.5) + aframe-extras 7.2.0 (`animation-mixer` only). Jira tasks and `assets/Assets.md` mention MindAR/`.mind` targets, but the implemented decision is **keep AR.js** — do not rewrite to MindAR.

## AR page contract (`pages/page_AR.html`)

- `<a-assets>` must stay a direct child of `<a-scene>`; the `<a-entity id="character">` must stay inside `<a-nft id="target">`.
- `markerFound` → show overlay + `character visible=true` + play narration from `currentTime = 0`. `markerLost` → hide everything + `narration.pause()`. Keep the Instagram overlay behavior when adding features.
- Model swap: change only the `MODEL_URL` const (currently a KhronosGroup CDN `.glb`); the script applies it to `<a-asset-item id="personagem">`. Local replacement goes in `assets/models/`.
- NFT `scale`/`position` (currently `50 50 50` / `0 0 0`) are uncalibrated guesses — expect 1–2 iterations on a real device per marker.

## Gotchas

- Camera only works in a **secure context**: test via `localhost` or `https`, never `file://`.
- Mobile browsers block audio autoplay: the page unlocks on first `touchend`/`click` (`narration.load()`) and shows `#audio-fallback` if `play()` rejects. Preserve both mechanisms.
- Shell here is Windows PowerShell 7 (`pwsh`): no `head`/`grep` — use `Select-Object -First` / `Select-String`. Python may not be installed.
- Git: feature branches are `feat/<name>-KAN-<n>` (e.g. `feat/personagem-3d-KAN-3`); `main` history uses explicit `--no-ff` merge commits. Jira project is `KAN`, cloudId `ff83f948-9656-48e4-8dc3-4dfa83b4a654`.
