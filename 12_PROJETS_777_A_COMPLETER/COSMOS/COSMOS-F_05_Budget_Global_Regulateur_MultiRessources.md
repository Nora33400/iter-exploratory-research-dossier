# COSMOS-F - Structure 05
## Budget global de complexite et regulateur dynamique multi-ressources

**Version :** 0.5  
**Statut :** specification conceptuelle, mathematique et technique  
**Dependances :** Structure 01 - NCU ; Structure 02 - Algebre COSMOS-F ; Structure 03 - Moteur de branches concurrentes ; Structure 04 - Zoom fractal et coordonnees multi-echelles ; OCCP ; TimeWarp ; AIONE Forge.

---

## Resume

Cette structure definit le regulateur qui decide **combien de complexite COSMOS-F peut ouvrir, maintenir, explorer, simuler et executer a un instant donne**.

Le systeme ne dispose pas d'un budget unique. Il gere un vecteur de ressources :

```text
B_t = (
  cognition,
  attention,
  temps,
  RAM,
  VRAM,
  CPU,
  GPU,
  stockage,
  tokens,
  energie,
  branches,
  profondeur,
  dimensions,
  risque,
  permissions,
  coordination
)
```

Le regulateur doit :
- mesurer la charge actuelle ;
- estimer le cout d'une operation avant execution ;
- refuser ou degrader proprement une operation trop couteuse ;
- redistribuer dynamiquement le budget ;
- ralentir l'ouverture lorsque la stabilite baisse ;
- conserver une reserve de securite ;
- prioriser la continuite, la provenance et les chemins de retour ;
- coordonner les budgets humain, machine et collectif ;
- enregistrer toutes les decisions de regulation dans TimeWarp.

Principe central :

```text
ouvrir autant que possible
mais jamais plus vite, plus profondement ou plus largement
que le systeme ne peut comprendre, tracer, verifier et refermer.
```

# 1. Pourquoi un budget global

Les Structures 01 a 04 permettent de representer et explorer un espace conceptuel extensible.

Sans regulateur, trois explosions apparaissent :

```text
explosion_de_branches
explosion_dimensionnelle
explosion_de_materialisation
```

auxquelles s'ajoutent :
- saturation RAM/VRAM ;
- temps de calcul excessif ;
- consommation de tokens ;
- surcharge humaine ;
- accumulation de conflits non resolus ;
- perte de lisibilite ;
- perte de reversibilite.

Le budget global transforme donc un espace potentiellement illimite en un processus local, controle et stabilisable.


# 2. Budget comme vecteur

On definit :

```text
B_t =
[
  B_H,
  B_M,
  B_G,
  B_R,
  B_S
]
```

avec :
- `B_H` : budget humain ;
- `B_M` : budget machine ;
- `B_G` : budget collectif ;
- `B_R` : budget de risque ;
- `B_S` : reserve de stabilite.

Chaque composante est elle-meme vectorielle.

Le systeme ne reduit pas automatiquement tout a un seul nombre.


# 3. Budget humain

Le budget humain peut etre modelise par :

```text
B_H =
(
  attention,
  energie,
  temps_disponible,
  charge_cognitive,
  comprehension,
  stress,
  tolerance_aux_interruptions
)
```

Ces variables peuvent etre :
- declarees par l'utilisateur ;
- estimees avec prudence ;
- inconnues.

Une valeur inconnue n'est pas remplacee par une estimation arbitraire.

Exemple :

```json
{
  "attention": 0.6,
  "time_minutes": 35,
  "cognitive_load_max": 0.7,
  "confidence": 0.5
}
```

Le systeme peut alors choisir des operations plus courtes, plus explicables ou plus reversibles.


# 4. Budget machine

Budget machine :

```text
B_M =
(
  RAM_available,
  VRAM_available,
  CPU_available,
  GPU_available,
  storage_IO,
  disk_space,
  tokens,
  model_context,
  wall_time,
  power_limit
)
```

Le regulateur distingue :
- capacite totale ;
- capacite disponible ;
- reserve minimale ;
- capacite engagee.

Pour une ressource `r` :

```text
available_r =
capacity_r
- reserved_r
- committed_r
```


# 5. Budget collectif

Le budget collectif decrit les limites d'une equipe ou d'un ensemble d'agents :

```text
B_G =
(
  personnes,
  agents,
  temps_coordination,
  capacite_validation,
  capacite_relecture,
  canaux_communication,
  conflits,
  dependances
)
```

Un systeme multi-agent peut disposer de beaucoup de calcul tout en etant limite par la capacite humaine de validation.

Le minimum admissible devient donc souvent :

```text
B_effective =
min_constraints(
  B_H,
  B_M,
  B_G
)
```

et non une simple somme.


# 6. Budget de risque

Le risque est traite comme une ressource consommable.

```text
B_R = risk_tolerance_remaining
```

Une operation possede :

```text
risk(op) =
(
  impact,
  probability,
  irreversibility,
  uncertainty,
  blast_radius
)
```

