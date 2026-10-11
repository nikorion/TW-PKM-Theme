# TW-Theme

**English** · [Français](README.fr.md)

![Status](https://img.shields.io/badge/status-personal-orange)
![TiddlyWiki](https://img.shields.io/badge/TiddlyWiki-%E2%89%A55.3.5-blue)

A TiddlyWiki plugin gathering the author's personal look: stylesheets, and the overrides of shadow tiddlers (Vanilla theme settings, Kookma plugin styles, core custom properties) that used to be loose tiddlers of the production wiki.

## Overview

- `src/theme/styles/`: six stylesheets (body text, headings, rules, code highlighting, frames, coloured blocks), keeping their original `$:/nikorion/styles/*` titles.
- `src/theme/overrides/`: tiddlers that share the title of a shadow tiddler of the core, the Vanilla theme or a third-party plugin, and so replace it.
- `plugin-priority` 100: the plugin registers after the core (0), the themes and all ordinary plugins (1), so its shadows always win.

## Installation

Personal plugin, not published. Link `src/theme` as `nikorion/theme` in `TIDDLYWIKI_PLUGIN_PATH` and list `"nikorion/theme"` in the `plugins` of the wiki's `tiddlywiki.info`.

Requires TiddlyWiki ≥ 5.3.5, the fonts Inter, Palatino Linotype and JetBrains Mono on the machine, and `files/logo-goat-mech.svg` in the wiki folder for the rules.

## Development

```
pnpm install
pnpm dev      # dev wiki + hot reload; the URL (random free port) is printed on start
pnpm build    # dist/TW-Theme-Plugin.json + docs/
```

Sources are in `src/theme/`; the dev wiki (`wiki/`) shows every part in its Playground.

## Files

| File | Role |
|---|---|
| `src/theme/plugin.info` | Plugin metadata, `plugin-priority` |
| `src/theme/styles/*.tid` | Stylesheets tagged `$:/tags/Stylesheet` |
| `src/theme/overrides/*` | Shadow-tiddler overrides (title imposed by the overridden tiddler) |

## License

MIT License — see `LICENSE`. `highlight.css` is the Obsidian theme of highlight.js (BSD 3-Clause, ported by Alexander Marenin).
