# COSMOS-F - Structure 06
## Stabilisation dynamique, checkpoints, rollback et recovery multi-niveaux

**Version :** 0.6  
**Statut :** specification conceptuelle, mathematique et technique  
**Dependances :** Structure 01 - NCU ; Structure 02 - Algebre COSMOS-F ; Structure 03 - Moteur de branches concurrentes ; Structure 04 - Zoom fractal ; Structure 05 - Regulateur multi-ressources ; OCCP ; TimeWarp ; AIONE Forge.

---

## Resume

Cette structure definit comment COSMOS-F conserve un etat coherent pendant une exploration longue, comment il detecte qu'un etat devient instable, comment il cree des points de restauration, comment il annule une transformation, comment il rejoue un historique, et comment il reprend apres erreur, crash ou corruption partielle.

Le systeme distingue plusieurs formes de retour :

```text
ROLLBACK_CONCEPTUEL
ROLLBACK_NAVIGATION
ROLLBACK_TRANSACTIONNEL
ROLLBACK_MACHINE
ROLLBACK_CONFIGURATION
ROLLBACK_MODELE
ROLLBACK_ACTION
```

Ces retours n'ont pas tous la meme semantique.

Exemple :

```text
revenir a une ancienne hypothese
!=
restaurer un fichier
!=
restaurer un processus
!=
annuler une action externe
```

Le moteur doit donc connaitre :
- ce qui est reversible ;
- comment le restaurer ;
- jusqu'a quel niveau ;
- avec quelle fidelite ;
- a quel cout ;
- avec quelles preuves de restauration.

La structure introduit aussi une chaine de recovery en couches :

```text
Resolution rapide
    -> Doctor
        -> ConnectBusBot
            -> Immune
```

Cette chaine est definie ici comme **architecture de responsabilites** :
- Resolution rapide : reparations locales, bornees et reversibles ;
- Doctor : diagnostic approfondi et plan de reparation ;
- ConnectBusBot : restauration des liaisons, dependances et composants de diagnostic ;
- Immune : confinement, integrite, quarantaine et restauration de dernier recours.

Principe central :

```text
un systeme autonome n'est pas robuste parce qu'il evite toute erreur,
mais parce qu'il sait detecter l'erreur,
limiter sa propagation,
revenir vers un etat prouve,
et reprendre sans perdre la provenance.
```

# 1. Objectif general

La stabilisation est une fonction permanente, pas seulement une reaction aux crashs.

On note l'etat global :

```text
X_t = (
  concepts,
  branches,
  navigation,
  budgets,
  permissions,
  processes,
  configuration,
  storage,
  models,
  ledger
)
```

Le regulateur observe :

```text
X_t -> stability_score(X_t)
```

et maintient :

```text
stability_score(X_t) >= theta_stable
```

ou, si ce n'est plus possible, il cherche un etat precedent `X_k` tel que :

```text
k < t
AND stable(X_k) = true
AND restorable(X_k) = true
```


# 2. Etat stable

Un etat stable n'est pas un etat immobile.

On definit :

```text
Stable(X) =
InvariantOK(X)
AND ProvenanceOK(X)
AND ResourceMarginsOK(X)
AND RecoveryPathExists(X)
AND CriticalServicesOK(X)
```

Un etat peut donc continuer a evoluer tout en restant stable.

Le statut d'un checkpoint peut etre :

```text
CANDIDATE
VALIDATING
STABLE
DEGRADED_STABLE
UNSTABLE
INVALID
```


# 3. Stabilite locale et globale

On distingue :

```text
S_local(component)
S_space(conceptual_space)
S_session
S_system
```

Un composant peut etre instable sans que tout le systeme le soit.

Exemple :

```text
branche B7 corrompue
```

peut etre isolee pendant que les autres branches restent accessibles.

Le recovery doit donc preferer le plus petit rayon de restauration suffisant.


# 4. Rayon de restauration

On definit :

```text
radius in {
  OBJECT,
  BRANCH,
  VIEW,
  TRANSACTION,
  SERVICE,
  WORKSPACE,
  SYSTEM
}
```

Principe :

```text
choose smallest radius
that restores invariants
```

Cela reduit :
- perte de travail ;
- temps de restauration ;
- cout de revalidation ;
- blast radius.


# 5. Checkpoint canonique

Un checkpoint :

```text
C_k = (
  checkpoint_id,
  parent_checkpoint,
  scope,
  timestamp,
  state_refs,
  hashes,
  invariants,
  resource_state,
  permissions,
  provenance,
  restore_recipe,
  verification_recipe,
  status
)
```

Il ne contient pas necessairement toutes les donnees.

Il peut referencer :
- snapshots ;
- deltas ;
- objets immuables ;
- blobs ;
- revisions Git ;
- bases de donnees ;
- journaux.


# 6. Checkpoint complet et incremental

Deux familles :

```text
FULL_CHECKPOINT
INCREMENTAL_CHECKPOINT
```

Un checkpoint complet contient un etat suffisant pour restauration directe.

Un incremental contient :

```text
delta(C_{k-1} -> C_k)
```

Restauration :

```text
C_0 + Delta_1 + ... + Delta_k
```

Le systeme peut periodiquement consolider plusieurs deltas en un nouveau full checkpoint.


# 7. Checkpoint logique vs physique

Checkpoint logique :
- etat conceptuel ;
- branches ;
- frames ;
- navigation ;
- permissions ;
- budgets.

Checkpoint physique :
- fichiers ;
- DB ;
- processus ;
- configuration ;
- modele charge ;
- environnement.

Un checkpoint global peut combiner les deux :

```text
C_global = (C_logical, C_physical)
```

mais ils restent distingues pour eviter un faux sentiment de restauration complete.


# 8. Fidelite de restauration

Une restauration peut etre :

```text
EXACT
EQUIVALENT
APPROXIMATE
REGENERATED
```

`EXACT` :
- memes objets/version ;
- memes hashes attendus.

`EQUIVALENT` :
- semantique preservee ;
- representation interne differente.

`APPROXIMATE` :
- perte mesuree.

`REGENERATED` :
- etat reconstruit depuis un resume ou des sources.

Le niveau doit etre expose.


# 9. Hashes et integrite

Chaque artefact critique peut avoir :

```text
hash = H(content)
```

Le checkpoint conserve :
- hash attendu ;
- taille ;
- schema/version ;
- dependances.

Verification :

```text
H(restored_content) == expected_hash
```

si une restauration exacte est attendue.

Pour les representations semantiques non deterministes, on utilise des tests d'equivalence plutot qu'un hash seul.


# 10. Deltas

Une transformation :

```text
X_t --op--> X_{t+1}
```

