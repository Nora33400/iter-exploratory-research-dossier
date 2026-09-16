# Lot 777 - projets et prototypes a completer

Statut: ajout public de revue, non destructif, prepare le 2026-09-16.

Ce dossier ajoute les elements demandes autour de `COSMOS`, `TimeWarp` et `FractalFormulaCorpus`. Il ne publie pas les racines originales completes. Les fichiers volumineux, les archives internes non auditees, les historiques d'assistant, les corpus massifs et les elements pouvant contenir des donnees privees restent exclus du depot public.

## Portee du lot

| Element | Source locale auditee | Statut public | Decision |
| --- | --- | --- | --- |
| COSMOS | lecteur S | Prototype conceptuel / documentation | Selection Markdown copiee dans `COSMOS/`. |
| TimeWarp | lecteurs S et F | Prototype / hypothese de modele temporel | Inventaire d'archives publie, contenu complet a relire avant depot dedie. |
| FractalFormulaCorpus | lecteurs S et F, Drive public a verifier | Corpus exploratoire | Manifestes d'archives publies; corpus brut non pousse. |
| Projets zippes/dezippes | archives detectees dans les racines ciblees | A verifier | References par SHA-256, sans publication brute. |

## Regle de publication

- Un zip existant n'est pas republie sans extraction, scan de secrets, verification de licence et controle de taille.
- Un dossier dezippé n'est pas rezippe automatiquement s'il duplique une archive deja referencee.
- Les archives de plus de 100 Mo ne sont pas adaptees a GitHub classique et doivent passer par un stockage de release, Drive ou Git LFS apres validation.
- Les historiques de conversation, journaux d'assistant, caches, environnements et backups restent exclus.
- Les hypotheses scientifiques restent des cadres exploratoires; aucune originalite n'est consideree comme demontree.

## Fichiers publies dans ce lot

- `ROOT_SUMMARY_PUBLIC.csv`: resume statistique sans chemins locaux detailles.
- `COSMOS/`: selection de fichiers Markdown et manifeste racine.
- `TIMEWARP/ARCHIVE_INVENTORY_TIMEWARP_PUBLIC.csv`: archives detectees sur S/F avec SHA-256.
- `FRACTAL_FORMULA_CORPUS/ARCHIVE_INVENTORY_FRACTAL_FORMULA_CORPUS_PUBLIC.csv`: chunks et archives du corpus avec SHA-256.
- `LOT_777_EXCLUSIONS.md`: exclusions et raisons.

## Lien Drive

Le dossier Drive public indique par Nora est reference comme emplacement documentaire externe:

https://drive.google.com/drive/folders/1HeYt9b7QejQBD0Lzj4S5LD1AE2KJfFIq?usp=sharing

Le lien repond au moment de la verification HTTP, mais le contenu Drive lui-meme doit encore etre compare fichier par fichier avec les copies locales si une synchronisation scientifique stricte est souhaitee.
