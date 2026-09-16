# COSMOS-F - Structure 07
## TimeWarp complet : memoire temporelle, journal causal et branches temporelles

**Version :** 0.7  
**Statut :** specification conceptuelle, mathematique et technique  
**Dependances :** Structure 01 - NCU ; Structure 02 - Algebre COSMOS-F ; Structure 03 - Moteur de branches concurrentes ; Structure 04 - Zoom fractal ; Structure 05 - Regulateur multi-ressources ; Structure 06 - Stabilisation/checkpoints/recovery.  
**Interfaces aval :** Structure 08 - Orchestrateur ; Structure 09 - Validation epistemique ; Structure 10 - Simulation multi-mondes.

---

## Resume

TimeWarp transforme l'histoire du systeme en un espace navigable, comparable, branchable et rejouable.

Il conserve non seulement la derniere version, mais aussi :

```text
ce qui s'est produit
quand cela s'est produit
quand cela a ete observe
quand cela a ete ingere
quand cela a ete revise
quelle cause operationnelle l'a declenche
quelle branche etait active
quels deltas ont transforme l'etat
quels checkpoints etaient disponibles
quelles informations etaient connues au moment d'une decision
```

TimeWarp distingue :

```text
event_time
observation_time
ingestion_time
revision_time
transaction_time
valid_time
logical_time
simulation_time
wall_clock_time
monotonic_time
```

Ses operations principales sont :

```text
STATE_AT
DIFF
HISTORY
CAUSES
SNAPSHOT
CHECKPOINT_REF
FORK
MERGE
REBASE
REPLAY
REVERT
ANCHOR
COMPRESS_HISTORY
```

Principes centraux :

```text
naviguer vers le passe != reecrire le passe
voir un ancien etat != restaurer activement cet ancien etat
chronologie != causalite
simulation temporelle != histoire reelle
```

---

# 1. Mission de TimeWarp

TimeWarp est la couche temporelle commune de COSMOS-F.

Il fournit huit fonctions fondamentales :

```text
CAPTURE
ORDER
LINK
RECONSTRUCT
COMPARE
BRANCH
REPLAY
AUDIT
```

Il sert a la fois de :
- memoire systeme ;
- journal de provenance ;
- support de debugging ;
- base de recovery ;
- support de preregistration ;
- moteur d'analyse historique ;
- interface vers les forks de simulation.

# 2. Etat global temporel

On represente un etat global comme :

```text
X_t = (
  NCU_t,
  Branches_t,
  Navigation_t,
  Budgets_t,
  Permissions_t,
  Plans_t,
  Models_t,
  Evidence_t,
  Recovery_t
)
```

TimeWarp ne stocke pas necessairement `X_t` en entier a chaque instant.

Il reconstruit :

```text
X_t =
Reduce(
  snapshot_nearest_before(t),
  events_after_snapshot_until(t)
)
```

# 3. Temps multiples

Le moteur utilise un vecteur temporel :

```text
T(e) = (
  t_event,
  t_observed,
  t_ingested,
  t_revised,
  t_logical
)
```

Ces valeurs peuvent differer fortement.

Exemple :

```text
08:00 evenement
08:02 observation
08:07 ingestion
09:30 correction de l'interpretation
```

# 4. Event time

`event_time` correspond au moment ou l'evenement est suppose s'etre produit dans le domaine observe.

Il peut etre :

```text
EXACT
INTERVAL
ESTIMATED
UNKNOWN
```

Un evenement importe tardivement garde son ancien `event_time`.

# 5. Observation time

`observation_time` est le moment ou l'information a ete obtenue par :
- humain ;
- instrument ;
- service ;
- agent ;
- monitor.

Il ne prouve pas que l'evenement s'est produit exactement a cet instant.

# 6. Ingestion time

`ingestion_time` est le moment ou TimeWarp recoit l'information.

Il permet de distinguer :

```text
"cela s'est passe hier"
```

de :

```text
"le systeme l'a appris aujourd'hui"
```

# 7. Revision time

`revision_time` indique quand un objet ou une interpretation a ete revise.

Un evenement ancien peut recevoir une nouvelle interpretation sans que son event time soit change.

# 8. Logical time

Pour les composants distribues, TimeWarp conserve un ordre logique :

```text
logical_seq
```

ou un mecanisme de Lamport/vector clock.

Le temps logique sert a l'ordre causal, pas a mesurer une duree physique.

# 9. Wall clock et monotonic time

`wall_clock_time` sert au calendrier.

`monotonic_time` sert aux durees :

```text
duration =
monotonic_end - monotonic_start
```

Cela evite les erreurs dues aux ajustements d'horloge.

# 10. Bitemporalite

TimeWarp peut conserver deux axes :

```text
valid_time
transaction_time
```

`valid_time` :
- quand l'information est consideree valide dans le domaine.

`transaction_time` :
- quand le systeme connaissait/enregistrait cette information.

On peut alors demander :

```text
"Que croyait le systeme le 20 a propos de ce qui etait valide le 18 ?"
```

# 11. Intervalles temporels

Un fait peut etre valide sur :

```text
[t_start, t_end)
```

Une fin inconnue est representee explicitement.

Le moteur supporte :
- instant ;
- intervalle ;
- intervalle ouvert ;
- temps incertain.

