# Vendored Red-Web-Dashboard

This directory is a vendored, editable copy of
[AAA3A-AAA3A/Red-Web-Dashboard](https://github.com/AAA3A-AAA3A/Red-Web-Dashboard)
v1.8.2, used as the Flask frontend for the Requiem dashboard.

## Why it is vendored

`reddash` was originally pip-installed into `redvenv/Lib/site-packages`, which
made three things impossible:

- Editing templates. Phases 2, 3 and 5 all rewrite Jinja templates
  (`layouts/base.html`, `pages/*.html`, new public routes). A site-packages
  copy cannot be edited meaningfully and is destroyed on every reinstall.
- Emitting build output into the repo. Vite writes to
  `reddash/app/static/dist/`; inside site-packages that output is untracked
  and lost.
- Committing the work. There is no way to review or revert a theme change.

So the package is vendored here and installed with `pip install -e` with
`--no-deps`, which makes `import reddash` resolve here and makes every byte
we ship reviewable in git.

## What differs from upstream

Only the packaging files. Upstream's `pyproject.toml`, `setup.py` and
`setup.cfg` are not present in a built wheel, so they were reproduced from
upstream v1.8.2:

- `setup.py` — verbatim from upstream.
- `setup.cfg` — upstream's, except `version` is pinned to `1.8.2` instead of
  the `attr: reddash.__version__` indirection, and the `Topic` classifier is
  correct. Dependency list is unchanged, so `pip install -e . --no-deps`
  reuses what is already in `redvenv`.
- `MANIFEST.in` — so static assets and templates are packaged.
- `README.vendor.md` — this file.

The Python sources under `reddash/` are byte-for-byte from the installed
v1.8.2 wheel. As Phases 2–6 modify them, those diffs become the real record of
what this project changed.

## Refreshing from upstream

```bash
pip uninstall -y Red-Web-Dashboard
# replace the reddash/ subdirectory with a fresh copy, keeping the packaging
# files above
pip install -e ./vendor/reddash --no-deps
```

## Licence

Red-Web-Dashboard is **AGPL-3.0**. The vendored copy keeps the upstream
`LICENSE` file. Under AGPL-3.0, offering this over a network requires
publishing the corresponding source. The Argon Dashboard 2 template it builds
on is MIT (Creative Tim) and requires attribution, which
`templates/pages/credits.html` and the site footer carry. Both obligations
survive the retheme and must be preserved in the rebuilt templates.