Une forme simple :

```text
R(op) =
impact
* probability
* irreversibility
* uncertainty_factor
```

Une operation peut etre peu couteuse en calcul mais trop couteuse en risque.


# 7. Reserve de stabilite

Une partie du budget ne doit jamais etre engagee en exploration normale.

```text
B_S = reserve
```

Elle sert a :
- rollback ;
- journalisation ;
- sauvegarde ;
- compression ;
- reindexation ;
- recuperation ;
- diagnostic.

Invariant :

```text
exploration_budget
<=
total_budget - stability_reserve
```

Sans cette reserve, le systeme peut saturer au moment meme ou il doit se recuperer.


# 8. Unite de cout abstraite

Pour comparer des ressources heterogenes, on peut definir un vecteur de cout :

```text
C(op) =
(
  c_attention,
  c_time,
  c_ram,
  c_vram,
  c_cpu,
  c_gpu,
  c_tokens,
  c_storage,
  c_risk,
  c_coordination
)
```

Le regulateur conserve ce vecteur.

Une fonction scalaire peut etre utilisee pour le classement :

```text
C*(op) = sum_i w_i * normalized(c_i)
```

mais ne remplace jamais les valeurs originales.


# 9. Normalisation des ressources

Les ressources sont normalisees :

```text
n_i = usage_i / admissible_i
```

avec :

```text
n_i in [0,+infinity)
```

Interpretation :
- `< 0.5` : zone confortable ;
- `0.5 - 0.8` : charge active ;
- `0.8 - 1.0` : zone de pression ;
- `> 1.0` : violation.

Les seuils restent configurables par ressource.


# 10. Charge globale

Indice de charge :

```text
L_t =
Norm(
  W * Usage_t
)
```

Une approximation simple :

```text
L_t = max_i(n_i)
```

Cette forme est conservatrice : la ressource la plus saturee determine la charge globale.

Une autre forme :

```text
L_t =
alpha * max_i(n_i)
+ (1-alpha) * mean_i(n_i)
```

permet de distinguer saturation locale et pression generale.


# 11. Zones de regulation

On definit cinq regimes :

```text
GREEN
YELLOW
ORANGE
RED
RECOVERY
```

Exemple :

```text
GREEN    : L < 0.50
YELLOW   : 0.50 <= L < 0.70
ORANGE   : 0.70 <= L < 0.85
RED      : 0.85 <= L < 1.00
RECOVERY : L >= 1.00 ou invariant critique viole
```

Chaque zone modifie les politiques d'ouverture.


# 12. Politique GREEN

En `GREEN` :
- exploration normale ;
- creation de branches autorisee ;
- nouvelles dimensions admissibles ;
- zoom profond possible ;
- simulations concurrentes ;
- cache genereux.

Le systeme conserve toutefois la reserve de stabilite.


# 13. Politique YELLOW

En `YELLOW` :
- reduction du nombre de branches nouvelles ;
- voisinages plus petits ;
- priorite aux operations a fort gain d'information ;
- compression des regions froides ;
- limitation des simulations longues.

Le but n'est pas d'arreter, mais d'eviter une acceleration non necessaire.


# 14. Politique ORANGE

En `ORANGE` :
- gel des expansions faibles ;
- interdiction de certaines nouvelles dimensions ;
- pruning de vue ;
- reduction du contexte des agents ;
- consolidation TimeWarp ;
- priorite au diagnostic.

Le regulateur exige une utilite plus forte pour accepter une operation.


# 15. Politique RED

En `RED` :
- aucune ouverture speculative non essentielle ;
- fin des simulations non critiques ;
- compression agressive de vue ;
- sauvegarde des checkpoints ;
- liberation de RAM/VRAM ;
- limitation des tokens ;
- retour vers un etat stable.

Les operations de securite et de recuperation restent prioritaires.


# 16. Mode RECOVERY

`RECOVERY` est active si :
- une ressource depasse son plafond ;
- un invariant critique echoue ;
- un rollback est requis ;
- la provenance devient incoherente ;
- le moteur perd son chemin de retour.

Actions :

```text
FREEZE_EXPANSION
CHECKPOINT
ROLLBACK_IF_NEEDED
RELEASE_RESOURCES
REBUILD_INDEXES
VERIFY_INVARIANTS
RETURN_STABLE
```

Le moteur ne reprend l'exploration qu'apres verification.


# 17. Cout previsionnel

Avant une operation :

```text
C_hat(op) = PredictCost(op, state_t)
```

Le predicteur utilise :
- statistiques historiques ;
- taille de l'espace vise ;
- profondeur ;
- nombre de branches ;
- modele selectionne ;
- longueur de contexte ;
- type de simulation.

La prediction porte une incertitude :

```text
C_hat +/- epsilon
```

Le regulateur reserve de preference le cout haut :

```text
C_reserved = C_hat + safety_margin
```


# 18. Reservation de ressources

