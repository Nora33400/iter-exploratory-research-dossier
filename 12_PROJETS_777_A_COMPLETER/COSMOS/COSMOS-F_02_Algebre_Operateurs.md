# COSMOS-F — Structure 02
## Algèbre complète des opérateurs de navigation conceptuelle

**Version :** 0.2 — spécification structurante  
**Portée :** COSMOS-F / OCCP / TimeWarp / Forge / HUD  
**Objet :** définir une algèbre opérationnelle permettant de naviguer, transformer, relier, stabiliser et rejouer des espaces conceptuels fractals sans confondre exploration, preuve et action.

---

# 0. Résumé exécutif

La Structure 01 définit le **Nœud Conceptuel Universel (NCU)** comme unité de représentation. La Structure 02 définit ce qu'il est possible de **faire** avec ces nœuds.

L'objectif n'est pas seulement de disposer d'une liste de commandes. Il faut une **algèbre d'opérations contrôlées**, où chaque opérateur possède :

- une sémantique ;
- des entrées et sorties ;
- des préconditions ;
- des postconditions ;
- un coût ;
- un niveau de risque ;
- des permissions ;
- une éventuelle opération inverse ;
- une stratégie de rollback ;
- une trace journalisée ;
- un statut épistémique indépendant ;
- une compatibilité avec OCCP, TimeWarp, Forge et le HUD.

Le noyau proposé est :

```text
NAV = {
  DOWN, UP, SHIFT, PERSPECT, TIME,
  LINK, UNLINK, ANCHOR, RETURN,
  SPLIT, MERGE, EXPAND, COMPRESS,
  FILTER, PROJECT, COMPARE, ALIGN,
  FORK, REPLAY, VALIDATE, EXECUTE
}
```

Le principe central est :

$$
\boxed{\text{Explorer} \neq \text{Valider} \neq \text{Exécuter}}
$$

Une opération de navigation peut enrichir la carte sans produire aucune action réelle.

---

# 1. Domaine algébrique

## 1.1 État global

On définit l'état du système à l'instant `t` :

$$
\Sigma_t=(\mathcal N_t,\mathcal R_t,\mathcal A_t,\mathcal H_t,\mathcal P_t,\mathcal B_t,\mathcal L_t)
$$

avec :

- `\mathcal N_t` : ensemble des NCU actifs ou connus ;
- `\mathcal R_t` : relations entre nœuds ;
- `\mathcal A_t` : ancres et points stables ;
- `\mathcal H_t` : historique / branches TimeWarp ;
- `\mathcal P_t` : permissions ;
- `\mathcal B_t` : budgets de complexité ;
- `\mathcal L_t` : journal append-only des opérations.

## 1.2 Opérateur générique

Tout opérateur `O` est une transformation partielle :

$$
O : (\Sigma_t, X, C_t) \rightharpoonup (\Sigma_{t+1},Y,\Delta_O)
$$

- `X` : arguments ;
- `C_t` : contexte ;
- `Y` : résultat ;
- `\Delta_O` : delta réversible si possible.

L'opérateur peut échouer sans modifier l'état :

$$
O(\Sigma_t,X,C_t)=\bot
$$

## 1.3 Contrat commun

Chaque opérateur implémente le contrat :

```text
OperatorSpec {
  id
  family
  intent
  inputs
  outputs
  preconditions
  occp_gates
  cost_model
  effect
  postconditions
  inverse
  rollback
  log_schema
  errors
}
```

---

# 2. Lois fondamentales

## 2.1 Conservation de provenance

Aucun opérateur ne doit supprimer silencieusement l'origine d'une information.

