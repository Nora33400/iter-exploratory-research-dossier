
# COSMOS-F — Structure 03
## Moteur de branches concurrentes et espace des hypothèses

**Version :** 0.3  
**Statut :** spécification conceptuelle et technique  
**Dépendances :** Structure 01 — Nœud Conceptuel Universel (NCU) ; Structure 02 — Algèbre des opérateurs COSMOS-F ; OCCP ; TimeWarp ; AIONE Forge.

---

## Résumé

Cette structure définit le moteur qui permet à COSMOS-F de conserver, développer, comparer, réduire, réouvrir et simuler **plusieurs interprétations concurrentes d'un même contexte** sans les confondre entre elles et sans transformer une possibilité en vérité.

Le moteur traite une branche comme une trajectoire conceptuelle explicite. Chaque branche possède :
- une origine ;
- une hypothèse ;
- des preuves et contre-preuves ;
- un niveau de confiance ;
- un statut épistémique ;
- un coût d'exploration ;
- un risque ;
- une utilité estimée ;
- des permissions ;
- une histoire de transformations ;
- un état de cycle de vie ;
- des liens avec les autres branches.

Le principe central est :

```text
possible != vrai != activable != exécuté
```

Une branche peut rester utile même lorsqu'elle est peu probable : elle peut servir de contre-modèle, d'alternative, d'hypothèse de sécurité, de scénario de simulation ou de voie de recherche future.

Le moteur doit donc optimiser non pas une seule "meilleure réponse", mais un **portefeuille de branches** sous contraintes de budget, de sécurité, de cohérence et de réversibilité.

# 1. Objectifs du moteur

Le moteur de branches concurrentes, abrégé **MBC**, doit satisfaire huit objectifs.

1. **Préserver la pluralité.** Plusieurs explications incompatibles peuvent coexister tant qu'aucune preuve suffisante ne permet de trancher.
2. **Éviter la fusion prématurée.** Deux branches proches ne sont fusionnées que si leurs différences sont explicitement évaluées.
3. **Tracer l'origine.** Toute branche doit conserver la provenance des événements, traces, transformations et décisions qui l'ont produite.
4. **Gérer l'incertitude.** La confiance n'est ni binaire ni confondue avec le statut de preuve.
5. **Contrôler les coûts.** L'espace des hypothèses peut être potentiellement infini ; l'espace réellement actif doit rester borné.
6. **Permettre le retour.** Une branche élaguée ou archivée peut être restaurée si de nouvelles preuves l'exigent.
7. **Séparer exploration et action.** Une hypothèse explorée ne devient jamais automatiquement une action.
8. **Rendre le raisonnement inspectable.** L'utilisateur ou un autre module doit pouvoir voir pourquoi une branche existe, pourquoi elle a été priorisée et pourquoi elle a été suspendue.

On note l'ensemble actif des branches au temps t :

```text
B_t = { b_1, b_2, ..., b_n }
```

où chaque `b_i` est un objet versionné et adressable.


# 2. Objet branche : définition canonique

Une branche est définie par :

```text
b_i = (
    id,
    parentage,
    hypothesis,
    scope,
    evidence,
    counter_evidence,
    epistemic_state,
    confidence,
    utility,
    cost,
    risk,
    permissions,
    provenance,
    lifecycle,
    time,
    relations,
    state_hash
)
```

### 2.1 Identité

`id` est un identifiant stable de branche. Une branche modifiée ne change pas nécessairement d'identité, mais son `revision_id` change.

Exemple :

```json
{
  "branch_id": "br_01JX...",
  "revision_id": 17,
  "created_at": "2026-08-29T18:00:00+02:00",
  "created_by": "forge:planner"
}
```

### 2.2 Parentage

Une branche peut provenir :
- d'une seule branche ;
- de plusieurs branches ;
- directement d'un nœud conceptuel ;
- d'une réouverture d'archive ;
- d'une simulation indépendante.

```text
parents(b_i) = {b_a, b_b, ...}
```

On autorise donc un DAG de branches, pas seulement un arbre.

### 2.3 Hypothèse

L'hypothèse est une proposition explicite :

```text
H_i : proposition -> statut
```

Elle doit pouvoir être formulée de manière vérifiable autant que possible.

### 2.4 Scope

Le scope définit l'espace où la branche prétend être pertinente :
- concept ;
- acteur ;
- période ;
- contexte ;
- dimension ;
- niveau d'abstraction ;
- domaine de validité.

Deux branches ne sont contradictoires que si leurs scopes se recouvrent suffisamment.


# 3. Statuts épistémiques

Le moteur distingue les statuts suivants :

```text
IMAGINED
FORMALIZED
SIMULATED
TESTABLE
OBSERVED
CORROBORATED
REFUTED
UNDETERMINED
```

Ces statuts ne sont pas un score numérique. Ils décrivent **la nature du support disponible**.

Exemples :
- `IMAGINED` : idée non encore structurée ;
- `FORMALIZED` : proposition définie de façon cohérente ;
- `SIMULATED` : comportement testé dans un modèle ;
- `TESTABLE` : protocole empirique identifié ;
- `OBSERVED` : observation disponible ;
- `CORROBORATED` : observations indépendantes concordantes ;
- `REFUTED` : incompatibilité forte avec les preuves ;
- `UNDETERMINED` : information insuffisante ou contradictoire.