# 12. Incertitude temporelle

Representation :

```text
TimeValue =
Exact(t)
| Interval(a,b)
| Distribution(p)
| Unknown
```

Le moteur n'invente jamais une precision absente.

# 13. Event canonique

Objet :

```text
Event = (
  event_id,
  event_type,
  payload_ref,
  event_time,
  observation_time,
  ingestion_time,
  logical_time,
  actor,
  branch_id,
  causation_id,
  correlation_id,
  object_refs,
  provenance,
  hash
)
```

# 14. Immutabilite

Un event committe est immuable.

Une correction cree :

```text
CORRECTION_EVENT
```

qui reference l'event original.

Principe :

```text
correct != overwrite
```

# 15. Event sourcing

L'etat est derive du journal :

```text
State_k =
Reduce(e_1, e_2, ..., e_k)
```

Les vues actuelles peuvent etre reconstruites si les reducers et schemas restent disponibles.

# 16. Projection de vue

Une vue est :

```text
View =
Project(
  EventLog,
  query,
  branch,
  time_frame
)
```

La vue n'est pas la source historique.

# 17. Delta canonique

Une transformation produit :

```text
Delta = (
  operation,
  before_ref,
  after_ref,
  changed_fields,
  side_effects,
  inverse_info,
  cause,
  provenance
)
```

# 18. Classification des deltas

Un delta peut etre :

```text
INVERTIBLE
COMPENSATABLE
REGENERABLE
NON_REVERSIBLE
```

Structure 06 utilise cette classification pour le recovery.

# 19. Snapshot canonique

```text
Snapshot = (
  snapshot_id,
  branch_id,
  logical_seq,
  state_ref,
  schema_version,
  reducer_version,
  hash,
  created_at,
  status
)
```

# 20. Snapshot complet

Un snapshot complet permet une reconstruction directe d'un etat.

Il consomme plus de stockage mais reduit le temps de replay.

# 21. Snapshot incremental

Un incremental contient les differences depuis un parent.

Reconstruction :

```text
FullSnapshot
+ Delta_1
+ ...
+ Delta_n
```

# 22. Snapshot vs checkpoint

```text
Snapshot = representation d'etat
Checkpoint = snapshot valide + invariants + restauration testable
```

Structure 06 construit les checkpoints sur TimeWarp.

# 23. Frequence des snapshots

La frequence depend de :

```text
event_rate
rebuild_cost
storage_cost
RPO
RTO
risk
```

Structure 05 peut ajuster dynamiquement la cadence.

# 24. Reconstruction STATE_AT

Algorithme :

```text
1. find nearest compatible snapshot <= target
2. load snapshot
3. fetch ordered events until target
4. apply reducers
5. validate state
6. return read-only historical view
```

# 25. STATE_AT read-only

Par defaut :

```text
STATE_AT(t)
```

ne modifie jamais le present.

C'est une consultation.

# 26. ACTIVE_REVERT

Pour revenir activement a un ancien etat :

```text
ACTIVE_REVERT(target)
```

TimeWarp cree une nouvelle revision presente.

Le passe original reste.

# 27. VIEW_PAST vs REVERT

Invariant :

```text
VIEW_PAST != ACTIVE_REVERT
```

Le HUD et l'API doivent les distinguer clairement.

# 28. Timeline

Une timeline est un ensemble d'evenements ordonnes totalement ou partiellement.

Dans un composant simple :

```text
e1 < e2 < e3
```

Dans un systeme distribue :

```text
e1 || e2
```

peut signifier "concurrents".

# 29. Happened-before

Relation :

```text
a -> b
```

si `a` precede causalement `b` selon la logique de Lamport.

Cette relation est plus informative que le simple timestamp.

# 30. Lamport clock

Regle :

```text
L_local =
max(L_local, L_received) + 1
```

Elle ordonne des evenements mais ne mesure pas le temps physique.

# 31. Vector clock

```text
V = [v1,v2,...,vn]
```

permet de determiner :
- `A` avant `B` ;
- `B` avant `A` ;
- concurrence.

# 32. Version vector

Pour un objet distribue :

```text
VersionVector(object)
```

permet de detecter des editions concurrentes.

# 33. Causation ID

Un evenement peut reference son declencheur direct :

```text
causation_id
```

Exemple :

```text
TASK_FAILED
-> RECOVERY_STARTED
```

# 34. Correlation ID

Une requete, un incident ou un plan possede :

```text
correlation_id
```

Tous ses events partagent ce lien.

# 35. Chronologie vs causalite

Invariant :

```text
A avant B
```

ne signifie pas automatiquement :

```text
A cause B
```

Le graphe de causation TimeWarp decrit une causalite operationnelle, non une preuve scientifique.

# 36. Graphe causal operationnel

```text
G_T = (Events, CausalEdges)
```

Operations :
- ancestors ;
- descendants ;
- shortest causal path ;
- incident subgraph.

# 37. Branches temporelles

Chaque event appartient a :

```text
branch_id
```

La branche principale peut etre :

```text
main
```

# 38. Fork

Operation :

```text
FORK(
  source_branch,
  at_event,
  new_branch
)
```

Le nouvel espace partage l'histoire jusqu'au point de fork.

# 39. Invariant de fork