produit :

```text
Delta_t = (
  operation,
  before_refs,
  after_refs,
  changed_fields,
  side_effects,
  inverse,
  provenance
)
```

Un delta peut etre :

```text
INVERTIBLE
COMPENSATABLE
NON_REVERSIBLE
```

Cette classification est connue avant execution autant que possible.


# 11. Inverse operationnel

Si un operateur possede un inverse :

```text
op^-1(op(X)) = X
```

alors rollback exact possible sous conditions.

Mais de nombreux operateurs ne sont pas parfaitement inversibles.

Exemple :
- supprimer un cache : regenerable ;
- envoyer un message : non annulable ;
- publier un fichier : peut demander compensation ;
- ecrire en DB : reversible si journal transactionnel.

Le moteur doit eviter de marquer "inverse" lorsqu'il s'agit seulement d'une compensation.


# 12. Compensation

Une compensation cherche a neutraliser les effets sans pretendre revenir exactement au passe.

```text
COMPENSATE(op)
```

Exemples :
- message envoye -> envoyer correction ;
- reservation externe -> annuler reservation ;
- fichier publie -> publier nouvelle version.

Statut :

```text
COMPENSATED != ROLLED_BACK
```

TimeWarp conserve cette difference.


# 13. Rollback conceptuel

Le rollback conceptuel restaure :
- branches ;
- scores ;
- statuts epistemiques ;
- liens ;
- frames ;
- vues.

Il ne supprime pas les preuves arrivees depuis.

Deux modes :

```text
HISTORICAL_VIEW
ACTIVE_REVERT
```

`HISTORICAL_VIEW` :
- voir l'ancien etat sans modifier le present.

`ACTIVE_REVERT` :
- creer une nouvelle revision ressemblant a l'ancienne.

Le passe n'est jamais reecrit silencieusement.


# 14. Rollback navigation

Le rollback navigation restaure :

```text
focus
scale
frame
dimensions
branch
time
filters
```

Il s'appuie sur :
- NavStack ;
- ancres ;
- TimeWarp.

Commande :

```text
RETURN anchor:A
```

La restauration de vue n'implique aucune modification des donnees conceptuelles.


# 15. Rollback transactionnel

Une transaction :

```text
BEGIN
  op1
  op2
  op3
COMMIT
```

Si `op3` echoue :

```text
ROLLBACK
```

selon les inverses disponibles.

Le transaction manager stocke :
- operations appliquees ;
- inverses ;
- effets externes ;
- compensation requise.


# 16. Rollback machine

Le rollback machine peut viser :
- service ;
- processus ;
- config ;
- package ;
- environnement ;
- modele ;
- fichier ;
- base.

Il requiert des recettes propres a chaque type.

Exemple :

```text
restore_config
restart_service
verify_health
```

Un simple "restart" n'est pas considere comme rollback si l'etat sous-jacent reste inconnu.


# 17. Rollback de configuration

Pour toute configuration critique :

```text
config_current
config_previous
config_hash
schema_version
```

Avant modification :

```text
snapshot(config)
validate(new_config)
apply
health_check
```

En cas d'echec :

```text
restore(previous)
revalidate
restart_if_needed
```


# 18. Rollback de modele

Un modele IA peut changer :
- poids ;
- quantification ;
- system prompt ;
- template ;
- contexte ;
- route provider.

Checkpoint :

```text
ModelState = (
  model_id,
  digest,
  quantization,
  runtime,
  prompt_profile,
  router_config
)
```

Retour :

```text
LOAD known_good_model_state
```

puis test de comportement minimal.


# 19. Rollback de donnees

Strategie :
- snapshots ;
- WAL ;
- event sourcing ;
- copy-on-write ;
- versioning.

Une restauration DB doit definir :
- point temporel ;
- tables concernees ;
- transactions incluses ;
- verification referentielle.

On prefere le recovery partiel au retour global lorsque possible.


# 20. Event sourcing

Au lieu de sauvegarder seulement l'etat final :

```text
state = reduce(events)
```

Chaque evenement immuable :

```text
E_k = (
  id,
  type,
  payload,
  actor,
  timestamp,
  causation_id,
  correlation_id
)
```

Le replay reconstruit l'etat.

Avantage :
- audit ;
- TimeWarp ;
- debugging ;
- branching historique.


# 21. Journal causal

Un simple timestamp ne suffit pas toujours.

On conserve :

```text
caused_by
depends_on
triggered
compensates
reverts
```

On obtient un DAG causal.

Question possible :

```text
"quel evenement a finalement conduit a cette degradation ?"
```

Le moteur peut remonter la chaine plutot que lister seulement les logs recents.


# 22. Correlation IDs

Une action utilisateur ou un plan Forge recoit :

```text
correlation_id
```

Toutes les operations associees le conservent.

Exemple :

```text
request:R1
 -> plan:P1
 -> op:A
 -> op:B
 -> recovery:Rcv
```

Cela rend les investigations multi-services tractables.


# 23. Replay

Le replay peut etre :

```text
DRY_REPLAY
SANDBOX_REPLAY
LIVE_REPLAY
```

`DRY_REPLAY` :
- calcule les transitions sans effets externes.

`SANDBOX_REPLAY` :
- reexecute dans environnement isole.

`LIVE_REPLAY` :
- interdit par defaut pour les effets externes non idempotents.

Le moteur verifie l'idempotence avant replay.


# 24. Idempotence

Une operation idempotente :

```text
op(op(X)) = op(X)
```

Exemples potentiels :
- "ensure directory exists" ;
- "set config to value V".

Non idempotent :
- "increment counter" ;
- "send message" ;
- "charge account".

Le replay doit connaitre cette propriete.


# 25. Checkpoint avant risque

Avant une operation a risque :

```text
if risk(op) >= theta_checkpoint:
    create_checkpoint()
```

Le checkpoint doit etre valide avant l'operation.

Sinon :

```text
DO_NOT_EXECUTE
```

si aucune strategie de compensation acceptable n'existe.


# 26. Checkpoint periodique

Regles possibles :

```text
every N operations
every T minutes
after major merge
before schema migration
before external action
when regime enters ORANGE
```

Le rythme depend du cout et de la criticite.


# 27. Checkpoint adaptatif

Frequence :

```text
checkpoint_rate =
f(
  change_rate,
  risk,
  instability,
  recovery_cost,
  value_of_recent_work
)
```

Plus le systeme change vite, plus les checkpoints peuvent etre frequents.


# 28. Validation de checkpoint

Un checkpoint n'est `STABLE` qu'apres :

```text
ValidateCheckpoint(C)
```