Transition :

```text
IMAGINED -> FORMALIZED -> SIMULATED -> TESTABLE -> OBSERVED -> CORROBORATED
```

mais le graphe autorise les retours :

```text
CORROBORATED -> UNDETERMINED
```

si une contradiction nouvelle apparaît.

Aucune transition n'est irréversible.


# 4. Confiance et incertitude

La confiance `w_i` est distincte du statut épistémique.

```text
w_i in [0,1]
```

Elle peut être calculée à partir de plusieurs composantes :

```text
w_i = F(
  q_source,
  q_evidence,
  consistency,
  reproducibility,
  recency,
  independence,
  counter_evidence
)
```

Une forme simple :

```text
w_i =
  sigma(
      a1*source
    + a2*evidence
    + a3*consistency
    + a4*reproducibility
    + a5*independence
    - a6*counter_evidence
  )
```

où `sigma` borne le résultat entre 0 et 1.

Le moteur stocke aussi une incertitude :

```text
u_i in [0,1]
```

avec, par exemple :

```text
u_i = 1 - information_coverage_i
```

Une branche peut donc avoir :
- confiance élevée et incertitude faible ;
- confiance élevée mais incertitude élevée ;
- confiance faible mais potentiel exploratoire élevé.

La décision ne doit jamais se réduire à `max(w_i)`.


# 5. Preuves, contre-preuves et provenance

Chaque branche possède deux ensembles séparés :

```text
E_i+ = preuves en faveur
E_i- = preuves en défaveur
```

Une preuve est un objet :

```json
{
  "evidence_id": "ev_...",
  "kind": "observation|document|measurement|simulation|test|statement",
  "source": "...",
  "timestamp": "...",
  "content_ref": "...",
  "quality": 0.82,
  "independence_group": "grp_...",
  "supports": ["br_..."],
  "opposes": ["br_..."]
}
```

Une même preuve peut soutenir plusieurs branches ou en contredire plusieurs.

### Provenance obligatoire

Toute mutation importante enregistre :

```text
PROVENANCE = (
  actor,
  timestamp,
  source_state,
  operation,
  parameters,
  evidence_used,
  resulting_state
)
```

Sans provenance suffisante, une branche peut rester exploratoire mais ne doit pas être promue vers un statut fort.


# 6. Relations entre branches

Deux branches peuvent être reliées par :

```text
SUPPORTS
CONTRADICTS
REFINES
GENERALIZES
SPECIALIZES
OVERLAPS
ORTHOGONAL
DEPENDENT
EXCLUSIVE
DERIVED_FROM
SIMULATES
EXPLAINS
```

Une relation possède :
- source ;
- cible ;
- type ;
- force ;
- justification ;
- scope ;
- date ;
- provenance.

Exemple :

```text
R_ij = (b_i, CONTRADICTS, b_j, strength=0.91)
```

La contradiction n'est valide que sur l'intersection :

```text
scope(b_i) ∩ scope(b_j)
```

Si l'intersection est vide, les branches ne sont pas réellement contradictoires.


# 7. Génération de branches

Le moteur crée des branches depuis :
- un événement ;
- une trace ;
- un nœud NCU ;
- une question ;
- une contradiction ;
- une anomalie ;
- une simulation ;
- une demande explicite de divergence.

On définit :

```text
GenerateBranches(context, seed, constraints) -> {b_1,...,b_k}
```

La génération doit produire des branches **distinctes par construction**.

Un générateur de diversité peut imposer :

```text
distance(b_i, b_j) >= delta_min
```

pour éviter dix paraphrases de la même hypothèse.

### Modes de génération

```text
CONSERVATIVE   -> branches proches du modèle courant
DIVERGENT      -> alternatives structurelles
ADVERSARIAL    -> contre-hypothèses
NULL           -> hypothèse "aucun lien significatif"
CAUSAL         -> causes alternatives
TEMPORAL       -> scénarios de temporalité
PERSPECTIVAL   -> variations d'acteur/perspective
```

Le mode `NULL` est important : il évite que le système force une interprétation lorsqu'aucune relation n'est démontrée.


# 8. Cycle de vie d'une branche

Une branche possède un état :

```text
NEW
ACTIVE
STABLE
SUSPENDED
PRUNED
ARCHIVED
REOPENED
MERGED
REFUTED
EXECUTION_CANDIDATE
```

Transitions principales :

```text
NEW -> ACTIVE
ACTIVE -> STABLE
ACTIVE -> SUSPENDED
ACTIVE -> PRUNED
STABLE -> ARCHIVED
PRUNED -> REOPENED
ARCHIVED -> REOPENED
ACTIVE -> MERGED
ACTIVE -> REFUTED
STABLE -> EXECUTION_CANDIDATE
```

Le pruning ne détruit pas la branche. Il retire seulement la branche de l'ensemble actif.

```text
PRUNED != DELETED
```

Une suppression physique n'est permise qu'après politique de rétention explicite.


# 9. Scoring multi-critères

La priorité d'exploration est calculée par un vecteur, pas par un seul score.

Pour chaque branche :

```text
S_i = (
  plausibility,
  information_gain,
  utility,
  novelty,
  urgency,
  safety,
  reversibility,
  cost,
  risk
)
```

