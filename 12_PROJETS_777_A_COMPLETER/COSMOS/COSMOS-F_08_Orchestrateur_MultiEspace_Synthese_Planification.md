# COSMOS-F - Structure 08
## Orchestrateur multi-espace, synthese transversale et planification adaptative

**Version :** 0.8  
**Statut :** specification conceptuelle, mathematique et technique  
**Dependances :** Structure 01 - NCU ; Structure 02 - Algebre COSMOS-F ; Structure 03 - Moteur de branches concurrentes ; Structure 04 - Zoom fractal ; Structure 05 - Regulateur multi-ressources ; Structure 06 - Stabilisation/recovery.  
**Interface attendue :** Structure 07 - TimeWarp complet, sans en supposer ici l'implementation detaillee.

---

## Resume

La Structure 08 definit la couche d'orchestration qui transforme une demande complexe en un **plan explicite, multi-espace, multi-branche, multi-echelle et multi-agent**.

Elle ne remplace aucun sous-systeme. Elle coordonne :

```text
NCU            -> objets conceptuels
MBC            -> hypotheses concurrentes
Navigator      -> espaces, frames, dimensions et echelles
Regulator      -> budgets et politiques de charge
Recovery       -> checkpoints, rollback et escalation
TimeWarp       -> historique et causalite
Forge          -> execution et production
```

L'orchestrateur doit pouvoir :

```text
demande
-> analyser l'intention
-> decomposer en sous-objectifs
-> identifier les espaces pertinents
-> construire plusieurs plans possibles
-> estimer couts, risques et gains
-> selectionner un plan admissible
-> executer par etapes
-> observer les resultats
-> replanifier
-> synthetiser sans effacer les contradictions
-> livrer un resultat tracable
```

Principe central :

```text
orchestration != fusion
```

Coordonner plusieurs espaces ne signifie pas les reduire a un seul modele.

Autre principe :

```text
planifier != predeterminer
```

Le plan est revise lorsque les observations changent.

# 1. Role de la Structure 08

Les Structures 01 a 06 definissent des mecanismes puissants mais locaux.

La Structure 08 repond a une question differente :

```text
"Dans quel ordre, avec quelles ressources, dans quels espaces,
et avec quels criteres doit-on utiliser ces mecanismes pour atteindre un objectif ?"
```

Elle agit comme :
- planificateur ;
- routeur ;
- coordinateur ;
- moteur de synthese ;
- controleur de progression.

Elle ne declare pas seule qu'une hypothese est vraie et ne contourne jamais les gates OCCP.


# 2. Entree canonique : GoalRequest

Une demande est normalisee en :

```text
GoalRequest = (
  request_id,
  raw_input,
  goals,
  constraints,
  preferences,
  context_refs,
  deadlines,
  quality_target,
  risk_tolerance,
  permissions,
  output_contract
)
```

Exemple :

```json
{
  "request_id": "req_001",
  "goals": ["comprendre", "comparer", "produire un prototype"],
  "constraints": ["local-only", "reversible"],
  "quality_target": "high",
  "output_contract": ["markdown", "evidence-map"]
}
```

Le texte original reste conserve dans la provenance.


# 3. Intentions multiples

Une demande peut porter plusieurs intentions :

```text
INTENT = {
  SEARCH,
  EXPLAIN,
  COMPARE,
  DESIGN,
  TEST,
  IMPLEMENT,
  REPAIR,
  SYNTHESIZE,
  NAVIGATE,
  VALIDATE,
  MONITOR
}
```

On conserve un vecteur :

```text
I = [(intent_1,w_1),...,(intent_n,w_n)]
```

plutot que d'imposer une seule classe.


# 4. Objectifs et sous-objectifs

Un objectif :

```text
G_i = (
  statement,
  success_criteria,
  dependencies,
  priority,
  scope,
  deadline
)
```

Decomposition :

```text
G_root
  -> G_1
  -> G_2
  -> ...
```

La decomposition forme un DAG et non necessairement un arbre.

Un sous-objectif peut servir plusieurs objectifs parents.


# 5. DAG de planification

Le plan :

```text
P = (V,E)
```

avec :
- `V` : taches ;
- `E` : dependances.

Une tache :

```text
Task = (
  id,
  operator,
  inputs,
  outputs,
  resources,
  permissions,
  preconditions,
  postconditions,
  rollback,
  verification
)
```

Le DAG rend explicites :
- parallelisme ;
- blocages ;
- ordre causal ;
- points de synchronisation.


# 6. Types de taches

Categories :

```text
OBSERVE
RETRIEVE
GENERATE
COMPARE
SIMULATE
VALIDATE
TRANSFORM
NAVIGATE
EXECUTE
SYNTHESIZE
CHECKPOINT
RECOVER
PRESENT
```

Chaque type a un contrat de ressources et un niveau de risque par defaut.


# 7. Etats d'une tache

```text
PLANNED
READY
RUNNING
WAITING
BLOCKED
SUCCEEDED
PARTIAL
FAILED
CANCELLED
ROLLED_BACK
COMPENSATED
SKIPPED
```

`PARTIAL` est important : le plan peut continuer si les criteres le permettent.


# 8. Etats d'un plan

```text
DRAFT
VALIDATING
ADMISSIBLE
RUNNING
DEGRADED
REPLANNING
RECOVERING
COMPLETED
PARTIAL
FAILED
MANUAL_REQUIRED
```

Un plan peut finir `PARTIAL` tout en livrant un resultat utile et trace.


# 9. Espaces candidats

Pour un objectif `G`, l'orchestrateur determine :

```text
CandidateSpaces(G) = {S_1,...,S_k}
```