Tests :
- hashes ;
- schemas ;
- invariants ;
- acces ;
- restauration testable ;
- ressources ;
- provenance.

Un fichier de snapshot cree mais jamais teste reste `CANDIDATE`.


# 29. Restore rehearsal

Periodiquement :

```text
RESTORE_REHEARSAL
```

restaure un checkpoint dans un environnement isole.

Objectif :
- prouver que le backup est utilisable ;
- mesurer le temps ;
- detecter dependances manquantes.

Metrique :

```text
RTO_tested
```


# 30. RPO et RTO

Deux objectifs :

```text
RPO = Recovery Point Objective
RTO = Recovery Time Objective
```

RPO :
- quantite de travail acceptablement perdue.

RTO :
- temps cible de restauration.

Le moteur peut choisir une strategie selon :
- valeur des donnees ;
- cout de snapshot ;
- criticite du service.


# 31. Etat connu bon

Un checkpoint `KNOWN_GOOD` est plus fort que `STABLE`.

Il a :
- tests passes ;
- restauration testee ;
- periode d'observation acceptable ;
- aucune alerte critique.

On maintient :

```text
last_known_good
```

comme cible de dernier recours.


# 32. Etat degrade stable

Parfois, la restauration complete est impossible.

On peut accepter :

```text
DEGRADED_STABLE
```

Exemple :
- un module optionnel indisponible ;
- fonctions critiques operationnelles.

Le HUD doit afficher clairement les limitations.


# 33. Detection d'instabilite

Signaux :
- invariant echoue ;
- taux d'erreur ;
- hausse de rollback ;
- conflits ;
- saturation ressources ;
- divergence de hash ;
- service unhealthy ;
- corruption de schema ;
- boucle d'operations.

Score :

```text
I_t = Instability(signals_t)
```

Si :

```text
I_t >= theta_recovery
```

le recovery manager intervient.


# 34. Detection de boucle

Pattern :

```text
X_a -> X_b -> X_a -> X_b
```

ou :

```text
same_error repeated N times
```

Le moteur bloque la repetition :

```text
CIRCUIT_BREAKER_OPEN
```

et escalade vers diagnostic plutot que retenter indefiniment.


# 35. Circuit breaker

Etats :

```text
CLOSED
OPEN
HALF_OPEN
```

`CLOSED` :
- operations normales.

`OPEN` :
- appels bloques.

`HALF_OPEN` :
- test limite.

Cette logique convient aux dependances et services repetitivement defaillants.


# 36. Retry policy

Un retry doit definir :

```text
max_attempts
backoff
jitter
retryable_errors
stop_conditions
```

Exemple :

```text
3 tentatives rapides maximum
```

puis escalation.

Le systeme ne confond pas retry et diagnostic.


# 37. Resolution rapide

Niveau L0 :

```text
QuickResolution
```

Cible :
- panne connue ;
- risque faible ;
- recette deterministe ;
- rollback simple.

Cycle :

```text
detect
-> classify
-> apply known fix
-> retest
```

Maximum recommande :

```text
attempts <= 3
```

Puis escalation.


# 38. Doctor

Niveau L1 : **Doctor**.

Responsabilites :
- diagnostic structure ;
- collecte de preuves ;
- hypothese de cause ;
- plan de reparation ;
- test de non-regression ;
- verification post-reparation.

Doctor ne "devine" pas silencieusement.

Il produit :

```text
Diagnosis = (
  symptoms,
  hypotheses,
  evidence,
  proposed_repairs,
  confidence,
  rollback_plan
)
```


# 39. Doctor - protocole

Pipeline :

```text
OBSERVE
ISOLATE
HYPOTHESIZE
TEST
IDENTIFY
REPAIR
VERIFY
DOCUMENT
```

Si Doctor echoue :
- aucune cause prouvee ;
- composant inaccessible ;
- diagnostic lui-meme casse ;
- dependance de diagnostic manquante ;

alors escalation vers ConnectBusBot.


# 40. ConnectBusBot

Niveau L2 : **ConnectBusBot**.

Mission :
- restaurer la connectivite entre composants ;
- verifier bus, IPC, sockets, endpoints ;
- reinstaller/reconnecter Doctor si necessaire ;
- restaurer dependances et routes de diagnostic ;
- reconstruire le chemin de controle.

Il ne remplace pas Doctor pour le diagnostic applicatif.

Il restaure l'infrastructure permettant a Doctor de fonctionner.


# 41. ConnectBusBot - graphe de dependances

Representer :

```text
ServiceGraph = (services, connections, contracts)
```

Verifier :
- service present ;
- version ;
- endpoint ;
- permission ;
- protocole ;
- health ;
- dependances.

Il peut comparer avec un manifeste `KNOWN_GOOD`.


# 42. ConnectBusBot - reinstallation controlee

Reinstallation possible seulement si :
- artefact source verifie ;
- version cible connue ;
- backup config ;
- rollback recipe ;
- permissions.

Flux :

```text
snapshot
-> verify package
-> reinstall
-> restore config
-> reconnect
-> health check
-> hand back to Doctor
```


# 43. Immune

Niveau L3 : **Immune**.

Mission :
- proteger l'integrite ;
- contenir une anomalie ;
- isoler les composants ;
- revenir a un dernier etat connu bon ;
- empecher la propagation.

Immune intervient si :
- corruption ;
- comportement incoherent repete ;
- violation d'invariant critique ;
- echec de Doctor + ConnectBusBot ;
- suspicion d'artefact non fiable.

Immune privilegie la surete et la recuperabilite sur la disponibilite immediate.


# 44. Immune - quarantaine

Quarantaine :

```text
QUARANTINE(component)
```

Effets :
- stop interactions ;
- preserve evidence ;
- snapshot ;
- restrict permissions ;
- create forensic bundle.

La quarantaine ne supprime pas automatiquement le composant.


# 45. Immune - dernier etat connu bon

Si reparation locale impossible :

```text
RESTORE(last_known_good)
```

Puis :
- validation ;
- comparaison des deltas ;
- reapplication selective des changements surs.

Le systeme peut perdre une partie des modifications recentes, mais conserve leur journal pour analyse.


# 46. Escalade formelle

Machine d'etats :

```text
NORMAL
 -> QUICK_FIX
 -> DOCTOR
 -> CONNECTBUSBOT
 -> IMMUNE
 -> MANUAL_REQUIRED
```

Transitions :
- succes -> VERIFY -> NORMAL ;
- echec -> niveau suivant ;
- risque trop eleve -> niveau superieur directement ;
- permission absente -> MANUAL_REQUIRED.


# 47. Retour apres escalation

Apres reparation :

```text
RECOVERY_VERIFY
```

