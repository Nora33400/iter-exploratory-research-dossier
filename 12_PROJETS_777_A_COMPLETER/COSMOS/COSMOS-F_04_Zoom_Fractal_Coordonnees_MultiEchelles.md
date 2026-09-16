# COSMOS-F - Structure 04
## Zoom fractal et coordonnees conceptuelles multi-echelles

**Version :** 0.4  
**Statut :** specification conceptuelle, mathematique et technique  
**Dependances :** Structure 01 - NCU ; Structure 02 - Algebre des operateurs ; Structure 03 - Moteur de branches concurrentes ; OCCP ; TimeWarp ; AIONE Forge.

---

## Resume

Cette structure definit comment COSMOS-F peut representer un espace conceptuel potentiellement sans borne sans tenter de materialiser "l'infini" en memoire.

Le principe est de remplacer un espace absolu complet par une **navigation locale multi-echelles**. Le systeme ne charge que :
- le noeud courant ;
- ses voisinages pertinents ;
- les ancres necessaires au retour ;
- les sur-espaces utiles ;
- les sous-espaces ouverts ;
- les dimensions activees par la question courante.

Un concept peut donc etre observe comme une unite, puis ouvert comme un espace, puis re-ouvert a une echelle plus fine. Inversement, plusieurs details peuvent etre compresses en un sur-concept.

La structure formalise :
- l'echelle fractale ;
- les coordonnees relatives ;
- les espaces et sous-espaces ;
- le zoom ;
- les changements de dimension ;
- les voisinages ;
- les distances contextuelles ;
- les projections ;
- les cartes locales ;
- les ancres ;
- le lazy loading conceptuel ;
- la compression inter-echelles ;
- la coherence des transformations ;
- l'integration avec NCU, MBC, OCCP, TimeWarp et Forge.

Principe central :

```text
espace potentiellement infini
!=
espace integralement materialise
```

et :

```text
naviguer dans l'infini
=
construire localement ce qui est necessaire,
conserver les chemins de retour,
et pouvoir changer d'echelle sans perdre la provenance.
```

# 1. Probleme formel

Un espace conceptuel peut contenir un nombre arbitraire de concepts, relations, perspectives, temporalites et dimensions descriptives.

On note l'espace total theorique :

```text
Omega = union de tous les espaces conceptuels possibles
```

Mais `Omega` n'est jamais instancie entierement.

A l'instant `t`, COSMOS-F manipule seulement une fenetre locale :

```text
W_t subset Omega
```

La fenetre active est determinee par :

```text
W_t = Window(
    focus_t,
    scale_t,
    active_dimensions_t,
    budget_t,
    query_t
)
```

L'objectif est que `W_t` soit suffisamment riche pour repondre a la question courante sans exploser le cout de calcul.


# 2. Principe de fractalisation

Un noeud conceptuel peut simultanement etre :
- un point dans un espace plus grand ;
- un espace contenant des points plus fins.

On ecrit :

```text
N_i in S_parent
```

et :

```text
S_i = Expand(N_i)
```

Ainsi :

```text
N_i <-> S_i
```

selon l'echelle d'observation.

Cette dualite point/espace est le coeur du zoom fractal.

Exemple :

```text
"reseau"
  -> comme noeud dans "informatique"
  -> comme espace contenant :
       protocoles
       topologies
       flux
       identites
       securite
       temporalites
```

La representation depend donc de l'echelle, pas d'une ontologie unique figée.


# 3. Niveaux d'echelle

On introduit un indice d'echelle :

```text
lambda in Z
```

Convention :
- `lambda = 0` : echelle locale de reference ;
- `lambda > 0` : abstraction croissante ;
- `lambda < 0` : detail croissant.

Exemple :

```text
lambda +2 : systeme socio-technique
lambda +1 : ecosysteme logiciel
lambda  0 : application
lambda -1 : composant
lambda -2 : module
lambda -3 : fonction
```

Le nombre de niveaux n'est pas fixe.

On peut aussi utiliser une echelle continue :

```text
lambda in R
```

si le systeme souhaite interpoler entre niveaux de granularite.


# 4. Coordonnees conceptuelles relatives

COSMOS-F n'impose pas un repere absolu global.

Un point est repere relativement a un cadre :

```text
x_N^F = (x1, x2, ..., xd)
```

ou :
- `N` = noeud ;
- `F` = frame ou repere ;
- `d` = nombre de dimensions actives.

Les coordonnees peuvent exprimer :
- proximite semantique ;
- temporalite ;
- perspective ;
- abstraction ;
- causalite ;
- confiance ;
- risque ;
- utilite ;
- tout autre axe declare.

Les coordonnees sont donc **descriptives et contextuelles**, pas physiques par defaut.


# 5. Frames ou reperes

Une frame est definie par :

```text
F = (
  frame_id,
  origin,
  axes,
  metric,
  scale,
  scope,
  provenance
)
```

Exemple :

```json
{
  "frame_id": "frame_debug",
  "origin": "node:service_failure",
  "axes": [
    "causal_distance",
    "execution_layer",
    "confidence"
  ],
  "scale": 0,
  "scope": ["diagnostic", "local-system"]
}
```

Deux frames peuvent voir le meme noeud differemment.

Le systeme doit toujours pouvoir repondre :

```text
"coordonnees dans quel repere ?"
```


# 6. Transformation entre reperes