Au point `k` :

```text
X_new(k) = X_parent(k)
```

Puis les histories divergent.

# 40. Fork read-only

Utilise pour examiner une alternative sans aucune mutation.

# 41. Fork experimental

Autorise :
- simulations ;
- changements internes ;
- replays ;
- tests.

Aucune consequence externe automatique.

# 42. Fork counterfactual

Structure 10 peut creer :

```text
COUNTERFACTUAL_BRANCH
```

Le branch type indique explicitement :

```text
SIMULATED
```

et non `HISTORICAL`.

# 43. Genealogie des branches

```text
Branch = (
  id,
  parent_branch,
  fork_event,
  type,
  status
)
```

La genealogie forme un DAG sans cycle.

# 44. Statut d'une branche

```text
ACTIVE
PAUSED
MERGED
ARCHIVED
ABANDONED
INVALIDATED
```

Aucun statut ne supprime l'histoire.

# 45. Merge temporel

```text
MERGE(A,B)
```

utilise :
- base commune ;
- deltas A ;
- deltas B ;
- politique de conflit.

# 46. Three-way merge

```text
O = common base
A = left
B = right
```

Comparer :

```text
Diff(O,A)
Diff(O,B)
```

avant integration.

# 47. Conflit de merge

Objet :

```text
TemporalConflict = (
  base,
  left,
  right,
  object,
  field,
  conflict_type,
  status
)
```

# 48. Strategies de conflit

```text
KEEP_LEFT
KEEP_RIGHT
MERGE_VALUES
KEEP_BOTH_BRANCHES
MANUAL_REQUIRED
```

Pour les hypotheses, `KEEP_BOTH_BRANCHES` est souvent preferable.

# 49. Rebase

```text
REBASE(branch_B onto branch_A)
```

rejoue les deltas de B sur une base plus recente.

L'ancienne branche reste archivee.

# 50. Replay

Modes :

```text
DRY_REPLAY
SANDBOX_REPLAY
LIVE_REPLAY
```

Le mode est obligatoire.

# 51. Dry replay

Recalcule les etats sans effets externes.

Usage :
- verification ;
- migration ;
- debugging ;
- audit.

# 52. Sandbox replay

Reexecute dans un environnement isole.

Usage :
- reproduire incident ;
- tester une version ;
- comparer deux reducers.

# 53. Live replay

Autorise uniquement si :
- permissions ;
- idempotence ;
- effet externe connu ;
- verification.

Par defaut, les effets externes sont bloques.

# 54. Replay contract

```text
ReplayContract = (
  deterministic,
  idempotent,
  external_effects,
  required_artifacts,
  required_runtime,
  tolerance
)
```

# 55. Determinisme

Replay exact possible seulement si :
- code compatible ;
- version connue ;
- inputs ;
- seeds ;
- ordre ;
- environnement.

# 56. Replay equivalent

Pour GPU/LLM/stochastic :

```text
EQUIVALENT_REPLAY
```

peut etre valide selon une tolerance ou un contrat statistique.

# 57. Seed et RNG state

Une operation aleatoire peut stocker :

```text
seed
rng_state
sampler_version
```

afin de maximiser la reproductibilite.

# 58. Temporal address

Exemples :

```text
timewarp://branch/main/event/e100
timewarp://branch/b2/state/seq/450
timewarp://object/node-12/history
```

# 59. Ancre temporelle

```text
Anchor = (
  anchor_id,
  branch,
  event_or_seq,
  label,
  reason,
  scope
)
```

Exemples :
- before-migration ;
- before-experiment ;
- known-good.

# 60. Retour a une ancre

Deux modes :

```text
VIEW
ACTIVE_REVERT
```

Le mode doit etre explicite.

# 61. Late events

Un evenement peut arriver tardivement.

Exemple :

```text
event_time = t0
ingestion_time = t5
```

Le moteur garde les deux.

# 62. Donnees hors ordre

TimeWarp supporte les events dont l'ordre d'ingestion differe de l'ordre d'evenement.

Les projections par event time sont recalculables.

# 63. Watermark

Pour les flux :

```text
watermark(t)
```

indique jusqu'ou le moteur considere la fenetre event-time suffisamment complete.

# 64. Politique des late data

Options :
- recompute ;
- historical correction ;
- notify dependents ;
- close window with warning.

Le choix est versionne.

# 65. Bitemporal query

Une assertion peut etre interrogee selon :

```text
valid_at(tv)
known_at(tt)
```

Cela permet d'eviter le hindsight bias.

# 66. KNOWN_AT

Fonction :

```text
KNOWN_AT(t)
```

ne retourne que les informations ingerees avant `t`.

# 67. Hindsight-safe backtest

Pour une decision historique :

```text
input.ingestion_time <= decision_time
```

condition obligatoire.

# 68. Correction retroactive

Une information peut etre valide historiquement mais decouverte plus tard.

On conserve :

```text
valid_time = old
transaction_time = new
```

# 69. HISTORY(object)

Commande :

```text
HISTORY(object_id)
```

retourne :
- revisions ;
- events ;
- relations ;
- status changes.

# 70. Field history

```text
HISTORY(object.field)
```

montre :
- valeurs successives ;
- auteurs ;
- causes ;
- timestamps.

