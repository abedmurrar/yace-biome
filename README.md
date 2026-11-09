# YACE Biome

[Biome](https://biomejs.dev)'s linter and formatter in
[YACE](https://github.com/abedmurrar/yace): problems in the Problems
panel, quick fixes on `Ctrl+.`, and formatting, for JavaScript,
TypeScript, JSX, TSX, JSON, JSONC, CSS and GraphQL files.

It's a manifest only, with no plugin code: it tells YACE to start Biome's
own language server (`biome lsp-proxy`) next to the main server for those
languages. Hover, completion and go to definition still come from the
TypeScript server.

## Install

In YACE 0.13 or later: **Extensions: Browse Plugins…** and pick Biome, or
from a shell:

```sh
yace --install-plugin io.github.abedmurrar.yace-biome
```

Your project needs Biome and a configuration file:

```sh
npm install --save-dev --save-exact @biomejs/biome
npx @biomejs/biome init
```

## What it does

- **Starts only in Biome projects:** for files with `biome.json` or
  `biome.jsonc` in their folder or a folder above it, up to the workspace
  folder. Elsewhere nothing changes.
- **Uses your project's Biome:** `node_modules/.bin/biome` in the
  workspace folder or a folder above it, else `biome` on your `PATH`.
- **Problems** come with the source `biome`, next to the TypeScript
  server's. The status bar shows `typescript + biome`.
- **Quick fixes** (`Ctrl+.`) list Biome's fixes and suppressions with the
  other servers' actions.
- **Formatting:** to format with Biome instead of the TypeScript server,
  set this in `~/.config/yace/config.toml`:

  ```toml
  [editor]
  defaultFormatter = "biome"
  formatOnSave = true   # optional
  ```

  YACE imports VS Code's `"editor.defaultFormatter": "biomejs.biome"` the
  same way.

Disabling or uninstalling the plugin stops Biome and removes its problems.
`yace --safe-mode` doesn't start it.

## Development

`scripts/package.sh` builds `dist/yace-biome-<version>.tar.gz` with
`yace --package-plugin`, which validates the manifest. CI packages and
installs it with the latest YACE on every push.

The end-to-end test lives in the YACE repository (`real_biome` in
`crates/yace/tests/lsp_plugins.rs`): it installs this folder into YACE,
opens a project, and checks the problems, a quick fix and formatting:

```sh
cd path/to/yace
YACE_TEST_BIOME=$HOME/Dev/yace-biome:/path/to/project-with-biome.json \
  cargo test -p yace --test lsp_plugins real_biome -- --nocapture
```

To release: bump `version` in `yace-plugin.toml`, commit, and push a tag
`vX.Y.Z`. The release workflow packages it and publishes the package with
its SHA-256. Then list the new version in the YACE plugin index, from a
YACE checkout:

```sh
scripts/plugin_index.py add https://github.com/abedmurrar/yace-biome/releases/download/vX.Y.Z/yace-biome-X.Y.Z.tar.gz
```

## Licence

Dual-licensed under [MIT](LICENSE-MIT) or [Apache-2.0](LICENSE-APACHE), at
your option.
