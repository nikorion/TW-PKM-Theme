# TW-Theme

[English](README.md) · **Français**

![Status](https://img.shields.io/badge/status-personal-orange)
![TiddlyWiki](https://img.shields.io/badge/TiddlyWiki-%E2%89%A55.3.5-blue)

Un plugin TiddlyWiki qui réunit l'habillage personnel de l'auteur : feuilles de style et surcharges de shadow tiddlers (réglages du thème Vanilla, styles des plugins Kookma, propriétés CSS du cœur) qui étaient auparavant des tiddlers libres du wiki de production.

## Vue d'ensemble

- `src/theme/styles/` : six feuilles de style (texte courant, titres, filets, coloration du code, cadres, blocs colorés), qui gardent leurs titres d'origine `$:/nikorion/styles/*`.
- `src/theme/overrides/` : tiddlers qui portent le titre d'un shadow tiddler du cœur, du thème Vanilla ou d'un plugin tiers, et le remplacent donc.
- `plugin-priority` 100 : le plugin s'enregistre après le cœur (0), les thèmes et tous les plugins ordinaires (1), donc ses shadows l'emportent toujours.

## Installation

Plugin personnel, non publié. Reliez `src/theme` comme `nikorion/theme` dans `TIDDLYWIKI_PLUGIN_PATH` et listez `"nikorion/theme"` dans les `plugins` du `tiddlywiki.info` du wiki.

Nécessite TiddlyWiki ≥ 5.3.5, les polices Inter, Palatino Linotype et JetBrains Mono sur la machine, et `files/logo-goat-mech.svg` dans le dossier du wiki pour les filets.

## Développement

```
pnpm install
pnpm dev      # wiki de dev + rechargement à chaud ; l'URL (port libre aléatoire) s'affiche au démarrage
pnpm build    # dist/TW-Theme-Plugin.json + docs/
```

Les sources sont dans `src/theme/` ; le wiki de dev (`wiki/`) montre chaque partie dans son Playground.

## Fichiers

| Fichier | Rôle |
|---|---|
| `src/theme/plugin.info` | Métadonnées du plugin, `plugin-priority` |
| `src/theme/styles/*.tid` | Feuilles de style taguées `$:/tags/Stylesheet` |
| `src/theme/overrides/*` | Surcharges de shadow tiddlers (titre imposé par le tiddler surchargé) |

## Licence

Licence MIT — voir `LICENSE`. `highlight.css` est le thème Obsidian de highlight.js (BSD 3-Clause, porté par Alexander Marenin).