Chaque espace possede :
- domaine ;
- frame ;
- echelle ;
- statut epistemique ;
- cout d'entree ;
- pertinence.

On evite d'ouvrir tous les espaces disponibles.


# 10. Selection d'espaces

Score :

```text
Q_space =
relevance
* expected_information_gain
* interoperability
/
(1 + cost + risk)
```

On conserve un ensemble Pareto de candidats lorsque plusieurs espaces sont complementaires.


# 11. Federation multi-espace

La federation permet de coordonner plusieurs espaces sans les fusionner.

```text
Federation = {
  space_A,
  space_B,
  space_C
}
```

Chaque espace conserve :
- son schema ;
- ses identifiants ;
- ses regles ;
- ses statuts epistemiques.

La couche federatrice ne transforme pas silencieusement leurs conventions.


# 12. Namespaces

Identifiants :

```text
cosmos://space-a/node/42
cosmos://space-b/node/42
```

Ils sont differents.

Une resolution globale :

```text
GlobalRef = namespace + local_id
```

evite les collisions.


# 13. Ponts inter-espaces

Un bridge :

```text
Bridge = (
  source_ref,
  target_ref,
  mapping_type,
  confidence,
  loss,
  provenance
)
```

Types :

```text
EQUIVALENT_TO
ANALOGOUS_TO
TRANSLATES_TO
REFINES
PROJECTS_TO
PARTIALLY_OVERLAPS
```

`ANALOGOUS_TO` n'est jamais traite comme `EQUIVALENT_TO`.


# 14. Alignement semantique

Pour deux schemas :

```text
Align(S_A,S_B) -> MappingSet
```

Chaque correspondance porte :
- confiance ;
- exceptions ;
- perte ;
- hypotheses.

L'alignement peut rester partiel.

```text
unmapped != invalid
```


# 15. Synthese non destructive

La synthese produit :

```text
Synthesis = (
  common_core,
  convergences,
  divergences,
  unresolved_points,
  evidence_map,
  confidence_map,
  next_tests
)
```

Elle ne remplace pas les sources.

Les contradictions restent visibles.


# 16. Synthese a plusieurs niveaux

Niveaux :

```text
L0 raw evidence
L1 branch summaries
L2 space summaries
L3 cross-space synthesis
L4 decision-oriented synthesis
```

Le HUD ou le rapport peut zoomer d'un niveau a l'autre.


# 17. Synthese orientee objectif

Une meme federation peut produire des syntheses differentes.

Exemple :

```text
goal = comprendre
```

favorise :
- mecanismes ;
- nuances ;
- incertitudes.

```text
goal = decider
```

favorise :
- options ;
- risques ;
- criteres ;
- prochaine action.


# 18. Conservation des desaccords

On definit :

```text
DisagreementSet = {
  proposition,
  branch_A,
  branch_B,
  overlap_scope,
  evidence_A,
  evidence_B
}
```

Le moteur n'aplatit pas :

```text
A = 0.7
B = 0.6
```

en une pseudo-moyenne sans semantique.


# 19. Propagation d'incertitude

Une synthese derivee de plusieurs sources doit propager l'incertitude.

```text
U_out = F(U_inputs, dependencies, mapping_loss)
```

Si plusieurs elements dependent de la meme source primaire, leur combinaison n'augmente pas artificiellement la confiance.


# 20. Dependances de preuve

On construit un graphe :

```text
EvidenceDependencyGraph
```

pour detecter :
- sources communes ;
- reprises ;
- simulations partageant les memes hypotheses ;
- sorties d'un meme modele.

La synthese affiche le nombre de sources reellement independantes.


# 21. Plan candidat

Un candidat :

```text
PlanCandidate = (
  tasks,
  expected_gain,
  expected_cost,
  risk,
  reversibility,
  coverage,
  latency,
  quality
)
```

Plusieurs plans sont generes avant selection.


# 22. Strategies de plan

Modes :

```text
FAST
BALANCED
DEEP
CONSERVATIVE
DIVERGENT
RECOVERY_FIRST
EVIDENCE_FIRST
LOCAL_ONLY
```

Ces modes modifient les poids, pas les invariants.


# 23. Evaluation multi-objectifs

On optimise :

```text
maximize:
  coverage
  information_gain
  robustness
  explainability

minimize:
  cost
  latency
  risk
  irreversible_effects
```

Le resultat peut etre un front de Pareto.


# 24. Selection de plan

Forme :

```text
P* =
argmax_P
[
  U(P)
  - lambda_C C(P)
  - lambda_R R(P)
]
```

sous :

```text
permissions(P) = OK
budget(P) = OK
quality(P) >= Q_min
recovery(P) available
```


# 25. Planification hierarchique

Le planificateur peut utiliser :

```text
HTN - Hierarchical Task Networks
```

Exemple :

```text
"valider une hypothese"
  -> definir prediction
  -> identifier test
  -> collecter donnees
  -> analyser
  -> comparer prediction/resultat
```

Les methodes HTN restent versionnees.


# 26. Planification par operateurs

La Structure 02 fournit les operateurs.

Une route peut etre :

```text
ANCHOR
-> PERSPECT
-> DOWN
-> SPLIT
-> COMPARE
-> VALIDATE
-> SYNTHESIZE
```

Le planificateur compile les objectifs vers cette algebre.


# 27. Preconditions et postconditions

Chaque tache declare :

```text
pre(task)
post(task)
```

Une tache devient `READY` seulement si ses preconditions sont satisfaites.

Apres execution, les postconditions sont verifiees avant de debloquer les dependants.


# 28. Planification conditionnelle