doit verifier :
- cause supprimee ;
- services sains ;
- invariants ;
- config ;
- ressources ;
- absence de boucle ;
- provenance complete.

Puis :

```text
OBSERVATION_WINDOW
```

avant de declarer `KNOWN_GOOD`.


# 48. Etats du recovery manager

```text
IDLE
DETECTED
CONTAINING
DIAGNOSING
REPAIRING
RESTORING
VERIFYING
OBSERVING
RECOVERED
FAILED
MANUAL_REQUIRED
```

Chaque transition produit un evenement TimeWarp.


# 49. Recovery plan

Objet :

```text
RecoveryPlan = (
  incident_id,
  scope,
  target_state,
  steps,
  checkpoints,
  expected_cost,
  risk,
  rollback_of_recovery,
  verification
)
```

Le recovery lui-meme peut echouer.

Il a donc besoin de sa propre strategie de rollback.


# 50. Nested rollback

Exemple :

```text
Recovery R1
  step A
  step B
  step C fails
```

On peut devoir annuler `B` et `A`.

Donc :

```text
rollback(recovery_plan)
```

est un objet de premiere classe.


# 51. Recovery conceptuel

Une incoherence conceptuelle peut etre :
- merge invalide ;
- preuves mal associees ;
- score corrompu ;
- branche dupliquee ;
- frame incompatible.

Recovery :
- restaurer revisions ;
- recalculer index ;
- verifier provenance ;
- re-scoring ;
- rouvrir les branches si necessaire.


# 52. Recovery de l'atlas

Si une carte locale est corrompue :
- invalider cache ;
- conserver les noeuds persistants ;
- reconstruire chart ;
- verifier transformations de frame ;
- recharger ancres.

La carte est regenerable ; les sources ne doivent pas etre touchees.


# 53. Recovery du MBC

Si le moteur de branches echoue :
- geler la frontier ;
- checkpoint ;
- verifier DAG ;
- detecter cycles invalides ;
- reconstruire scores ;
- restaurer relations ;
- verifier pruning/reopen metadata.

Un branch ledger append-only facilite cette operation.


# 54. Recovery du regulateur

Si le regulateur de ressources devient incoherent :
- passer en mode conservateur ;
- bloquer exploration ;
- recalculer usage reel ;
- reconstruire reservations ;
- liberer reservations orphelines ;
- restaurer policy connue bonne.

Le recovery manager ne depend pas entierement du regulateur qu'il repare.


# 55. Recovery TimeWarp

TimeWarp est critique.

Protection :
- event log append-only ;
- checksums ;
- segments ;
- index regenerable.

Si l'index est corrompu :
- ne pas supprimer le log ;
- reconstruire l'index depuis les evenements.


# 56. Recovery Forge

Si Forge s'arrete pendant un plan :
- identifier transaction/plan ;
- lire dernier checkpoint ;
- marquer etapes :
  - DONE ;
  - IN_PROGRESS ;
  - NOT_STARTED ;
  - UNKNOWN.

Une etape `UNKNOWN` doit etre verifiee avant reprise pour eviter double execution.


# 57. Exactly-once vs at-least-once

Pour les actions externes :

```text
exactly_once
```

est difficile.

On prefere :
- idempotency keys ;
- deduplication ;
- verification d'effet.

Politique :

```text
if effect_status == UNKNOWN:
    CHECK_BEFORE_RETRY
```


# 58. Idempotency keys

Une action externe recoit :

```text
idempotency_key = unique(request, action)
```

Un service compatible peut refuser la duplication.

Le ledger conserve la cle avec le resultat.


# 59. Saga

Pour une operation distribuee :

```text
A -> B -> C
```

chaque etape possede compensation :

```text
A^-c
B^-c
C^-c
```

Si `C` echoue :

```text
B^-c
A^-c
```

C'est un modele de **Saga**, pas une transaction ACID globale.


# 60. Transaction locale vs distribuee

Local :
- DB transaction possible.

Distribue :
- compensation ;
- idempotence ;
- reconciliation.

Le moteur choisit la semantique correcte selon le systeme concerne.


# 61. Reconciliation

Apres recovery, deux sources peuvent diverger.

```text
Reconcile(A,B)
```

Etapes :
- comparer versions ;
- identifier source d'autorite ;
- detecter changements concurrents ;
- proposer merge ;
- conserver conflits.

Aucune ecriture arbitraire "last write wins" pour les donnees critiques sans politique.


# 62. Source of truth

Chaque categorie declare une source d'autorite :

```text
AuthorityMap = {
  "permissions": PermissionStore,
  "concepts": NCULedger,
  "events": TimeWarp,
  "runtime_health": RuntimeMonitor
}
```

Le recovery sait quelle source utiliser pour reconstruire les autres vues.


# 63. Verification post-restore

Une restauration reussie techniquement n'est pas suffisante.

Verifier :
- integrite ;
- fonctionnalite ;
- invariants ;
- performances minimales ;
- permissions ;
- traces ;
- absence d'effets residuels.

Sortie :

```text
RESTORED_UNVERIFIED
```

puis :

```text
RESTORED_VERIFIED
```


# 64. Tests de fumee

Smoke tests :
- service demarre ;
- endpoint repond ;
- DB accessible ;
- modele repond ;
- NCU charge ;
- MBC liste branches ;
- TimeWarp append ;
- regulator snapshot.

Rapides mais non exhaustifs.


# 65. Tests de non-regression

Apres Doctor/recovery :
- tests unitaires cibles ;
- integration ;
- contrats ;
- invariants ;
- comparaison avec last_known_good.

Le niveau de test depend du rayon de restauration.


# 66. Canary recovery

Pour un composant replicable :
- restaurer une instance ;
- tester ;
- observer ;
- generaliser.

Cette strategie reduit le blast radius.


# 67. Shadow validation

Une version restauree peut etre executee en parallele sans prendre le controle :

```text
SHADOW
```

Comparer :
- outputs ;
- erreurs ;
- latence.

Puis promotion si stable.


# 68. Recovery sans destruction de preuve

Avant nettoyage :
- sauvegarder logs ;
- snapshots ;
- hashes ;
- erreurs ;
- configuration.

Principe :

```text
preserve_evidence_before_mutation
```

particulierement pour les anomalies difficiles a reproduire.


# 69. Forensic bundle

Bundle :

```text
IncidentBundle/
  manifest.json
  logs/
  configs/
  hashes/
  process_state/
  resource_state/
  relevant_timewarp/
  recovery_actions/
```

Il facilite analyse posterieure.


# 70. Incident ID

Chaque incident :

```text
INC-YYYYMMDD-XXXX
```