Une fonction de classement possible :

```text
Q_i =
  (
    w_p*plausibility
  + w_g*information_gain
  + w_u*utility
  + w_n*novelty
  + w_s*safety
  + w_v*reversibility
  )
  /
  (
    1
    + w_c*cost
    + w_r*risk
  )
```

Mais le moteur conserve le vecteur original afin que les raisons du classement restent inspectables.

### Pareto

On peut garder toutes les branches non dominées au sens de Pareto :

```text
Pareto(B_t)
```

Une branche faible en plausibilité peut rester non dominée si son coût est très faible et son gain d'information élevé.


# 10. Budget de branches

Le moteur dispose d'un budget :

```text
Budget_t = (
  max_active_branches,
  max_depth,
  max_tokens,
  max_compute,
  max_memory,
  max_wall_time,
  max_risk
)
```

Contrainte globale :

```text
Sum_i cost_i * active_i <= B_t
```

Le budget peut être hiérarchique :

```text
B_global
  -> B_session
      -> B_concept
          -> B_branch
```

Une branche ne peut pas monopoliser tout le budget sauf autorisation explicite.

### Réallocation

Si une branche gagne fortement en valeur :

```text
Delta Q_i > threshold
```

le moteur peut transférer du budget depuis des branches moins prometteuses.

Toute réallocation est journalisée.


# 11. Allocation dynamique : exploration vs exploitation

Le moteur doit arbitrer entre :
- **exploitation** : approfondir les branches déjà crédibles ;
- **exploration** : tester des branches moins connues.

On introduit :

```text
UCB_i = mean_reward_i + c * sqrt(log(N) / n_i)
```

ou une politique analogue.

Cette idée peut être adaptée conceptuellement :

```text
priority_i =
  expected_value_i
  + exploration_bonus_i
```

L'objectif n'est pas de prouver une hypothèse par répétition, mais de choisir où investir le prochain coût marginal d'exploration.

Un paramètre :

```text
epsilon_explore in [0,1]
```

contrôle la proportion de budget dédiée aux branches alternatives.


# 12. Détection de duplication

Avant de créer une nouvelle branche, le moteur calcule :

```text
sim(b_new, b_existing)
```

Si :

```text
sim >= theta_duplicate
```

il ne crée pas automatiquement une nouvelle branche.

Trois cas :

1. **Même hypothèse, nouvelles preuves** -> enrichir la branche existante.
2. **Même noyau, scope différent** -> créer une spécialisation.
3. **Différence substantielle** -> conserver une nouvelle branche.

La déduplication tient compte de :
- sémantique ;
- scope ;
- temporalité ;
- acteurs ;
- causalité ;
- statut épistémique.

Deux formulations proches linguistiquement peuvent être conceptuellement différentes.


# 13. Contradictions et conflits

Un conflit est représenté explicitement :

```text
Conflict_ij = (
  b_i,
  b_j,
  overlap_scope,
  contradiction_strength,
  unresolved_questions
)
```

Le moteur ne doit pas "résoudre" un conflit par moyenne automatique.

Stratégies :

```text
KEEP_BOTH
REQUEST_EVIDENCE
SPLIT_SCOPE
TEMPORALIZE
PERSPECTIVIZE
SIMULATE
REFUTE_ONE
MERGE_IF_COMPATIBLE
```

Exemple de séparation de scope :

```text
H1: X est vrai dans C1
H2: X est faux dans C2
```

peut remplacer une fausse contradiction globale.


# 14. SPLIT : divergence contrôlée

L'opérateur `SPLIT` de la Structure 02 devient ici une opération complète :

```text
SPLIT(b, criteria) -> {b_1,...,b_k}
```

Préconditions :
- critères de divergence explicites ;
- budget suffisant ;
- provenance conservée.

Postconditions :
- chaque enfant référence le parent ;
- le parent peut rester actif comme abstraction ;
- les différences sont documentées.

Exemple :

```text
b0 = "le signal a une cause externe"

SPLIT causal:
  b1 = "cause environnementale"
  b2 = "cause logicielle"
  b3 = "cause instrumentale"
  b4 = "cause inconnue"
```

L'opération doit inclure une branche nulle ou inconnue lorsque cela est pertinent.


# 15. MERGE : fusion sous preuve de compatibilité

La fusion :

```text
MERGE({b_1,...,b_k}) -> b_m
```

n'est autorisée que si :

```text
compatibility >= theta_merge
AND unresolved_contradictions <= epsilon
AND provenance_preserved = true
```

Le résultat contient :

```text
merged_from = [b_1,...,b_k]
```

et conserve les divergences résiduelles.

### Fusion avec perte

Si la fusion compresse de l'information :

```text
loss(b_m) > 0
```

elle doit produire un `compression_report`.

Une fusion ne détruit jamais immédiatement ses parents.


# 16. Pruning réversible

Le pruning réduit l'espace actif.

```text
PRUNE(b_i, reason)
```

Critères possibles :
- coût trop élevé ;
- faible information attendue ;
- redondance ;
- risque excessif ;
- contradiction forte ;
- budget saturé ;
- faible pertinence contextuelle.

Score possible :

```text
P_i =
  low_value
  + redundancy
  + cost_pressure
  + risk
```

Si `P_i >= theta_prune`, la branche passe à `PRUNED`.

