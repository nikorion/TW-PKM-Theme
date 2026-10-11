# TW-Theme — contexte projet pour Claude

> **Avant toute tâche sur ce plugin, consulter d'abord le `CLAUDE.md` du workspace** (`../CLAUDE.md`) et ses `guides/` : outillage de dev commun (pnpm, `dev.cjs`/HMR, Ctrl+C, git push), pièges PowerShell/Windows, `publishFilter`, symlink. Ci-dessous : uniquement le spécifique à TW-Theme.

## Ce que c'est
Plugin TiddlyWiki (`$:/plugins/nikorion/theme`, créé 2026-10-11) : le visuel personnel de l'utilisateur, extrait de **kms-prod** (tiddlers libres de `tiddlers/system/`) — feuilles de style + surcharges de shadows. Plugin **personnel, git local seul** (ni remote, ni CI, pas dans `tw-dev/plugins.json` ni la bibliothèque) tant que l'utilisateur ne décide pas de le publier. Chargé par kms-prod ; absent des autres wikis de prod.

## Décisions à ne pas défaire
- **`"plugin-priority": "100"`** dans `plugin.info` : TW enregistre les shadows par priorité croissante, le dernier gagne (`boot.js` `unpackPluginTiddlers`) ; cœur = 0, plugins/thèmes sans champ = 1, départagés par titre (`$:/plugins/nikorion/…` < `$:/plugins/tiddlywiki/…` < `$:/themes/…`, donc sans priorité explicite le thème Vanilla et les plugins `tiddlywiki/*` écraseraient ce plugin). Ne pas baisser.
- **Titres des feuilles de style `$:/nikorion/styles/*.css` conservés** (pas `$:/plugins/nikorion/theme/styles/…`) : l'ordre de cascade CSS suit l'ordre alphabétique des titres `$:/tags/Stylesheet` ; renommer changerait quel style gagne face aux plugins Kookma. Exception assumée à « titre = chemin fichier ».
- `overrides/` : titres imposés par les tiddlers surchargés (core, Vanilla, Kookma). `core-custom-properties.tid` est une copie modifiée du cœur : **à comparer avec la version du cœur à chaque montée de TiddlyWiki**.
- Aucun JS, pas de `pnpm lint`.

## Dépendances hors plugin
- `hr.css` lit `/files/logo-goat-mech.svg` : fichier du dossier `files/` du wiki (non livré).
- Polices Inter, Palatino Linotype, JetBrains Mono : installées sur la machine (ou Font Manager).
- `settings/backgroundimage` désigne un tiddler du wiki (`vecteezy-summer-landscape-fields.svg`).

## Structure
```
src/theme/
  plugin.info
  readme.tid / history.tid / licence.tid   ← sélecteurs de langue
  settings.tid                              ← placeholder "Coming soon."
  language/{lingo.tid, en-GB|fr-FR/…}
  styles/*.tid       ← 6 feuilles (type text/css, taguées $:/tags/Stylesheet)
  overrides/         ← surcharges de shadows : core custom-properties, refnotes footnote-counter,
                       tweaks headings (vidé), shiraz-formatter highlight-colors, vanilla.multids
wiki/                ← wiki de dev : Playground (titres, blocs, code, filet)
```

## Laissé dans kms-prod (non visuel ou hors périmètre, 2026-10-11)
Surcharges fonctionnelles (templates Shiraz status/priority, `MoreSideBar/User`, macro `plugin-info-tree/macros/tree`), réglages `$:/config/*`, styles du plugin perso « classification », `$:/plugins/nikorion/layout-scroll/stylesheet` (ancien layout, sans effet depuis `scroll-layout`).