Une transformation de frame :

```text
T_FG : F -> G
```

transforme les coordonnees :

```text
x^G = T_FG(x^F)
```

La transformation peut etre :
- exacte ;
- approximative ;
- partielle ;
- non definie.

Elle possede donc un niveau de confiance :

```text
conf(T_FG) in [0,1]
```

et un cout informationnel :

```text
loss(T_FG) >= 0
```

Si la transformation perd une dimension, cette perte doit etre annoncee.


# 7. Dimensions dynamiques

Le nombre de dimensions actives varie avec la question.

```text
D_active(t) subset D_possible
```

Selection :

```text
D_active =
SelectDimensions(
  query,
  focus,
  budget,
  expected_information_gain
)
```

Exemple :

Pour une panne reseau, on peut activer :

```text
D_active = {
  couche,
  causalite,
  temporalite,
  confiance,
  cout_test
}
```

et ignorer temporairement des dimensions non pertinentes.

Le systeme peut ajouter une dimension sans reconstruire tout l'espace.


# 8. Activation et desactivation dimensionnelle

Deux operateurs :

```text
DIM_ON(d)
DIM_OFF(d)
```

`DIM_ON` :
- ajoute un axe ;
- recalcule seulement les voisins affectes ;
- journalise l'effet ;
- verifie le budget.

`DIM_OFF` :
- projette l'espace vers les dimensions restantes ;
- conserve les valeurs originales en archive ;
- produit un rapport de perte.

Invariant :

```text
DIM_OFF != DELETE_DATA
```

Desactiver une dimension signifie la retirer de la vue active, pas detruire son information.


# 9. Projection dimensionnelle

Une projection :

```text
Pi_D : R^n -> R^k
```

avec `k <= n`.

Dans COSMOS-F :

```text
Projection(space, dimensions_kept) -> view
```

La projection produit :
- coordonnees projetees ;
- dimensions omises ;
- collisions eventuelles ;
- perte estimee ;
- provenance.

Deux noeuds distincts peuvent devenir superposes apres projection.

Le systeme doit donc conserver leur identite meme si leur representation visuelle coincide.


# 10. Embedding et coordonnees explicites

COSMOS-F peut combiner deux types de coordonnees.

### Coordonnees explicites
Axes interpretable :

```text
abstraction = 0.8
confidence = 0.4
risk = 0.1
```

### Embedding latent
Vecteur appris :

```text
z in R^m
```

L'embedding sert a calculer des voisinages semantiques, mais ne remplace pas les axes explicites.

Principe :

```text
latent_similarity != semantic_truth
```

Le moteur peut utiliser le latent pour proposer des voisins, puis les valider avec les relations explicites.


# 11. Distance conceptuelle

La distance est contextuelle.

```text
d_q(A,B)
```

depend de la question `q`.

Forme composite :

```text
d_q(A,B) =
  w_sem*d_sem
+ w_scope*d_scope
+ w_time*d_time
+ w_persp*d_persp
+ w_epi*d_epistemic
+ w_rel*d_relational
+ w_scale*d_scale
```

Les poids sont normalises :

```text
sum(w_i) = 1
```

Pour une requete temporelle, `w_time` augmente.
Pour une requete de preuve, `w_epi` augmente.
Pour une navigation hierarchique, `w_scale` augmente.


# 12. Distances asymetriques

Certaines relations ne sont pas symetriques.

Exemple :

```text
"chien" -> "animal"
```

est une generalisation naturelle, mais le chemin inverse ouvre de nombreuses possibilites.

On peut donc definir :

```text
d(A -> B) != d(B -> A)
```

La distance peut integrer :
- cout d'expansion ;
- nombre de choix ;
- information perdue ;
- permissions.

Cette asymetrie est utile pour mesurer le cout de navigation.


# 13. Voisinage local

Le voisinage d'un noeud `N` :

```text
V_k(N)
```

contient au plus `k` voisins selectionnes.

Criteres :
- distance ;
- pertinence ;
- diversite ;
- confiance ;
- nouveaute ;
- cout.

Selection :

```text
V_k(N) =
TopK_Diverse(
  admissible_neighbors(N)
)
```

Le voisinage ne cherche pas tous les noeuds possibles. Il construit une carte locale utile.


# 14. Voisinages multi-types

Un noeud peut avoir plusieurs voisinages :

```text
V_semantic(N)
V_causal(N)
V_temporal(N)
V_structural(N)
V_perspective(N)
V_evidence(N)
```

L'utilisateur peut changer de mode de voisinage sans changer de noeud central.

Exemple :

```text
SHOW NEIGHBORS node:X BY causal
SHOW NEIGHBORS node:X BY evidence
SHOW NEIGHBORS node:X BY semantic
```

Cela evite de reduire toutes les relations a une seule notion de "proximite".


# 15. Sous-espace

Un sous-espace `S_child` est ouvert depuis un noeud ou une region :

```text
S_child = Expand(parent, rule)
```

Il contient :
- sa propre frame ;
- son budget ;
- ses dimensions actives ;
- ses noeuds ;
- ses relations ;
- ses ancres de sortie.

Un sous-espace peut avoir plusieurs parents conceptuels.

Donc :

```text
parents(S_child) = {S_a, S_b, ...}
```

COSMOS-F reste un hypergraphe multi-parent.


# 16. Sur-espace

Le sur-espace est une abstraction qui rassemble plusieurs elements :