Mais on conserve :

```text
snapshot
provenance
evidence_refs
reason
reopen_conditions
```

Le moteur doit pouvoir expliquer : "cette branche a été élaguée parce que..."


# 17. Réouverture

Une branche élaguée peut être réouverte lorsqu'un déclencheur apparaît :

```text
REOPEN(b_i, trigger)
```

Déclencheurs :
- nouvelle preuve ;
- invalidation d'une branche dominante ;
- changement de contexte ;
- budget augmenté ;
- demande utilisateur ;
- changement de permission ;
- découverte d'une relation.

Condition :

```text
reopen_score_i =
  relevance_new
  * evidence_novelty
  * expected_information_gain
```

Si le seuil est atteint, la branche retourne dans l'ensemble actif avec un nouveau numéro de révision.


# 18. Simulation de branches

Une branche peut être simulée sans être acceptée.

```text
SIMULATE(b_i, model, horizon) -> simulation_result
```

Le résultat doit être séparé de la branche source :

```text
simulation != observation
```

Une simulation produit :
- hypothèses d'entrée ;
- paramètres ;
- modèle ;
- traces ;
- sortie ;
- sensibilité ;
- limites.

On peut comparer :

```text
CompareSimulations(b_i, b_j)
```

sur des métriques communes.

La simulation peut faire monter une branche de `FORMALIZED` à `SIMULATED`, mais pas directement à `OBSERVED`.


# 19. Gain d'information

Le moteur peut choisir la prochaine expérience selon le gain d'information attendu.

Pour une action de test `a` :

```text
IG(a) = H(Before) - E[H(After | a)]
```

où `H` mesure l'incertitude sur l'ensemble de branches.

Intuition :
- un test est intéressant s'il permet de distinguer plusieurs branches ;
- un test qui confirme ce que toutes les branches prédisent apporte peu de discrimination.

On définit donc :

```text
test_value =
  information_gain
  * feasibility
  * safety
  / (1 + cost + risk)
```

Cette fonction permet à Forge de proposer l'expérience la plus discriminante au lieu de simplement approfondir la branche dominante.


# 20. Consensus, désaccord et absence de consensus

Le moteur ne force pas un consensus.

On peut produire :

```text
ConsensusState = (
  dominant_branch,
  alternatives,
  unresolved_conflicts,
  confidence_gap,
  evidence_gap
)
```

Cas :
- **consensus fort** : une branche domine avec preuves indépendantes ;
- **consensus faible** : une branche domine légèrement ;
- **pluralité stable** : plusieurs branches restent crédibles ;
- **indétermination** : aucune branche ne dispose de support suffisant.

Une réponse utilisateur peut donc être :

```text
"Trois hypothèses restent ouvertes ; aucune ne peut être privilégiée avec les preuves actuelles."
```

C'est un résultat valide.


# 21. Frontière entre hypothèse et action

Aucune branche n'agit directement.

Pipeline :

```text
BRANCH
  -> EVALUATE
  -> VALIDATE
  -> ACTION_CANDIDATE
  -> OCCP_GATE
  -> PERMISSION
  -> EXECUTE
```

Une branche peut devenir `EXECUTION_CANDIDATE` seulement si :
- son action associée est définie ;
- le risque est évalué ;
- la permission existe ;
- l'action est réversible ou explicitement validée ;
- les alternatives importantes ont été considérées.

Invariant :

```text
branch.active = true
does NOT imply
action.allowed = true
```


# 22. Intégration OCCP

OCCP régule l'ouverture et la fermeture.

Pour une branche :

```text
Gate_i = G(
  coherence_i,
  permission_i,
  capacity_i,
  reversibility_i,
  safety_i
)
```

Le moteur peut appliquer plusieurs niveaux :

```text
G_GENERATE
G_EXPAND
G_SIMULATE
G_VALIDATE
G_EXECUTE
```

Une permission de générer une branche ne vaut pas permission de la simuler avec ressources importantes, ni permission d'agir.

### Vecteur d'ouverture

```text
V_i+ = alpha_i * direction_i * permission_i * safety_i
```

La somme des ouvertures doit respecter le budget global.


# 23. Intégration TimeWarp

TimeWarp conserve la trajectoire temporelle des branches.

Chaque mutation produit :

```text
Delta_t = (
  branch_id,
  revision_before,
  operation,
  revision_after,
  timestamp,
  cause
)
```

On peut reconstruire :

```text
b_i(t0)
b_i(t1)
...
b_i(tn)
```

et demander :
- "que croyait le moteur à t0 ?"
- "quelle preuve a changé la priorité ?"
- "quelles branches ont été abandonnées ?"
- "quel serait l'état si l'événement e n'avait pas eu lieu ?"

Cela permet aussi des **forks temporels** :

```text
FORK_TIME(snapshot_tk) -> alternate_branch_space
```

sans modifier l'historique réel.


# 24. Intégration NCU

Chaque branche référence un ou plusieurs NCU.

```text
branch.targets = [node_id_1, node_id_2, ...]
```

Une branche peut :
- interpréter un nœud ;
- proposer une relation entre nœuds ;
- créer un sous-espace ;
- réviser le statut épistémique d'un nœud ;
- proposer un nouveau nœud.

