# TW-PKM-Theme

[English](README.md) · **Français**

![Status](https://img.shields.io/badge/status-experimental-orange)
![Status](https://img.shields.io/badge/status-personal-orange)
![TiddlyWiki](https://img.shields.io/badge/TiddlyWiki-%E2%89%A55.3.5-blue)

Un thème TiddlyWiki basé sur Vanilla (type de plugin theme) avec l'habillage personnel de l'auteur : feuilles de style et réglages de Vanilla, feuilles de l'auteur, et surcharges de shadow tiddlers (styles des plugins Kookma, propriétés CSS du cœur) qui étaient auparavant des tiddlers libres du wiki de production. Les couleurs viennent du plugin compagnon TW-PKM-Palette ; le layout reste celui de Layout.

## Vue d'ensemble

- `dependents` de `plugin.info` : Vanilla reste chargé ; rien n'en est copié.
- `src/pkm-theme/overrides/vanilla-settings.multids` : les valeurs de l'auteur des réglages de Vanilla.
- `src/pkm-theme/styles/` : six feuilles de style (texte courant, titres, filets, coloration du code, cadres, blocs colorés), titrées `$:/themes/nikorion/pkm/styles/*`, comme l'arborescence des sources.
- `src/pkm-theme/overrides/` : tiddlers qui portent le titre d'un shadow tiddler du cœur, du thème Vanilla ou d'un plugin tiers, et le remplacent donc.
- `plugin-priority` 100 : le plugin s'enregistre après le cœur (0), les thèmes et tous les plugins ordinaires (1), donc ses shadows l'emportent toujours.

## Installation

Plugin personnel, non publié. Reliez `src/pkm-theme` comme `nikorion/pkm-theme` dans `TIDDLYWIKI_PLUGIN_PATH` et listez `"nikorion/pkm-theme"` dans les `plugins` du `tiddlywiki.info` du wiki.

Nécessite TiddlyWiki ≥ 5.3.5 et les polices Inter, Palatino Linotype et JetBrains Mono sur la machine.

## Développement

```
pnpm install
pnpm dev      # wiki de dev + rechargement à chaud ; l'URL (port libre aléatoire) s'affiche au démarrage
pnpm build    # dist/TW-PKM-Theme-Plugin.json + docs/
```

Les sources sont dans `src/pkm-theme/` ; le wiki de dev (`wiki/`) montre chaque partie dans son Playground.

## Fichiers

| Fichier | Rôle |
|---|---|
| `src/pkm-theme/plugin.info` | Métadonnées du plugin, `plugin-priority` |
| `src/pkm-theme/styles/*.tid` | Feuilles de style taguées `$:/tags/Stylesheet` |
| `src/pkm-theme/images/` | Logo du filet horizontal et fond d'écran (inlinés en data URI, aucun fichier servi) |
| `src/pkm-theme/overrides/*` | Surcharges de shadow tiddlers (titre imposé par le tiddler surchargé) |

## Licence

Licence MIT — voir `LICENSE`. `highlight.css` est le thème Obsidian de highlight.js (BSD 3-Clause, porté par Alexander Marenin).