# 71. DIFF

```text
DIFF(t0,t1,scope)
```

retourne :
- creations ;
- modifications ;
- suppressions logiques ;
- status changes ;
- relation changes.

# 72. Semantic diff

Pour NCU/claims :
- changement de proposition ;
- changement de scope ;
- changement de statut ;
- changement de preuves.

# 73. Structural diff

Pour graphes :
- nodes ;
- edges ;
- hyperedges ;
- branches ;
- frames ;
- dimensions.

# 74. Plan diff

Structure 08 peut comparer :
- plan v1 ;
- plan v2 ;
- tasks ;
- permissions ;
- couts ;
- priorities.

# 75. Model diff

Structure 10 compare :
- equations ;
- rules ;
- parameters ;
- calibration ;
- scope ;
- implementation.

# 76. Causal diff

Question :

```text
"quels changements en amont precedent ce changement en aval ?"
```

Le moteur explore le graphe de causation.

# 77. Timeline d'incident

Structure 06 enregistre :

```text
detection
quick fix
Doctor
ConnectBusBot
Immune
restore
verify
```

sur une timeline unique.

# 78. Timeline de plan

Structure 08 enregistre :
- GoalRequest ;
- plan versions ;
- task states ;
- replan triggers ;
- synthese.

# 79. Timeline epistemique

Structure 09 enregistre :

```text
CLAIM_CREATED
PREDICTION_REGISTERED
PROTOCOL_REGISTERED
DATA_COLLECTED
ANALYSIS_RUN
STATUS_CHANGED
```

# 80. Timeline de simulation

Structure 10 enregistre :
- model version ;
- world ;
- seed ;
- intervention ;
- run ;
- result ;
- invalidation.

# 81. Multi-resolution history

Niveaux :

```text
raw events
session summaries
daily summaries
milestones
major eras
```

Le detail reste ouvrable selon retention.

# 82. TIME_ZOOM

Operations :

```text
TIME_ZOOM_IN
TIME_ZOOM_OUT
TIME_PAN
```

permettent de naviguer du macro au micro.

# 83. Temporal frame

Une vue peut etre ordonnee par :

```text
event_time
observation_time
ingestion_time
logical_time
```

Le frame temporel courant est visible.

# 84. Temporal projection

Filtres :
- incidents ;
- plans ;
- claims ;
- simulations ;
- ressources ;
- branches.

Cela reduit le bruit.

# 85. Atlas temporel

L'atlas contient :
- milestones ;
- forks ;
- merges ;
- known-good ;
- incidents ;
- experiments.

Il ne tente pas d'afficher tous les events.

# 86. Milestone

```text
Milestone = (
  id,
  time_ref,
  label,
  significance,
  refs
)
```

Peut etre cree manuellement ou automatiquement.

# 87. Known-good milestone

Structure 06 peut marquer :

```text
KNOWN_GOOD
```

comme ancre de recovery.

# 88. Preregistration milestone

Structure 09 marque le moment ou une prediction/protocole est fige.

# 89. Experiment milestone

Structure 10 marque :
- campaign start ;
- calibration ;
- validation ;
- stress failure.

# 90. Temporal hypergraph

COSMOS-F peut representer :

```text
G(t) = (V(t),E(t),H(t))
```

Les relations elles-memes possedent :
- valid_from ;
- valid_to.

# 91. Historique de relation

Une edge peut etre :
- ajoutee ;
- suspendue ;
- remplacee ;
- refutee.

Son histoire est consultable.

# 92. Branch-time coordinates

Un objet peut etre adresse par :

```text
(branch, seq, valid_time, revision)
```

# 93. Distance temporelle

```text
d_T(A,B)
```

peut combiner :
- duree ;
- nombre de revisions ;
- causal steps ;
- changement semantique.

# 94. Distance causale

Nombre ou cout d'arcs dans le graphe causal operationnel.

# 95. Distance de revision

Nombre de revisions entre deux etats.

# 96. Distance semantique temporelle

Mesure l'ampleur du changement conceptuel entre deux versions.

# 97. Allen interval relations

Pour intervalles :

```text
BEFORE
MEETS
OVERLAPS
STARTS
DURING
FINISHES
EQUAL
```

utile pour phases et contraintes temporelles.

# 98. Contraintes temporelles

Exemple :

```text
TaskB starts after TaskA finishes
```

Le moteur peut detecter les violations.

# 99. Transaction temporelle

Une transaction utilise :

```text
transaction_id
```

Tous ses events partagent ce lien.

Un marker `COMMIT` determine la completion.

# 100. Transaction incomplete

Apres crash :
- events prepares ;
- events commits ;
- effets externes.

Structure 06 decide replay/rollback/compensation.

# 101. Saga timeline

Pour une operation distribuee :

```text
A
B
C
compensate B
compensate A
```

TimeWarp conserve la saga complete.

# 102. External effect record

Pour action externe :

```text
request
idempotency_key
effect_status
verification
```

Statuts :

```text
CONFIRMED
NOT_DONE
UNKNOWN
```

# 103. UNKNOWN effect

Invariant :

```text
UNKNOWN -> CHECK_BEFORE_RETRY
```

Jamais de replay aveugle.

# 104. Locks temporels