```text
S_super = Abstract({N_1,...,N_k})
```

Il doit conserver :
- quels elements ont ete agreges ;
- quels criteres ont ete utilises ;
- quelle information a ete compressee ;
- quelles exceptions subsistent.

Un sur-espace n'est pas necessairement "plus vrai" que ses sous-espaces.

Il est seulement une representation a plus grande echelle.


# 17. Zoom IN

Operateur :

```text
ZOOM_IN(target, delta_lambda)
```

Effets :
1. diminuer `lambda` ;
2. ouvrir le sous-espace ;
3. selectionner les dimensions utiles ;
4. charger les voisins locaux ;
5. creer une ancre de retour ;
6. journaliser le changement.

Preconditions :
- cible expandable ;
- budget suffisant ;
- permission d'exploration.

Postcondition :

```text
focus_{t+1} in Expand(focus_t)
```


# 18. Zoom OUT

Operateur :

```text
ZOOM_OUT(delta_lambda)
```

Effets :
- augmenter l'abstraction ;
- compresser la vue locale ;
- conserver un resume ;
- revenir vers un parent ou un sur-espace ;
- restaurer un contexte plus large.

Si plusieurs parents existent, le moteur ne choisit pas silencieusement.

Il peut :
- utiliser le parent d'origine ;
- demander ;
- proposer les parents possibles ;
- appliquer une politique de retour memorisee.


# 19. Zoom lateral

Un deplacement a echelle constante :

```text
PAN(direction)
```

permet de changer de region conceptuelle sans modifier `lambda`.

Exemples :
- concept voisin ;
- acteur voisin ;
- periode voisine ;
- technologie voisine.

La navigation complete devient :

```text
ZOOM_IN
ZOOM_OUT
PAN
ROTATE_FRAME
PROJECT
```

analogue a une camera, mais dans un espace conceptuel multi-dimensionnel.


# 20. Rotation de perspective

Operateur :

```text
ROTATE_FRAME(axis_or_perspective)
```

Il change le repere sans deplacer le concept.

Exemple :

```text
focus = "meme incident"
frame_user -> frame_developer -> frame_auditor
```

On distingue donc :

```text
move(content)
```

et :

```text
change_view(content)
```

Cette separation evite de confondre changement de perspective et changement de fait.


# 21. Ancres

Une ancre est un point de retour stable :

```text
A_k = (
  anchor_id,
  node,
  frame,
  scale,
  dimensions,
  branch_state,
  timestamp,
  reason
)
```

Creation :

```text
ANCHOR("before-deep-exploration")
```

Retour :

```text
RETURN(anchor_id)
```

L'ancre peut etre :
- locale ;
- temporelle ;
- de session ;
- persistante ;
- partagee.


# 22. Pile de navigation

En plus des ancres nommees, le moteur conserve une pile :

```text
NavStack = [
  state_0,
  state_1,
  ...
  state_n
]
```

Chaque etat contient :
- focus ;
- frame ;
- echelle ;
- dimensions ;
- filtres ;
- branche active.

Operations :

```text
BACK
FORWARD
RETURN_HOME
```

Le comportement ressemble a un navigateur, mais sur des espaces conceptuels.


# 23. Adresse conceptuelle

Un objet peut recevoir une adresse relative :

```text
cosmos://space/root/node/X?scale=-2&frame=debug
```

ou structurée :

```text
Address = (
  universe,
  space_path,
  node_id,
  scale,
  frame,
  branch,
  time
)
```

L'adresse ne signifie pas qu'il existe un chemin unique.

Elle encode un itineraire reproductible suffisamment precis pour reconstruire la vue.


# 24. Chemins multiples

Deux chemins peuvent conduire au meme noeud :

```text
P1: A -> B -> X
P2: A -> C -> D -> X
```

Le noeud `X` conserve son identite, mais le contexte d'arrivee differe.

On stocke donc :

```text
ArrivalContext = path + frame + scale + active_dimensions
```

Le sens d'un noeud peut changer selon le chemin.

COSMOS-F doit pouvoir comparer ces contextes d'arrivee.


# 25. Navigation paresseuse

Le systeme utilise un modele de **lazy expansion**.

Pseudo-code :

```python
def navigate(target):
    if not cache.contains(target):
        local = build_local_neighborhood(target)
        cache.add(local)
    set_focus(target)
```

Aucun sous-espace n'est developpe avant d'etre necessaire.

Un noeud peut annoncer :

```text
expandable = true
estimated_children = unknown
```

sans generer ses enfants.


# 26. Frontiere de materialisation

La frontiere de materialisation `M_t` separe :
- ce qui est actuellement explicite ;
- ce qui reste seulement generable.

```text
Omega = M_t union Potential_t
```

mais seul `M_t` consomme des ressources detaillees.

Une branche potentielle peut etre representee par un stub :

```json
{
  "node_id": "stub:abc",
  "expandable": true,
  "estimated_cost": 0.12
}
```

L'expansion remplace le stub par une structure plus riche.


# 27. Budget de materialisation

Budget :

```text
B_materialize = (
  max_nodes,
  max_edges,
  max_depth,
  max_dimensions,
  max_memory,
  max_tokens,
  max_compute
)
```

Si la limite est atteinte :
- compresser ;
- archiver ;
- ejecter du cache ;
- reduire le voisinage ;
- refuser une expansion.