Avant execution :

```text
RESERVE(op)
```

Si le budget est suffisant :

```text
available -= reserved
committed += reserved
```

Apres execution :

```text
actual = measured_cost
release(reserved - actual)
```

Si le cout reel depasse la reservation :
- utiliser une marge ;
- degrader ;
- suspendre ;
- ou interrompre proprement.


# 19. Admission control

Fonction :

```text
Admit(op, B_t, policy_t)
```

Retour :

```text
ACCEPT
ACCEPT_DEGRADED
QUEUE
ASK_PERMISSION
REJECT
RECOVERY_REQUIRED
```

Le resultat depend de :
- cout ;
- risque ;
- utilite ;
- reversibilite ;
- urgence ;
- disponibilite ;
- permissions.


# 20. Utilite marginale

Une operation n'est pas jugee seulement par son utilite totale, mais par son gain marginal :

```text
MU(op) =
ExpectedGain(op)
/
IncrementalCost(op)
```

Le moteur favorise les actions qui apportent le plus d'information ou de valeur par unite de cout.

Cela limite l'approfondissement d'une branche dont les nouvelles iterations n'apportent presque plus rien.


# 21. Rendements decroissants

Pour une branche `b` :

```text
gain(depth)
```

peut decroitre avec la profondeur.

Critere de stop :

```text
d gain / d cost < theta_min
```

Le moteur peut alors :
- stabiliser ;
- changer de branche ;
- remonter d'echelle ;
- demander un nouvel objectif.


# 22. Budget de branches

On reserve :

```text
B_branch = (
  max_active,
  max_total_generated,
  max_depth_per_branch,
  max_parallel_expansions
)
```

Politique possible :

```text
dominant_branches     : 40%
alternatives          : 30%
counter_hypotheses    : 15%
unknown/null          : 5%
reserve               : 10%
```

Ces proportions sont configurables.


# 23. Budget dimensionnel

Nombre de dimensions actives :

```text
|D_active| <= D_max(t)
```

`D_max` depend de :
- charge machine ;
- charge humaine ;
- lisibilite HUD ;
- complexite des calculs de distance.

Une dimension est admise si :

```text
gain_dimension
>
cost_dimension + complexity_penalty
```


# 24. Budget de profondeur

Pour le zoom fractal :

```text
depth <= depth_max(context)
```

Le plafond peut varier selon le chemin.

Une branche critique peut recevoir plus de profondeur qu'une branche secondaire.

On introduit :

```text
depth_budget(b_i)
```

plutot qu'un plafond uniforme.


# 25. Budget temporel

Trois temps sont distingues :

```text
wall_time
compute_time
human_wait_time
```

Une operation interactive peut devoir respecter :

```text
latency_target <= 2 s
```

tandis qu'une simulation hors interaction peut recevoir un budget plus long.

Le regulateur adapte la strategie au mode :
- interactif ;
- batch ;
- diagnostic ;
- simulation ;
- recovery.


# 26. Budget tokens et contexte

Pour un modele :

```text
B_tokens =
(
  context_limit,
  prompt_budget,
  output_budget,
  reserve_for_tools,
  reserve_for_summary
)
```

Quand le contexte approche de la limite :
- resumer les regions anciennes ;
- externaliser dans le ledger ;
- garder les ancres ;
- reconstruire a la demande.

Invariant :

```text
context_window != long_term_memory
```


# 27. Budget RAM

Le regulateur RAM maintient :

```text
RAM_free >= RAM_reserve
```

Politique :
- ejecter caches froids ;
- compresser embeddings ;
- paginer les graphes ;
- decharger les simulations terminees ;
- eviter les duplications de structures.

Les donnees persistantes ne doivent pas etre perdues lors d'une eviction RAM.


# 28. Budget VRAM

VRAM est geree par :
- modele actif ;
- contexte GPU ;
- tenseurs ;
- embeddings ;
- rendu/vision.

Le regulateur peut :
- reduire batch size ;
- quantifier ;
- decharger un modele ;
- basculer CPU ;
- serialiser les simulations ;
- choisir un modele plus petit.

Chaque degradation doit etre journalisee.


# 29. Budget CPU/GPU

Le regulateur distingue :
- debit ;
- latence ;
- priorite.

Un scheduler peut utiliser :

```text
priority_queue(
  recovery,
  user_interactive,
  validation,
  exploration,
  background_indexing
)
```

Les operations d'exploration ne doivent pas bloquer les fonctions critiques.


# 30. Budget stockage

Le stockage est separe en :
- hot state ;
- snapshots ;
- ledger ;
- archives ;
- artefacts ;
- caches regenerables.

Politique de retention :

```text
keep_forever:
  provenance critique
  checkpoints stables

keep_long:
  branches archivees

evictable:
  caches regenerables
```

La suppression suit une politique explicite.


# 31. Budget de coordination

Pour plusieurs agents :

```text
coordination_cost =
messages
+ synchronizations
+ conflict_resolution
+ duplicated_work
```