Un plan peut contenir :

```text
IF evidence supports H1:
    route A
ELSE:
    route B
```

Le plan n'a donc pas besoin d'etre totalement lineaire.


# 29. Points de decision

Un `DecisionNode` contient :
- alternatives ;
- criteres ;
- donnees requises ;
- strategie de tie-break ;
- permissions.

Il est journalise comme evenement distinct.


# 30. Replanification

Declencheurs :
- tache echouee ;
- cout reel trop eleve ;
- nouvelle preuve ;
- budget change ;
- utilisateur modifie objectif ;
- branche dominante change ;
- recovery event.

```text
P_{t+1} = Replan(P_t, observations)
```


# 31. Replanification locale

On prefere modifier le plus petit sous-graphe possible.

```text
repair_plan_subgraph
```

plutot que reconstruire tout le plan.

Cela preserve :
- taches deja validees ;
- caches ;
- preuves ;
- provenance.


# 32. Replanification globale

Necessaire si :
- objectif change ;
- hypothese centrale refutee ;
- dependance majeure disparait ;
- permission critique retiree.

Le plan precedent reste archive.


# 33. Plan versionne

```text
Plan v1
Plan v2
Plan v3
```

Chaque nouvelle version reference :
- precedente ;
- raison ;
- deltas ;
- observations declencheuses.

TimeWarp pourra rejouer l'evolution.


# 34. Execution speculative

Certaines branches du plan peuvent etre simulees :

```text
SIMULATE_PLAN(P)
```

sans effets externes.

On estime :
- cout ;
- conflicts ;
- saturation ;
- risques ;
- points de blocage.


# 35. Modes d'execution

```text
PREVIEW
DRY_RUN
SANDBOX
APPLY
```

`PREVIEW` :
- affiche le plan.

`DRY_RUN` :
- execute les controles sans mutation.

`SANDBOX` :
- execute en environnement isole.

`APPLY` :
- autorise les mutations permises.


# 36. Contrat d'application

Pour passer en `APPLY` :

```text
PlanApplyContract = (
  permissions,
  checkpoints,
  rollback,
  budget,
  verification,
  blast_radius
)
```

Sans contrat suffisant, l'orchestrateur reste en mode non mutatif.


# 37. Orchestration multi-agent

Les agents ont des roles :

```text
PLANNER
RESEARCHER
BUILDER
TESTER
REVIEWER
RECOVERY
SYNTHESIZER
```

Un role n'est pas une identite fixe : un agent peut remplir plusieurs roles selon politique.


# 38. Contrat d'agent

```text
AgentContract = (
  capabilities,
  tools,
  permissions,
  resource_limits,
  accepted_inputs,
  output_schema,
  failure_modes
)
```

L'orchestrateur ne route pas une tache a un agent ne satisfaisant pas le contrat.


# 39. Registry de capacites

```text
CapabilityRegistry
```

expose :

```text
capability -> providers
```

Exemple :

```text
"python-test" -> [agent_local_1]
"semantic-search" -> [agent_local_2, service_x]
```

Le choix tient compte des contraintes local-only ou prive.


# 40. Routing de taches

Score :

```text
Q_route(agent,task) =
capability_fit
* reliability
* locality
* availability
/
(1 + cost + latency)
```

Les permissions restent des contraintes dures.


# 41. Redondance d'agents

Pour une tache critique :

```text
N-of-M verification
```

peut etre utilisee.

Mais plusieurs agents utilisant la meme source/model ne comptent pas automatiquement comme validations independantes.


# 42. Separation builder/reviewer

Politique possible :

```text
builder != reviewer
```

pour les changements critiques.

Le reviewer verifie :
- contrat ;
- tests ;
- provenance ;
- risques ;
- sortie.


# 43. Message bus

Bus :

```text
EventBus
```

Evenements :

```text
TASK_READY
TASK_STARTED
TASK_RESULT
TASK_FAILED
BUDGET_CHANGED
BRANCH_UPDATED
CHECKPOINT_CREATED
RECOVERY_STARTED
PLAN_REVISED
```

Les consommateurs s'abonnent sans couplage direct excessif.


# 44. Enveloppe d'evenement

```json
{
  "event_id": "evt_...",
  "type": "TASK_RESULT",
  "correlation_id": "req_001",
  "causation_id": "task_17",
  "timestamp": "...",
  "payload_ref": "..."
}
```

La provenance traverse tout le bus.


# 45. Commandes vs evenements

Commande :

```text
"faites X"
```

Evenement :

```text
"X s'est produit"
```

On ne les confond pas.

Cela facilite :
- replay ;
- idempotence ;
- audit.


# 46. Bus local et adaptateurs

Le coeur peut etre local.

Adaptateurs :
- fichiers ;
- processus ;
- APIs ;
- MCP ;
- plugins ;
- DB ;
- queue.

Chaque adaptateur declare :
- capacites ;
- securite ;
- schemas ;
- erreurs.


# 47. Permission Broker

Le broker evalue :

```text
Allow(subject, action, object, context)
```

Retour :

```text
ALLOW
DENY
ASK
ALLOW_WITH_LIMITS
```

Les limites peuvent porter sur :
- scope ;
- nombre d'actions ;
- duree ;
- ressources ;
- profondeur autonome.


# 48. Capacite != permission

Invariant :

```text
can_do(task) != may_do(task)
```

Un agent techniquement capable d'ecrire un fichier ne recoit pas automatiquement l'autorisation.


# 49. Scopes de permission

Exemples :

```text
read:workspace
write:generated-only
execute:tests
network:none
install:ask
external-action:deny
```

Les scopes sont inclus dans le plan.