Associe :
- correlation IDs ;
- composants ;
- severity ;
- recovery chain ;
- cause finale si connue.

Les incidents peuvent etre relies.


# 71. Severite

Exemple :

```text
S0 informational
S1 local degradation
S2 service impaired
S3 critical function unavailable
S4 integrity/security critical
```

La severite influence :
- rayon de checkpoint ;
- escalation ;
- permissions ;
- observation.


# 72. Stop conditions

Une chaine automatique s'arrete si :
- nombre max de tentatives ;
- aucune nouvelle information ;
- risque augmente ;
- preuve insuffisante ;
- permission requise ;
- restoration impossible ;
- composant non compris.

Sortie :

```text
MANUAL_REQUIRED
```

avec dossier complet.


# 73. Eviter le repair loop

Une reparation ne doit pas se re-appliquer indefiniment.

On enregistre :

```text
repair_signature
```

Si la meme signature echoue plusieurs fois :

```text
BLOCK_REPEAT
ESCALATE
```


# 74. Confiance de diagnostic

Doctor peut produire :

```text
diagnosis_confidence
```

Mais la decision de reparation considere aussi :
- risque ;
- reversibilite ;
- cout de test ;
- possibilite de verifier.

Une confiance elevee ne remplace pas le rollback plan.


# 75. Hypotheses de panne

Doctor reutilise le MBC :

```text
H1 dependency
H2 permission
H3 config
H4 network
H5 resource
H6 unknown
```

Chaque test reduit l'espace d'hypotheses.

Le recovery est donc lui-meme une navigation conceptuelle controlee.


# 76. Budget du recovery

Le recovery consomme :

```text
B_recovery
```

mais la Structure 05 reserve une marge.

Le plan choisit la reparation maximisant :

```text
expected_recovery_gain
/
(cost + risk)
```

sous contraintes.


# 77. Priority inversion

Pendant incident critique, les taches de faible priorite peuvent tenir des ressources.

Le recovery manager doit pouvoir :
- preempter ;
- liberer ;
- suspendre.

Il doit eviter qu'un job d'exploration bloque un diagnostic critique.


# 78. Recovery concurrent

Deux incidents peuvent survenir.

On evite des recoveries conflictuels en verrouillant :

```text
recovery_scope_lock
```

Un lock peut porter sur :
- composant ;
- config ;
- workspace.

Les recoveries non conflictuels peuvent continuer en parallele.


# 79. Deadlock recovery

Si deux plans attendent mutuellement :

```text
detect wait-for graph cycle
```

Politique :
- abort plan moins prioritaire ;
- rollback ;
- liberer locks.

Le choix est journalise.


# 80. Versioning des recettes

Une recette de recovery :

```text
recipe_id
version
compatible_versions
preconditions
steps
rollback_steps
verification
```

On ne rejoue pas une vieille recette sur une version incompatible sans validation.


# 81. Recipes as code

Les recettes peuvent etre declaratives :

```yaml
name: restore-doctor
steps:
  - checkpoint: doctor-config
  - verify_package: doctor
  - reinstall: doctor
  - reconnect: bus
  - health_check: doctor
```

Elles passent :
- lint ;
- dry-run ;
- test sandbox.


# 82. Permissions recovery

Les permissions sont graduees :

```text
P_READ
P_RESTART
P_RECONFIGURE
P_REINSTALL
P_ROLLBACK
P_QUARANTINE
P_SYSTEM_RESTORE
```

Doctor peut avoir moins de permissions qu'Immune.

Le niveau ne s'auto-eleve pas sans politique autorisee.


# 83. Mode read-only

Avant modification, Doctor peut fonctionner en :

```text
DIAGNOSE_ONLY
```

Il collecte :
- logs ;
- etat ;
- hypotheses.

Cette phase est utile lorsque le risque de mutation est eleve.


# 84. Quarantaine de branche conceptuelle

Une branche conceptuelle suspecte peut etre :

```text
QUARANTINED
```

Elle reste visible mais :
- exclue des decisions ;
- exclue des merges ;
- conservee pour analyse.

Cela evite qu'une corruption logique contamine le graphe actif.


# 85. Corruption et migrations de schema

Lors d'une migration :
- snapshot avant ;
- migration versionnee ;
- validation ;
- downgrade recipe si possible.

Si downgrade impossible :
- migration irreversible marquee ;
- backup complet obligatoire.


# 86. Compatibilite de checkpoint

Un checkpoint ancien peut devenir incompatible apres changement de schema.

On stocke :

```text
schema_version
runtime_version
recipe_version
```

Restauration :
- directe ;
- via migration ;
- via environnement ancien ;
- impossible.

Le statut doit etre connu avant incident si possible.


# 87. Portable recovery

Un bon checkpoint peut documenter :
- dependances ;
- versions ;
- chemins ;
- variables ;
- artefacts.

Objectif :
- restaurer sur une autre machine compatible si necessaire.

Mais `portable` est un statut teste, pas suppose.


# 88. Recovery de secrets

Les secrets ne sont pas dupliques en clair dans les checkpoints.

Le checkpoint conserve :
- references ;
- identifiants ;
- requirements.

La restauration doit reacquerir les secrets depuis un store autorise.


# 89. Journal append-only

Le ledger du recovery est append-only :

```text
START
STEP
RESULT
ERROR
ROLLBACK
VERIFY
END
```

Une correction de journal se fait par nouvel evenement, pas modification silencieuse.


# 90. Horloges

On conserve :
- timestamp wall clock ;
- monotonic time ;
- event sequence.

Cela evite certaines ambiguities dues au changement d'heure ou a la synchronisation.


# 91. Snapshot consistant

Un snapshot multi-composants doit etre coherent.

Strategies :
- quiesce ;
- barrier ;
- transaction snapshot ;
- version vectors.

Sinon, le checkpoint est marque :

```text
FUZZY_SNAPSHOT
```

et la recette de restauration doit en tenir compte.


# 92. Barrier de checkpoint

Une barrier :

```text
prepare
-> flush
-> snapshot
-> release
```

coordonne plusieurs composants.

Le cout doit rester compatible avec les objectifs de disponibilite.


# 93. Version vectors

Dans un systeme distribue, un simple numero global peut etre insuffisant.

On peut stocker :

```text
V = {component_a: 12, component_b: 9, component_c: 31}
```

pour representer la version relative.


# 94. Determinisme

Le replay exact est plus facile si les operations sont deterministes.

Pour les operations non deterministes, stocker :
- seed ;
- modele ;
- parametres ;
- inputs ;
- environnement.

Cela augmente la reproductibilite.


# 95. Recovery de modele generatif

Une sortie LLM n'est pas toujours reproductible bit-a-bit.