La priorite est de preserver :
1. focus courant ;
2. chemin de retour ;
3. preuves importantes ;
4. ancres ;
5. branches actives.


# 28. Cache multi-echelles

Le cache contient plusieurs niveaux :

```text
L0 hot   : focus + voisins immediats
L1 warm  : espaces recents
L2 cold  : resumes compresses
L3 store : representation persistante
```

Eviction :

```text
eviction_score =
  age
  + low_relevance
  + low_revisit_probability
  - anchor_importance
```

Les objets importants pour un retour coherent ne doivent pas etre evinces sans snapshot.


# 29. Compression inter-echelles

On definit :

```text
COMPRESS_SCALE(S, lambda -> lambda+1)
```

Le resultat contient :
- resume ;
- representants ;
- distribution des statuts ;
- conflits non resolus ;
- exceptions ;
- pointeurs vers le detail.

Le resume doit etre **decompressible conceptuellement** :
on peut retrouver les sources et reouvrir le detail.

Invariant :

```text
summary != replacement_of_sources
```


# 30. Decompression

Operateur :

```text
DECOMPRESS(summary)
```

Deux modes :

```text
EXACT_REOPEN
REGENERATE
```

`EXACT_REOPEN` recharge les sources conservees.
`REGENERATE` reconstruit une approximation lorsque seul le resume est disponible.

Le moteur marque clairement le cas :

```text
reconstructed = true
```

afin de ne pas confondre reconstruction et etat historique exact.


# 31. Coherence inter-echelles

Une affirmation a une echelle ne doit pas necessairement etre vraie a toutes les autres.

On definit :

```text
Claim(C, lambda, scope)
```

Une projection vers une autre echelle exige :

```text
LiftOrProject(Claim, lambda1 -> lambda2)
```

avec resultat :
- preserved ;
- weakened ;
- strengthened ;
- undefined ;
- contradictory.

Exemple : une moyenne globale peut masquer des comportements locaux opposes.

Le moteur stocke donc la portee de chaque affirmation.


# 32. Invariants fractals

Invariants :

```text
F1. Tout zoom cree un chemin de retour.
F2. Toute compression conserve la provenance.
F3. Une projection n'efface pas les identites sources.
F4. Un changement de frame ne modifie pas les faits sources.
F5. Un noeud peut avoir plusieurs parents.
F6. Un parent n'implique pas une causalite.
F7. Le budget actif est borne.
F8. L'infini potentiel n'est jamais materialise integralement.
F9. Toute reconstruction est etiquetee.
F10. Les pertes de transformation sont explicites.
```


# 33. Espace local et atlas

Plutot qu'une carte globale, COSMOS-F construit un **atlas**.

```text
Atlas = {Chart_1, Chart_2, ..., Chart_n}
```

Chaque chart est une carte locale :

```text
Chart_i = (
  frame,
  region,
  local_coordinates,
  transitions
)
```

Les zones de recouvrement definissent les transformations :

```text
T_ij : Chart_i overlap Chart_j
```

Cette approche permet de naviguer dans des espaces dont aucune coordonnee globale simple n'existe.


# 34. Atlas fractal

Un chart peut lui-meme contenir un atlas :

```text
Chart_i
  -> Atlas_i
      -> Chart_i1
      -> Chart_i2
```

Le systeme devient recursive :

```text
Atlas(lambda)
  contains Atlas(lambda-1)
```

mais chaque niveau reste charge a la demande.

On obtient une structure potentiellement illimitee tout en conservant une memoire finie.


# 35. Topologie conceptuelle

Toutes les relations n'ont pas besoin de coordonnees numeriques.

COSMOS-F peut definir une topologie par voisinages :

```text
N -> {sets considered local around N}
```

Deux noeuds sont proches s'ils partagent suffisamment de voisinages pertinents.

Cette couche topologique permet de travailler meme lorsqu'aucune metrique fiable n'existe.

La metrique peut etre ajoutee plus tard.


# 36. Hypergraphe multi-echelles

Structure de base :

```text
G = (V, E, H, S)
```

ou :
- `V` : noeuds ;
- `E` : aretes simples ;
- `H` : hyperaretes ;
- `S` : relations d'echelle.

Une hyperarete peut connecter :

```text
{A, B, C} -> relation R
```

sans reduire la relation a trois aretes binaires independantes.

Les relations d'echelle disent :

```text
detail_of
summary_of
instance_of
part_of
view_of
```

sans les confondre.


# 37. Coordonnees de branche

Le MBC ajoute une dimension de branche :

```text
x = (concept_coords, branch_id)
```

Deux branches peuvent occuper le meme concept mais porter des interpretations differentes.

Le HUD peut afficher :
- espace commun ;
- divergence de branches ;
- zones de reconvergence ;
- conflits.

Un changement de branche n'est pas un changement de noeud.


# 38. Coordonnees temporelles

TimeWarp ajoute :

```text
t_event
t_observation
t_revision
t_simulation
```

On evite un seul temps ambigu.

Adresse temporelle :

```text
node:X @ revision:17 @ observed_at:t1
```

Le systeme peut naviguer :

```text
TIME_BACK
TIME_FORWARD
FORK_TIME
COMPARE_TIME
```

sans reecrire l'historique.


# 39. Distances temporelles non lineaires

Le cout temporel peut etre non lineaire.