# 50. Heritages de permission

Une sous-tache ne peut pas augmenter ses permissions au-dela du parent sans nouvelle autorisation.

```text
P_child subseteq P_parent
```

sauf delegation explicite.


# 51. Contexte minimal

Chaque tache recoit uniquement le contexte necessaire :

```text
Context(task) = minimal_sufficient_subset
```

Avantages :
- cout ;
- confidentialite ;
- reduction de bruit ;
- explicabilite.


# 52. Context packing

Le pack contient :
- objectif local ;
- donnees necessaires ;
- contraintes ;
- references ;
- schema de sortie ;
- provenance.

Le texte complet de toute la session n'est pas envoye par defaut.


# 53. Memoire de travail vs memoire persistante

```text
working_context != persistent_memory
```

L'orchestrateur peut :
- charger ;
- resumer ;
- ejecter ;
- recharger.

Les informations critiques restent dans les ledgers persistants.


# 54. Couche de synthese

`SynthesisEngine` recoit :
- sorties de taches ;
- branches ;
- preuves ;
- conflits ;
- provenance.

Il produit :
- reponse ;
- carte de support ;
- limites ;
- prochaines etapes.


# 55. Regles de synthese

```text
S1. Ne pas masquer les contradictions pertinentes.
S2. Ne pas augmenter artificiellement la confiance.
S3. Distinguer resultat source, inference et simulation.
S4. Indiquer les zones non couvertes.
S5. Conserver les references de provenance.
S6. Adapter le niveau de detail a l'objectif.
```


# 56. Fusion de resultats compatibles

Deux resultats compatibles peuvent etre combines si :
- scopes compatibles ;
- termes alignes ;
- provenance conservee ;
- contradictions absentes ou explicites.

La fusion produit un rapport de merge.


# 57. Resultats incompatibles

On produit :

```text
CONFLICTED_SYNTHESIS
```

avec :
- position A ;
- position B ;
- preuve discriminante manquante ;
- prochaines actions possibles.

Un conflit non resolu est un resultat valide.


# 58. Resultat partiel

Une tache manquante n'annule pas forcement la synthese.

```text
coverage = 0.72
```

Le livrable indique :
- ce qui est couvert ;
- ce qui manque ;
- impact probable.


# 59. Contrat de sortie

```text
OutputContract = (
  format,
  sections,
  evidence_requirements,
  max_length,
  machine_readable_schema,
  artifact_types
)
```

Exemple :
- Markdown ;
- PDF ;
- JSON de provenance ;
- graphe.


# 60. Verification de sortie

Avant livraison :

```text
ValidateOutput
```

controle :
- criteres utilisateur ;
- coherence ;
- fichiers ;
- references ;
- absence de contradictions masquees ;
- statuts epistemiques.


# 61. Budget d'orchestration

Le plan lui-meme consomme des ressources.

```text
B_orch = (
  planning_time,
  planner_tokens,
  coordination_messages,
  scheduling_overhead
)
```

Un plan excessivement sophistique peut couter plus que la tache.


# 62. Seuil de planification

Pour une tache simple :

```text
direct_execute
```

Pour une tache complexe :

```text
explicit_plan
```

Critere :

```text
planning_value > planning_cost
```

Cela evite la sur-orchestration.


# 63. Parallelisme

Taches independantes :

```text
A || B
```

peuvent etre paralleles si :
- ressources disponibles ;
- pas de conflit de lock ;
- pas de dependance cachee.

Le regulateur peut reduire le parallelisme.


# 64. Synchronisation

Un `JOIN` attend :

```text
ALL
ANY
QUORUM
TIMEOUT
```

selon le plan.

Exemple :
- attendre 2 validations sur 3 ;
- continuer avec resultat partiel apres deadline.


# 65. Barriere de preuve

Une tache d'action peut dependre d'une barriere :

```text
EVIDENCE_BARRIER
```

qui exige :
- niveau de preuve ;
- tests ;
- accord de reviewer ;
- permission.

Cela separe explicitement exploration et execution.


# 66. Verrouillage

Locks :

```text
READ
WRITE
EXCLUSIVE
RECOVERY
```

Portee :
- objet ;
- fichier ;
- service ;
- espace ;
- config.

Les locks evitent des modifications concurrentes incoherentes.


# 67. Detection de deadlock

Construire :

```text
WaitForGraph
```

Si cycle :
- interrompre tache moins prioritaire ;
- rollback ;
- replanifier.

Le choix est journalise.


# 68. Idempotence des taches

Chaque tache declare :

```text
idempotent: true/false/unknown
```

Une tache `unknown` n'est pas automatiquement relancee apres crash.


# 69. Reprise apres crash

Au boot :

```text
RecoverPlan
```

classe les taches :

```text
CONFIRMED_DONE
CONFIRMED_NOT_DONE
UNKNOWN
```

Les `UNKNOWN` sont verifies avant repetition.


# 70. Checkpoints de plan

Un checkpoint de plan contient :
- version ;
- taches terminees ;
- sorties ;
- reservations ;
- locks ;
- prochaine frontiere.

Il permet une reprise sans reexecuter l'ensemble.


# 71. Recovery d'orchestration

En cas d'echec :
1. geler nouvelles taches ;
2. checkpoint ;
3. identifier scope ;
4. appeler Structure 06 ;
5. reparer ;
6. revalider sorties ;
7. replanifier localement.


# 72. QuickResolution/Doctor dans le plan

Le planificateur peut injecter :

```text
RECOVERY_SUBPLAN
```

Lorsque plusieurs echecs simples surviennent.

Doctor devient une tache specialisee avec acces aux preuves de plan.