$$
Prov(N') \supseteq Prov(N) \cup Prov(O)
$$

## 2.2 Conservation épistémique

Une transformation structurelle ne peut pas promouvoir automatiquement une hypothèse en fait.

$$
E(N)=\text{hypothèse} \land O\in NAV
\Rightarrow E(O(N))\neq\text{vérifié}
$$

sauf si `O = VALIDATE` et qu'un protocole de validation explicite réussit.

## 2.3 Permission monotone par défaut

Une opération ne gagne pas automatiquement plus de privilèges que son entrée.

$$
Perm(O(N)) \subseteq Closure(Perm(N),Policy_O)
$$

## 2.4 Réversibilité privilégiée

Si deux solutions sont équivalentes, l'opération réversible est préférée :

$$
Utility(O_1)=Utility(O_2),\quad Rev(O_1)>Rev(O_2)
\Rightarrow O_1 \succ O_2
$$

## 2.5 Journalisation atomique

Toute mutation validée produit exactement une entrée de journal principale :

$$
Commit(O) \Rightarrow \exists!\; L_O
$$

## 2.6 Séparation navigation / action réelle

$$
O\in NAV\setminus\{EXECUTE\}
\Rightarrow \text{NoExternalSideEffect}(O)
$$

---

# 3. Taxonomie des opérateurs

| Famille | Opérateurs | Rôle |
|---|---|---|
| Échelle | DOWN, UP, EXPAND, COMPRESS | changer la granularité |
| Déplacement | SHIFT, RETURN | se déplacer sans changer de profondeur |
| Perspective | PERSPECT, PROJECT | changer axes/acteur/vue |
| Temps | TIME, FORK, REPLAY | naviguer dans les états temporels |
| Relation | LINK, UNLINK, ALIGN, COMPARE | créer/inspecter les relations |
| Structure | SPLIT, MERGE | transformer la composition |
| Contrôle | ANCHOR, FILTER, VALIDATE | stabiliser, sélectionner, vérifier |
| Action | EXECUTE | franchir explicitement la frontière vers un effet réel |

---

# 4. DOWN — ouvrir un sous-espace

## 4.1 Intention

`DOWN` augmente la résolution locale d'un nœud en ouvrant un ou plusieurs sous-nœuds.

$$
DOWN(N,d,q) \rightarrow \{N_1,\ldots,N_k\}
$$

`d` est une dimension ou un axe d'exploration ; `q` est une question ou directive.

## 4.2 Préconditions

- le nœud existe ;
- le statut du nœud est compatible avec l'exploration ;
- le budget disponible autorise l'expansion ;
- la profondeur maximale locale n'est pas dépassée ;
- aucune règle de permission ne bloque l'axe demandé.

## 4.3 Gates OCCP

$$
G_{DOWN}=G_{coh}\land G_{perm}\land G_{budget}\land G_{rev}
$$

## 4.4 Effet

- création de sous-nœuds ;
- lien `contains` ou `decomposes_into` ;
- conservation du parent ;
- propagation contrôlée de provenance ;
- pas de promotion épistémique.

## 4.5 Inverse

L'inverse logique est `UP`, mais `UP` ne détruit pas les sous-nœuds ; il change le focus. Pour annuler matériellement la création, on utilise le rollback du delta `DOWN`.

## 4.6 Exemple

```text
N = "sécurité d'un agent local"
DOWN(N, "permissions")
=> isolation, capacité, consentement, audit, rollback
```

---

# 5. UP — remonter vers un sur-espace

## 5.1 Intention

`UP` réduit la granularité du focus et remonte vers un ou plusieurs parents conceptuels.

$$
UP(N)\rightarrow Parents(N)
$$

Un nœud pouvant avoir plusieurs parents, `UP` peut retourner un ensemble.

## 5.2 Ambiguïté multi-parent

$$
|Parents(N)|>1 \Rightarrow UP(N,selector)
$$

Le sélecteur peut être :

- parent principal ;
- parent causal ;
- parent documentaire ;
- parent choisi par utilisateur ;
- ensemble de parents.

## 5.3 Loi importante

$$
UP(DOWN(N)) \approx N
$$

mais pas nécessairement identité stricte, car l'exploration peut avoir enrichi le contexte.

---

# 6. SHIFT — déplacement latéral

`SHIFT` change de nœud à granularité comparable.

$$
SHIFT(N,r,\theta)\rightarrow N'
$$

- `r` : type de relation ;
- `\theta` : seuil de proximité.

Exemples : voisin sémantique, alternative, dépendance, analogue, contradiction, implémentation parallèle.

La distance peut être :

$$
d(N_i,N_j)=\sum_k \lambda_k d_k(N_i,N_j)
$$

avec composantes sémantique, temporelle, structurelle, épistémique, perspective et provenance.

---

# 7. PERSPECT — changer de perspective

## 7.1 Intention

Un même objet peut être examiné depuis plusieurs acteurs ou cadres.

$$
PERSPECT(N,p_a\rightarrow p_b)\rightarrow N^{(p_b)}
$$

## 7.2 Règle de non-écrasement

Changer de perspective ne remplace pas l'ancienne :

$$
View(N)=\{p_1,p_2,\ldots,p_n\}
$$

## 7.3 Contradictions

Deux perspectives incompatibles restent séparées :

$$
Contradict(p_i,p_j)=1
\not\Rightarrow Merge(p_i,p_j)
$$

Le moteur peut créer un méta-nœud de comparaison au lieu de fusionner.

---

# 8. TIME — déplacement temporel

## 8.1 Intention

`TIME` change le référentiel temporel sans supposer que le passé ou le futur est identique au présent.

$$
TIME(N,t_a\rightarrow t_b)\rightarrow N@t_b
$$

## 8.2 Modes

- `observed_past` ;
- `recorded_state` ;
- `reconstructed_past` ;
- `current` ;
- `projected_future` ;
- `counterfactual`.

Les modes sont explicitement étiquetés.

## 8.3 Interdiction

$$
ProjectedFuture \neq ObservedFuture
$$

Une projection demeure une projection.

---

# 9. LINK — créer une relation

$$
LINK(N_a,N_b,r,m)\rightarrow R_{ab}
$$

`r` décrit la relation, `m` ses métadonnées.

Types possibles :

```text
contains, depends_on, causes?, correlates_with,
contradicts, supports, derived_from, analogue_of,
implements, observed_with, user_associated
```

Les relations causales incertaines doivent être marquées comme hypothétiques.

## 9.1 Symétrie

Certaines relations sont symétriques :

$$
analogue\_of(a,b)=analogue\_of(b,a)
$$

D'autres non :

$$
depends\_on(a,b)\neq depends\_on(b,a)
$$

---

# 10. UNLINK — retirer une relation

`UNLINK` retire une relation active sans effacer son historique.

$$
UNLINK(R_{ab})\rightarrow inactive(R_{ab})
$$

Par défaut, suppression logique plutôt que suppression physique.

Le journal conserve : auteur, raison, date, relation précédente, autorisation.

---

# 11. ANCHOR — stabiliser un point

## 11.1 Intention

Une ancre est un état jugé suffisamment stable pour servir de retour.

$$
ANCHOR(\Sigma_t,label)\rightarrow A_t
$$

Elle conserve :

- hash de l'état ;
- sous-graphe concerné ;
- budgets ;
- versions ;
- statut épistémique ;
- permissions ;
- dépendances ;
- contexte minimal de restauration.

## 11.2 Niveaux

```text
soft_anchor       = point de navigation
stable_anchor     = état cohérent
verified_anchor   = état avec validations associées
release_anchor    = état destiné à l'exécution/distribution
```

---

# 12. RETURN — revenir à un état

$$
RETURN(A_i)\rightarrow Restore(A_i)
$$

Modes :

1. `focus_only` : retour de navigation ;
2. `workspace_restore` : restauration du sous-graphe ;
3. `branch_restore` : création d'une nouvelle branche depuis l'ancre ;
4. `hard_rollback` : annulation transactionnelle si possible.

Le mode par défaut doit être non destructif.

---

# 13. SPLIT — séparer un nœud composite

## 13.1 Cas d'usage

Un nœud mélange plusieurs affirmations, perspectives ou statuts.

$$
SPLIT(N,criterion)\rightarrow \{N_1,\ldots,N_k\}
$$

Critères :

- sémantique ;
- perspective ;
- temporalité ;
- provenance ;
- niveau de preuve ;
- fonction ;
- permission.

## 13.2 Invariant

$$
Information(N) \subseteq \bigcup_i Information(N_i)\cup MetaSplit
$$

Aucune information significative ne doit disparaître.

---

# 14. MERGE — fusion contrôlée

## 14.1 Risque

`MERGE` est l'un des opérateurs les plus dangereux sur le plan conceptuel car il peut effacer des distinctions.

$$
MERGE(N_1,\ldots,N_k,policy)\rightarrow N_m
$$

## 14.2 Conditions fortes

- compatibilité de type ;
- provenance préservée ;
- conflits explicités ;
- statuts épistémiques conservés ;
- aucune permission élargie implicitement ;
- rollback possible ou validation élevée.

## 14.3 Modes

```text
union        = juxtaposition structurée
synthesis    = synthèse avec liens vers sources
canonicalize = création d'une représentation canonique
collapse     = fusion destructive, interdite par défaut
```

## 14.4 Loi

$$
MERGE(SPLIT(N)) \approx N
$$

mais seulement si aucune information nouvelle ni conflit n'a été introduit.

---

# 15. EXPAND — augmenter les dimensions actives

Contrairement à `DOWN`, qui ouvre des sous-nœuds, `EXPAND` ouvre de nouveaux **axes descriptifs**.

$$
EXPAND(N,D^+)\rightarrow N[D\cup D^+]
$$

Exemple : ajouter axes coût, risque, acteur, temps, preuve.

Le nombre de dimensions actives doit respecter le budget :

$$
|D_{active}|\leq D_{max}(B_t)
$$

---

# 16. COMPRESS — réduire sans oublier

$$
COMPRESS(S,policy)\rightarrow (N_c,ArchiveRef)
$$

Compression signifie produire une représentation compacte et conserver les détails accessibles.

Modes :

- résumé ;
- clustering ;
- abstraction ;
- fermeture de branches faibles ;
- externalisation en archive.

Invariant :

$$
DecompressRef(N_c) \Rightarrow accès\ aux\ détails\ originaux
$$

quand ceux-ci n'ont pas été explicitement purgés.

---

# 17. FILTER — sélectionner un sous-espace

$$
FILTER(S,predicate)\rightarrow S'
$$

Le filtre ne modifie pas nécessairement le graphe ; il peut seulement modifier la vue.

Exemples :

```text
preuve >= corroboré
risque <= faible
perspective == utilisateur
temps >= t0
relation == depends_on
```

Il faut distinguer `hidden_by_filter` de `deleted`.

---

# 18. PROJECT — projeter sur quelques dimensions

$$
PROJECT(N,D')\rightarrow \pi_{D'}(N)
$$

Exemple : un NCU 12D peut être affiché en 2D dans le HUD sur axes `temps × confiance`.

La projection est une vue, non une perte du modèle complet.

---

# 19. COMPARE — comparer sans fusionner

$$
COMPARE(N_a,N_b,D)\rightarrow C_{ab}
$$

Sortie :

```text
similarities
differences
conflicts
missing_dimensions
provenance_divergence
epistemic_divergence
cost_delta
```

`COMPARE` est recommandé avant `MERGE`.

---

# 20. ALIGN — créer une correspondance

$$
ALIGN(N_a,N_b,map)\rightarrow M_{ab}
$$

L'alignement indique que deux structures peuvent être mises en correspondance sans affirmer qu'elles sont identiques.

Exemple : aligner vocabulaire d'un modèle utilisateur et vocabulaire d'une API.

---

# 21. FORK — créer une branche indépendante

$$
FORK(A_t,h)\rightarrow Branch_h
$$

Le fork permet d'explorer une hypothèse sans contaminer la branche stable.

Propriétés :

- copie logique, idéalement copy-on-write ;
- identifiant de branche ;
- ancêtre commun ;
- budget propre ;
- journal propre + référence au journal parent.

---

# 22. REPLAY — rejouer une trajectoire

$$
REPLAY(L_{i:j},mode)\rightarrow \Sigma'
$$

Modes :

- `dry_run` ;
- `simulation` ;
- `deterministic_replay` ;
- `best_effort`.

Le replay doit vérifier versions, dépendances et permissions actuelles.

Une ancienne permission ne doit pas être supposée encore valide.

---

# 23. VALIDATE — changer explicitement de statut épistémique

## 23.1 Intention

`VALIDATE` est le seul opérateur standard autorisé à proposer une promotion de statut.

$$
VALIDATE(N,protocol,evidence)\rightarrow Verdict
$$

Verdicts :

```text
PASS
FAIL
INCONCLUSIVE
CONTRADICTED
NEEDS_REPLICATION
```

## 23.2 Promotion

$$
E_{new}=Promote(E_{old},Verdict,Policy)
$$

Jamais :

$$
PASS_{local} \Rightarrow vérité\ universelle
$$

La validation est scoped : test, environnement, version, date, protocole.

---

# 24. EXECUTE — franchir la frontière vers l'action

## 24.1 Particularité

`EXECUTE` n'est pas un simple opérateur de navigation. Il peut provoquer un effet réel.

$$
EXECUTE(plan,capability,approval)\rightarrow Result
$$

## 24.2 Conditions minimales

$$
G_{exec}=G_{permission}\land G_{capability}\land G_{risk}\land G_{scope}\land G_{audit}
$$

Selon la politique, une validation utilisateur explicite peut être obligatoire.

## 24.3 Séparation stricte

```text
EXPLORE -> PLAN -> SIMULATE -> VALIDATE -> EXECUTE
```

et non :

```text
EXPLORE -> EXECUTE
```

par défaut.

---

# 25. Composition des opérateurs

## 25.1 Composition séquentielle

$$
(O_2\circ O_1)(\Sigma)=O_2(O_1(\Sigma))
$$

Exemple :

```text
DOWN -> PERSPECT -> COMPARE -> ANCHOR
```

## 25.2 Composition conditionnelle

$$
O = G ? O_1 : O_2
$$

Exemple : si budget suffisant `EXPAND`, sinon `COMPRESS`.

## 25.3 Composition parallèle

Des opérations purement exploratoires sur branches indépendantes peuvent s'exécuter en parallèle :

$$
PAR(O_1,O_2,\ldots,O_n)
$$

Le commit commun nécessite résolution des conflits.

---

# 26. Non-commutativité

Beaucoup d'opérations ne commutent pas :

$$
PERSPECT\circ TIME \neq TIME\circ PERSPECT
$$

car changer d'époque puis de perspective peut produire un contexte différent de l'ordre inverse.

Même chose :

$$
FILTER\circ COMPRESS \neq COMPRESS\circ FILTER
$$

Cette propriété doit apparaître dans les métadonnées d'opérateurs.

---

# 27. Idempotence

Certains opérateurs peuvent être idempotents sous conditions :

$$
ANCHOR(A)=A
$$

si l'état n'a pas changé.

$$
FILTER(FILTER(S,p),p)=FILTER(S,p)
$$

En revanche :

$$
DOWN(DOWN(N))\neq DOWN(N)
$$

car la profondeur augmente.

---

# 28. Modèle de coût

Chaque opération reçoit un vecteur de coût :

$$
Cost(O)=
(c_{cpu},c_{gpu},c_{ram},c_{vram},c_{io},c_{token},c_{time},c_{human})
$$

Score normalisé :

$$
C_O=\sum_i w_i c_i
$$

Une opération admissible vérifie :

$$
C_O\leq B_t
$$

Le budget peut être local à une branche.

---

# 29. Modèle de risque

$$
Risk(O)=
(r_{epistemic},r_{structural},r_{privacy},r_{security},r_{external},r_{irreversible})
$$

Score :

$$
R_O=Agg(Risk(O))
$$

Politique générique :

```text
low      -> automatique possible
medium   -> journal + contrôles renforcés
high     -> simulation + validation
critical -> blocage ou approbation explicite forte
```

---

# 30. Gates OCCP standardisées

Chaque opérateur peut demander une combinaison de gates :

```text
G_coherence
G_permission
G_budget
G_capacity
G_reversibility
G_security
G_epistemic
G_scope
G_user
```

Forme générique :

$$
Admissible(O)=\bigwedge_{g\in Gates(O)} g(\Sigma,O,C_t)
$$

Les gates retournent idéalement :

```text
ALLOW
DENY
UNKNOWN
REQUIRES_CONFIRMATION
```

plutôt qu'un booléen trop pauvre.

---

# 31. Machine à états transactionnelle

Chaque opération mutante suit :

```text
PROPOSED
  -> PRECHECKED
  -> SIMULATED? 
  -> AUTHORIZED
  -> APPLIED
  -> VERIFIED
  -> COMMITTED
```

ou :

```text
... -> FAILED -> ROLLED_BACK
```

Une opération externe ajoute :

```text
EXECUTION_PENDING -> EXECUTED -> OBSERVED
```

---

# 32. Delta et rollback

Un delta standard :

```json
{
  "op_id": "...",
  "created_nodes": [],
  "updated_nodes": [],
  "created_relations": [],
  "disabled_relations": [],
  "previous_values": {},
  "side_effects": [],
  "reversible": true
}
```

Rollback :

$$
Rollback(\Sigma_{t+1},\Delta_O)\rightarrow \Sigma_t
$$

Si `reversible=false`, l'opération doit l'annoncer avant commit.

---

# 33. Journal d'opération

Schéma minimal :

```json
{
  "event_id": "evt_...",
  "timestamp": "...",
  "branch": "main",
  "operator": "DOWN",
  "actor": "user|aione|forge|system",
  "inputs": {},
  "context_hash": "...",
  "gates": {},
  "cost": {},
  "risk": {},
  "result_refs": [],
  "delta_ref": "...",
  "status": "COMMITTED"
}
```

Le journal doit être lisible par humain et machine.

---

# 34. Sémantique des erreurs

Familles :

```text
E_INPUT_INVALID
E_NODE_NOT_FOUND
E_PERMISSION_DENIED
E_BUDGET_EXCEEDED
E_GATE_UNCERTAIN
E_CONFLICT
E_NON_REVERSIBLE
E_VERSION_MISMATCH
E_STALE_CONTEXT
E_EXTERNAL_FAILURE
E_VALIDATION_FAILED
```

Une erreur ne doit pas être convertie silencieusement en résultat vide.

---

# 35. Exemple de langage d'opérations

Une syntaxe déclarative simple :

```text
ANCHOR "security-baseline"
DOWN node:security BY dimension:permissions DEPTH 2
PERSPECT current AS user
PERSPECT current AS agent
COMPARE view:user WITH view:agent
FILTER current WHERE epistemic >= observed
FORK "sandbox-test"
VALIDATE current USING protocol:static-audit-v2
RETURN anchor:"security-baseline" MODE focus_only
```

---

# 36. AST d'une commande

```json
{
  "operator": "DOWN",
  "target": "ncu:security",
  "args": {
    "dimension": "permissions",
    "depth": 2
  },
  "constraints": {
    "max_cost": 0.15,
    "branch": "main",
    "external_effects": false
  }
}
```

---

# 37. Planificateur Forge

Forge peut transformer une intention en séquence :

$$
Intent \xrightarrow{Planner} [O_1,O_2,\ldots,O_n]
$$

Le planificateur minimise par exemple :

$$
J=\alpha Cost+\beta Risk+\gamma Irreversibility-\delta InformationGain
$$

sous contraintes de permission et budget.

Il ne doit pas inventer une permission manquante.

---

# 38. Gain d'information

Une exploration peut être priorisée par :

$$
IG(O)=H(\mathcal H_t)-\mathbb E[H(\mathcal H_{t+1}|O)]
$$

Le système cherche des opérations qui réduisent utilement l'incertitude sans coût excessif.

Score :

$$
Priority(O)=\frac{IG(O)\cdot Utility(O)\cdot Safety(O)}{1+Cost(O)+Risk(O)}
$$

---

# 39. Distance à l'ancre

Pour éviter une dérive excessive :

$$
d(\Sigma_t,A_{stable})\leq \delta_{max}
$$

Au-delà :

```text
ANCHOR / COMPRESS / RETURN / demande de validation
```

La distance combine profondeur, divergence de modèle, nombre de branches, modifications et incertitude.

---

# 40. Profondeur fractale contrôlée

$$
depth(N)\leq d_{max}(B_t,R_t,U_t)
$$

Le maximum n'est pas globalement fixe. Il diminue lorsque risque ou incertitude augmentent.

Exemple :

$$
d_{max}=\lfloor d_0\cdot Safety\cdot BudgetFactor\rfloor
$$

---

# 41. Résolution des conflits

Lors d'une composition ou d'un merge :

```text
DETECT -> CLASSIFY -> ISOLATE -> COMPARE -> RESOLVE? -> COMMIT
```

Types :

- conflit de valeur ;
- conflit de provenance ;
- conflit de statut épistémique ;
- conflit de permission ;
- conflit temporel ;
- conflit de version.

Le mode sûr est de conserver les alternatives si aucune résolution n'est justifiée.

---

# 42. Multi-agent / multi-acteur

Chaque opération possède un `actor_id` et éventuellement un `on_behalf_of`.

$$
Authority(actor,O,target)
$$

Une suggestion d'un autre acteur n'est pas une permission :

$$
Suggestion_A(O_B)\neq Authorization_B(O_B)
$$

C'est essentiel pour les systèmes collaboratifs humains/IA.

---

# 43. HUD : représentation interactive

Mapping recommandé :

```text
DOWN      -> entrer / zoomer
UP        -> remonter
SHIFT     -> glisser latéralement
PERSPECT  -> changer de couche/acteur
TIME      -> scrub temporel
LINK      -> tracer un lien
ANCHOR    -> épingle stable
RETURN    -> retour ancre
SPLIT     -> éclater
MERGE     -> regrouper contrôlé
EXPAND    -> ajouter axes
COMPRESS  -> condenser
FORK      -> branche visuelle
VALIDATE  -> badge de preuve
EXECUTE   -> action séparée, visuellement distincte
```

Le bouton `EXECUTE` ne doit pas ressembler à une simple navigation.

---

# 44. API minimale

```http
POST /ops/propose
POST /ops/simulate
POST /ops/commit
POST /ops/rollback
GET  /ops/{id}
GET  /branches/{id}/history
POST /anchors
POST /anchors/{id}/return
```

Réponse type :

```json
{
  "op_id": "op_123",
  "admissibility": "ALLOW",
  "estimated_cost": {},
  "risk": {},
  "requires_confirmation": false,
  "simulation_ref": "sim_42"
}
```

---

# 45. Tables de stockage

Schéma relationnel minimal :

```text
operators
operator_runs
operator_deltas
operator_gates
operator_errors
anchors
branches
branch_heads
relations
nodes
node_versions
```

Index importants :

```text
operator_runs(branch_id, timestamp)
relations(source_id, type, target_id)
node_versions(node_id, version)
anchors(branch_id, created_at)
```

---

# 46. Invariants testables

1. Aucun `DOWN` ne supprime le parent.
2. Aucun `PERSPECT` n'écrase silencieusement une autre perspective.
3. Aucun `TIME(projected_future)` ne devient `observed` sans preuve.
4. `UNLINK` conserve l'historique.
5. `MERGE` conserve la provenance.
6. `COMPRESS` conserve un chemin vers les détails si la politique l'exige.
7. `RETURN` ne produit pas d'effet externe par défaut.
8. `REPLAY` revalide les permissions.
9. `VALIDATE` est scoped.
10. `EXECUTE` est séparé des opérateurs purement conceptuels.

---

# 47. Tests de propriété

Exemples property-based :

```text
UP(DOWN(N)).contains(N)
FILTER(FILTER(S,p),p) == FILTER(S,p)
rollback(commit(O)) ~= state_before(O)
provenance_after(MERGE) >= union(provenance_inputs)
permissions_after(O) !> allowed_permissions(O)
```

`~=` signifie équivalence selon les champs stables, pas nécessairement identité binaire.

---

# 48. Scénario complet AIONE

Objectif : explorer une nouvelle fonction locale sans l'exécuter.

```text
1. ANCHOR baseline
2. FORK experiment-01
3. DOWN feature BY architecture
4. EXPAND current ADD [security,cost,testability]
5. PERSPECT current AS user
6. PERSPECT current AS system
7. COMPARE user_view WITH system_view
8. FILTER current WHERE risk <= medium
9. DOWN candidate BY implementation
10. VALIDATE candidate USING static-test
11. COMPRESS branch
12. ANCHOR candidate-reviewed
```

Aucune étape n'utilise `EXECUTE`. L'espace peut donc être exploré, formalisé et validé avant toute action réelle.

---

# 49. Scénario TimeWarp

```text
A0 = ANCHOR(main)
FORK branch-A
DOWN -> LINK -> PERSPECT -> VALIDATE
FORK branch-B FROM A0
DOWN -> SHIFT -> COMPARE
COMPARE branch-A WITH branch-B
RETURN A0 MODE branch_restore
```

Le système conserve les trois trajectoires : origine, A et B.

---

# 50. Scénario de complexité adaptative

Si :

$$
Budget\downarrow \lor Risk\uparrow \lor Uncertainty\uparrow
$$

alors le régulateur peut choisir :

```text
EXPAND -> interdit
DOWN(depth>1) -> réduit
PAR -> séquentiel
COMPRESS -> favorisé
ANCHOR -> favorisé
RETURN -> disponible
```

Inversement, avec budget élevé et risque bas, plusieurs branches peuvent être explorées en parallèle.

---

# 51. Algèbre compacte

On peut résumer l'algèbre avec quatre classes :

$$
\mathcal O = \mathcal O_N \cup \mathcal O_R \cup \mathcal O_T \cup \mathcal O_X
$$

- `\mathcal O_N` : transformations de nœuds ;
- `\mathcal O_R` : transformations de relations ;
- `\mathcal O_T` : transformations temporelles / branches ;
- `\mathcal O_X` : passage éventuel vers l'exécution.

Toute séquence :

$$
\Pi=O_n\circ\cdots\circ O_2\circ O_1
$$

est admissible si :

$$
\forall i,\quad Admissible(O_i,\Sigma_i)=1
$$

et si les postconditions de `O_i` satisfont les préconditions de `O_{i+1}`.

---

# 52. Opérateurs dérivés

Des macros peuvent être construites :

## 52.1 INVESTIGATE

```text
ANCHOR -> FORK -> DOWN -> EXPAND -> COMPARE -> COMPRESS
```

## 52.2 VERIFY

```text
FILTER -> COMPARE -> VALIDATE -> ANCHOR
```

## 52.3 SAFE_TRY

```text
ANCHOR -> FORK -> SIMULATE -> VALIDATE -> RETURN/COMMIT
```

## 52.4 RECONCILE

```text
COMPARE -> ALIGN -> SPLIT(conflicts) -> MERGE(union)
```

---

# 53. Politique par défaut recommandée

```yaml
defaults:
  navigation_external_effects: false
  destructive_merge: false
  hard_delete_relations: false
  replay_revalidate_permissions: true
  projected_future_is_observed: false
  branch_before_high_risk_transform: true
  anchor_before_irreversible_operation: true
  explicit_execute_boundary: true
```

---

# 54. MVP implémentable

Première version réellement codable :

### Phase A — noyau

```text
DOWN UP SHIFT LINK UNLINK ANCHOR RETURN
```

### Phase B — perspectives et structure

```text
PERSPECT SPLIT MERGE EXPAND COMPRESS FILTER COMPARE
```

### Phase C — TimeWarp

```text
TIME FORK REPLAY
```

### Phase D — preuve et action

```text
VALIDATE EXECUTE
```

Chaque phase doit garder les mêmes contrats d'opérateurs.

---

# 55. Pseudocode du moteur

```python
def apply_operator(state, op, context):
    spec = registry.get(op.name)
    validate_inputs(spec, op)

    gate_result = evaluate_gates(spec.gates, state, op, context)
    if not gate_result.allowed:
        return Rejected(gate_result)

    estimate = estimate_cost_and_risk(spec, state, op)
    if not within_budget(estimate, state.budget):
        return Rejected("E_BUDGET_EXCEEDED")

    simulation = simulate(spec, state, op)

    if simulation.requires_confirmation:
        return PendingConfirmation(simulation)

    new_state, delta = commit_atomically(spec, state, op)
    verify_postconditions(spec, new_state, delta)
    append_log(spec, op, delta, estimate)

    return Applied(new_state, delta)
```

---

# 56. Exemple JSON d'une spécification d'opérateur

```json
{
  "name": "MERGE",
  "family": "structure",
  "mutates_graph": true,
  "external_side_effects": false,
  "reversibility": "delta_required",
  "gates": [
    "coherence",
    "permission",
    "budget",
    "epistemic",
    "reversibility"
  ],
  "risk_floor": "medium",
  "requires_compare_first": true,
  "preserve": [
    "provenance",
    "epistemic_status",
    "conflicts",
    "permissions"
  ]
}
```

---

# 57. Critères de maturité

## Niveau 0 — syntaxe

Les opérations sont reconnues.

## Niveau 1 — sémantique

Préconditions et effets sont déterministes.

## Niveau 2 — contrôle

Budgets, risques et permissions fonctionnent.

## Niveau 3 — réversibilité

Anchors, delta, rollback, TimeWarp sont prouvés.

## Niveau 4 — orchestration

Forge planifie des séquences complexes.

## Niveau 5 — autonomie bornée

Le système choisit des opérations exploratoires sous politiques formelles, sans franchir implicitement la frontière `EXECUTE`.

---

# 58. Équation maîtresse de la Structure 02

$$
\boxed{
\Sigma_{t+1}
=
O_t(\Sigma_t)
\quad\text{sous}\quad
G_t=1,
\ C(O_t)\leq B_t,
\ R(O_t)\leq R_{max},
\ Prov_{t+1}\supseteq Prov_t
}
$$

avec :

$$
O_t\in\mathcal O
$$

et la contrainte de frontière :

$$
\boxed{
O_t\neq EXECUTE
\Rightarrow
NoExternalSideEffect(O_t)
}
$$

---

# 59. Forme opérationnelle complète

```text
INTENTION
  ↓
PARSE
  ↓
PROPOSE OPERATOR(S)
  ↓
CHECK PRECONDITIONS
  ↓
OCCP GATES
  ↓
COST + RISK
  ↓
SIMULATE
  ↓
APPLY / BRANCH / REJECT
  ↓
VERIFY POSTCONDITIONS
  ↓
LOG DELTA
  ↓
ANCHOR / CONTINUE / RETURN
  ↓
OPTIONAL VALIDATE
  ↓
EXPLICIT EXECUTE BOUNDARY
```

---

# 60. Conclusion

La Structure 02 transforme COSMOS-F d'une cartographie statique en **système navigable et manipulable**.

Le point décisif est que l'algèbre n'est pas seulement descriptive. Elle définit :

- comment se déplacer dans un espace conceptuel fractal ;
- comment augmenter ou réduire sa complexité ;
- comment préserver les alternatives ;
- comment comparer sans fusionner ;
- comment revenir en arrière ;
- comment rejouer une trajectoire ;
- comment valider explicitement ;
- comment empêcher qu'une exploration conceptuelle devienne automatiquement une action réelle.

La chaîne devient :

```text
NCU
 -> opérateurs COSMOS-F
 -> gates OCCP
 -> branches TimeWarp
 -> orchestration Forge
 -> visualisation HUD
 -> validation
 -> action explicite éventuelle
```

Cette algèbre constitue donc le **moteur de mouvement** de COSMOS-F.

---

# Annexe A — Registre condensé

| Opérateur | Mutation | Inverse logique | Effet externe | Risque par défaut |
|---|---:|---|---:|---|
| DOWN | oui | UP/rollback | non | faible |
| UP | non | DOWN | non | faible |
| SHIFT | non | SHIFT inverse | non | faible |
| PERSPECT | vue/possible création | PERSPECT | non | faible |
| TIME | vue/branche | TIME/RETURN | non | moyen |
| LINK | oui | UNLINK | non | faible-moyen |
| UNLINK | oui | relink | non | moyen |
| ANCHOR | oui | delete anchor | non | faible |
| RETURN | selon mode | replay | non par défaut | moyen |
| SPLIT | oui | MERGE contrôlé | non | moyen |
| MERGE | oui | rollback | non | moyen-élevé |
| EXPAND | oui/vue | COMPRESS | non | faible-moyen |
| COMPRESS | oui/vue | expand via archive | non | moyen |
| FILTER | vue | clear filter | non | faible |
| PROJECT | vue | restore dimensions | non | faible |
| COMPARE | non | - | non | faible |
| ALIGN | oui | unlink alignment | non | moyen |
| FORK | oui | abandon branch | non | faible |
| REPLAY | selon mode | RETURN | non par défaut | moyen |
| VALIDATE | métadonnées | revoke/append verdict | non | moyen |
| EXECUTE | potentiellement oui | variable | oui | élevé |

---

# Annexe B — Priorité d'implémentation

```text
P0: ANCHOR, RETURN, DOWN, UP, LINK, UNLINK
P1: SHIFT, PERSPECT, FILTER, COMPARE, FORK
P2: SPLIT, MERGE, EXPAND, COMPRESS, PROJECT, ALIGN
P3: TIME, REPLAY, VALIDATE
P4: EXECUTE + politiques de capacité
```

**Fin de la Structure 02.**