Le NCU stocke l'objet conceptuel ; le MBC stocke **les trajectoires concurrentes portant sur cet objet**.

Séparation :

```text
NCU = "ce qui est représenté"
MBC = "les interprétations concurrentes de ce qui est représenté"
```


# 25. Intégration à l'algèbre COSMOS-F

Les opérateurs de la Structure 02 deviennent des transitions de branche.

Exemples :

```text
DOWN(b)       -> approfondit le scope
UP(b)         -> généralise
SHIFT(b)      -> change de voisin conceptuel
PERSPECT(b)   -> change d'acteur/perspective
TIME(b)       -> change de fenêtre temporelle
LINK(b1,b2)   -> crée une relation
SPLIT(b)      -> crée des alternatives
MERGE(B)      -> fusionne des compatibles
COMPRESS(b)   -> réduit la représentation
EXPAND(b)     -> ajoute des dimensions
COMPARE(B)    -> produit une matrice de différence
VALIDATE(b)   -> évalue les gates
```

Toute opération produit un delta et une provenance.


# 26. Matrice de comparaison

Pour un ensemble de branches :

```text
M_ij = Compare(b_i, b_j)
```

La matrice peut contenir :

```text
semantic_distance
scope_overlap
evidence_overlap
prediction_difference
risk_difference
cost_difference
epistemic_difference
```

Exemple conceptuel :

| | b1 | b2 | b3 |
|---|---:|---:|---:|
| b1 | 0 | 0.72 | 0.31 |
| b2 | 0.72 | 0 | 0.81 |
| b3 | 0.31 | 0.81 | 0 |

Cette matrice sert à :
- détecter les doublons ;
- choisir des branches maximisant la diversité ;
- sélectionner des tests discriminants ;
- proposer des merges.


# 27. Distance entre branches

Une distance composite :

```text
D(b_i,b_j) =
  beta_s * D_semantic
+ beta_c * D_scope
+ beta_t * D_time
+ beta_p * D_perspective
+ beta_e * D_evidence
+ beta_r * D_relations
```

Les poids dépendent de la requête.

Pour une question temporelle, `beta_t` augmente.
Pour une question multi-acteurs, `beta_p` augmente.

La distance n'est donc pas absolue :

```text
D = D_contextual(b_i,b_j | query)
```


# 28. Compression et résumés hiérarchiques

Un grand espace de branches doit être compressible.

On définit des clusters :

```text
Cluster_k = {b_i | similarity >= theta_cluster}
```

Chaque cluster reçoit un résumé :

```text
summary_k = (
  shared_core,
  principal_differences,
  strongest_evidence,
  unresolved_points,
  representative_branches
)
```

La compression ne remplace pas les branches sources.

Niveaux :

```text
L0 = branches détaillées
L1 = groupes de branches
L2 = familles d'hypothèses
L3 = méta-hypothèses
```

Le HUD peut changer de niveau sans perdre la structure.


# 29. Architecture logicielle

Architecture proposée :

```text
BranchEngine
├── Generator
├── Deduplicator
├── Scorer
├── BudgetManager
├── ConflictDetector
├── EvidenceManager
├── Simulator
├── Pruner
├── Reopener
├── Merger
├── ProvenanceLedger
├── TimeWarpAdapter
├── OCCPGateAdapter
├── NCUAdapter
└── QueryPlanner
```

### Responsabilités

**Generator** : produit des alternatives.  
**Deduplicator** : évite les variantes redondantes.  
**Scorer** : calcule les vecteurs de priorité.  
**BudgetManager** : alloue ressources et profondeur.  
**ConflictDetector** : détecte incompatibilités réelles.  
**EvidenceManager** : indexe preuves et contre-preuves.  
**Simulator** : exécute des mondes hypothétiques isolés.  
**Pruner/Reopener** : contrôle l'espace actif.  
**ProvenanceLedger** : fournit l'audit complet.


# 30. Modèle de données JSON

Exemple minimal :

```json
{
  "branch_id": "br_001",
  "revision": 4,
  "hypothesis": {
    "statement": "H1",
    "scope": ["node:abc"],
    "epistemic_state": "FORMALIZED"
  },
  "scores": {
    "plausibility": 0.58,
    "uncertainty": 0.37,
    "utility": 0.76,
    "information_gain": 0.82,
    "cost": 0.21,
    "risk": 0.08
  },
  "evidence": ["ev_1", "ev_4"],
  "counter_evidence": ["ev_8"],
  "parents": ["br_000"],
  "relations": [
    {"type": "CONTRADICTS", "target": "br_002", "strength": 0.7}
  ],
  "lifecycle": "ACTIVE",
  "permissions": {
    "expand": true,
    "simulate": true,
    "execute": false
  },
  "provenance": {
    "operation": "SPLIT",
    "actor": "forge"
  }
}
```


# 31. Schéma SQLite

Tables principales :