# 73. ConnectBusBot dans le plan

Si un agent ou un service devient inaccessible :
- verifier liaison ;
- contrats ;
- routes ;
- versions ;
- reconnecter.

Puis restituer la tache au plan principal.


# 74. Immune et suspension globale

Immune peut envoyer :

```text
GLOBAL_FREEZE(scope)
```

L'orchestrateur suspend les taches affectees, preserve les preuves et attend la restauration.


# 75. Quality gates

Exemples :

```text
GATE_SCHEMA
GATE_TESTS
GATE_EVIDENCE
GATE_SECURITY
GATE_PERMISSIONS
GATE_REVERSIBILITY
GATE_OUTPUT
```

Chaque gate produit un resultat explicable.


# 76. Critere de progression

Le plan avance si :

```text
ProgressGain > epsilon
```

Sinon :
- changer de strategie ;
- chercher nouvelle information ;
- stopper.

Cela evite des boucles de "travail" sans gain.


# 77. Detection de stagnation

Signaux :
- memes resultats ;
- memes erreurs ;
- confiance stable ;
- cout croissant ;
- aucun nouveau noeud pertinent.

```text
STAGNATION -> REPLAN or STOP
```


# 78. Conditions d'arret

```text
SUCCESS
QUALITY_TARGET_REACHED
BUDGET_EXHAUSTED
DEADLINE
NO_PROGRESS
RISK_LIMIT
PERMISSION_BOUNDARY
MANUAL_REQUIRED
```

Le stop reason apparait dans la synthese.


# 79. Mesure de couverture

```text
Coverage =
covered_required_aspects
/
total_required_aspects
```

Les aspects peuvent etre ponderes.

La couverture ne mesure pas la verite, seulement la completude par rapport au contrat.


# 80. Mesure de robustesse

Robustesse :
- plusieurs sources ;
- tests ;
- contre-hypotheses ;
- sensibilite.

```text
RobustnessScore
```

est affiche separement de la confiance.


# 81. Trace de decision

Chaque choix de plan conserve :

```text
DecisionRecord = (
  alternatives,
  selected,
  criteria,
  scores,
  rejected_reasons,
  evidence
)
```

Cela rend le plan explicable.


# 82. Explication utilisateur

Le systeme peut produire deux niveaux :
- vue courte ;
- vue audit.

La vue audit montre :
- plan ;
- espaces ouverts ;
- branches ;
- gates ;
- changements de plan.


# 83. Graphe de provenance

```text
Output
 <- Synthesis
 <- TaskResults
 <- Evidence
 <- Sources
```

Le graphe permet de remonter de chaque conclusion vers ses supports.


# 84. Data lineage

Pour les transformations :

```text
source
-> normalization
-> projection
-> aggregation
-> output
```

Chaque etape porte :
- code/version ;
- parametres ;
- hash ;
- timestamp.


# 85. Isolation des experiences

Une experience speculative est executee dans :

```text
ExperimentScope
```

avec :
- ressources ;
- fichiers ;
- branches ;
- permissions dedies.

Ses sorties ne contaminent pas automatiquement l'espace stable.


# 86. Promotion d'un resultat experimental

Promotion :

```text
EXPERIMENTAL
-> REVIEWED
-> VALIDATED
-> STABLE
```

selon les gates.

La promotion cree un evenement et conserve l'origine experimentale.


# 87. Mode hypothese libre

L'orchestrateur peut autoriser une phase divergente :

```text
DIVERGE
```

avec faible cout d'action et zero execution externe.

Ensuite :

```text
CONVERGE
```

pour filtrer, comparer et tester.


# 88. Alternance divergence/convergence

Cycle :

```text
DIVERGE
-> CLUSTER
-> COMPARE
-> TEST
-> CONVERGE
-> STABILIZE
```

Cela formalise l'ouverture controlee des possibles.


# 89. Meta-planification

Le systeme peut choisir la strategie de planification elle-meme.

```text
MetaPlan = choose(
  direct,
  HTN,
  graph-search,
  branch-and-bound,
  anytime
)
```

selon la taille du probleme.


# 90. Strategie anytime

Le plan produit rapidement un premier resultat stable puis l'ameliore.

```text
R_0 -> R_1 -> ... -> R_n
```

A tout instant :

```text
best_stable_result
```

est livrable.


# 91. Plan minimal viable

Le planificateur peut d'abord construire :

```text
MVP_PLAN
```

qui satisfait les criteres essentiels.

Puis ajouter :
- profondeur ;
- variantes ;
- validations.

Cela evite de depenser tout le budget avant d'avoir un resultat utilisable.


# 92. Gestion des deadlines

Si deadline approche :
- reduire profondeur ;
- fermer branches secondaires ;
- prioriser synthese ;
- conserver liste des travaux non termines.

Le systeme n'invente pas la completion.


# 93. Gestion du changement de priorite

Une priorite utilisateur nouvelle peut preempter des taches.

```text
priority_update
```

declenche :
- suspension propre ;
- checkpoint ;
- reallocation.


# 94. Multi-utilisateur ou multi-acteur

Des objectifs peuvent appartenir a plusieurs acteurs.

```text
GoalOwner
```

Les permissions, priorites et vues sont distinctes.

Une synthese commune ne supprime pas les perspectives individuelles.


# 95. Conflits d'objectifs

Exemple :
- minimiser cout ;
- maximiser exhaustivite.

On represente :

```text
GoalConflict
```

et propose plusieurs compromis Pareto.


# 96. Politique de compromis

```text
Policy = (
  weights,
  hard_constraints,
  tie_breakers
)
```

Elle est explicite et versionnee.

