# Projets et prototypes a revoir pour GitHub

Statut: synthese publique issue d'un audit local non destructif des lecteurs S, F et B.

Cette page ne publie pas les inventaires bruts, les chemins internes detailles, les journaux de scan, ni les fichiers exclus. Elle sert de feuille de route pour completer progressivement les comptes GitHub `Nora33400` et `ErrorCat04` avec des projets propres, separes et relus.

## Regles avant tout nouveau push

- Ne pas pousser un dossier entier sans copie publique expurgee.
- Scanner les secrets, tokens, cles API, donnees privees, fichiers medicaux, bancaires ou d'identite.
- Verifier les licences et dependances tierces.
- Distinguer projet original, dependance externe, backup, worktree, environnement virtuel et export public.
- Publier par petits lots, avec README, licence/statut, manifestes SHA-256 et limites.

## Resultat de l'audit local

- Racines ciblees inspectees: familles AIONE, FractalOS, TimeWarp, qiskit/quantum, CoherentAI, ForgeAI, AgoraMirror, LayerOS, MDVWE, Noralios, TileMindFS, FOR-T, projets Nora, B: CoherentAI et B: Systemique Changes.
- Inventaire local complet conserve hors GitHub dans le dossier de travail.
- Aucune publication automatique de ces projets n'a ete effectuee pendant cet audit.

## Deja relies a des depots GitHub

Ces projets semblent deja avoir une remote GitHub ou un export public. Action recommandee: verifier l'etat local, les secrets, puis synchroniser seulement si le contenu est propre.

| Famille | Depot / export | Action prudente |
| --- | --- | --- |
| Portfolio IA | `ai-systems-infrastructure-portfolio` | Verifier README, statut scientifique et droits des contenus avant sync. |
| TileMindFS public innovations | `adaptive-process-governor` | Revoir puis sync si aucun secret ni fichier prive. |
| TileMindFS public innovations | `contextualizer-decompose-kit` | Revoir puis sync si aucun secret ni fichier prive. |
| TileMindFS public innovations | `derived-adaptive-process-learning` | Revoir puis sync si aucun secret ni fichier prive. |
| TileMindFS public innovations | `derived-contextualizer-endpoint-map` | Revoir puis sync si aucun secret ni fichier prive. |
| TileMindFS public innovations | `derived-intent-proof-ranker` | Revoir puis sync si aucun secret ni fichier prive. |
| TileMindFS public innovations | `derived-seedcube-state-timeline` | Revoir puis sync si aucun secret ni fichier prive. |
| TileMindFS public innovations | `derived-xconcept-autorepair` | Revoir puis sync si aucun secret ni fichier prive. |
| TileMindFS public innovations | `intent-router-hybrid-search` | Revoir puis sync si aucun secret ni fichier prive. |
| TileMindFS public innovations | `seedcube-state-engine` | Revoir puis sync si aucun secret ni fichier prive. |
| TileMindFS public innovations | `xconcept-error-engine` | Revoir puis sync si aucun secret ni fichier prive. |
| Coherenta/Core | `coherenta-core` | Dedoublonner les copies avant sync. |

## Candidats a nouveaux depots apres scan

Ces familles ont ete reperees comme projets/prototypes locaux plausibles. Elles ne doivent pas etre poussees telles quelles sans selection publique.

| Priorite | Famille | Pourquoi | Prochaine etape |
| --- | --- | --- | --- |
| Haute | AIONE Product Workspace | Workspace canonique, nombreux modules actifs. | Ne pas pousser le monorepo entier; preparer exports par module. |
| Haute | FractalOS / AIONE / Ecosystem | Source historique et prototypes. | Identifier l'autorite canonique et exclure backups/worktrees. |
| Haute | CoherentAI | Prototype autodidacte et runtime contexte. | Publier une copie propre si tests et licences passent. |
| Haute | TimeWarp | Axe central du dossier ITER. | Creer depot dedie avec modele, variables, simulation jouet et limites. |
| Haute | qiskit / quantum exploratoire | Simulations et modeles quantiques exploratoires. | Publier seulement scripts reproductibles et `MODEL_DEFINED / EXPLORATORY`. |
| Moyenne | AgoraMirror | Prototype application/recherche multi-source. | Verifier tokens, API, feeds et dependances avant publication. |
| Moyenne | ForgeAI / AIONE Forge | Prototypes agents/forge. | Exclure tokens, runtime local et donnees utilisateur. |
| Moyenne | LayerOS / HUD | Prototypes interface/HUD. | Publier seulement code source propre et instructions de build. |
| Moyenne | Noralios / CalContexte | Prototypes contexte/calendrier/methode. | Verifier donnees personnelles et exports. |
| Moyenne | FOR-T builders | Prototypes constructeurs autonomes. | Relire autonomie, permissions, scripts et limites. |
| Moyenne | MDVWE | Projet local a clarifier. | Identifier objectif, licence et contenu publiable. |
| Basse | Systemique Changes | Corpus/documentation source. | Ne pas publier brut; produire synthese expurgee seulement. |

## A exclure ou traiter separement

- Dependances tierces clonees depuis GitHub.
- Worktrees Forge et dossiers d'execution temporaires.
- Environnements Python ou Node (`.venv`, `node_modules`, caches).
- Backups, restores, dossiers generes et exports non relus.
- Inventaires contenant chemins locaux ou informations privees.

## Lot conseille pour la prochaine publication

1. `TimeWarp` comme depot de modele/simulation minimal.
2. `CoherentAI` comme depot prototype local avec tests.
3. `qiskit / quantum exploratoire` comme depot de simulations reproductibles, avec limites scientifiques.
4. `AIONE Product Workspace` par modules separes, pas en monorepo brut.
5. Exports TileMindFS deja publics: verifier et synchroniser en dernier.