```sql
branches(
  branch_id TEXT,
  revision INTEGER,
  hypothesis TEXT,
  epistemic_state TEXT,
  lifecycle TEXT,
  created_at TEXT,
  updated_at TEXT,
  state_hash TEXT,
  PRIMARY KEY(branch_id, revision)
);

branch_parents(
  branch_id TEXT,
  revision INTEGER,
  parent_id TEXT,
  parent_revision INTEGER
);

branch_scores(
  branch_id TEXT,
  revision INTEGER,
  metric TEXT,
  value REAL
);

branch_evidence(
  branch_id TEXT,
  revision INTEGER,
  evidence_id TEXT,
  polarity INTEGER
);

branch_relations(
  source_id TEXT,
  target_id TEXT,
  relation_type TEXT,
  strength REAL,
  scope TEXT
);

branch_events(
  event_id TEXT PRIMARY KEY,
  branch_id TEXT,
  revision_before INTEGER,
  revision_after INTEGER,
  operation TEXT,
  payload TEXT,
  timestamp TEXT
);
```

Les révisions sont append-only afin de faciliter TimeWarp et l'audit.


# 32. API proposée

Endpoints conceptuels :

```text
POST /branches/generate
GET  /branches/{id}
GET  /branches/{id}/history
POST /branches/{id}/expand
POST /branches/{id}/split
POST /branches/compare
POST /branches/merge
POST /branches/{id}/prune
POST /branches/{id}/reopen
POST /branches/{id}/simulate
POST /branches/{id}/validate
GET  /spaces/{space_id}/frontier
GET  /spaces/{space_id}/conflicts
GET  /spaces/{space_id}/pareto
```

Réponse typique :

```json
{
  "result": "accepted",
  "operation_id": "op_...",
  "created_branches": ["br_12", "br_13"],
  "warnings": [],
  "budget_after": {...}
}
```

Une opération refusée doit fournir une raison inspectable.


# 33. Langage de requête

Exemples de commandes haut niveau :

```text
BRANCH FROM node:abc MODE divergent LIMIT 5
COMPARE br:1 br:2 ON evidence,scope,predictions
EXPAND br:3 DEPTH 2 BUDGET low
PRUNE WHERE utility < 0.2 AND redundancy > 0.8
REOPEN br:9 BECAUSE evidence:ev_44
SIMULATE br:2 HORIZON 10
SHOW CONFLICTS FOR node:abc
SHOW PARETO FOR space:s1
```

Le parser compile ces commandes vers l'algèbre COSMOS-F puis vers des opérations atomiques auditables.


# 34. Scheduler Forge

Forge peut utiliser le moteur comme planificateur.

Boucle :

```text
1. Observe current branch frontier
2. Calculate unresolved questions
3. Estimate value of candidate operations
4. Apply OCCP gates
5. Allocate budget
6. Execute one or more safe operations
7. Record deltas
8. Re-score
9. Stabilize
10. Ask user if a permission boundary is reached
```

Pseudo-code :

```python
while budget.available():
    frontier = engine.frontier()
    ops = planner.candidate_operations(frontier)
    ranked = scorer.rank(ops)
    op = gates.first_admissible(ranked)
    if op is None:
        break
    result = execute(op)
    ledger.append(result)
    engine.rescore()
```

L'autonomie reste bornée par les permissions définies dans OCCP.


# 35. Invariants formels

Le moteur doit préserver au minimum :

```text
I1. Toute branche possède une provenance.
I2. Toute révision possède un parent temporel ou un événement de création.
I3. PRUNED n'implique jamais DELETED.
I4. SIMULATED n'implique jamais OBSERVED.
I5. ACTIVE n'implique jamais EXECUTABLE.
I6. Une fusion conserve les identifiants des sources.
I7. Une contradiction possède un scope explicite.
I8. Une preuve ne peut pas être comptée plusieurs fois comme indépendante si elle partage la même source primaire.
I9. Le budget actif ne peut pas dépasser le budget admissible.
I10. Toute action externe passe par le gate d'exécution.
```

Ces invariants peuvent être vérifiés automatiquement après chaque transaction.


# 36. Transactions et rollback

Une transformation complexe est exécutée dans une transaction :

```text
BEGIN
  snapshot
  validate_preconditions
  execute operations
  validate_invariants
  commit
ELSE
  rollback
END
```

Exemple :

```text
SPLIT -> SCORE -> LINK -> PRUNE
```

Si `LINK` crée une violation d'invariant, l'ensemble est annulé ou laissé dans un état explicitement partiel selon la politique.

Le journal stocke :
- opérations commencées ;
- opérations terminées ;
- erreurs ;
- rollback ;
- état final.


# 37. Gestion des erreurs

Catégories :

```text
E_BRANCH_NOT_FOUND
E_REVISION_CONFLICT
E_BUDGET_EXCEEDED
E_PERMISSION_DENIED
E_SCOPE_INVALID
E_DUPLICATE_BRANCH
E_MERGE_CONFLICT
E_SIMULATION_UNAVAILABLE
E_PROVENANCE_MISSING
E_INVARIANT_FAILED
E_GATE_REJECTED
```

Chaque erreur doit inclure :
- code ;
- message humain ;
- contexte ;
- opération ;
- remédiation possible ;
- identifiant de trace.

Le moteur ne doit jamais masquer une erreur en produisant une branche "probablement correcte".


# 38. Tests unitaires

Tests essentiels :

```text
test_create_branch
test_revision_append_only
test_split_preserves_parent
test_merge_preserves_sources
test_prune_is_reversible
test_reopen_on_new_evidence
test_simulation_not_observation
test_execute_requires_gate
test_duplicate_detection
test_scope_aware_conflict
test_budget_enforcement
test_provenance_required
```