Le moteur ne cache pas les arbitrages.


# 97. Mode local-first

Politique :

```text
LOCAL_FIRST
```

Ordre :
1. outils locaux ;
2. donnees locales ;
3. services autorises ;
4. externe si permission.

Compatible avec une Forge locale.


# 98. Mode privacy-first

Minimise :
- export de donnees ;
- contexte transmis ;
- identifiants.

Les sous-taches externes recoivent seulement des donnees autorisees.


# 99. Mode evidence-first

Avant generation longue :
- inventorier sources ;
- verifier qualite ;
- identifier lacunes.

Puis seulement construire la synthese.


# 100. Mode build-first

Pour prototype :
- squelette minimal ;
- test smoke ;
- iteration.

La documentation et les optimisations viennent apres un executable minimal stable, selon politique.


# 101. Mode repair-first

Pour incident :
- geler expansions ;
- observer ;
- isoler ;
- QuickResolution/Doctor ;
- verifier ;
- reprendre plan.


# 102. API conceptuelle

```text
POST /orchestrator/request
GET  /plans/{id}
POST /plans/{id}/validate
POST /plans/{id}/run
POST /plans/{id}/pause
POST /plans/{id}/replan
POST /plans/{id}/cancel

GET  /plans/{id}/tasks
GET  /plans/{id}/provenance
GET  /plans/{id}/conflicts
GET  /plans/{id}/coverage

POST /synthesis/build
POST /synthesis/validate
```


# 103. DSL d'orchestration

```text
GOAL "formaliser concept X"
CONSTRAINT local-only
QUALITY high
PLAN MODE evidence-first
OPEN SPACE cognition
OPEN SPACE software
PARALLEL compare,search
JOIN ALL
SYNTHESIZE PRESERVE_CONFLICTS
VALIDATE evidence
OUTPUT markdown,pdf
```


# 104. DSL conditionnel

```text
IF confidence(H1) < 0.6:
    SPLIT H1 MODE adversarial
    TEST discriminating
ELSE:
    VALIDATE H1
```

Le DSL compile vers le DAG.


# 105. Schema SQLite

```sql
plans(
  plan_id TEXT,
  version INTEGER,
  status TEXT,
  goal_json TEXT,
  policy_json TEXT,
  created_at TEXT,
  PRIMARY KEY(plan_id, version)
);

tasks(
  task_id TEXT PRIMARY KEY,
  plan_id TEXT,
  plan_version INTEGER,
  type TEXT,
  status TEXT,
  contract_json TEXT
);

task_dependencies(
  parent_task TEXT,
  child_task TEXT,
  condition_json TEXT
);

plan_events(
  event_id TEXT PRIMARY KEY,
  plan_id TEXT,
  event_type TEXT,
  payload_json TEXT,
  timestamp TEXT
);

syntheses(
  synthesis_id TEXT PRIMARY KEY,
  plan_id TEXT,
  payload_json TEXT,
  provenance_ref TEXT
);
```


# 106. Architecture logicielle

```text
Orchestrator
├── IntentAnalyzer
├── GoalDecomposer
├── SpaceSelector
├── PlanGenerator
├── PlanEvaluator
├── TaskScheduler
├── AgentRouter
├── PermissionBrokerAdapter
├── EventBus
├── ContextPackager
├── SynthesisEngine
├── ConflictPreserver
├── OutputValidator
├── ProvenanceGraph
├── RegulatorAdapter
├── RecoveryAdapter
├── TimeWarpAdapter
├── ForgeAdapter
└── HUDAdapter
```


# 107. Interfaces minimales

```text
NCU:
  get_node
  expand
  link

MBC:
  branches
  split
  compare
  validate

Navigator:
  route
  zoom
  project

Regulator:
  estimate
  admit
  reserve

Recovery:
  checkpoint
  recover

TimeWarp:
  append
  state_at
  replay
```


# 108. Invariants d'orchestration

```text
O1. Une tache ne s'execute que si ses preconditions sont satisfaites.
O2. Capacite et permission restent separees.
O3. Toute mutation non triviale possede verification et strategie de retour.
O4. Une synthese conserve les conflits pertinents.
O5. Les sources dependantes ne sont pas comptees comme independantes.
O6. Toute replanification est versionnee.
O7. Une tache UNKNOWN apres crash est verifiee avant retry.
O8. Les sous-taches n'augmentent pas silencieusement les permissions.
O9. Le plan respecte le budget actif.
O10. Le resultat final indique les parties non couvertes.
O11. Les experiences ne sont pas promues automatiquement.
O12. Toute sortie peut etre reliee a sa provenance.
```


# 109. Invariants de synthese

```text
SYN1. Simulation != observation.
SYN2. Analogie != equivalence.
SYN3. Consensus d'agents != corroboration independante.
SYN4. Absence de preuve != preuve d'absence.
SYN5. Resultat partiel est marque comme partiel.
SYN6. Les pertes de mapping inter-espace sont exposees.
```


# 110. Tests unitaires

```text
test_goal_decomposition
test_dag_no_invalid_cycle
test_permission_inheritance
test_task_preconditions
test_plan_replan_version
test_partial_result
test_conflict_preserved
test_agent_capability_routing
test_unknown_task_not_retried_blindly
test_output_contract_validation
test_bridge_mapping_loss
test_budget_admission
```


# 111. Tests de proprietes

```text
P1. Every RUNNING task was READY previously.
P2. Every APPLY task passed permission gate.
P3. Replanning never deletes historical plan versions.
P4. Every synthesis claim has provenance or is explicitly marked inference.
P5. Plan active cost <= admissible budget.
P6. No child task has broader permissions than its parent without explicit delegation.
```