Chaque lock a :
- owner ;
- scope ;
- acquired_at ;
- released_at.

TimeWarp peut reconstruire un deadlock historique.

# 105. Wait-for history

Le graphe d'attente a t peut etre reconstruit pour analyse.

# 106. Resource timeline

Structure 05 peut publier :
- RAM ;
- VRAM ;
- tokens ;
- regime ;
- reservations.

Permet corréler charge et incidents.

# 107. Sampling dynamique

Metrics systeme :
- faible frequence en stable ;
- haute frequence en incident.

Le regulateur pilote le cout.

# 108. Anomaly event

Une anomalie detectee produit :

```text
ANOMALY_DETECTED
```

avec :
- pattern ;
- score ;
- scope ;
- causes candidates.

# 109. Loop signature

Une sequence repetee peut etre hashée :

```text
LOOP_SIGNATURE
```

Structure 06 peut ouvrir un circuit breaker.

# 110. Snapshot compaction

Une chaine longue de deltas peut etre consolidee en nouveau snapshot.

Le ledger reste la provenance.

# 111. Log segmentation

Le journal peut etre decoupe :

```text
segment_0001
segment_0002
...
```

pour stockage et verification.

# 112. Segment sealing

Un segment ferme devient immuable et hashé.

Il peut etre compresse ou archive.

# 113. Chainage de hash

Concept :

```text
H_n =
Hash(
  segment_n,
  H_{n-1}
)
```

permet de detecter des alterations.

# 114. Index temporel

Index :
- event time ;
- ingestion time ;
- object ;
- branch ;
- correlation ;
- causation.

Les index restent regenerables.

# 115. Index causal

Optimise :
- ancestors ;
- descendants ;
- incident graph.

# 116. Index par objet

```text
object_id -> event_ids
```

permet HISTORY rapide.

# 117. Retention policy

Categories :

```text
CRITICAL
LONG_TERM
COMPACTABLE
EPHEMERAL
```

La politique est versionnee.

# 118. Historique brut vs resume

Un resume ne remplace pas les sources critiques.

On distingue :

```text
RAW_LEDGER
SNAPSHOT
SUMMARY
```

# 119. Compression historique

Pour anciennes zones :
- summaries ;
- milestones ;
- checkpoints ;
- references.

Le detail peut etre archive.

# 120. Compression avec perte

Si perte :

```text
LOSSY_HISTORY_COMPRESSION
```

doit etre marquee avec rapport de perte.

# 121. Compaction sans perte

Peut :
- changer le format ;
- fusionner index ;
- compresser bytes.

Sans changer la semantique.

# 122. Temporal summary

Resume de periode :
- changements majeurs ;
- decisions ;
- incidents ;
- claims ;
- experiments ;
- next state.

Chaque item pointe vers events sources.

# 123. Cache d'etat historique

Les `STATE_AT` recents peuvent etre caches.

Le hash du ledger range garantit l'invalidation.

# 124. State memoization

Memoisation selon :

```text
(branch, seq, reducer_version)
```

# 125. Migration de schema

Les anciens events conservent leur schema original.

Des `upcasters` construisent une vue compatible actuelle.

# 126. Upcaster

```text
event_v1
-> normalized_view_v2
```

sans reecrire l'event original.

# 127. Compatibilite de replay

Un replay historique peut etre :
- EXACT ;
- EQUIVALENT ;
- PARTIAL ;
- IMPOSSIBLE.

Le statut est expose.

# 128. Permissions TimeWarp

Scopes :

```text
history:read
history:fork
history:replay-sandbox
history:merge
history:revert
history:admin
```

# 129. Lecture historique par defaut

Le mode d'audit est read-only.

Aucune action sur le present.

# 130. Protection append-only

Pas de `UPDATE event`.

Les changements passent par nouveaux events.

# 131. Tombstone

Si un objet doit etre retire des vues :

```text
TOMBSTONE
```

conserve la coherence historique selon politique.

# 132. Redaction-aware ledger

Pour donnees sensibles :
- payload externalise ;
- reference redacted ;
- metadata minimale conservee si autorisee.

Les exigences legales/politiques priment.

# 133. Chiffrement

Segments ou payloads sensibles peuvent etre chiffres.

Les cles restent hors ledger.

# 134. Audit trail

Rapport :

```text
who
what
when
why
from
to
result
recovery
```

# 135. Reconstruction d'une decision

Question :

```text
"Pourquoi cette decision a-t-elle ete prise ?"
```

TimeWarp rassemble :
- ce qui etait connu ;
- plan ;
- evidence ;
- budgets ;
- permissions ;
- alternatives.

# 136. Knowledge-at-time

```text
KNOWN_AT(t)
```

est central pour les analyses sans hindsight.

# 137. Import historique

Un vieux document importe aujourd'hui :

```text
event_time = old
ingestion_time = today
```

Il ne devient pas automatiquement une information "connue a l'epoque".

# 138. Git integration

Un commit peut etre lie :

```text
commit_hash
branch
author_time
commit_time
```

TimeWarp peut aligner commits et events AIONE.

# 139. Database integration

WAL/transaction IDs peuvent etre references.

Le ledger TimeWarp ne doit pas dupliquer inutilement un journal DB fiable.

# 140. Logs integration