On peut restaurer :
- prompt ;
- contexte ;
- modele ;
- seed si disponible ;
- outils ;
- resultat original.

Le resultat original reste la reference historique.


# 96. Difference entre replay et regeneration

```text
REPLAY = rejouer les memes evenements/operations
REGENERATE = recalculer un nouvel etat depuis des sources
```

Ils peuvent diverger.

Le systeme l'indique explicitement.


# 97. Recovery tree

Une tentative de recovery peut creer plusieurs chemins :

```text
R0
├─ R1 quick fix
├─ R2 doctor plan A
└─ R3 restore known-good
```

On peut simuler plusieurs plans avant execution.

Le MBC peut scorer ces plans comme branches concurrentes.


# 98. Simulation de recovery

Avant une restauration lourde :

```text
SIMULATE_RECOVERY(plan)
```

Verifier :
- dependances ;
- espace disque ;
- collisions ;
- versions ;
- duree estimee ;
- pertes.

La simulation ne garantit pas le resultat reel, mais reduit l'incertitude.


# 99. Score d'un plan de recovery

```text
Q_recovery =
(probability_success
 * integrity_gain
 * reversibility)
/
(1 + time + cost + risk + data_loss)
```

Le moteur conserve plusieurs dimensions au lieu de n'utiliser que le score.


# 100. Choix entre repair et restore

Repair :
- conserve plus de travail recent ;
- peut etre plus complexe.

Restore :
- plus previsible ;
- peut perdre du travail.

Decision selon :

```text
expected_repair_cost
vs
restore_cost + replay_cost + lost_work
```


# 101. Choix entre restart et rollback

Restart est utile si l'etat fautif est transitoire.

Rollback est necessaire si :
- config ;
- binaire ;
- data ;
- version
est la cause.

Doctor doit distinguer ces cas.


# 102. Recovery autonome borne

Le systeme peut automatiser :
- quick fixes ;
- restarts autorises ;
- restauration locale prouvee ;
- reindexation ;
- cache rebuild.

Les actions plus larges suivent :
- permissions ;
- seuils ;
- validation.

Autonomie :

```text
bounded_by(policy, scope, risk)
```


# 103. Human handoff

Si `MANUAL_REQUIRED` :

Le dossier doit contenir :
- symptomes ;
- tentatives ;
- preuves ;
- dernier etat stable ;
- risques ;
- commandes/actions suggerees ;
- ce qui ne doit pas etre repete.

Le but est que l'humain reprenne sans refaire toute l'enquete.


# 104. Rapport d'incident

Structure :

```text
Summary
Impact
Timeline
Detection
Hypotheses
Root cause
Recovery
Verification
Data loss
Residual risk
Preventive actions
Evidence links
```

Si root cause inconnue, l'ecrire explicitement.


# 105. Postmortem

Le postmortem transforme l'incident en amelioration :

```text
incident
-> cause/systemic factors
-> guardrail
-> test
-> recipe
-> monitoring
```

Eviter de reduire l'analyse a "erreur humaine".


# 106. Apprentissage des pannes

Une panne recurrente peut devenir :

```text
KnownIncidentPattern
```

avec :
- signature ;
- diagnostic ;
- quick fix ;
- verification ;
- escalation.

Mais une signature partielle ne doit pas declencher automatiquement une reparation destructive.


# 107. Catalogue de signatures

Exemple :

```json
{
  "pattern_id": "PAT-PORT-IN-USE",
  "signals": ["EADDRINUSE", "bind failed"],
  "confidence_threshold": 0.8,
  "quick_actions": ["inspect-port-owner"],
  "repair_requires": ["permission:restart"]
}
```


# 108. Observabilite du recovery

Metriques :

```text
incident_count
quick_fix_success_rate
doctor_success_rate
connectbus_success_rate
immune_activation_count
mean_time_to_detect
mean_time_to_recover
rollback_success_rate
restore_verification_rate
checkpoint_age
last_known_good_age
recovery_loop_block_count
```


# 109. SLO de recovery

Exemples :

```text
95% quick fixes < target
99% critical checkpoints verifiables
100% recovery operations journalisees
0 external replay without idempotence check
```

Ces SLO sont adaptes au projet.


# 110. HUD recovery

Vue :

```text
System: DEGRADED
Incident: INC-...
Level: DOCTOR
Last known good: 14 min ago
Checkpoint: verified
Current hypothesis: config mismatch
Attempts: 1/3
Rollback available: yes
Risk: low
```

Actions :
- voir preuves ;
- simuler repair ;
- rollback ;
- escalate ;
- export incident bundle.


# 111. Timeline visuelle

Timeline :

```text
18:02 change config
18:03 health fail
18:03 quick fix #1
18:04 Doctor start
18:06 cause isolated
18:07 rollback config
18:08 verification pass
```

Chaque etape pointe vers les evenements TimeWarp.


# 112. API

```text
POST /checkpoint/create
POST /checkpoint/validate
POST /checkpoint/restore
GET  /checkpoint/{id}
GET  /checkpoint/last-known-good

POST /recovery/detect
POST /recovery/plan
POST /recovery/simulate
POST /recovery/execute
POST /recovery/escalate
POST /recovery/verify

GET  /incidents/{id}
GET  /incidents/{id}/timeline
POST /incidents/{id}/bundle
```


# 113. DSL

```text
CHECKPOINT CREATE scope:workspace
CHECKPOINT VALIDATE last
RESTORE checkpoint:C42 scope:service
ROLLBACK transaction:T12
RECOVERY START incident:INC-42
RECOVERY ESCALATE DOCTOR
RECOVERY ESCALATE CONNECTBUSBOT
QUARANTINE component:router
SHOW LAST_KNOWN_GOOD
REPLAY DRY correlation:R1
```


# 114. Schema SQLite

```sql
checkpoints(
  checkpoint_id TEXT PRIMARY KEY,
  parent_id TEXT,
  scope TEXT,
  status TEXT,
  created_at TEXT,
  manifest_json TEXT
);

checkpoint_artifacts(
  checkpoint_id TEXT,
  artifact_ref TEXT,
  hash TEXT,
  restore_mode TEXT
);

recovery_incidents(
  incident_id TEXT PRIMARY KEY,
  severity TEXT,
  status TEXT,
  detected_at TEXT,
  resolved_at TEXT
);

recovery_events(
  event_id TEXT PRIMARY KEY,
  incident_id TEXT,
  event_type TEXT,
  payload_json TEXT,
  timestamp TEXT
);

recovery_recipes(
  recipe_id TEXT,
  version TEXT,
  payload_json TEXT,
  PRIMARY KEY(recipe_id, version)
);
```