# 112. Tests de chaos

Injecter :
- agent indisponible ;
- tache lente ;
- resultat invalide ;
- bus redemarre ;
- budget chute ;
- permission retiree ;
- recovery event.

Verifier :
- replanification ;
- reprise ;
- pas de double action ;
- sortie partielle correcte.


# 113. Tests de charge

Scenarios :
- 1000 taches planifiees ;
- 100 espaces ;
- 500 branches ;
- 20 agents ;
- 10 plans concurrents.

Mesurer :
- latence de scheduling ;
- taille du DAG ;
- contention ;
- debit d'evenements ;
- cout de replanification.


# 114. Observabilite

Metriques :

```text
plans_active
tasks_ready
tasks_running
tasks_failed
replan_count
average_plan_depth
parallelism
agent_utilization
permission_denials
budget_denials
synthesis_conflict_count
coverage_mean
provenance_completeness
```


# 115. HUD orchestrateur

Vue :

```text
Goal
 |
Plan v3
 |-- T1 DONE
 |-- T2 RUNNING
 |-- T3 BLOCKED
 |-- T4 READY
 |
Synthesis coverage: 68%
Conflicts: 2
Budget: YELLOW
Recovery: IDLE
```

Actions :
- pause ;
- inspect task ;
- inspect branch ;
- replan ;
- change priority ;
- open provenance.


# 116. Vue DAG

Le HUD permet :
- zoom ;
- filtres ;
- couleur par statut ;
- groupement par espace ;
- groupement par agent ;
- chemin critique.

Le graphe est une vue, pas la source de verite.


# 117. Vue multi-espace

Panneaux :
- espace cognition ;
- espace technique ;
- espace preuve ;
- ponts.

Les bridges affichent :
- type ;
- confiance ;
- perte.

Le moteur montre les zones non mappees.


# 118. Exemple A - recherche multi-domaine

Objectif :

```text
"comparer un concept en psychologie, informatique et philosophie"
```

Plan :
1. ouvrir trois espaces ;
2. extraire definitions locales ;
3. aligner termes ;
4. construire bridges ;
5. detecter faux amis ;
6. conserver divergences ;
7. synthese multi-niveau.

Le resultat distingue clairement :
- analogies ;
- equivalences ;
- divergences.


# 119. Exemple B - prototype logiciel

Objectif :

```text
"creer un prototype local"
```

Plan :
- formaliser exigences ;
- identifier composants ;
- verifier environnement ;
- generer squelette ;
- tests smoke ;
- implementation incrementale ;
- validation ;
- artefact ;
- checkpoint.

Si une dependance casse :
- recovery subplan ;
- reprise de la frontier.


# 120. Exemple C - diagnostic complexe

Symptome :
- service instable.

Orchestrateur :
1. mode repair-first ;
2. stop explorations secondaires ;
3. ouvre espace runtime/config/network ;
4. MBC produit hypotheses ;
5. tests discriminants ;
6. QuickResolution puis Doctor si besoin ;
7. synthese incident ;
8. reprise du plan principal.


# 121. Exemple D - comparaison de deux modeles

Plan :
- definir dimensions ;
- fixer jeux de tests ;
- executer en parallele ;
- normaliser metriques ;
- conserver sorties brutes ;
- comparer ;
- analyser erreurs ;
- synthese.

Si cout GPU augmente :
- regulateur reduit parallelisme.


# 122. Exemple E - production d'un paper

Plan :
1. goal decomposition ;
2. espace theorie ;
3. espace preuves ;
4. espace contre-arguments ;
5. structure du paper ;
6. redaction par sections ;
7. validation terminologique ;
8. verification provenance ;
9. synthese finale ;
10. artefacts.

Les hypotheses non validees restent marquees comme telles.


# 123. Exemple F - divergence puis convergence

Question ouverte.

Phase 1 :

```text
DIVERGE -> 12 branches
```

Phase 2 :
- deduplication ;
- clustering ;
- Pareto ;
- tests.

Phase 3 :

```text
CONVERGE -> 4 branches fortes + 2 alternatives
```

Synthese :
- noyau commun ;
- divergences ;
- prochaines experiences.


# 124. Securite conceptuelle

Le systeme evite :
- fusion prematuree ;
- faux consensus ;
- sur-planification ;
- boucle sans gain ;
- escalade de permission ;
- replay non idempotent ;
- analogie presentee comme identite ;
- omission des zones inconnues.


# 125. Securite operationnelle

Avant `APPLY` :
- checkpoint ;
- permission ;
- budget ;
- tests ;
- rollback ;
- blast radius.

Les effets externes restent distincts des transformations internes.


# 126. Politique de minimisation du blast radius

Choisir :

```text
smallest_effective_scope
```

pour toute mutation.

Une tache globale n'est utilisee que si les alternatives locales sont insuffisantes ou plus dangereuses.


# 127. Critere d'autonomie

Autonomie autorisee si :

```text
bounded
reversible
observable
budgeted
permissioned
verifiable
```

Si une de ces proprietes manque, le plan passe en mode :
- preview ;
- ask ;
- manual.


# 128. Orchestration reflexive

Le systeme peut evaluer son propre plan :

```text
PlanCritique(P)
```

Questions :
- plan trop complexe ?
- espaces inutiles ?
- validations insuffisantes ?
- risques mal controles ?
- sortie conforme ?


# 129. Meta-evaluation

Apres completion :

```text
PlanOutcome = (
  success,
  cost,
  time,
  coverage,
  errors,
  recovery_events,
  user_value
)
```