Ajouter des agents n'augmente donc pas toujours la capacite effective.

Le regulateur peut preferer :
- 1 agent profond ;
- 3 agents paralleles ;
- 1 planificateur + 2 executants ;
selon le cout de coordination.


# 32. Budget de validation

Une ressource critique est la capacite de verifier.

```text
B_validation =
(
  tests,
  reviewers,
  evidence_checks,
  time_for_validation
)
```

Si le moteur produit plus vite qu'il ne peut valider :

```text
generation_rate > validation_rate
```

alors un backlog de dette epistemique apparait.

Le regulateur doit reduire la generation.


# 33. Dette de complexite

On definit :

```text
Debt_t =
unvalidated_branches
+ unresolved_conflicts
+ uncompressed_history
+ pending_tests
+ undocumented_transforms
```

Une dette excessive provoque une phase de consolidation.

Le systeme ne doit pas continuer a ouvrir indefiniment en accumulant des elements non verifies.


# 34. Dette epistemique

La dette epistemique mesure les affirmations avancees plus vite que leur verification.

```text
EpiDebt =
claims_without_evidence
+ weak_provenance
+ unresolved_source_dependence
```

Politique :

```text
if EpiDebt > threshold:
    reduce_generation()
    prioritize_validation()
```

Cela maintient la distinction entre hypothese, simulation, observation et corroboration.


# 35. Dette de reversibilite

Une operation irreversible augmente :

```text
RevDebt
```

Si trop d'operations difficiles a annuler s'accumulent, le regulateur peut bloquer de nouvelles actions irreversibles.

Critere :

```text
RevDebt <= RevBudget
```

La dette diminue lorsque :
- sauvegarde existe ;
- rollback est teste ;
- reconstruction est prouvee.


# 36. Vitesse d'ouverture

Le regulateur limite :

```text
v_K = ||dK/dt||
```

ou `K` est le vecteur de complexite.

Limite :

```text
v_K <= v_max(
  stability,
  budget,
  risk,
  uncertainty
)
```

Quand la stabilite baisse, `v_max` diminue automatiquement.


# 37. Acceleration de complexite

On limite aussi :

```text
a_K = ||d2K/dt2||
```

Cela evite :
- ajout brutal de nombreuses branches ;
- changement simultane de plusieurs frames ;
- activation soudaine de dizaines de dimensions.

Une acceleration excessive declenche :

```text
THROTTLE
```


# 38. Stabilite dynamique

Indice :

```text
S_t =
1 - normalized(
  unresolved_conflicts
  + rollback_rate
  + error_rate
  + uncertainty_growth
  + overload
)
```

Le systeme peut exiger :

```text
S_t >= S_min
```

avant de reouvrir de nouvelles complexites.


# 39. Hysteresis

Pour eviter les oscillations entre regimes :

```text
GREEN -> YELLOW
```

ne revient pas immediatement a `GREEN` des que la charge baisse legerement.

On utilise deux seuils :

```text
enter_yellow = 0.55
exit_yellow  = 0.45
```

Cette hysteresis stabilise le regulateur.


# 40. Rate limiting

Limites :

```text
max_new_branches_per_min
max_new_dimensions_per_min
max_zoom_depth_per_session
max_simulations_parallel
max_external_actions
```

Le rate limiting protege meme lorsque chaque operation individuelle semble acceptable.


# 41. Quotas par sous-systeme

Exemple :

```text
Forge        : 35%
MBC          : 20%
Navigator    : 15%
TimeWarp     : 10%
Validation   : 10%
Recovery     : 10% reserve
```

Les quotas peuvent etre elastiques.

La reserve de recovery reste prioritaire.


# 42. Priorites

Classes :

```text
P0 RECOVERY
P1 SAFETY
P2 USER_INTERACTIVE
P3 VALIDATION
P4 EXECUTION
P5 EXPLORATION
P6 MAINTENANCE
```

Le scheduler peut preempter `P5` si `P0-P3` ont besoin de ressources.


# 43. Preemption

Une operation longue doit declarer si elle est :

```text
PREEMPTIBLE
CHECKPOINTABLE
NON_PREEMPTIBLE
```

Pour une simulation checkpointable :

```text
pause -> save_state -> release_GPU
```

puis reprise plus tard depuis le checkpoint.


# 44. Degradation gracieuse

Si l'operation complete n'est pas admissible :

```text
FULL
-> REDUCED_DEPTH
-> FEWER_BRANCHES
-> FEWER_DIMENSIONS
-> SMALLER_MODEL
-> SUMMARY_ONLY
-> DEFER
-> REJECT
```

Le regulateur choisit la meilleure version utile sous budget.

Il annonce toujours la degradation appliquee.


# 45. Qualite minimale

La degradation ne doit pas franchir un niveau de qualite minimal :

```text
Q(op_degraded) >= Q_min
```

Sinon :

```text
REJECT
```

