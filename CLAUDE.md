# TW-PKM-Theme — contexte projet pour Claude

> **Avant toute tâche sur ce plugin, consulter d'abord le `CLAUDE.md` du workspace** (`../CLAUDE.md`) et ses `guides/` : outillage de dev commun (pnpm, `dev.cjs`/HMR, Ctrl+C, git push), pièges PowerShell/Windows, `publishFilter`, symlink. Ci-dessous : uniquement le spécifique à TW-PKM-Theme.

## Ce que c'est
Thème TiddlyWiki (`$:/themes/nikorion/pkm`, `"plugin-type": "theme"`, créé 2026-10-11) construit **sur** Vanilla sans le copier : `plugin.info` déclare `"dependents": "$:/themes/tiddlywiki/vanilla"` (le gestionnaire de thème enregistre aussi les dépendants du thème choisi), puis ce plugin superpose ses réglages Vanilla (`overrides/vanilla-settings.multids`), ses feuilles (`styles/`) et ses surcharges de shadows (`overrides/`), extraits de **kms-prod**. Aucun fichier du cœur n'est copié ni surchargé : **rien à re-synchroniser à chaque montée de TiddlyWiki**. Compagnon de `TW-PKM-Palette` (palette `$:/palettes/nikorion`). **Personnel, git local seul** (ni remote, ni CI, hors `plugins.json`/bibliothèque). Le layout reste `layout` (pas de layout custom, choix utilisateur 2026-10-11).