Exemple :
- deux evenements separes d'une heure mais causalement proches ;
- deux revisions separees de dix secondes mais conceptuellement tres differentes.

On peut definir :

```text
d_time =
alpha * chronological_distance
+ beta * revision_distance
+ gamma * causal_distance
```

selon le mode de navigation.


# 40. Navigation par objectif

Au lieu de donner une direction brute, l'utilisateur peut donner un objectif :

```text
NAVIGATE TO "cause la plus probable"
NAVIGATE TO "concept commun entre A et B"
NAVIGATE TO "niveau d'abstraction superieur"
NAVIGATE TO "branche la plus discriminante"
```

Le planificateur cherche un chemin :

```text
P* = argmin_P Cost(P)
```

sous contraintes :
- permissions ;
- budget ;
- risque ;
- perte informationnelle ;
- nombre maximal d'etapes.


# 41. Cout de chemin

Pour un chemin :

```text
P = (o1, o2, ..., on)
```

cout :

```text
C(P) =
sum(
  compute_cost
+ cognitive_cost
+ risk
+ information_loss
+ uncertainty_penalty
)
```

On peut au contraire maximiser :

```text
Utility(P) - Cost(P)
```

Le moteur ne cherche donc pas necessairement le chemin le plus court, mais le chemin le plus utile sous contraintes.


# 42. Routes explicables

Toute route proposee doit etre explicable.

Exemple :

```text
A -> zoom out -> concept parent
  -> shift causal -> hypothese H2
  -> zoom in -> evidence E7
```

Le systeme fournit :
- raison de chaque transition ;
- cout ;
- perte ;
- alternative rejetee ;
- gate OCCP.

Ainsi, la navigation reste auditable.


# 43. Recherche de concept commun

Pour deux noeuds `A` et `B`, on cherche un sur-espace commun :

```text
Common(A,B)
```

mais il peut y en avoir plusieurs.

On classe :

```text
score(S) =
relevance
* coverage
* interpretability
/ (1 + abstraction_loss)
```

Le meilleur concept commun n'est pas toujours le parent le plus proche.

On peut retourner un ensemble de candidats Pareto.


# 44. Plus proche voisin contextuel

Requete :

```text
NEAREST(node, context=q, k=5)
```

Le moteur :
1. choisit une frame ;
2. active les dimensions ;
3. calcule les distances ;
4. applique filtres et permissions ;
5. diversifie les resultats.

Le resultat inclut la raison :

```text
"proche surtout par causalite et temporalite,
moins proche semantiquement"
```


# 45. Navigation vers plus d'abstraction

Operateur :

```text
ABSTRACT_UP(N, criteria)
```

Il recherche :
- categories ;
- fonctions communes ;
- mecanismes communs ;
- objectifs communs ;
- structures analogues.

Le moteur peut produire plusieurs abstractions concurrentes.

Exemple :

```text
"port TCP 8080"
-> ressource reseau
-> interface de communication
-> dependance d'execution
```

Ces chemins correspondent a des axes d'abstraction differents.


# 46. Navigation vers plus de concret

Operateur :

```text
CONCRETIZE_DOWN(N, criteria)
```

Il genere :
- instances ;
- composants ;
- mesures ;
- exemples ;
- tests ;
- implementation.

Le passage vers le concret doit respecter le statut epistemique.

Une idee generale peut conduire a un prototype, mais le prototype n'est pas une validation du principe general.


# 47. Dimensions infinies potentielles

`infinity-D` signifie ici :

```text
le systeme peut ajouter de nouveaux axes sans borne theorique fixe
```

et non :

```text
le systeme stocke un vecteur de taille infinie
```

On represente donc :

```text
D_possible = generator(context)
```

et :

```text
D_active finite subset D_possible
```

A chaque instant, le nombre de dimensions actives reste fini.


# 48. Generation de nouvelles dimensions

Une nouvelle dimension peut etre proposee si les dimensions actuelles expliquent mal les differences.

```text
SuggestDimension(branches, residual_conflict)
```

Critere :

```text
gain_dimension =
reduction_of_ambiguity
* interpretability
/ (1 + cost)
```

Exemple :
deux branches semblent contradictoires jusqu'a l'ajout de la dimension `perspective`.

La dimension est testee avant d'etre integree durablement.


# 49. OCCP et vitesse de zoom

OCCP limite la vitesse d'ouverture :

```text
|d lambda / dt| <= v_scale_max
```

et le rythme d'ajout de dimensions :

```text
|d |D_active| / dt| <= v_dim_max
```

Si l'espace devient trop complexe :
- stop expansion ;
- stabilisation ;
- resume ;
- reduction du voisinage ;
- retour a une ancre.

Le zoom n'est donc pas seulement geometrique : il est regule par la capacite du systeme.


# 50. Mesure de surcharge

Indice de surcharge :

```text
L_t =
a*active_nodes
+b*active_edges
+c*active_dimensions
+d*branch_count
+e*unresolved_conflicts
+f*memory_pressure
```

Si :

```text
L_t > L_max
```

alors :

```text
STABILIZE
COMPRESS
PRUNE_VIEW
```

La surcharge de la vue ne doit pas entrainer la suppression des donnees persistantes.


# 51. Stabilisation multi-echelles

Routine :

```text
STABILIZE_SCALE
```