Le systeme ne doit pas produire une sortie trompeuse uniquement pour respecter le budget.


# 46. Strategie anytime

Certaines operations sont "anytime" :

```text
result_1
result_2
...
result_n
```

Chaque etape ameliore la qualite.

Le moteur peut s'arreter quand le budget est epuise et retourner le meilleur resultat stable deja obtenu.

Cela convient a :
- recherche ;
- clustering ;
- route planning ;
- exploration de branches.


# 47. Budget adaptatif

Le budget peut augmenter ou diminuer selon les resultats.

```text
B_{t+1} =
Adapt(
  B_t,
  observed_cost,
  observed_gain,
  stability,
  user_priority
)
```

Si une branche produit beaucoup de gain pour peu de cout, elle peut recevoir plus de ressources.


# 48. Allocation Pareto

On peut optimiser plusieurs objectifs :

```text
maximize:
  information_gain
  utility
  coverage
  robustness

minimize:
  cost
  risk
  latency
  overload
```

Le regulateur conserve un front de Pareto des allocations possibles.

La politique choisit ensuite selon le contexte.


# 49. Allocation par enchere interne

Chaque sous-systeme peut proposer :

```text
Bid = (
  requested_resources,
  expected_gain,
  urgency,
  risk,
  deadline
)
```

Le regulateur accepte les offres les plus utiles sous contraintes.

Ce mecanisme permet de coordonner Forge, MBC, Navigator et TimeWarp sans codage rigide de chaque combinaison.


# 50. Penalite de changement de contexte

Changer souvent de modele, frame ou objectif a un cout :

```text
switch_cost
```

Le scheduler evite les oscillations :

```text
A -> B -> A -> B
```

si le gain attendu ne compense pas les couts de changement.


# 51. Co-localite des taches

Deux operations utilisant les memes donnees peuvent etre groupees.

```text
shared_cache_gain > coordination_cost
```

Exemple :
- comparaison de trois branches ;
- embeddings deja charges ;
- meme modele actif.

Le regulateur peut batcher ces operations.


# 52. Budget par transaction

Une transaction recoit :

```text
TxnBudget
```

Elle ne peut depasser ce budget sans renegociation.

Si elle depasse :
- checkpoint ;
- demande de budget supplementaire ;
- degradation ;
- rollback.

Cela evite qu'une operation composee consomme silencieusement toute la reserve.


# 53. Contrats de ressources

Chaque operation declare :

```text
ResourceContract = (
  minimum,
  preferred,
  maximum,
  preemptible,
  checkpointable,
  risk,
  deadline
)
```

Le regulateur peut ainsi choisir une configuration.

Exemple :

```json
{
  "gpu_vram_mb": {"min": 800, "preferred": 3500, "max": 6000},
  "tokens": {"min": 1200, "preferred": 5000},
  "checkpointable": true
}
```


# 54. Admission OCCP

Le gate final :

```text
G_resource =
G_capacity
AND G_risk
AND G_permission
AND G_reversibility
AND G_stability
```

Une operation ne passe que si tous les gates obligatoires passent.

Certains gates peuvent retourner :

```text
PASS_WITH_DEGRADATION
```

plutot qu'un simple booleen.


# 55. Permissions budgetaires

Les permissions peuvent limiter :
- GPU ;
- reseau ;
- stockage ;
- nombre d'agents ;
- actions externes ;
- profondeur autonome.

Exemple :

```text
permission.max_autonomous_depth = 3
```

Le budget technique disponible ne contourne jamais une permission.


# 56. Budget d'action externe

Les actions externes ont un budget distinct :

```text
B_external =
(
  max_actions,
  max_frequency,
  max_scope,
  max_risk
)
```

Exploration interne :

```text
large_budget
```

Action externe :

```text
strict_budget
```

Cette separation est essentielle.


# 57. Fenetre de stabilisation

Apres `N` operations ou un seuil de charge :

```text
STABILIZATION_WINDOW
```

Le moteur :
- stoppe les nouvelles expansions ;
- valide les invariants ;
- compresse ;
- recalcule les scores ;
- met a jour les budgets ;
- cree un checkpoint.

La duree de la fenetre est adaptee au contexte.


# 58. Checkpoints budgetaires

Un checkpoint enregistre :

```text
BudgetSnapshot = (
  capacities,
  reservations,
  usages,
  debts,
  regime,
  policies
)
```

TimeWarp peut alors comparer :

```text
budget_at(t0)
budget_at(t1)
```

et expliquer pourquoi une operation etait admissible a un moment mais pas a un autre.


# 59. Historique des couts

Chaque operation produit :

```text
CostRecord = (
  predicted,
  reserved,
  actual,
  delta,
  outcome
)
```

Le predicteur apprend de ces ecarts.

Metrique :

```text
prediction_error =
|actual - predicted| / max(actual, epsilon)
```

Une erreur persistante augmente la marge de securite.


# 60. Detection d'anomalies