Les logs externes sont ingeres avec :
- source ;
- offset ;
- timestamp ;
- hash si pertinent.

# 141. EventBus integration

Structure 08 publie les events d'orchestration vers TimeWarp.

Commandes et events restent distingues.

# 142. NCU integration

Events possibles :

```text
NODE_CREATED
NODE_UPDATED
RELATION_ADDED
RELATION_REMOVED
STATUS_CHANGED
```

# 143. MBC integration

Events :

```text
BRANCH_CREATED
BRANCH_SCORED
BRANCH_PRUNED
BRANCH_REOPENED
BRANCH_MERGED
```

# 144. Navigator integration

Optionnel :
- ZOOM ;
- PAN ;
- FRAME_CHANGE ;
- ANCHOR ;
- ROUTE.

Permet rejouer un parcours conceptuel.

# 145. Regulator integration

Events :
- REGIME_CHANGED ;
- BUDGET_RESERVED ;
- BUDGET_RELEASED ;
- DEGRADATION_APPLIED ;
- RECOVERY_MODE.

# 146. Recovery integration

Structure 06 utilise TimeWarp pour :
- timeline incident ;
- last-known-good ;
- retry history ;
- recovery plan ;
- verification.

# 147. Orchestrator integration

Structure 08 stocke :
- request ;
- plan vN ;
- task events ;
- replan ;
- synthesis.

# 148. Epistemic integration

Structure 09 utilise :
- preregistration time ;
- observation time ;
- analysis time ;
- status history.

# 149. Simulation integration

Structure 10 lie :
- model version ;
- world branch ;
- seed ;
- intervention ;
- run ;
- result.

Les contrefactuels restent clairement simules.

# 150. Query WHAT_CHANGED

```text
WHAT_CHANGED(scope,t0,t1)
```

retourne :
- structural ;
- epistemic ;
- operational ;
- budget ;
- model ;
- recovery changes.

# 151. Query WHY

```text
WHY(event)
```

explore :
- causation ;
- dependencies ;
- decision records.

# 152. Query WHAT_WAS_KNOWN

```text
WHAT_WAS_KNOWN_AT(t,scope)
```

filtre sur transaction/ingestion time.

# 153. Query WHAT_IF

```text
WHAT_IF(t,modification)
```

cree un fork pour Structure 10.

Il ne produit jamais automatiquement une "histoire alternative vraie".

# 154. Query CAN_RESTORE

```text
CAN_RESTORE(target)
```

verifie :
- snapshot ;
- artifacts ;
- schemas ;
- permissions ;
- recipe.

# 155. Query CAN_REPLAY

```text
CAN_REPLAY(range)
```

retourne :
- EXACT ;
- EQUIVALENT ;
- PARTIAL ;
- IMPOSSIBLE.

# 156. TimeWarp API

```text
POST /timewarp/events
GET  /timewarp/events/{id}
GET  /timewarp/state-at
GET  /timewarp/diff
GET  /timewarp/history/{object}
GET  /timewarp/causes/{event}
POST /timewarp/fork
POST /timewarp/merge
POST /timewarp/rebase
POST /timewarp/replay
POST /timewarp/revert
POST /timewarp/anchors
GET  /timewarp/branches
```

# 157. DSL

```text
TIME STATE AT "2026-08-29T18:00"
TIME DIFF t0 t1 SCOPE workspace
TIME HISTORY node:abc
TIME CAUSES event:e10 DEPTH 5
TIME FORK main AT e500 AS experiment
TIME REPLAY branch:experiment MODE sandbox
TIME MERGE experiment INTO main
TIME ANCHOR "before-wave"
```

# 158. Schema SQLite minimal

```sql
events(
  event_id TEXT PRIMARY KEY,
  event_type TEXT,
  branch_id TEXT,
  event_time TEXT,
  observation_time TEXT,
  ingestion_time TEXT,
  logical_seq INTEGER,
  causation_id TEXT,
  correlation_id TEXT,
  payload_json TEXT,
  hash TEXT
);

branches(
  branch_id TEXT PRIMARY KEY,
  parent_branch TEXT,
  fork_event_id TEXT,
  branch_type TEXT,
  status TEXT,
  created_at TEXT
);

snapshots(
  snapshot_id TEXT PRIMARY KEY,
  branch_id TEXT,
  logical_seq INTEGER,
  state_ref TEXT,
  schema_version TEXT,
  reducer_version TEXT,
  hash TEXT
);

anchors(
  anchor_id TEXT PRIMARY KEY,
  branch_id TEXT,
  event_id TEXT,
  label TEXT
);

object_events(
  object_id TEXT,
  event_id TEXT
);

causal_edges(
  source_event TEXT,
  target_event TEXT,
  relation TEXT
);
```

# 159. Architecture logicielle

```text
TimeWarpEngine
├── EventStore
├── EventValidator
├── SequenceManager
├── TemporalIndex
├── CausalGraph
├── BranchManager
├── SnapshotManager
├── StateRebuilder
├── DiffEngine
├── ReplayEngine
├── MergeEngine
├── QueryEngine
├── RetentionManager
├── IntegrityVerifier
├── SchemaUpcaster
├── AuditEngine
└── Adapters
```

# 160. EventStore

