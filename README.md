# TW-PKM-Theme

**English** · [Français](README.fr.md)

![Status](https://img.shields.io/badge/status-personal-orange)
![TiddlyWiki](https://img.shields.io/badge/TiddlyWiki-%E2%89%A55.3.5-blue)

A Vanilla-based TiddlyWiki theme (plugin type theme) with the author's personal look: Vanilla's stylesheets and settings, the author's stylesheets, and the overrides of shadow tiddlers (Kookma plugin styles, core custom properties) that used to be loose tiddlers of the production wiki. Colours come from the companion TW-PKM-Palette plugin; the layout stays with Layout.

## Overview

- `plugin.info` `dependents`: Vanilla stays loaded; nothing of it is copied.
- `src/pkm-theme/overrides/vanilla-settings.multids`: the author's values of Vanilla's settings.
- `src/pkm-theme/styles/`: six stylesheets (body text, headings, rules, code highlighting, frames, coloured blocks), titled `$:/themes/nikorion/pkm/styles/*`, following the source tree.
- `src/pkm-theme/overrides/`: tiddlers that share the title of a shadow tiddler of the core, the Vanilla theme or a third-party plugin, and so replace it.
- `plugin-priority` 100: the plugin registers after the core (0), the themes and all ordinary plugins (1), so its shadows always win.

## Installation

Personal plugin, not published. Link `src/pkm-theme` as `nikorion/pkm-theme` in `TIDDLYWIKI_PLUGIN_PATH` and list `"nikorion/pkm-theme"` in the `plugins` of the wiki's `tiddlywiki.info`.

Requires TiddlyWiki ≥ 5.3.5 and the fonts Inter, Palatino Linotype and JetBrains Mono on the machine.

## Development

```
pnpm install
pnpm dev      # dev wiki + hot reload; the URL (random free port) is printed on start
pnpm build    # dist/TW-PKM-Theme-Plugin.json + docs/
```

Sources are in `src/pkm-theme/`; the dev wiki (`wiki/`) shows every part in its Playground.

## Files

| File | Role |
|---|---|
| `src/pkm-theme/plugin.info` | Plugin metadata, `plugin-priority` |
| `src/pkm-theme/styles/*.tid` | Stylesheets tagged `$:/tags/Stylesheet` |
| `src/pkm-theme/images/` | Logo of the horizontal rule and wallpaper (inlined as data URIs, no served file) |
| `src/pkm-theme/overrides/*` | Shadow-tiddler overrides (title imposed by the overridden tiddler) |

## License

MIT License — see `LICENSE`. `highlight.css` is the Obsidian theme of highlight.js (BSD 3-Clause, ported by Alexander Marenin).