1. verifier les ancres ;
2. resumer le niveau courant ;
3. detecter les doublons ;
4. recalculer les voisinages ;
5. archiver les regions froides ;
6. consolider les transformations de frame ;
7. produire un checkpoint TimeWarp ;
8. recalculer les budgets.

Le resultat est un etat stable navigable.


# 52. Integration NCU

Le NCU fournit les objets de base.

Chaque NCU expose :

```text
expandability
parents
children_refs
frames
scale_relations
coordinates
```

La Structure 04 n'impose pas le contenu interne du noeud ; elle definit **comment le placer, le relier, l'ouvrir et le voir a plusieurs echelles**.

Separation :

```text
NCU = identite et contenu
Structure 04 = geometrie/navigation conceptuelle
```


# 53. Integration MBC

Le MBC fournit des branches concurrentes.

Structure 04 ajoute :
- position de branche ;
- distances entre branches ;
- regions de divergence ;
- regions de reconvergence ;
- cartes de conflits.

On peut demander :

```text
SHOW SPACE FOR branches {b1,b2,b3}
```

et obtenir une carte locale commune.


# 54. Integration de l'algebre COSMOS-F

Mapping :

```text
DOWN        -> ZOOM_IN
UP          -> ZOOM_OUT
SHIFT       -> PAN
PERSPECT    -> ROTATE_FRAME
PROJECT     -> PROJECT_DIMENSIONS
EXPAND      -> MATERIALIZE
COMPRESS    -> COMPRESS_SCALE
ANCHOR      -> CREATE_ANCHOR
RETURN      -> RESTORE_ANCHOR
TIME        -> MOVE_TIME_FRAME
COMPARE     -> COMPARE_REGIONS
```

La Structure 04 donne une semantique spatiale/multi-echelle a ces operateurs.


# 55. Integration TimeWarp

Chaque navigation peut produire un evenement :

```text
NavigationEvent = (
  from_state,
  operator,
  to_state,
  timestamp,
  cost,
  reason
)
```

TimeWarp peut rejouer :

```text
REPLAY_NAV(session_id)
```

ou reconstruire :

```text
STATE_AT(t)
```

Cela permet d'etudier non seulement les concepts, mais **le chemin de raisonnement/navigation**.


# 56. Architecture logicielle

Architecture :

```text
FractalNavigator
├── FrameManager
├── ScaleManager
├── DimensionManager
├── NeighborhoodEngine
├── MetricEngine
├── ProjectionEngine
├── AtlasManager
├── MaterializationManager
├── CacheManager
├── AnchorManager
├── RoutePlanner
├── CompressionEngine
├── OCCPAdapter
├── TimeWarpAdapter
├── NCUAdapter
└── MBCAdapter
```

Chaque module reste separable et testable.


# 57. Modele JSON d'une vue

```json
{
  "view_id": "view_001",
  "focus": "node:abc",
  "scale": -2,
  "frame": "frame:debug",
  "dimensions": [
    "causality",
    "time",
    "confidence"
  ],
  "neighbors": ["node:def", "node:ghi"],
  "branch": "br_12",
  "anchor_stack": ["a1", "a2"],
  "budget": {
    "max_nodes": 80,
    "max_depth": 3
  }
}
```

Cette vue peut etre sauvegardee puis restauree.


# 58. Schema SQLite

Tables possibles :

```sql
frames(
  frame_id TEXT PRIMARY KEY,
  origin_node TEXT,
  scale REAL,
  scope_json TEXT,
  axes_json TEXT
);

coordinates(
  node_id TEXT,
  frame_id TEXT,
  dimension TEXT,
  value_json TEXT,
  confidence REAL
);

scale_relations(
  source_id TEXT,
  target_id TEXT,
  relation_type TEXT,
  scale_delta REAL
);

anchors(
  anchor_id TEXT PRIMARY KEY,
  state_json TEXT,
  created_at TEXT
);

navigation_events(
  event_id TEXT PRIMARY KEY,
  from_state TEXT,
  operator TEXT,
  to_state TEXT,
  reason TEXT,
  timestamp TEXT
);

atlas_charts(
  chart_id TEXT PRIMARY KEY,
  frame_id TEXT,
  region_json TEXT,
  summary_json TEXT
);
```


# 59. API proposee

```text
POST /navigation/zoom-in
POST /navigation/zoom-out
POST /navigation/pan
POST /navigation/rotate-frame
POST /navigation/project
POST /navigation/anchor
POST /navigation/return
POST /navigation/route
GET  /nodes/{id}/neighbors
GET  /nodes/{id}/frames
GET  /spaces/{id}/atlas
POST /spaces/{id}/materialize
POST /spaces/{id}/compress
GET  /views/{id}
POST /views/{id}/restore
```

Toute reponse doit inclure :
- nouvel etat ;
- cout ;
- pertes ;
- warnings ;
- gate result.


# 60. DSL de navigation

Exemples :

```text
FOCUS node:abc
ZOOM IN 2
PAN BY causal LIMIT 5
FRAME perspective:user
DIM ON confidence
DIM OFF novelty
PROJECT KEEP causal,time,confidence
ANCHOR "before-branch-comparison"
ROUTE TO concept:"common cause"
RETURN "before-branch-comparison"
```

Le DSL compile vers les operateurs atomiques de la Structure 02.


# 61. Route planner

Le planificateur construit un graphe d'etats de navigation.

Etat :