Ces donnees ameliorent les strategies futures sans modifier retroactivement le resultat historique.


# 130. Bibliotheque de patrons

Patterns :

```text
RESEARCH_PATTERN
COMPARE_PATTERN
BUILD_PATTERN
REPAIR_PATTERN
VALIDATE_PATTERN
SYNTHESIS_PATTERN
```

Un pattern est un point de depart, pas un plan rigide.


# 131. Promotion de patterns

Un plan recurrent et efficace peut devenir pattern si :
- plusieurs succes ;
- risque faible ;
- invariants respectes ;
- tests disponibles.

Il reste versionne.


# 132. De-promotion de patterns

Si :
- taux d'echec ;
- changement de schema ;
- cout devenu excessif ;

le pattern est :
- deprecie ;
- retire ;
- renvoye en experimentation.


# 133. Compatibilite avec TimeWarp 07

La Structure 08 attend de TimeWarp :
- journal append-only ;
- correlation/causation IDs ;
- snapshots ;
- state_at ;
- replay ;
- forks.

Elle n'impose pas encore le format definitif de la Structure 07.

Cela permet d'ecrire 08 avant 07 sans casser l'architecture.


# 134. Formule maitresse

Demande :

```text
R
```

Decomposition :

```text
G = Decompose(R)
```

Espaces :

```text
S = SelectSpaces(G)
```

Plans candidats :

```text
P_candidates = GeneratePlans(G,S)
```

Evaluation :

```text
P* =
SelectAdmissible(
  Pareto(
    Evaluate(P_candidates)
  )
)
```

Execution adaptative :

```text
while not Stop:
    frontier = ReadyTasks(P*)
    admitted = Regulator(frontier)
    results = Execute(admitted)
    UpdateSpaces(results)
    UpdateBranches(results)
    if NeedReplan(results):
        P* = Replan(P*, results)
```

Synthese :

```text
Y =
Synthesize(
  results,
  evidence,
  conflicts,
  provenance,
  output_contract
)
```

Validation finale :

```text
Deliver(Y)
iff
ValidateOutput(Y) = PASS
```


# 135. Equation de valeur d'une tache

Pour une tache `t` :

```text
V(t) =
(
  expected_goal_progress
  * information_gain
  * robustness_gain
)
/
(
  1
  + cost
  + risk
  + coordination_overhead
)
```

Cette valeur sert au scheduling, pas a declarer une verite.


# 136. Equation de replanification

```text
ReplanTrigger =
w1*failure
+w2*cost_delta
+w3*evidence_shift
+w4*goal_shift
+w5*permission_shift
+w6*stagnation
```

Si :

```text
ReplanTrigger >= theta
```

alors replanification.


# 137. Equation de synthese

Pour des propositions `p_i` :

```text
Synthesis(p) =
Aggregate(
  supports,
  objections,
  dependencies,
  scope,
  mapping_loss
)
```

Le resultat n'est jamais une simple moyenne numerique si les propositions sont semantiquement incompatibles.


# 138. MVP experimental

### M0 - Goal + Plan DAG
- GoalRequest ;
- Task ;
- dependencies ;
- status.

### M1 - Scheduler
- READY ;
- RUNNING ;
- retry bornes ;
- partial.

### M2 - Integration structures
- NCU ;
- MBC ;
- Navigator ;
- Regulator ;
- Recovery.

### M3 - Multi-agent
- capability registry ;
- routing ;
- permissions.

### M4 - Synthesis
- convergences ;
- divergences ;
- provenance ;
- coverage.

### M5 - TimeWarp interface
- plan events ;
- versions ;
- replay hooks.

### M6 - HUD/API
- DAG ;
- spaces ;
- conflicts ;
- budgets.


# 139. Criteres d'acceptation

```text
A1. Une demande peut devenir un DAG de taches.
A2. Les sous-objectifs ont des criteres de succes.
A3. Plusieurs espaces peuvent etre federes sans fusion.
A4. Les ponts inter-espaces declarent confiance et perte.
A5. Plusieurs plans candidats peuvent etre compares.
A6. Le plan choisi respecte budget, permissions et qualite minimale.
A7. Une tache peut finir PARTIAL sans faire croire a un succes complet.
A8. Une replanification cree une nouvelle version.
A9. Les resultats contradictoires restent visibles.
A10. Les agents sont routes par contrat de capacite.
A11. Les permissions enfant ne s'elargissent pas silencieusement.
A12. Les taches UNKNOWN apres crash sont verifiees avant retry.
A13. Un plan peut etre PREVIEW/DRY_RUN/SANDBOX/APPLY.
A14. Toute synthese expose sa provenance.
A15. La couverture finale est calculee.
A16. Le systeme detecte la stagnation.
A17. Les conditions d'arret sont explicites.
A18. Recovery peut suspendre puis reprendre le plan.
A19. Le HUD affiche DAG, espaces, conflits et budget.
A20. Un resultat stable minimal peut etre produit avant exploration complete.
```


# 140. Suite logique vers Structure 09

La suite naturelle est un **moteur de preuve, validation epistemique et protocoles experimentaux**.

Il pourra formaliser :
- types de preuves ;
- qualite de source ;
- independance ;
- protocoles ;
- predictions ;
- falsifiabilite ;
- preregistration ;
- simulations vs observations ;
- replication ;
- meta-analyse locale ;
- propagation de confiance ;
- statut d'une affirmation ;
- seuils de promotion/refutation ;
- paquet de preuve exportable pour paper.

La Structure 08 dit **comment coordonner l'exploration**.
La Structure 09 dira **comment une affirmation gagne ou perd du statut epistemique a travers cette exploration**.
