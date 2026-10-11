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
- **Write-back navigateur → source, généralisé** (2026-10-11, `"twDev": {"writeBack": "src/theme"}` dans `package.json`, `tw-dev/writeback.cjs` appelé depuis `dev-hmr.cjs`) : **dans ce wiki de dev, tout ce qui est créé ou modifié dans l'UI est du thème** (choix utilisateur). Titre déjà livré → fichier source mis à jour (`.tid`, ou ligne de `overrides/vanilla.multids` ; valeur multi-ligne → `.tid` à part) ; titre nouveau → feuille de style dans `styles/`, nouveau réglage Vanilla dans `vanilla.multids`, binaire dans `images/`, le reste dans `overrides/<slug>.tid` ; puis le fichier enregistré est supprimé de `wiki/tiddlers/`. Jamais pris : brouillons, états de session, et les tiddlers qui étaient déjà des fichiers de `wiki/tiddlers/` au démarrage du serveur (contenu propre du wiki : Playground, tiddler d'avis, titre du site…). Conséquences : le `$:/config/SyncFilter` n'exclut plus que le transitoire et `$:/plugins/nikorion/` ; ne **pas** créer à la main de fichier dans `wiki/tiddlers/` pendant `pnpm dev` (il serait avalé par le plugin) ; relire `git status` du plugin après une session. Ne marche que pendant `pnpm dev`. Tiddler d'avis affiché en premier : `Browser edits go to the source` (il précise aussi : ne pas créer de fichier à la main dans `wiki/tiddlers/` pendant `pnpm dev`, créer avant ou arrêter le serveur).
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