Chaque opérateur doit posséder :
- cas nominal ;
- cas limite ;
- entrée invalide ;
- rollback ;
- idempotence si applicable.


# 39. Tests de propriétés

Exemples :

```text
Property 1:
PRUNE(REOPEN(b)) preserves branch identity.

Property 2:
MERGE(SPLIT(b)) is not assumed equal to b;
difference must be measured.

Property 3:
For every committed operation:
all invariants hold.

Property 4:
No simulation result can transition directly to OBSERVED
without an external observation record.

Property 5:
Total active cost <= admissible budget.
```

Les tests génératifs peuvent créer des graphes aléatoires de branches pour rechercher des violations.


# 40. Tests de charge

Scénarios :

```text
10 branches
100 branches
1 000 branches
10 000 archived branches
100 000 evidence links
```

Mesures :
- temps de scoring ;
- temps de déduplication ;
- mémoire ;
- coût de reconstruction TimeWarp ;
- latence de requête du HUD ;
- taille du journal.

L'objectif n'est pas de garder 10 000 branches actives. Le moteur doit montrer que l'archivage et la compression maintiennent une frontière active petite.


# 41. Observabilité

Métriques :

```text
branch_active_count
branch_archived_count
branch_generation_rate
branch_prune_rate
branch_reopen_rate
mean_branch_depth
evidence_count
conflict_count
budget_utilization
simulation_count
gate_rejection_count
rollback_count
```

Logs structurés :

```json
{
  "event": "branch_pruned",
  "branch_id": "br_...",
  "reason": "redundancy",
  "score": 0.91,
  "reopen_condition": "new_independent_evidence"
}
```

Le HUD doit pouvoir afficher les explications sans lire les logs bruts.


# 42. HUD de navigation

Vue recommandée :

```text
[Concept central]
      |
  [Famille A] ---- [Famille B]
    /   \             |
  b1    b2            b3
```

Encodages visuels possibles :
- taille = utilité ;
- profondeur = degré d'expansion ;
- bordure = statut épistémique ;
- opacité = activité ;
- symbole = conflit ;
- badge = preuve nouvelle.

Panneaux :
- branche sélectionnée ;
- preuves / contre-preuves ;
- relations ;
- score vectoriel ;
- historique ;
- opérations disponibles ;
- budget ;
- raison de pruning ;
- conditions de réouverture.

La représentation graphique reste une vue ; elle n'est pas la source de vérité.


# 43. Exemple complet A — hypothèses techniques

Question :

```text
"Pourquoi un service local ne démarre-t-il pas ?"
```

Branches :

```text
b1: port déjà occupé
b2: dépendance absente
b3: permission insuffisante
b4: configuration invalide
b5: ressource GPU indisponible
b6: cause inconnue
```

Le moteur associe des tests :
- vérifier le port ;
- vérifier les dépendances ;
- lire les permissions ;
- valider le fichier de config ;
- interroger l'état GPU.

Forge choisit d'abord les tests à faible coût et fort gain d'information.

Après vérification du port :
- b1 passe à `REFUTED` ;
- les autres sont rescored.

Après lecture d'un log de dépendance absente :
- b2 passe à `OBSERVED` ;
- b2 devient dominante ;
- aucune action n'est encore exécutée tant que l'opération de correction n'a pas passé le gate.


# 44. Exemple complet B — interprétations conceptuelles

Un événement `e_t` est compatible avec plusieurs interprétations :

```text
b1: relation causale
b2: simple corrélation
b3: effet d'un facteur tiers
b4: artefact de mesure
b5: coïncidence
```

Le moteur évite de supprimer `b5` uniquement parce qu'elle paraît moins intéressante.

Il demande :
- quelles prédictions différencient les branches ?
- quel test est faisable ?
- quelles preuves sont indépendantes ?
- quel scope est concerné ?

Une observation supplémentaire peut augmenter `b3` et diminuer `b1`, sans changer les identités des branches.

La carte conserve donc l'historique du changement d'interprétation.


# 45. Exemple complet C — branches temporelles

On compare trois scénarios :

```text
b1: effet immédiat
b2: effet retardé
b3: absence d'effet
```

TimeWarp associe à chaque branche une fonction prédictive :

```text
prediction_i(t)
```

Le moteur peut créer :

```text
TIME_SHIFT(b1, +1h)
TIME_SHIFT(b2, +24h)
```

et comparer les observations réelles aux prédictions.

La temporalisation empêche de déclarer deux branches contradictoires simplement parce qu'elles parlent de moments différents.


# 46. Exemple complet D — perspectives multiples

Un même système peut avoir plusieurs perspectives :

```text
p_user
p_operator
p_developer
p_auditor
```

Branches :

```text
b1@user
b2@developer
b3@auditor
```

`PERSPECT` modifie le cadre sans effacer le contenu source.

Une contradiction peut disparaître lorsque les scopes de perspective sont explicités.

Le moteur peut ensuite construire une méta-branche :

```text
b_meta = "les trois descriptions sont compatibles car elles décrivent des niveaux différents"
```

mais uniquement si la compatibilité est démontrée.


# 47. Politique de sécurité conceptuelle

Le moteur doit éviter plusieurs échecs :

### Sur-interprétation
Créer une branche "aucun lien démontré".