# 115. Architecture logicielle

```text
StabilityRecoveryEngine
├── StabilityMonitor
├── InvariantChecker
├── CheckpointManager
├── DeltaLedger
├── TransactionManager
├── ReplayEngine
├── RestoreEngine
├── IncidentManager
├── QuickResolution
├── Doctor
├── ConnectBusBot
├── Immune
├── CircuitBreakerManager
├── RecipeRegistry
├── VerificationEngine
├── ForensicBundler
├── TimeWarpAdapter
├── RegulatorAdapter
├── ForgeAdapter
└── HUDAdapter
```


# 116. Interfaces de composants

Chaque composant recuperable expose idealement :

```text
health()
snapshot()
restore(snapshot)
verify()
dependencies()
version()
```

Optionnel :

```text
repair()
quarantine()
migrate()
```

Cette interface standard simplifie Doctor et Immune.


# 117. Health contract

Un health check ne doit pas seulement dire :

```text
200 OK
```

Il peut couvrir :
- liveness ;
- readiness ;
- dependencies ;
- data integrity ;
- version.

On distingue :

```text
ALIVE
READY
HEALTHY
DEGRADED
```


# 118. Invariants globaux

```text
R1. Tout rollback doit avoir une cible explicite.
R2. Toute restauration declare sa fidelite.
R3. Un checkpoint non valide n'est pas KNOWN_GOOD.
R4. Toute compensation est distinguee d'un rollback.
R5. Les effets externes UNKNOWN sont verifies avant retry.
R6. Le recovery preserve la provenance.
R7. La preuve est preservee avant mutation destructive.
R8. Toute escalation est journalisee.
R9. Un repair loop est borne.
R10. Le dernier etat connu bon reste adressable.
R11. Les permissions ne sont jamais augmentees silencieusement.
R12. Une restauration n'est terminee qu'apres verification.
```


# 119. Invariants Doctor

```text
D1. Doctor collecte avant de modifier si le contexte le permet.
D2. Doctor associe toute reparation a une hypothese.
D3. Toute reparation a un test de verification.
D4. Toute reparation non triviale a un plan de retour ou compensation.
D5. Echec apres seuil -> escalation.
```


# 120. Invariants ConnectBusBot

```text
C1. ConnectBusBot restaure les connexions, pas les conclusions diagnostiques.
C2. Toute reinstallation utilise un artefact verifie.
C3. La config est sauvegardee avant remplacement.
C4. Doctor est reteste apres reconnexion.
C5. Echec structurel -> Immune.
```


# 121. Invariants Immune

```text
I1. Confinement avant propagation.
I2. Preserve evidence before cleanup.
I3. Last-known-good prefere a une reconstruction non prouvee.
I4. Les actions de grande portee exigent les permissions correspondantes.
I5. Toute quarantaine est reversible ou documentee.
```


# 122. Tests unitaires

```text
test_checkpoint_requires_manifest
test_checkpoint_validation
test_exact_restore_hash
test_compensation_not_marked_rollback
test_unknown_external_effect_checked_before_retry
test_quick_fix_max_attempts
test_doctor_escalates
test_connectbus_restores_dependency
test_immune_quarantine
test_recovery_verification_required
test_replay_idempotence_guard
test_circuit_breaker_opens
```


# 123. Tests de proprietes

```text
P1. Restoring a verified exact checkpoint preserves expected hashes.
P2. Every committed mutation has a provenance event.
P3. Every recovery ends in RECOVERED, FAILED or MANUAL_REQUIRED.
P4. Retry count is finite.
P5. No checkpoint marked KNOWN_GOOD has failed validation.
P6. Active revert creates a new revision; it never erases historical events.
```


# 124. Chaos testing

Injecter :
- process kill ;
- disk full ;
- corrupted cache ;
- dependency unavailable ;
- partial write ;
- timeout ;
- bad config ;
- GPU OOM.

Verifier :
- detection ;
- containment ;
- checkpoint ;
- escalation ;
- recovery ;
- absence de perte non declaree.


# 125. Crash recovery

Au redemarrage :

```text
BOOT_RECOVERY
```

1. lire dernier shutdown marker ;
2. detecter transactions incompletes ;
3. verifier journal ;
4. reconstruire reservations ;
5. identifier plans Forge en cours ;
6. restaurer services critiques ;
7. smoke tests ;
8. reprendre ou marquer manuel.


# 126. Shutdown propre

Avant arret planifie :
- flush ;
- checkpoint ;
- close transactions ;
- save pending plan state ;
- mark clean shutdown.

Au boot, l'absence de marqueur `clean` signale un arret inattendu.


# 127. Write-ahead log

Avant mutation persistante :

```text
WAL append intent
-> apply
-> mark committed
```

Au crash :
- transactions committees sont rejouees ;
- non committees sont annulees selon la semantique.


# 128. Atomic replace

Pour les fichiers de config :

```text
write temp
fsync
validate
atomic rename
```

Cela evite les fichiers partiellement ecrits.


# 129. Double-buffer configuration

Deux slots :

```text
config_A
config_B
active_pointer
```

Ecrire le slot inactif, valider, puis basculer le pointeur.

Retour rapide en cas d'echec.


# 130. Blue-green recovery

Pour un service :
- environnement Blue actif ;
- restaurer Green ;
- verifier ;
- switch traffic ;
- conserver Blue pour rollback.

Approche utile lorsque les ressources le permettent.


# 131. Strategie progressive

Ordre recommande :

```text
observe
-> local repair
-> component restore
-> service restore
-> workspace restore
-> system restore
```

Ne pas commencer par la restauration la plus large.


# 132. Matrice panne / action

Exemple :

```text
known transient       -> retry/restart
known config defect   -> config rollback
dependency broken     -> ConnectBusBot
unknown local defect  -> Doctor
integrity failure     -> Immune
system-wide corruption-> last-known-good
```

Cette matrice est une politique, pas un diagnostic automatique.


# 133. Analyse de causalite apres incident

Apres recovery :

```text
RootCauseAnalysis
```

distingue :
- cause proximale ;
- conditions contributives ;
- defaillance de detection ;
- defaillance de recovery.

Cela evite de fixer seulement le symptome.


# 134. Preventive checkpoint

Un incident peut ajouter une regle :

```text
before operation_type X:
    require checkpoint
```

Le systeme s'ameliore par garde-fous plutot que par confiance abstraite.


# 135. Promotion d'une recette rapide

Une reparation Doctor peut devenir QuickResolution seulement apres :
- repetitions coherentes ;
- faible risque ;
- verification automatique ;
- rollback prouve.

Sinon elle reste dans Doctor.


