# Lot 777 - exclusions publiques

Ce fichier documente les exclusions appliquees pour eviter de publier des donnees non relues, trop lourdes ou potentiellement sensibles.

## Exclusions automatiques

| Element | Raison |
| --- | --- |
| `hashkiller24.txt` | Fichier d'environ 3 Go, non lie directement au dossier ITER public, risque de contenu inadapte. |
| Archives COSMOS internes (`*.zip`) | Non extraites et non auditees fichier par fichier dans ce passage. |
| Packs NIFFOI historiques | A relire separement: peuvent contenir des donnees personnelles, drafts ou exports non publics. |
| `Ia.zip` | Archive non auditee. |
| Archives TimeWarp volumineuses | Certaines archives depassent 90 Mo ou 180 Mo; publication GitHub directe non recommandee. |
| `.aider.chat.history.md` et journaux similaires | Historique d'assistant potentiellement prive ou bruyant. |
| Backups, staging, caches, environnements | Non pertinents pour un dossier public propre. |
| Chunks bruts FractalFormulaCorpus | Environ 4,11 Go par copie locale; necessite revue de licence, doublons et contenu. |

## Exclusions par principe scientifique

- Aucune hypothese n'est presentee comme resultat etabli.
- Les contenus nommes `TimeWarp`, `COSMOS` ou `FractalFormulaCorpus` sont des objets de travail a formaliser, pas des preuves.
- Les liens avec ITER, les plasmas ou la modelisation energetique restent des pistes d'orientation et doivent etre evalues par des specialistes.

## Prochaine etape recommandee

Creer des depots dedies et minimaux, un par axe, apres relecture:

1. `timewarp-exploratory-model`
2. `cosmos-fractal-knowledge-model`
3. `fractal-formula-corpus-manifest`

Chaque depot devrait contenir un README, une licence/statut, un manifeste SHA-256, une selection courte de fichiers et un rapport de securite.
