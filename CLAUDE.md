# TW-Theme — contexte projet pour Claude

> **Avant toute tâche sur ce plugin, consulter d'abord le `CLAUDE.md` du workspace** (`../CLAUDE.md`) et ses `guides/` : outillage de dev commun (pnpm, `dev.cjs`/HMR, Ctrl+C, git push), pièges PowerShell/Windows, `publishFilter`, symlink. Ci-dessous : uniquement le spécifique à TW-Theme.

## Ce que c'est
Thème TiddlyWiki (`$:/plugins/nikorion/theme`, `"plugin-type": "theme"`, créé 2026-10-11) construit **sur** Vanilla sans le copier : `plugin.info` déclare `"dependents": "$:/themes/tiddlywiki/vanilla"` (le gestionnaire de thème enregistre aussi les dépendants du thème choisi), puis ce plugin superpose ses réglages Vanilla (`overrides/vanilla-settings.multids`), ses feuilles (`styles/`) et ses surcharges de shadows (`overrides/`), extraits de **kms-prod**. Aucun fichier du cœur n'est copié ni surchargé : **rien à re-synchroniser à chaque montée de TiddlyWiki**. Compagnon de `TW-Palette` (palette `$:/palettes/nikorion`). **Personnel, git local seul** (ni remote, ni CI, hors `plugins.json`/bibliothèque). Le layout reste `layout` (pas de layout custom, choix utilisateur 2026-10-11).

## Décisions à ne pas défaire
- **Un thème est un plugin à part** (`plugin-type: theme`), jamais un sous-dossier d'un autre plugin (le chargeur absorberait ses fichiers). **Il ne peut pas se sélectionner lui-même** : `$tw.themeManager` lit `$:/theme` au démarrage, avant que les plugins de type theme soient enregistrés, donc un shadow `$:/theme` livré par le thème est invisible (vérifié 2026-10-11 : `$:/theme` vide, aucun style). `$:/theme` = `$:/plugins/nikorion/theme` est donc livré par un plugin ordinaire, **TW-Palette** (`theme-select.tid` ; si le thème n'est pas chargé TW retombe sur Vanilla), ou posé en vrai tiddler (kms-prod). Vanilla reste enregistré (dépendant) : ses tiddlers gardent leurs titres `$:/themes/tiddlywiki/vanilla/…`, que ce plugin surcharge par priorité (100 contre 0).
- **Vanilla doit être dans `themes` du `tiddlywiki.info`** (`tiddlywiki/vanilla`) : le thème en dépend, il n'en embarque rien. Sans lui, plus de CSS de base.
- **`"plugin-priority": "100"`** dans `plugin.info` : TW enregistre les shadows par priorité croissante, le dernier gagne (`boot.js` `unpackPluginTiddlers`) ; cœur = 0, plugins/thèmes sans champ = 1, départagés par titre (`$:/plugins/nikorion/…` < `$:/plugins/tiddlywiki/…` < `$:/themes/…`, donc sans priorité explicite le thème Vanilla et les plugins `tiddlywiki/*` écraseraient ce plugin). Ne pas baisser.
- **Titres des feuilles de style `$:/nikorion/styles/*.css` conservés** (pas `$:/plugins/nikorion/theme/styles/…`) : l'ordre de cascade CSS suit l'ordre alphabétique des titres `$:/tags/Stylesheet` ; renommer changerait quel style gagne face aux plugins Kookma. Exception assumée à « titre = chemin fichier ».
- `overrides/` : titres imposés par les tiddlers surchargés (Vanilla, Kookma). Ne **pas** surcharger `$:/core/stylesheets/custom-properties` : les variables `--tpc-nk-*` sont émises par TW-Palette (feuille `styles/properties`), ce qui isole nos ajouts des variables par défaut du cœur.
- Aucun JS, pas de `pnpm lint`.
- **Exception à la convention des wikis de dev (choix utilisateur 2026-10-11)** : `wiki/tiddlers/system/` ne contient **ni palette, ni `AnimationDuration`, ni `$:/view`, ni réglage Vanilla** (metrics/options/settings) — tout le visuel vient de `src/theme/`, le wiki de dev charge son propre plugin. Ne pas y recréer ces tiddlers ni les y copier : `TW-Theme` est dans `exclude` de `tw-dev/prefs.json` (la synchro des préférences d'interface ne lui en donne ni n'en prend). Un réglage visuel à changer se fait dans `src/theme/overrides/vanilla-settings.multids`.
- **Write-back navigateur → source, généralisé** (2026-10-11, `"twDev": {"writeBack": {"dirs": ["src/theme", "../TW-Palette/src/palette"], "default": "src/theme"}}`, `tw-dev/writeback.cjs` appelé depuis `dev-hmr.cjs`) : **dans ce wiki de dev, tout ce qui est créé ou modifié dans l'UI est du thème** (choix utilisateur). Titre déjà livré (thème, ou palette `$:/palettes/nikorion` → TW-Palette) → son fichier source mis à jour ; titre nouveau → `styles/` (feuille), `images/` (binaire), `overrides/<slug>.tid` (reste) ; puis le fichier enregistré est supprimé de `wiki/tiddlers/`. Jamais pris : brouillons, états de session, et les tiddlers déjà fichiers de `wiki/tiddlers/` au démarrage du serveur. Ne **pas** créer de fichier à la main dans `wiki/tiddlers/` pendant `pnpm dev` ; relire `git status` du thème **et** de TW-Palette après une session. Ne marche que pendant `pnpm dev`. Tiddler d'avis affiché en premier : `Edits here become the theme`.
- Le fond d'écran (`images/vecteezy-summer-landscape-fields.svg`, 1,3 Mo) est livré dans le plugin ; Vanilla l'inline en data URI dans la feuille de page (base.tid), ce qui alourdit le CSS de chaque page. Alternative si gênant : le servir par `_canonical_uri` (retour à un fichier externe).