```text
s = (
 focus,
 scale,
 frame,
 dimensions,
 branch,
 time
)
```

Transition :

```text
s --op--> s'
```

Recherche :
- Dijkstra pour cout positif ;
- A* avec heuristique ;
- multi-objectifs Pareto ;
- recherche bornee par budget.

Le moteur peut proposer plusieurs routes plutot qu'une seule.


# 62. Heuristiques

Heuristiques possibles :

```text
h_semantic
h_scale
h_scope
h_branch
h_information_gain
```

Une heuristique ne doit jamais etre confondue avec une preuve.

Elle sert uniquement a ordonner l'exploration.

Le journal distingue :

```text
"selected because heuristic predicted low cost"
```

de :

```text
"validated by evidence"
```


# 63. Tests unitaires

Tests :

```text
test_zoom_in_creates_return_path
test_zoom_out_preserves_identity
test_frame_change_does_not_mutate_source
test_projection_reports_loss
test_dimension_off_preserves_data
test_anchor_restore_exact_state
test_multi_parent_return
test_lazy_expansion
test_materialization_budget
test_cache_eviction_preserves_anchors
test_compression_preserves_provenance
test_reconstruction_marked
```


# 64. Tests de proprietes

Proprietes :

```text
P1. RETURN(ANCHOR(s)) == s
    si aucune migration incompatible n'a eu lieu.

P2. ZOOM_OUT(ZOOM_IN(N)) preserve l'identite de N
    mais pas necessairement la vue exacte.

P3. PROJECT puis recharger les dimensions sources
    ne doit pas avoir detruit les donnees.

P4. ActiveDimensions est toujours fini.

P5. MaterializedNodes <= Budget.max_nodes.

P6. Toute vue possede au moins un chemin de sortie,
    sauf espace explicitement terminal.
```


# 65. Tests de charge

Scenarios :

```text
100 000 noeuds persistants
1 000 000 relations persistantes
50 noeuds actifs
8 dimensions actives
5 echelles cachees
100 ancres
10 000 evenements TimeWarp
```

Mesures :
- latence voisinage ;
- cout de zoom ;
- temps de restauration ;
- memoire du cache ;
- taux d'eviction ;
- cout de compression ;
- temps de reconstruction d'une route.


# 66. Observabilite

Metriques :

```text
active_nodes
active_edges
active_dimensions
current_scale
zoom_rate
materialization_rate
cache_hit_rate
anchor_count
projection_loss_mean
route_cost_mean
frame_transform_failures
stabilization_count
```

Le HUD peut afficher :
- budget ;
- echelle ;
- dimensions ;
- frame ;
- profondeur ;
- charge ;
- chemin de retour.


# 67. HUD multi-echelles

Concept d'interface :

```text
[Sur-espace]
     |
  [Focus] ---- voisins
    /  \
 detail detail
```

Commandes directes :
- molette : zoom conceptuel ;
- drag : pan ;
- touche : changer de frame ;
- filtres : dimensions ;
- clic ancre : retour ;
- timeline : TimeWarp.

Le HUD affiche toujours :
- echelle courante ;
- frame ;
- branche ;
- statut epistemique ;
- budget restant.


# 68. Mini-map conceptuelle

Une mini-map ne tente pas d'afficher tout l'univers.

Elle montre :
- la region courante ;
- les ancres ;
- les sur-espaces ouverts ;
- quelques corridors vers des regions importantes.

Representation :

```text
[home] -- [research] -- [focus]
                  \
                   [alt branch]
```

Elle est reconstruite depuis l'historique et les charts actifs.


# 69. Exemple A - diagnostic informatique

Point de depart :

```text
service ne demarre pas
```

Echelle 0 :
- symptome ;
- logs ;
- processus.

Zoom -1 :
- port ;
- dependance ;
- fichier config ;
- permission.

Zoom -2 sur dependance :
- package ;
- version ;
- chemin ;
- variable environnement.

Zoom out +1 :
- "chaine de dependances d'execution".

Pan causal :
- causes voisines.

La navigation peut rester localement detaillee sans charger tout le graphe informatique.


# 70. Exemple B - recherche conceptuelle

Concept :

```text
"attention"
```

Frames possibles :
- cognitive ;
- informatique ;
- sociale ;
- philosophique.

Le meme noeud peut etre projete dans ces quatre frames.

L'utilisateur peut :

```text
FRAME cognitive
ZOOM IN
```

puis :

```text
ROTATE_FRAME philosophical
```

sans pretendre que les dimensions ont le meme sens entre les disciplines.

Les transformations de frame portent donc une confiance et des pertes.


# 71. Exemple C - recherche du concept commun

Noeuds :

```text
A = "memoire humaine"
B = "cache informatique"
```

Le moteur peut trouver plusieurs abstractions :

```text
S1 = stockage temporaire
S2 = mecanisme de reutilisation
S3 = systeme de reduction de cout
S4 = conservation d'etat
```

Chaque sur-espace est un candidat.

Le moteur les classe selon la question plutot que d'imposer une analogie unique.


# 72. Exemple D - navigation multi-branche

Branches :

```text
b1 = cause reseau
b2 = cause application
b3 = cause permission
```

Le HUD peut partager les noeuds communs et separer les regions specifiques.

`COMPARE_REGIONS(b1,b2,b3)` produit :
- intersection ;
- differences ;
- preuves communes ;
- tests discriminants.