Responsable :
- append ;
- segment ;
- deduplication ;
- integrity metadata.

Il ne fait pas la logique metier.

# 161. EventValidator

Verifie :
- schema ;
- ID ;
- branch ;
- times ;
- references ;
- payload.

Un event invalide est rejete ou mis en quarantaine.

# 162. BranchManager

Responsable :
- fork ;
- ancestry ;
- merge metadata ;
- status.

# 163. SnapshotManager

Decide :
- creation ;
- validation technique ;
- retention ;
- compaction.

# 164. StateRebuilder

Pipeline :
- snapshot ;
- event slice ;
- reducers ;
- validation ;
- cache.

# 165. DiffEngine

Modes :
- structural ;
- semantic ;
- causal ;
- plan ;
- model ;
- resource.

# 166. ReplayEngine

Applique :
- ReplayContract ;
- sandbox ;
- idempotence ;
- external-effect guard.

# 167. IntegrityVerifier

Verifie :
- hashes ;
- segment chain ;
- branch cycles ;
- broken refs ;
- snapshot compatibility.

# 168. Invariants TimeWarp

```text
TW1. Events append-only par defaut.
TW2. Toute correction produit un nouvel event.
TW3. VIEW_PAST ne modifie jamais le present.
TW4. ACTIVE_REVERT cree un nouvel event.
TW5. La genealogie des branches est acyclique.
TW6. Un merge conserve sa base et ses parents.
TW7. Un effet externe UNKNOWN n'est pas rejoue aveuglement.
TW8. Chaque snapshot declare schema et reducer version.
TW9. event_time et ingestion_time ne sont jamais fusionnes silencieusement.
TW10. Une branche simulee est typee comme telle.
TW11. KNOWLEDGE_AT(t) exclut les informations ingerees plus tard.
TW12. Chronologie et causalite restent distinctes.
```

# 169. Tests unitaires

```text
test_append_only
test_correction_event
test_state_at
test_snapshot_rebuild
test_fork_ancestry
test_three_way_merge
test_late_event
test_bitemporal_query
test_active_revert_new_revision
test_external_effect_guard
test_vector_clock_concurrency
test_known_at_time
```

# 170. Tests de proprietes

```text
P1. Rebuild same branch/seq from same ledger is stable within contract.
P2. Fork never mutates parent history.
P3. Merge never erases its parents.
P4. KnowledgeAt(t) cannot use later ingested events.
P5. Every object revision is traceable to events.
P6. Every correction preserves original event addressability.
```

# 171. Chaos tests

Injecter :
- events hors ordre ;
- duplications ;
- corruption segment ;
- crash snapshot ;
- clock skew ;
- merge incomplet ;
- index perdu.

Verifier :
- detection ;
- reconstruction ;
- pas de faux historique.

# 172. Duplicate event

Duplicate exact :
- idempotent accept/ignore.

Duplicate conflict :
- quarantaine ;
- investigation.

# 173. Tests de charge

Scenarios :
- 1 000 000 events ;
- 100 branches ;
- 100 000 object refs ;
- graphe causal profond ;
- snapshots espaces.

Mesures :
- append latency ;
- STATE_AT ;
- DIFF ;
- HISTORY ;
- CAUSES ;
- replay.

# 174. Observabilite

```text
events_total
append_rate
late_event_rate
snapshot_count
snapshot_age
state_rebuild_latency
replay_count
fork_count
merge_conflict_count
causal_query_latency
integrity_failures
storage_bytes
```

# 175. HUD TimeWarp

Vue :

```text
Branch: main
Frame: event_time
Seq: 2481
Forks: 3
Known-good: 2400
Incidents: 1
Claim changes: 4
Model changes: 2
```

# 176. Timeline scrubber

Le curseur temporel charge :

```text
STATE_AT
```

en lecture seule.

Le bouton `REVERT` reste separe.

# 177. Causal graph view

Selection d'un event affiche :
- causes ;
- consequences ;
- correlation group ;
- objects affected.

# 178. Branch graph view

```text
main
├─ experiment-A
├─ recovery-test
└─ model-v2
```

Forks et merges sont visibles.

# 179. Diff view

Deux curseurs :
- t0 ;
- t1.

Le diff est categorie par :
- concept ;
- branch ;
- plan ;
- resource ;
- evidence ;
- model.

# 180. Exemple A - bug logiciel

Timeline :

```text
config change
-> service failure
-> quick fix
-> Doctor
-> rollback config
-> verification
```

TimeWarp permet de retrouver le delta de configuration et le chemin causal operationnel.

# 181. Exemple B - preregistration

```text
t0 hypothesis
t1 prediction registered
t2 protocol registered
t3 observation
t4 analysis
```

TimeWarp prouve l'ordre temporel des etapes.

# 182. Exemple C - evidence tardive

Une preuve de `t0` arrive a `t5`.

Elle peut modifier l'evaluation actuelle, mais ne peut pas etre utilisee pour pretendre que la decision a `t2` la connaissait.

# 183. Exemple D - fork contrefactuel

A partir d'un ancien checkpoint :

```text
main@t0
-> fork counterfactual
-> intervention alternative
-> simulation
```

La branche reste `SIMULATED_COUNTERFACTUAL`.

# 184. Exemple E - replanification

Plan v1 echoue.