## Décisions à ne pas défaire
- **Un thème est un plugin à part** (`plugin-type: theme`), jamais un sous-dossier d'un autre plugin (le chargeur absorberait ses fichiers). **Il ne peut pas se sélectionner lui-même** : `$tw.themeManager` lit `$:/theme` au démarrage, avant que les plugins de type theme soient enregistrés, donc un shadow `$:/theme` livré par le thème est invisible (vérifié 2026-10-11 : `$:/theme` vide, aucun style). `$:/theme` = `$:/themes/nikorion/pkm` est donc livré par un plugin ordinaire, **TW-PKM-Palette** (`theme-select.tid` ; si le thème n'est pas chargé TW retombe sur Vanilla), ou posé en vrai tiddler (kms-prod). Vanilla reste enregistré (dépendant) : ses tiddlers gardent leurs titres `$:/themes/tiddlywiki/vanilla/…`, que ce plugin surcharge par priorité (100 contre 0).
- **Vanilla doit être dans `themes` du `tiddlywiki.info`** (`tiddlywiki/vanilla`) : le thème en dépend, il n'en embarque rien. Sans lui, plus de CSS de base.
- **`"plugin-priority": "100"`** dans `plugin.info` : TW enregistre les shadows par priorité croissante, le dernier gagne (`boot.js` `unpackPluginTiddlers`) ; cœur = 0, plugins/thèmes sans champ = 1, départagés par titre (`$:/plugins/nikorion/…` < `$:/plugins/tiddlywiki/…` < `$:/themes/…`, donc sans priorité explicite le thème Vanilla et les plugins `tiddlywiki/*` écraseraient ce plugin). Ne pas baisser.
- **Feuilles de style titrées `$:/themes/nikorion/pkm/styles/<fichier>`** (comme l'arborescence des sources, 2026-10-11 ; anciennement `$:/nikorion/styles/*.css`, qui se triait avant les plugins Kookma et les laissait gagner à spécificité égale). Désormais le thème se trie **après** `$:/plugins/kookma/…` dans la cascade (`$:/tags/Stylesheet` suit l'ordre alphabétique des titres) : les feuilles du thème l'emportent à spécificité égale. Si une règle Kookma réapparaît, c'est ce tri qu'il faut regarder. kms-prod garde des copies libres `$:/nikorion/styles/*.css` (anciennes, non supprimées) : elles s'appliquent en plus du thème.
- `overrides/` : titres imposés par les tiddlers surchargés (Vanilla, Kookma). Ne **pas** surcharger `$:/core/stylesheets/custom-properties` : les variables `--tpc-nk-*` sont émises par TW-PKM-Palette (feuille `styles/properties`), ce qui isole nos ajouts des variables par défaut du cœur.
- Aucun JS, pas de `pnpm lint`.
- **Exception à la convention des wikis de dev (choix utilisateur 2026-10-11)** : `wiki/tiddlers/system/` ne contient **ni palette, ni `AnimationDuration`, ni `$:/view`, ni réglage Vanilla** (metrics/options/settings) — tout le visuel vient de `src/pkm-theme/`, le wiki de dev charge son propre plugin. Ne pas y recréer ces tiddlers ni les y copier : `TW-PKM-Theme` est dans `exclude` de `tw-dev/prefs.json` (la synchro des préférences d'interface ne lui en donne ni n'en prend). Un réglage visuel à changer se fait dans `src/pkm-theme/overrides/vanilla-settings.multids`.
- **Write-back navigateur → source, généralisé** (2026-10-11, `"twDev": {"writeBack": {"dirs": ["src/pkm-theme", "../TW-PKM-Palette/src/palette"], "default": "src/pkm-theme"}}`, `tw-dev/writeback.cjs` appelé depuis `dev-hmr.cjs`) : **dans ce wiki de dev, tout ce qui est créé ou modifié dans l'UI est du thème** (choix utilisateur). Titre déjà livré (thème, ou palette `$:/palettes/nikorion` → TW-PKM-Palette) → son fichier source mis à jour ; titre nouveau → `styles/` (feuille), `images/` (binaire), `overrides/<slug>.tid` (reste) ; puis le fichier enregistré est supprimé de `wiki/tiddlers/`. Jamais pris : brouillons, états de session, et les tiddlers déjà fichiers de `wiki/tiddlers/` au démarrage du serveur. Ne **pas** créer de fichier à la main dans `wiki/tiddlers/` pendant `pnpm dev` ; relire `git status` du thème **et** de TW-PKM-Palette après une session. Ne marche que pendant `pnpm dev`. Tiddler d'avis affiché en premier : `Edits here become the theme`.
- **Piège vécu 2026-10-11 : le write-back reprend aussi les shadows des plugins frères.** Quand un plugin frère change pendant `pnpm dev` (ex. Plugin Info Tree), le HMR pousse sa source au navigateur, qui l'enregistre comme tiddler réel ; un titre hors du préfixe du thème (`$:/core/ui/PluginInfo/Default/contents`) est alors pris pour un titre nouveau et atterrit dans `overrides/` (fichier parasite à supprimer). Le `$:/config/SyncFilter` du wiki exclut donc `-[prefix[$:/plugins/nikorion/]]`, `-[prefix[$:/themes/nikorion/pkm]]` (titres du thème lui-même, déjà livrés) et ce titre précis : tout autre titre de plugin frère hors `$:/plugins/nikorion/` qui apparaît en fichier parasite doit y être ajouté. Ne **pas** retirer `$:/plugins/nikorion/` en changeant le préfixe du thème (les fichiers `$__plugins_nikorion_*` des frères fuient sinon dans `wiki/tiddlers/system/`).
- Le fond d'écran (`images/vecteezy-summer-landscape-fields.svg`, 1,3 Mo) est livré dans le plugin ; Vanilla l'inline en data URI dans la feuille de page (base.tid), ce qui alourdit le CSS de chaque page. Alternative si gênant : le servir par `_canonical_uri` (retour à un fichier externe).

## Couleurs : dans la palette, pas ici
(2026-10-11, choix utilisateur.) **Aucune couleur en dur dans les feuilles** : elles lisent les variables CSS `--tpc-<clé>` (celles du cœur pour les clés Vanilla, celles de `TW-PKM-Palette/src/palette/styles/properties.tid` pour les clés propres `nk-…`), jamais `<<colour>>`. Les couleurs propres au thème (`nk-site-title`, `nk-hljs-*`, `nk-info-*`, `nk-interro-*`, `nk-alert-*`, `nk-light-*`, `nk-tiddler-border`, `nk-footnote-counter`, `nk-background`) sont des clés de la palette Nikorion (`TW-PKM-Palette/src/palette/palette.tid`) : **ce thème exige le plugin `palette`** ; avec une autre palette ces variables sont indéfinies (déclarations ignorées, pas de repli). Une nouvelle couleur = une clé `nk-…` dans la palette + `var(--tpc-nk-…)` ici.

## Dépendances hors plugin
- Aucun fichier externe : le logo du filet est le tiddler `images/logo-goat-mech.svg` du plugin, inliné en data URI par `hr.tid` (macro `datauri` ; feuille wikitext donc ligne `\rules only …`).
- Polices Inter, Palatino Linotype, JetBrains Mono : installées sur la machine (ou Font Manager).

## Structure
```
src/pkm-theme/
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
