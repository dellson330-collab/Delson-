# Base44 sandbox notes

## What this repo is
- `Meus Atalhos.html` — the entire web app: one self-contained static page
  (inline CSS + vanilla JS, data in `localStorage` under key `atalhos.v1`).
  No build step, no backend, no database, no dependencies.
- `atalhos-apk.zip` — Android WebView wrapper around that page (Gradle project
  plus `gradle.properties` at the root). It is a downloadable artifact here; it
  is not built or previewed in the sandbox.
- No environment variables or external credentials are required.

## Running it
`docker compose -f docker-compose.base44.yml up -d` → <http://localhost:3000/>

- `nginx:alpine` serves the repository read-only from a bind mount, so edits to
  `Meus Atalhos.html` are visible on the next page load. There is no dev server
  and no HMR: **reload the preview after editing**.
- `/` is mapped to `Meus Atalhos.html` via the `index` directive in
  `.base44/nginx.conf` (the page is not named `index.html` and its filename
  contains a space).
- nginx runs its workers as `user root` on purpose: the bind-mounted repo
  directory is mode 700 owned by root, so an unprivileged worker gets EACCES.
- Requests for dot-paths (`/.git/...`, `/.base44/...`) are denied.

## Verifying
- `curl -s -o /dev/null -w '%{http_code}' http://localhost:3000/` → `200`.
- `docker compose -f docker-compose.base44.yml ps` → `web` healthy.
- Page behaviour to check by hand: the shortcuts grid renders 7 example tiles,
  "Editar" reveals the remove buttons, search filters by name/host, the
  "Adicionar atalho" form validates and appends, with undo in the toast.