Plan v2 est cree a partir de la frontiere restante.

TimeWarp conserve :
- cause ;
- tasks deja terminees ;
- changement de strategie.

# 185. Exemple F - recovery

Un incident escalade :

```text
QuickResolution
-> Doctor
-> ConnectBusBot
-> Immune
```

La timeline conserve toutes les tentatives et leur resultat.

# 186. Equation maitresse de reconstruction

Pour une branche `b` :

```text
X_b(k)
=
Reduce_b(
  S_b(j),
  E_b(j+1...k)
)
```

avec `j <= k` et `S_b(j)` snapshot compatible le plus proche.

# 187. Equation de fork

```text
b2 = Fork(b1,k)
```

et :

```text
X_b2(k) = X_b1(k)
```

Au-dela :

```text
X_b2(k+n)
```

depend des nouveaux events de `b2`.

# 188. Equation bitemporelle

Une assertion :

```text
A = (
  valid_interval,
  transaction_interval,
  value
)
```

Query :

```text
A.valid_at(tv)
AND
A.known_at(tt)
```

# 189. Equation de causalite operationnelle

```text
CausePath(a,b)
=
path(a -> ... -> b)
```

dans le graphe de causation.

L'absence de path n'est pas une preuve d'absence de causalite scientifique.

# 190. Cout de reconstruction

```text
C_rebuild(k)
=
C_load_snapshot(j)
+
sum C_apply(e_i)
```

Le SnapshotManager cherche a borner ce cout.

# 191. Valeur de retention

```text
RetentionValue(e)
=
audit_value
+ recovery_value
+ epistemic_value
+ reproducibility_value
- storage_cost
```

sous contraintes politiques et legales.

# 192. Valeur d'un fork

```text
ForkValue =
ExpectedInformationGain
* Reversibility
/
(1 + ComputeCost + BranchComplexity)
```

Le regulateur peut limiter l'explosion de forks.

# 193. MVP M0 - EventStore

- event schema ;
- append-only ;
- object refs ;
- causation/correlation IDs ;
- seq local.

# 194. MVP M1 - STATE_AT

- reducers ;
- snapshot ;
- reconstruction ;
- DIFF.

# 195. MVP M2 - Branches

- fork ;
- branch ancestry ;
- merge metadata ;
- conflicts.

# 196. MVP M3 - Graphe causal

- causation edges ;
- cause query ;
- incident subgraph.

# 197. MVP M4 - Bitemporal

- valid time ;
- transaction time ;
- late data ;
- KNOWLEDGE_AT.

# 198. MVP M5 - Replay

- dry replay ;
- sandbox replay ;
- ReplayContracts ;
- seed/runtime metadata.

# 199. MVP M6 - Integrite/retention

- segments ;
- hashes ;
- compaction ;
- retention policies ;
- index rebuild.

# 200. MVP M7 - Integrations

- Recovery 06 ;
- Orchestrator 08 ;
- Epistemic 09 ;
- Simulation 10.

# 201. MVP M8 - HUD

- scrubber ;
- branch graph ;
- causal view ;
- diff ;
- anchors ;
- replay sandbox.

# 202. Criteres d'acceptation

```text
A1. Les events sont append-only.
A2. event_time et ingestion_time sont distincts.
A3. STATE_AT reconstruit un etat historique.
A4. VIEW_PAST ne modifie pas le present.
A5. ACTIVE_REVERT produit une nouvelle revision.
A6. Un fork partage exactement sa base au point de divergence.
A7. Un merge conserve base et parents.
A8. Les conflits de merge sont explicites.
A9. Le moteur supporte late events.
A10. Les queries bitemporelles sont possibles.
A11. Causation et correlation IDs sont navigables.
A12. Les snapshots accelerent la reconstruction.
A13. Les index peuvent etre reconstruits.
A14. Un replay sandbox est possible.
A15. Les effets externes UNKNOWN sont proteges.
A16. KNOWLEDGE_AT(t) exclut les informations futures.
A17. Les branches simulees sont typees.
A18. Les corrections historiques sont tracees.
A19. Le HUD affiche timeline, forks, diff et causes.
A20. Structure 06 peut retrouver un last-known-good fiable.
```

# 203. Position de Structure 07 dans COSMOS-F

Structure 07 est la charniere temporelle :

```text
Structure 01 : ce qui existe conceptuellement
Structure 02 : comment on transforme
Structure 03 : quelles hypotheses coexistent
Structure 04 : ou et a quelle echelle on navigue
Structure 05 : combien on peut ouvrir
Structure 06 : comment on revient a un etat stable
Structure 07 : quand, dans quel ordre et selon quelle branche tout cela s'est produit
Structure 08 : comment on orchestre
Structure 09 : comment on valide epistemiquement
Structure 10 : comment on simule plusieurs mondes
```

# 204. Suite logique

Avec TimeWarp, les structures 01-10 peuvent etre reliees par une histoire commune.

La suite naturelle reste :

```text
Structure 11
Moteur de decision, controle et politiques d'action sous incertitude
```

TimeWarp lui fournira une question fondamentale :

```text
"Que savions-nous, dans quel etat, sous quelle branche,
avec quels budgets et quelles preuves,
au moment ou cette politique a ete choisie ?"
```