### Confirmation automatique
Toujours conserver ou générer des contre-hypothèses pertinentes.

### Boucle auto-renforçante
Une branche ne peut pas utiliser ses propres sorties comme preuve indépendante sans marquage de dépendance.

### Faux consensus
Plusieurs branches générées par le même modèle ne comptent pas comme plusieurs sources indépendantes.

### Action prématurée
Aucune priorité conceptuelle ne court-circuite les permissions d'exécution.

### Perte de provenance
Toute information non traçable est marquée comme telle et reçoit un statut faible.


# 48. Politique de sources indépendantes

On définit des groupes de dépendance :

```text
independence_group(evidence)
```

Deux articles qui reprennent la même source primaire ne comptent pas comme deux confirmations indépendantes.

Score d'indépendance :

```text
I(E) = number_of_independent_primary_groups / number_of_items
```

Le moteur peut afficher :

```text
5 preuves apparentes
2 sources réellement indépendantes
```

Cette distinction est essentielle pour la corroboration.


# 49. Frontière active

La **frontière active** est le petit ensemble sur lequel le moteur dépense actuellement du budget.

```text
F_t subset B_t
```

Sélection :

```text
F_t =
  TopK(
    Pareto(
      admissible(B_t)
    )
  )
```

On impose aussi une diversité minimale afin que `TopK` ne contienne pas cinq variantes presque identiques.

La frontière peut contenir par exemple :
- 2 branches dominantes ;
- 2 alternatives ;
- 1 contre-hypothèse ;
- 1 branche inconnue/null.


# 50. Stabilisation

À intervalles réguliers, le moteur lance :

```text
STABILIZE(space)
```

Étapes :
1. arrêter les expansions non nécessaires ;
2. recalculer les scores ;
3. dédupliquer ;
4. détecter les conflits ;
5. vérifier les invariants ;
6. compresser les branches anciennes ;
7. mettre à jour les résumés ;
8. recalculer le budget ;
9. produire un état stable TimeWarp.

Un état stable est un checkpoint, pas une vérité définitive.


# 51. Formule maîtresse du MBC

On peut résumer le moteur par :

```text
B_t
  --Generate--> B_t ∪ G_t
  --Score-->    S_t
  --Gate-->     A_t
  --Budget-->   F_t
  --Explore-->  B'_t
  --Evidence--> E'_t
  --Resolve-->  C'_t
  --Prune-->    P_t
  --Stabilize--> B_{t+1}
```

Formellement :

```text
B_{t+1} =
Stabilize(
  Reopen(
    Prune(
      UpdateEvidence(
        Explore(
          SelectBudgeted(
            Gate(
              Score(
                B_t ∪ Generate(C_t)
              )
            )
          )
        )
      )
    )
  )
)
```

avec la contrainte :

```text
ActiveCost(B_{t+1}) <= Budget_admissible(t+1)
```

et l'invariant :

```text
Hypothesis(b) != Fact(b) != Action(b)
```


# 52. MVP expérimental

Le premier MVP peut rester volontairement simple.

### Phase M0 — stockage
- SQLite ;
- branches ;
- parents ;
- scores ;
- preuves ;
- événements.

### Phase M1 — moteur
- `create_branch`;
- `split`;
- `score`;
- `compare`;
- `prune`;
- `reopen`.

### Phase M2 — OCCP
- budgets ;
- permissions ;
- gates.

### Phase M3 — TimeWarp
- révisions append-only ;
- checkpoints ;
- reconstruction.

### Phase M4 — Forge
- génération d'alternatives ;
- planification des prochains tests.

### Phase M5 — HUD
- graphe ;
- filtres ;
- frontier ;
- preuves ;
- historique.

Le MVP n'a pas besoin de simuler l'infini. Il doit montrer une propriété plus importante : **la croissance peut être ouverte tout en restant contrôlée, traçable et réversible**.


# 53. Critères d'acceptation

Le moteur est considéré fonctionnel lorsque :

```text
A1. 10+ branches peuvent coexister sans fusion involontaire.
A2. Une branche peut être pruned puis réouverte.
A3. Toute branche affiche son origine.
A4. Les preuves et contre-preuves sont séparées.
A5. Le budget limite réellement l'expansion.
A6. Les contradictions sont scope-aware.
A7. Une simulation n'est jamais affichée comme observation.
A8. Une action exige un gate distinct.
A9. TimeWarp reconstruit l'état antérieur.
A10. Le HUD peut expliquer pourquoi une branche est active, suspendue ou élaguée.
A11. Une nouvelle preuve peut inverser le classement.
A12. Les parents d'un merge restent récupérables.
```


# 54. Suite logique vers Structure 04

La Structure 03 produit un espace de possibilités robuste. La prochaine structure logique est le **système de zoom fractal et de coordonnées conceptuelles multi-échelles**.

Elle devra définir :
- profondeur fractale ;
- changement d'échelle ;
- sous-espaces et sur-espaces ;
- projection dimensionnelle ;
- conservation des ancres ;
- distance conceptuelle ;
- voisinages ;
- atlas local ;
- navigation infinie paresseuse ;
- compression inter-échelles ;
- coordonnées relatives plutôt qu'absolues.

Le MBC fournira alors les branches ; la Structure 04 déterminera **où elles vivent et comment on se déplace entre leurs échelles**.