Anomalies :
- cout x10 par rapport a l'historique ;
- fuite RAM ;
- VRAM non liberee ;
- explosion de tokens ;
- branche auto-replicative ;
- boucle de navigation ;
- rollback frequent.

Le regulateur peut declencher :

```text
ANOMALY -> THROTTLE -> DIAGNOSE
```


# 61. Boucles de controle

Le regulateur fonctionne comme une boucle :

```text
observe
-> estimate
-> decide
-> reserve
-> execute
-> measure
-> adapt
```

Forme :

```text
B_{t+1} =
Controller(
  B_t,
  Load_t,
  Gain_t,
  Risk_t,
  Stability_t
)
```


# 62. Controle PID conceptuel

Pour certaines ressources continues, un PID peut etre utilise.

Erreur :

```text
e_t = target_load - measured_load
```

Commande :

```text
u_t =
Kp*e_t
+ Ki*sum(e)
+ Kd*(e_t-e_{t-1})
```

`u_t` peut modifier :
- nombre de branches ;
- parallelisme ;
- profondeur ;
- taille de contexte.

Il faut toutefois eviter d'utiliser un PID brut pour les decisions epistemiques ou de securite.


# 63. Controle par Model Predictive Control

Pour des operations longues, un controle predictif peut comparer plusieurs futurs :

```text
MPC:
simulate budget evolution
choose sequence minimizing overload + lost utility
```

Horizon court :

```text
t ... t+H
```

Le regulateur replanifie apres chaque mesure.


# 64. Limites du controle automatique

Le regulateur ne doit pas :
- inventer la fatigue humaine ;
- transformer un score de confiance en fait ;
- franchir une permission ;
- masquer une reduction de qualite ;
- supprimer des preuves pour liberer de la memoire sans persistence ;
- privilegier le debit sur la tracabilite critique.

L'automatisation gere les ressources, pas la verite.


# 65. Integration Structure 03

Le MBC demande des budgets pour :
- generation ;
- comparaison ;
- simulation ;
- pruning ;
- reouverture.

Le regulateur retourne :

```text
allocation_per_branch
```

Une branche dominante ne monopolise pas necessairement toute la capacite.


# 66. Integration Structure 04

Le navigateur demande :
- noeuds materialisables ;
- profondeur ;
- dimensions ;
- cache ;
- route planning.

Le regulateur controle :

```text
window_size
depth_max
dimension_max
cache_policy
```

La Structure 04 definit comment naviguer ; la Structure 05 definit combien ouvrir.


# 67. Integration TimeWarp

TimeWarp stocke :
- regime ;
- budgets ;
- reservations ;
- couts reels ;
- degradations ;
- recovery events.

On peut rejouer :

```text
"pourquoi le systeme a-t-il reduit la profondeur ici ?"
```

avec une reponse basee sur l'etat budgetaire historique.


# 68. Integration Forge

Forge devient un demandeur de ressources.

Flux :

```text
Forge.plan
-> estimate resources
-> regulator.admit
-> reserve
-> execute
-> report actual
-> regulator.update
```

Le planificateur peut proposer plusieurs versions :

```text
plan_full
plan_medium
plan_minimal
```

et laisser le regulateur choisir.


# 69. Architecture logicielle

```text
ComplexityRegulator
├── ResourceMonitor
├── BudgetModel
├── CostPredictor
├── AdmissionController
├── ReservationManager
├── Scheduler
├── RiskBudget
├── DebtManager
├── StabilityController
├── DegradationPlanner
├── RecoveryManager
├── PolicyEngine
├── MetricsStore
├── TimeWarpAdapter
├── ForgeAdapter
├── MBCAdapter
└── NavigatorAdapter
```


# 70. Etat canonique

```json
{
  "regime": "YELLOW",
  "load": 0.63,
  "resources": {
    "ram": {"used": 18, "limit": 28, "unit": "GB"},
    "vram": {"used": 9.1, "limit": 11.0, "unit": "GB"},
    "tokens": {"used": 3400, "limit": 8000}
  },
  "complexity": {
    "active_branches": 8,
    "active_dimensions": 6,
    "depth": 4
  },
  "debts": {
    "epistemic": 0.22,
    "reversibility": 0.05
  }
}
```


# 71. Schema SQLite

```sql
resource_snapshots(
  snapshot_id TEXT PRIMARY KEY,
  timestamp TEXT,
  regime TEXT,
  payload_json TEXT
);

operation_costs(
  operation_id TEXT PRIMARY KEY,
  predicted_json TEXT,
  reserved_json TEXT,
  actual_json TEXT,
  outcome TEXT,
  timestamp TEXT
);

resource_reservations(
  reservation_id TEXT PRIMARY KEY,
  operation_id TEXT,
  resource TEXT,
  amount REAL,
  status TEXT
);

budget_policies(
  policy_id TEXT PRIMARY KEY,
  scope TEXT,
  policy_json TEXT,
  enabled INTEGER
);

debt_snapshots(
  snapshot_id TEXT,
  debt_type TEXT,
  value REAL
);
```