## Couleurs : dans la palette, pas ici
(2026-10-11, choix utilisateur.) **Aucune couleur en dur dans les feuilles** : elles lisent les variables CSS `--tpc-<clé>` (celles du cœur pour les clés Vanilla, celles de `TW-Palette/src/palette/styles/properties.tid` pour les clés propres `nk-…`), jamais `<<colour>>`. Les couleurs propres au thème (`nk-site-title`, `nk-hljs-*`, `nk-info-*`, `nk-interro-*`, `nk-alert-*`, `nk-light-*`, `nk-tiddler-border`, `nk-footnote-counter`, `nk-background`) sont des clés de la palette Nikorion (`TW-Palette/src/palette/palette.tid`) : **ce thème exige le plugin `palette`** ; avec une autre palette ces variables sont indéfinies (déclarations ignorées, pas de repli). Une nouvelle couleur = une clé `nk-…` dans la palette + `var(--tpc-nk-…)` ici.

## Dépendances hors plugin
- Aucun fichier externe : le logo du filet est le tiddler `images/logo-goat-mech.svg` du plugin, inliné en data URI par `hr.tid` (macro `datauri` ; feuille wikitext donc ligne `\rules only …`).
- Polices Inter, Palatino Linotype, JetBrains Mono : installées sur la machine (ou Font Manager).

## Structure
```
src/theme/
  plugin.info       ← plugin-type theme, priority 100
  readme.tid / history.tid / licence.tid   ← sélecteurs de langue
  settings.tid                              ← placeholder "Coming soon."
  language/{lingo.tid, en-GB|fr-FR/…}
  styles/*.tid       ← 6 feuilles (type text/css, taguées $:/tags/Stylesheet)
  images/logo-goat-mech.svg(.meta) ← logo du filet, inliné par hr.tid
  overrides/         ← surcharges de shadows : core custom-properties, refnotes footnote-counter,
                       tweaks headings (vidé), shiraz-formatter highlight-colors,
                       vanilla-settings.multids (nos valeurs des réglages Vanilla)
wiki/                ← wiki de dev : Playground (titres, blocs, code, filet)
```

## Laissé dans kms-prod (non visuel ou hors périmètre, 2026-10-11)
Surcharges fonctionnelles (templates Shiraz status/priority, `MoreSideBar/User`, macro `plugin-info-tree/macros/tree`), réglages `$:/config/*`, styles du plugin perso « classification », `$:/plugins/nikorion/layout-scroll/stylesheet` (ancien layout, sans effet depuis `layout`).