# 136. De-promotion

Si une quick fix echoue trop souvent :

```text
success_rate < threshold
```

elle est retiree du niveau rapide et renvoyee vers Doctor.


# 137. Catalogue de dernier recours

Immune peut posseder des recettes :
- isolate service ;
- disable plugin ;
- restore config baseline ;
- restore binary baseline ;
- rebuild index ;
- switch model baseline.

Chaque recette porte rayon et pertes potentielles.


# 138. Mode safe

Mode :

```text
SAFE_MODE
```

Charge seulement :
- composants critiques ;
- modele connu bon ;
- config minimale ;
- monitoring ;
- recovery engine.

Utilise pour diagnostiquer un systeme incapable de demarrer normalement.


# 139. Mode forensic read-only

Avant Immune :

```text
FORENSIC_READ_ONLY
```

bloque les mutations non necessaires et collecte l'etat.

Utile pour preservations de preuve et anomalies complexes.


# 140. Recovery planifiable par Forge

Forge peut proposer un plan, mais le plan doit passer :

```text
RecoveryPolicy
ResourceBudget
PermissionGate
RiskGate
VerificationContract
```

Le moteur de recovery garde la responsabilite de l'etat transactionnel.


# 141. MBC pour recovery

Les causes et plans de reparation sont des branches :

```text
cause branches
repair branches
```

Le MBC peut comparer :
- plausibilite ;
- cout ;
- risque ;
- gain d'information.

Le plan selectionne ne devient action qu'apres gates.


# 142. Structure 04 pour recovery

Le zoom fractal permet :
- zoomer du symptome vers composant ;
- remonter vers sur-systeme ;
- changer de frame :
  - runtime ;
  - config ;
  - network ;
  - security.

Doctor devient ainsi un navigateur multi-echelles de panne.


# 143. Structure 05 pour recovery

Le regulateur reserve les ressources necessaires au recovery.

Pendant incident :
- exploration ordinaire reduite ;
- P0/P1 prioritaires ;
- reserve deverrouillee pour recovery.

Mais la reserve ne signifie pas autorisation de toutes les actions.


# 144. TimeWarp comme preuve

TimeWarp conserve :
- avant ;
- evenement ;
- erreur ;
- actions ;
- restauration ;
- verification.

On peut comparer :

```text
diff(last_known_good, incident_state)
```

pour reduire l'espace de diagnostic.


# 145. Equation maitresse

Etat :

```text
X_t
```

Mesure :

```text
S_t = Stability(X_t)
```

Si stable :

```text
continue
```

Sinon :

```text
incident = Detect(X_t)
scope = MinRecoveryRadius(incident)
C* = SelectRestorePoint(scope)
P* = PlanRecovery(incident, C*)
```

Admission :

```text
G(P*) =
Permission
AND Capacity
AND Risk
AND Reversibility
AND EvidencePreservation
```

Execution :

```text
X_r =
Verify(
  ExecuteRecovery(
    CheckpointCurrent(
      Contain(X_t)
    ),
    P*
  )
)
```

Succes :

```text
Stable(X_r) = true
```

Puis :

```text
Observe(X_r, window)
```

et eventuellement :

```text
PromoteToKnownGood(X_r)
```


# 146. Formule d'escalade

```text
L_{n+1} =
Escalate(L_n)
```

si :

```text
attempts(L_n) >= max_attempts
OR confidence_gain <= epsilon
OR risk_increases
OR diagnostic_path_broken
```

Chaine :

```text
L0 QuickResolution
L1 Doctor
L2 ConnectBusBot
L3 Immune
L4 ManualRequired
```


# 147. Formule de choix de checkpoint

Parmi les checkpoints admissibles :

```text
C* =
argmax_C
[
  confidence_good(C)
  - alpha * age(C)
  - beta * restore_cost(C)
  - gamma * expected_data_loss(C)
]
```

sous :

```text
compatible(C) = true
verified(C) = true
```

pour les recoveries critiques.


# 148. MVP experimental

### M0 - Ledger
- operations ;
- deltas ;
- correlation IDs.

### M1 - Checkpoints
- manifest ;
- full/incremental ;
- validate ;
- restore.

### M2 - Transaction recovery
- rollback ;
- WAL ;
- crash marker.

### M3 - Incident manager
- detect ;
- severity ;
- circuit breaker ;
- retries bornes.

### M4 - Escalation
- QuickResolution ;
- Doctor ;
- ConnectBusBot ;
- Immune.

### M5 - Verification
- smoke ;
- invariants ;
- observation window.

### M6 - TimeWarp/HUD
- timeline ;
- last-known-good ;
- replay ;
- incident bundle.


# 149. Criteres d'acceptation

```text
A1. Un checkpoint peut etre cree, valide et restaure.
A2. Un checkpoint non valide n'est jamais KNOWN_GOOD.
A3. Le systeme distingue rollback, compensation et regeneration.
A4. Un rollback conceptuel ne supprime pas l'historique.
A5. Les retries sont bornes.
A6. QuickResolution escalade apres echec.
A7. Doctor produit hypotheses, preuves et plan de retour.
A8. ConnectBusBot peut restaurer une dependance de diagnostic.
A9. Immune peut isoler un composant et preserv er les preuves.
A10. Un incident critique dispose d'un dernier etat connu bon.
A11. Une restauration exige verification.
A12. TimeWarp reconstruit la timeline.
A13. Les actions externes UNKNOWN sont verifiees avant repetition.
A14. Le systeme sait reprendre apres crash simule.
A15. Le HUD affiche niveau de recovery, checkpoint et statut.
A16. Un repair loop est detecte et bloque.
A17. Le rayon minimal de restauration est privilegie.
A18. Les recettes sont versionnees.
A19. Les permissions de recovery sont explicites.
A20. Un incident non resolu produit MANUAL_REQUIRED avec dossier complet.
```


# 150. Suite logique vers Structure 07

La prochaine structure logique est le **systeme de memoire temporelle, journal causal et TimeWarp multi-branche complet**.

Elle pourra formaliser :
- temps d'evenement vs temps d'observation ;
- timelines paralleles ;
- branches temporelles ;
- deltas ;
- snapshots ;
- causalite ;
- vector clocks ;
- replay ;
- comparaison `t0/t1/t2` ;
- fork temporel ;
- merge temporel ;
- provenance temporelle ;
- prediction et validation ;
- cache temporel ;
- retention ;
- compression historique ;
- navigation du present vers un etat passe sans le confondre avec le present.

La Structure 06 definit **comment survivre a l'erreur et revenir proprement** ; la Structure 07 definira **comment toute l'histoire devient elle-meme un espace navigable, comparable et branchable**.