# 72. API proposee

```text
GET  /budget/state
GET  /budget/history
POST /budget/estimate
POST /budget/admit
POST /budget/reserve
POST /budget/release
POST /budget/policy
POST /budget/stabilize
POST /budget/recovery
GET  /budget/debts
GET  /budget/anomalies
```

Une admission retourne toujours les raisons.


# 73. DSL budgetaire

```text
BUDGET SHOW
BUDGET LIMIT branches 12
BUDGET RESERVE vram 2GB FOR simulation:42
POLICY SET regime:orange max_parallel=2
STABILIZE NOW
DEGRADE operation:77 TO medium
SHOW DEBT epistemic
SHOW COST HISTORY FOR EXPAND
```


# 74. Politique declarative

Exemple :

```yaml
regimes:
  yellow:
    max_parallel_simulations: 2
    max_new_branches: 4
    allow_new_dimensions: true
  orange:
    max_parallel_simulations: 1
    max_new_branches: 1
    allow_new_dimensions: false
```

Les politiques sont versionnees et auditables.


# 75. Tests unitaires

```text
test_reservation_never_exceeds_budget
test_recovery_preserves_reserve
test_red_blocks_speculative_expansion
test_degradation_reports_changes
test_permission_overrides_capacity
test_actual_cost_releases_unused_reservation
test_prediction_error_updates_margin
test_hysteresis_prevents_flapping
test_external_budget_separate
test_debt_triggers_consolidation
```


# 76. Tests de proprietes

```text
P1. available + committed + reserved <= capacity
P2. recovery_reserve is never consumed by normal exploration
P3. no admitted operation violates mandatory gates
P4. every degradation is observable
P5. every recovery event creates a checkpoint
P6. active complexity remains finite
```


# 77. Tests de charge

Scenarios :
- 100 branches actives ;
- 32 dimensions proposees ;
- 4 modeles locaux ;
- VRAM proche saturation ;
- 10 simulations concurrentes ;
- TimeWarp volumineux.

Verifier :
- maintien du HUD interactif ;
- absence de fuite ;
- preemption ;
- stabilisation ;
- retour a GREEN/YELLOW apres charge.


# 78. Observabilite

Metriques :

```text
budget_load
ram_pressure
vram_pressure
token_pressure
active_branch_pressure
dimension_pressure
risk_budget_remaining
stability_score
epistemic_debt
reversibility_debt
cost_prediction_error
degradation_count
recovery_count
```

Graphes temporels utiles :
- charge ;
- regime ;
- gain d'information ;
- dette ;
- cout reel vs predit.


# 79. HUD du regulateur

Le HUD peut afficher :

```text
REGIME: YELLOW
Load: 63%
RAM: 18/28 GB
VRAM: 9.1/11 GB
Branches: 8/12
Dimensions: 6/8
Depth: 4/6
Risk reserve: 78%
Stability: 0.84
```

Actions :
- stabiliser ;
- reduire profondeur ;
- geler nouvelles branches ;
- augmenter un quota autorise ;
- voir les raisons d'une admission/refus.


# 80. Exemple A - saturation VRAM

Situation :
- deux modeles charges ;
- simulation GPU demandee ;
- VRAM disponible insuffisante.

Options :
1. decharger modele secondaire ;
2. reduire quantification/batch ;
3. basculer simulation CPU ;
4. serialiser ;
5. refuser.

Le regulateur choisit selon :
- latence ;
- cout ;
- qualite minimale ;
- priorite ;
- possibilite de reprise.


# 81. Exemple B - explosion de branches

MBC genere 40 hypotheses.

Budget :

```text
max_active = 10
```

Le regulateur :
- garde 5 branches Pareto ;
- garde 2 contre-hypotheses ;
- garde 1 branche null ;
- archive 32 branches ;
- cree un resume de famille ;
- conserve les conditions de reouverture.

Aucune branche n'est supprimee uniquement pour respecter la vue active.


# 82. Exemple C - surcharge humaine

L'utilisateur declare :

```text
temps = 10 minutes
niveau_detail = faible
```

Le regulateur peut :
- limiter l'expansion ;
- afficher 3 branches max ;
- compresser les preuves ;
- conserver les details en arriere-plan persistant ;
- proposer un resume stable.

Le modele humain est utilise comme contrainte de presentation et de rythme, pas comme diagnostic.


# 83. Exemple D - dette epistemique

Le moteur a cree :
- 15 hypotheses ;
- 2 seulement testees ;
- 0 corroboration.

`EpiDebt` augmente.

Politique :
- interdire temporairement `DIVERGENT_GENERATION` ;
- prioriser `VALIDATE` ;
- chercher des tests discriminants ;
- resumer les branches non prioritaires.

L'ouverture reprend lorsque la dette diminue.


# 84. Exemple E - recovery apres erreur

Une expansion depasse la RAM prevue.

