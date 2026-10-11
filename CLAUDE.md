# TW-Theme — contexte projet pour Claude

> **Avant toute tâche sur ce plugin, consulter d'abord le `CLAUDE.md` du workspace** (`../CLAUDE.md`) et ses `guides/` : outillage de dev commun (pnpm, `dev.cjs`/HMR, Ctrl+C, git push), pièges PowerShell/Windows, `publishFilter`, symlink. Ci-dessous : uniquement le spécifique à TW-Theme.

## Ce que c'est
Plugin TiddlyWiki (`$:/plugins/nikorion/theme`, créé 2026-10-11) : le visuel personnel de l'utilisateur, extrait de **kms-prod** (tiddlers libres de `tiddlers/system/`) — feuilles de style + surcharges de shadows. Plugin **personnel, git local seul** (ni remote, ni CI, pas dans `tw-dev/plugins.json` ni la bibliothèque) tant que l'utilisateur ne décide pas de le publier. Chargé par kms-prod ; absent des autres wikis de prod.

## Décisions à ne pas défaire
- **`"plugin-priority": "100"`** dans `plugin.info` : TW enregistre les shadows par priorité croissante, le dernier gagne (`boot.js` `unpackPluginTiddlers`) ; cœur = 0, plugins/thèmes sans champ = 1, départagés par titre (`$:/plugins/nikorion/…` < `$:/plugins/tiddlywiki/…` < `$:/themes/…`, donc sans priorité explicite le thème Vanilla et les plugins `tiddlywiki/*` écraseraient ce plugin). Ne pas baisser.
- **Titres des feuilles de style `$:/nikorion/styles/*.css` conservés** (pas `$:/plugins/nikorion/theme/styles/…`) : l'ordre de cascade CSS suit l'ordre alphabétique des titres `$:/tags/Stylesheet` ; renommer changerait quel style gagne face aux plugins Kookma. Exception assumée à « titre = chemin fichier ».
- `overrides/` : titres imposés par les tiddlers surchargés (core, Vanilla, Kookma). `core-custom-properties.tid` est une copie modifiée du cœur : **à comparer avec la version du cœur à chaque montée de TiddlyWiki**.
- Aucun JS, pas de `pnpm lint`.
- **Exception à la convention des wikis de dev (choix utilisateur 2026-10-11)** : `wiki/tiddlers/system/` ne contient **ni palette, ni `AnimationDuration`, ni `$:/view`, ni réglage Vanilla** (metrics/options/settings) — tout le visuel vient de `src/theme/`, le wiki de dev charge son propre plugin. Ne pas y recréer ces tiddlers ni les y copier : `TW-Theme` est dans `exclude` de `tw-dev/prefs.json` (la synchro des préférences d'interface ne lui en donne ni n'en prend). Un réglage visuel à changer se fait dans `src/theme/overrides/vanilla.multids`.
- **`$:/config/SyncFilter` du wiki de dev étendu** : en plus de `$:/plugins/nikorion/`, il exclut tout ce que le plugin porte sous un autre titre (`$:/nikorion/styles/`, `custom-properties`, `$:/plugins/kookma/`, `$:/themes/tiddlywiki/vanilla/`) + `$:/palette`, `$:/view`, `AnimationDuration`. Sans ça le HMR pousse la feuille au navigateur, dont le syncer la réécrit dans `wiki/tiddlers/system/` (vécu 2026-10-11, `$__nikorion_styles_hr.css.tid`), et ce vrai tiddler masque le shadow du plugin pour de bon. Tout nouveau titre ajouté au plugin hors `$:/plugins/nikorion/theme/` → l'ajouter à ce filtre.
- Le fond d'écran (`images/vecteezy-summer-landscape-fields.svg`, 1,3 Mo) est livré dans le plugin ; Vanilla l'inline en data URI dans la feuille de page (base.tid), ce qui alourdit le CSS de chaque page. Alternative si gênant : le servir par `_canonical_uri` (retour à un fichier externe).

## Dépendances hors plugin
- Aucun fichier externe : le logo du filet est le tiddler `images/logo-goat-mech.svg` du plugin, inliné en data URI par `hr.tid` (macro `datauri` ; feuille wikitext donc `ules only …`).
- Polices Inter, Palatino Linotype, JetBrains Mono : installées sur la machine (ou Font Manager).

## Structure
```
src/theme/
  plugin.info
  readme.tid / history.tid / licence.tid   ← sélecteurs de langue
  settings.tid                              ← placeholder "Coming soon."
  language/{lingo.tid, en-GB|fr-FR/…}
  styles/*.tid       ← 6 feuilles (type text/css, taguées $:/tags/Stylesheet)
  images/logo-goat-mech.svg(.meta) ← logo du filet, inliné par hr.tid
  overrides/         ← surcharges de shadows : core custom-properties, refnotes footnote-counter,
                       tweaks headings (vidé), shiraz-formatter highlight-colors, vanilla.multids
wiki/                ← wiki de dev : Playground (titres, blocs, code, filet)
```

## Laissé dans kms-prod (non visuel ou hors périmètre, 2026-10-11)
Surcharges fonctionnelles (templates Shiraz status/priority, `MoreSideBar/User`, macro `plugin-info-tree/macros/tree`), réglages `$:/config/*`, styles du plugin perso « classification », `$:/plugins/nikorion/layout-scroll/stylesheet` (ancien layout, sans effet depuis `scroll-layout`).
