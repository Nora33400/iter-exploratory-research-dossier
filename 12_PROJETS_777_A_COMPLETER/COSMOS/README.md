# COSMOS-F — Pack de prompts de pilotage

Ce ZIP contient trois prompts complémentaires pour piloter COSMOS-F/AIONE comme un projet long, transversal, versionné et reprenable.

## 01 — Direction de projet
Fichier : `01_PROMPT_DIRECTION_DE_PROJET.md`

À utiliser :
- au lancement d'une nouvelle phase ;
- après une demande importante ;
- pour établir North Star, Goal DAG, plans candidats, gates, roadmap et prochaine action.

Produit notamment :
- `PROJECT_NORTH_STAR`
- `GOAL_DAG`
- `EXECUTION_DAG`
- `TIMEWARP_CHECKPOINT`

## 02 — Structuration transversale
Fichier : `02_PROMPT_STRUCTURATION_TRANSVERSALE.md`

À utiliser :
- lorsqu'il existe plusieurs modules/repos/documents/branches ;
- avant une réorganisation ;
- pour aligner schémas, interfaces, événements, vocabulaire et invariants sans fusion abusive.

Produit notamment :
- registry d'entités ;
- identity resolution matrix ;
- dependency graph ;
- interface catalog ;
- invariant matrix ;
- `TRANSVERSAL_HANDOFF`.

## 03 — Continuité objective
Fichier : `03_PROMPT_CONTINUITE_OBJECTIVE.md`

À utiliser :
- au début d'une nouvelle session ;
- après interruption/crash ;
- lors d'un changement d'agent ;
- pour reprendre un plan sans perdre l'objectif.

Produit notamment :
- état de continuité ;
- delta depuis checkpoint ;
- drift report ;
- current frontier ;
- une seule prochaine action ;
- nouveau checkpoint ;
- mini-prompt de reprise.

## Ordre recommandé

```text
Direction de projet
    ↓
Structuration transversale
    ↓
Exécution / validation / simulation
    ↓
Continuité objective
    ↺
Direction / replanification si nécessaire
```

## Invariants transversaux

```text
EXPLORER != VALIDER != EXECUTER
POSSIBLE != VRAI != ACTIVABLE != EXECUTE
CAPABILITY != PERMISSION
SIMULATION != OBSERVATION
MODEL != WORLD
COUNTERFACTUAL != HISTORY
CHRONOLOGIE != CAUSALITE
VIEW_PAST != ACTIVE_REVERT
ROLLBACK != COMPENSATION
PARTIAL != COMPLETE
UNKNOWN != FALSE
```

## Fichiers sources attendus

Le pack a été construit pour fonctionner avec les Structures COSMOS-F 01 à 10 :
1. Nœud Conceptuel Universel
2. Algèbre des opérateurs
3. Moteur de branches concurrentes
4. Zoom fractal et coordonnées multi-échelles
5. Budget global et régulateur
6. Stabilisation / checkpoints / rollback / recovery
7. TimeWarp
8. Orchestrateur multi-espace
9. Validation épistémique
10. Simulation multi-mondes

## Utilisation avec un agent

Colle d'abord le prompt approprié, puis fournis :
- les structures COSMOS-F utiles ;
- le checkpoint courant ;
- la demande utilisateur ;
- l'état vérifié du workspace si exécution technique.

Le prompt impose de marquer `UNKNOWN` ce qui n'est pas fourni plutôt que de le deviner.