Flux :

```text
ANOMALY
-> stop new allocations
-> checkpoint ledger
-> evict caches
-> rollback partial expansion
-> validate invariants
-> recalc budgets
-> return stable
```

Le systeme journalise :
- prediction ;
- cout reel ;
- cause ;
- actions de recuperation.


# 85. Exemple F - allocation multi-agent

Trois agents demandent :
- Agent A : validation ;
- Agent B : exploration ;
- Agent C : compression.

Sous charge ORANGE :
- validation P3 passe ;
- compression utile a la stabilite passe ;
- exploration est mise en attente.

Le scheduler privilegie la reduction de dette et la recuperation de marge.


# 86. Securite et limites

Le regulateur doit appliquer :

```text
capacity != permission
high_score != authorization
available_gpu != right_to_execute_external_action
```

Il ne doit pas utiliser l'optimisation des ressources pour contourner des controles.

Les politiques de risque et permission restent superieures aux politiques de performance.


# 87. Formule maitresse

Etat :

```text
X_t = (
  B_t,
  L_t,
  S_t,
  D_t,
  R_t,
  P_t
)
```

ou :
- `B_t` : budgets ;
- `L_t` : charge ;
- `S_t` : stabilite ;
- `D_t` : dettes ;
- `R_t` : risque ;
- `P_t` : politiques/permissions.

Pour une operation `o` :

```text
C_hat(o) = PredictCost(o, X_t)
U_hat(o) = PredictUtility(o, X_t)
```

Admission :

```text
A(o) =
Gate(
  capacity,
  risk,
  permission,
  reversibility,
  stability,
  quality
)
```

Puis :

```text
o* =
argmax_o
[
  ExpectedUtility(o)
  - lambda * ExpectedCost(o)
]
```

sous contraintes :

```text
C(o) <= B_available
Risk(o) <= B_R
Stability_after(o) >= S_min
Quality(o) >= Q_min
```

Mise a jour :

```text
X_{t+1}
=
Adapt(
  Measure(
    Execute(
      Reserve(o*, X_t)
    )
  )
)
```


# 88. Equilibre global

L'etat recherche n'est pas :

```text
usage = 0
```

ni :

```text
usage = 100%
```

mais une zone d'exploitation stable :

```text
target_load in [L_low, L_high]
```

avec :
- marge de recuperation ;
- debit utile ;
- faible dette ;
- capacite de repondre a une nouvelle priorite.


# 89. MVP experimental

### M0 - observabilite
- RAM ;
- VRAM ;
- CPU/GPU ;
- tokens ;
- branches ;
- dimensions ;
- profondeur.

### M1 - admission
- prediction simple ;
- quotas ;
- reserve ;
- ACCEPT/REJECT.

### M2 - regimes
- GREEN/YELLOW/ORANGE/RED ;
- hysteresis ;
- rate limits.

### M3 - degradation
- profondeur ;
- branches ;
- modele ;
- parallelisme.

### M4 - dettes
- epistemique ;
- reversibilite ;
- validation.

### M5 - TimeWarp/Forge
- cout historique ;
- plan adaptatif ;
- checkpoints ;
- recovery.

### M6 - HUD
- vue temps reel ;
- raisons ;
- politiques.


# 90. Criteres d'acceptation

```text
A1. Le systeme connait les ressources critiques disponibles.
A2. Toute operation non triviale peut declarer un cout.
A3. Une reservation est effectuee avant les operations couteuses.
A4. La reserve de recovery est protegee.
A5. Le regime change selon la charge avec hysteresis.
A6. Une operation peut etre degradee proprement.
A7. Les degradations sont visibles et journalisees.
A8. La generation ralentit lorsque la dette epistemique monte.
A9. Les actions externes ont un budget distinct.
A10. TimeWarp conserve l'historique des decisions budgetaires.
A11. Le moteur revient a un etat stable apres saturation simulee.
A12. Les permissions ne peuvent pas etre contournees par une allocation.
A13. Le nombre de branches/dimensions actives reste borne.
A14. Les couts reels ameliorent le predicteur.
A15. Le HUD expose charge, stabilite, dette et regime.
```


# 91. Suite logique vers Structure 06

La prochaine structure logique est le **moteur de stabilisation dynamique, checkpoints, rollback et recovery multi-niveaux**.

Elle pourra formaliser :
- etat stable ;
- checkpoints locaux et globaux ;
- deltas ;
- rollback ;
- replay ;
- journal causal ;
- recovery partiel ;
- recovery total ;
- invariants ;
- detection de corruption ;
- reprise apres crash ;
- reconciliation TimeWarp ;
- rollback conceptuel vs rollback machine ;
- modes Doctor / ConnectBusBot / Immune si on souhaite les relier ensuite a l'architecture AIONE.

La Structure 05 decide **quand ralentir ou arreter** ; la Structure 06 definira **comment revenir proprement a un etat stable et repartir sans perdre l'histoire**.