Une branche peut zoomer plus profondement que les autres sans perdre l'alignement.


# 73. Exemple E - navigation temporelle

Un noeud change de representation au cours du temps.

```text
t0 -> hypothese
t1 -> simulation
t2 -> observation
t3 -> revision
```

L'utilisateur peut garder le meme focus et bouger uniquement sur l'axe TimeWarp :

```text
TIME_BACK t1
TIME_FORWARD t3
```

Le systeme compare les versions plutot que de remplacer silencieusement le passe.


# 74. Securite conceptuelle

Risques controles :

### Explosion combinatoire
Budget + lazy loading + compression.

### Faux espace global
Les frames sont explicites ; aucune carte locale n'est presentee comme representation complete de tout `Omega`.

### Confusion entre dimensions descriptives et physiques
Chaque dimension porte un type et un domaine.

### Perte lors des projections
Rapport de perte obligatoire.

### Analogie excessive
Une transformation entre domaines possede confiance et provenance.

### Navigation sans retour
Toute expansion cree une ancre ou un parent de sortie.


# 75. Contrat de dimension

Chaque dimension est un objet :

```text
Dimension = (
  id,
  name,
  type,
  domain,
  range,
  semantics,
  normalization,
  uncertainty,
  provenance
)
```

Exemple :

```json
{
  "id": "dim:confidence",
  "type": "scalar",
  "range": [0,1],
  "semantics": "confidence assigned to current representation"
}
```

Deux dimensions portant le meme nom ne sont pas considerees identiques sans contrat compatible.


# 76. Contrat de frame

Une frame doit fournir :

```text
frame_id
origin
axes
scale
scope
metric
transformations_out
transformations_in
confidence
provenance
```

Validation :

```text
ValidateFrame(F)
```

refuse :
- axes non definis ;
- metrique incompatible ;
- transformation pretendue exacte mais perdant de l'information ;
- scope absent lorsque necessaire.


# 77. Contrat d'ancre

Une ancre valide doit contenir suffisamment d'information pour restaurer :

```text
focus
frame
scale
dimensions
branch
time
filters
```

Deux types :

```text
SOFT_ANCHOR
HARD_ANCHOR
```

`SOFT_ANCHOR` restaure une vue equivalente.
`HARD_ANCHOR` tente de restaurer exactement la revision historique via TimeWarp.


# 78. Formule maitresse

Etat de navigation :

```text
S_t = (
  N_t,
  F_t,
  lambda_t,
  D_t,
  B_t,
  T_t,
  A_t
)
```

Transition :

```text
S_{t+1} =
Stabilize(
  Gate_OCCP(
    O_t(S_t, query_t)
  )
)
```

ou `O_t` est un operateur de navigation.

Fenetre materialisee :

```text
W_t =
Materialize(
  neighborhood(N_t),
  scale=lambda_t,
  dimensions=D_t,
  budget=B_t
)
```

Contrainte :

```text
|W_t| <= Budget_t
```

et :

```text
Potential(Omega) peut etre non borne
tandis que
Materialized(W_t) reste fini.
```


# 79. MVP experimental

### M0 - donnees
- frames ;
- dimensions ;
- coordonnees ;
- scale relations ;
- anchors.

### M1 - navigation
- focus ;
- zoom in/out ;
- pan ;
- back/forward.

### M2 - multi-dimensions
- DIM ON/OFF ;
- projection ;
- distance contextuelle.

### M3 - atlas/cache
- chart local ;
- lazy materialization ;
- cache L0/L1/L2.

### M4 - TimeWarp/OCCP
- checkpoints ;
- gates ;
- budgets ;
- stabilisation.

### M5 - Forge/HUD
- route planning ;
- vue graphe ;
- mini-map ;
- commandes naturelles.


# 80. Criteres d'acceptation

La Structure 04 est consideree implementable lorsque :

```text
A1. Un noeud peut etre ouvert comme sous-espace.
A2. Un zoom cree toujours une route de retour.
A3. Plusieurs parents sont supportes.
A4. Les dimensions actives peuvent changer dynamiquement.
A5. Une projection annonce ses pertes.
A6. Le moteur ne materialise qu'une fenetre bornee.
A7. Le cache peut evincer sans perdre les ancres.
A8. TimeWarp restaure une vue historique.
A9. Les frames peuvent etre transformees avec confiance explicite.
A10. La distance depend du contexte.
A11. Le HUD affiche echelle, frame et dimensions.
A12. Une adresse conceptuelle permet de restaurer une vue.
A13. La compression garde la provenance.
A14. Une reconstruction approximative est marquee.
A15. Le nombre de dimensions actives reste fini.
```


# 81. Suite logique vers Structure 05

La prochaine structure naturelle est le **budget global de complexite et regulateur dynamique multi-ressources**.

Elle devra unifier :
- budget humain ;
- budget machine ;
- budget collectif ;
- RAM/VRAM ;
- tokens ;
- temps ;
- profondeur ;
- branches ;
- dimensions ;
- risque ;
- charge cognitive ;
- vitesse d'ouverture ;
- politiques de stabilisation ;
- priorites ;
- allocation adaptative ;
- degradation gracieuse.

Autrement dit, la Structure 04 dit **comment naviguer dans un espace potentiellement infini** ; la Structure 05 dira **combien on peut ouvrir a chaque instant et comment rester stable pendant cette navigation**.
