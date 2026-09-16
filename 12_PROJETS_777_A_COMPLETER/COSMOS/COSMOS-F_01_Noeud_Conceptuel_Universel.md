# COSMOS-F 01 - Noeud Conceptuel Universel (NCU)

**Version :** 0.1 - structure de reference  
**Objet :** definir une unite universelle, extensible et serialisable pour cartographier un espace conceptuel fractal, multi-perspectif et temporel.  
**Position dans l'architecture :** Structure 1 de COSMOS-F / OCCP.  

---

## 0. Resume executif

Le **Noeud Conceptuel Universel (NCU)** est l'unite minimale de representation de COSMOS-F. Il ne represente pas seulement un "concept" au sens lexical : il represente un **etat conceptuel contextualise**, c'est-a-dire un contenu interpretable accompagne de son echelle, de sa perspective, de sa temporalite, de ses relations, de son statut epistemique, de ses preuves, de ses permissions, de son cout d'exploration et de sa trace historique.

Le but est d'obtenir un objet suffisamment general pour representer :

- un mot ou une idee simple ;
- une question ;
- une hypothese ;
- une observation ;
- une theorie ;
- une action possible ;
- une experience ;
- un souvenir ou une trace ;
- un sous-espace conceptuel ;
- un sur-espace ;
- une perspective d'un acteur ;
- une bifurcation temporelle ;
- un modele entier, encapsule comme sous-graphe.

Le NCU doit permettre une croissance potentiellement indefinie du graphe **sans exiger de materialiser l'infini**. Il ne charge et n'active que les dimensions utiles au probleme courant.

Principe central :

> **Un noeud peut contenir une possibilite sans l'affirmer comme vraie, et peut decrire une action sans l'autoriser.**

Cette separation est fondamentale pour que l'ouverture de complexite reste controlee.

---

# 1. Definition canonique

## 1.1 Forme minimale

La forme conceptuelle initiale est :

```math
N=(C,S,P,R,T,E)
```

avec :

- `C` : contenu conceptuel ;
- `S` : echelle ;
- `P` : perspective ;
- `R` : relations ;
- `T` : temporalite ;
- `E` : statut epistemique.

Cette forme est utile pour raisonner, mais insuffisante pour une implementation. La forme operationnelle devient :

```math
\boxed{
N_t = (
I,
C,
\Sigma,
\Pi,
\mathcal R,
\Theta,
\mathcal E,
\mathcal Q,
\mathcal A,
\mathcal G,
\mathcal B,
\mathcal H,
\mathcal M
)
}
```

ou :

- `I` : identite stable du noeud ;
- `C` : contenu ;
- `Sigma` : structure d'echelle/fractalisation ;
- `Pi` : ensemble des perspectives ;
- `R` : relations ;
- `Theta` : temporalite ;
- `E` : epistemologie ;
- `Q` : qualite, confiance et incertitude ;
- `A` : espace d'actions associees ;
- `G` : portes, permissions et contraintes ;
- `B` : budgets et couts ;
- `H` : historique / provenance ;
- `M` : metadonnees et extension.

Le noeud n'est donc pas un simple sommet de graphe. C'est un **micro-espace structure**.

---

# 2. Identite du noeud - `I`

## 2.1 Pourquoi une identite stable

Un concept peut changer de nom, de formulation ou de niveau de detail sans devenir necessairement un autre objet. Il faut donc separer :

- l'identite logique ;
- les labels humains ;
- les versions ;
- les alias.

Structure :

```yaml
identity:
  node_id: "ncu:uuid"
  canonical_key: "cosmosf.concept.example"
  version: 1
  lineage_id: "lineage:uuid"
  aliases: []
  namespace: "user|project|global|experiment"
  created_at: "timestamp"
  updated_at: "timestamp"
```

## 2.2 Invariants d'identite

1. `node_id` ne change jamais.
2. Une nouvelle interpretation substantiellement differente peut produire une nouvelle version.
3. Une divergence non reconciliee doit produire un **nouveau noeud**, pas ecraser l'ancien.
4. Deux noeuds peuvent etre marques equivalentes sans etre fusionnes.
5. La fusion est une operation explicite et journalisee.

## 2.3 Lineage

```math
N^{(0)} \rightarrow N^{(1)} \rightarrow N^{(2)} \rightarrow \cdots
```

Le `lineage_id` permet de suivre la famille conceptuelle tandis que chaque version garde son propre `node_id`.

---

# 3. Contenu conceptuel - `C`

## 3.1 Le contenu ne se reduit pas au texte

```math
C=(L,D,F,X,K)
```

avec :

- `L` : label court ;
- `D` : description ;
- `F` : forme/formalisation ;
- `X` : exemples / instances ;
- `K` : mots-cles / concepts associes.

Structure :

```yaml
content:
  label: "Noeud conceptuel universel"
  summary: "Unite de representation fractale de COSMOS-F"
  description: "..."
  formal_definition: "..."
  examples: []
  counterexamples: []
  keywords: []
  language: "fr"
  modality:
    - textual
    - mathematical
```

## 3.2 Type du contenu

Un noeud peut etre type sans etre enferme dans un type unique.

```yaml
content_types:
  - concept
  - question
  - hypothesis
  - observation
  - model
  - rule
  - action_candidate
  - event
  - trace
  - actor
  - resource
  - constraint
```

Le systeme accepte plusieurs types simultanement lorsqu'ils sont compatibles.

## 3.3 Contenu interne vs referent externe

Un noeud doit distinguer :

```math
\text{representation interne} \neq \text{objet du monde}
```

Par exemple, un noeud "Soleil" est une representation du Soleil, pas le Soleil lui-meme. Cette distinction est indispensable pour eviter de confondre modele et realite.

---

# 4. Echelle et fractalisation - `Sigma`

## 4.1 Principe

Un meme objet peut etre vu comme :

- detail d'un ensemble ;
- objet autonome ;
- ensemble contenant des sous-objets.

On definit :

```math
\Sigma(N)=(\sigma,Parents,Children,Scopes)
```

avec `sigma` un niveau relatif, et non une grandeur physique absolue.

## 4.2 Relations d'echelle

```yaml
scale:
  level: 0
  parents: []
  children: []
  scopes:
    - "local"
  granularity: 0.5
  abstraction: 0.5
```

- `level` : profondeur topologique indicative ;
- `granularity` : precision descriptive ;
- `abstraction` : niveau d'abstraction.

## 4.3 Fractalisation

Chaque noeud peut ouvrir un sous-graphe :

```math
N_i \supset \mathcal G_i = \{N_{i1},N_{i2},\ldots\}
```

et appartenir simultanement a plusieurs sur-espaces :

```math
N_i \in U_1 \cap U_2 \cap \cdots \cap U_n
```

Il n'y a donc pas d'arbre unique. La structure correcte est un **hypergraphe multi-parent**.

## 4.4 Invariant de fermeture

Tout sous-espace doit pouvoir etre compresse en une representation de niveau superieur :

```math
Compress(Expand(N)) \approx N
```

avec une erreur mesuree :

```math
d(N,Compress(Expand(N))) \leq \epsilon_{compression}
```

Cette condition rend possible le zoom fractal sans perte incontrôlee.

---

# 5. Perspectives - `Pi`

## 5.1 Une perspective est une projection, pas une nouvelle verite

On definit :

```math
\Pi_N=\{\pi_1,\pi_2,\ldots,\pi_k\}
```

Chaque perspective contient :

```yaml
perspective:
  perspective_id: "p:uuid"
  actor_id: "actor:uuid|null"
  role: "user|system|researcher|observer|model"
  frame: "technical|personal|symbolic|scientific|economic|..."
  claim_scope: "what this perspective asserts"
  confidence: 0.0
  access_level: "private|shared|public"
```

## 5.2 Coexistence de perspectives contradictoires

Deux perspectives peuvent etre stockees sans resolution immediate :

```math
\pi_A(N)=x,
\qquad
\pi_B(N)=\neg x
```

Le graphe ne doit pas automatiquement choisir un gagnant.

On ajoute :

```yaml
perspective_relation:
  type: "agree|disagree|partial_overlap|unknown"
  evidence: []
```

## 5.3 Perspective composite

Une synthese est un nouvel objet :

```math
\pi_{AB}=Synthesize(\pi_A,\pi_B)
```

Elle ne remplace pas ses sources.

---

# 6. Relations - `R`

## 6.1 Relation comme objet de premier rang

Une relation ne doit pas etre un simple couple `(source,target)`.

```math
r=(s,t,\rho,w,q,\theta,h)
```

avec :

- `s` : source ;
- `t` : cible ;
- `rho` : type de relation ;
- `w` : poids ;
- `q` : qualite/confiance ;
- `theta` : validite temporelle ;
- `h` : provenance.

Structure :

```yaml
relation:
  relation_id: "r:uuid"
  source: "ncu:..."
  target: "ncu:..."
  type: "contains|part_of|causes|supports|contradicts|resembles|depends_on|precedes|enables|blocks|derived_from|equivalent_to|associated_with"
  directed: true
  weight: 0.8
  confidence: 0.7
  conditions: []
  valid_from: null
  valid_until: null
  provenance: []
```

## 6.2 Relations n-aires

Certaines relations impliquent plus de deux objets :

```math
r(N_1,N_2,N_3,\ldots,N_k)
```

On les represente comme **hyper-arcs** ou comme noeuds-relation.

Exemple : "A donne X a B a l'instant t" ne se reduit pas proprement a trois aretes independantes.

## 6.3 Relation conditionnelle

```math
r_{AB} \text{ active} \iff C_t \models \Gamma_r
```

Une relation peut donc exister dans la carte tout en etant inactive dans le contexte courant.

---

# 7. Temporalite - `Theta`

## 7.1 Plusieurs temps

Un noeud peut avoir :

- temps de creation ;
- temps de validite ;
- temps de l'evenement represente ;
- ordre narratif ;
- temps de simulation ;
- horizon futur.

Structure :

```yaml
time:
  created_at: "..."
  observed_at: null
  event_time:
    start: null
    end: null
  valid_time:
    from: null
    until: null
  sequence_index: null
  branch_id: "timeline:main"
  temporal_status: "past|present|future|timeless|simulated|unknown"
```

## 7.2 Branches temporelles

```math
T_0 \rightarrow
\begin{cases}
T_1^{(a)} \\
T_1^{(b)} \\
T_1^{(c)}
\end{cases}
```

Une prediction ou simulation ne doit pas modifier la branche observee.

## 7.3 TimeWarp

Toute transition majeure doit pouvoir etre journalisee :

```math
L_k=(N_{depart},N_{arrivee},Operation,Contexte,Preuves,Delta)
```

Cela rend possibles : retour, comparaison, replay et bifurcation.

---

# 8. Epistemologie - `E`

## 8.1 Statut obligatoire

Chaque assertion significative doit porter un statut.

Proposition de niveaux :

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

## 8.2 Progression

```math
Imagined
\rightarrow Formalized
\rightarrow Simulated
\rightarrow Testable
\rightarrow Observed
\rightarrow Corroborated
```

Cette progression n'est ni automatique ni irreversible.

## 8.3 Separation essentielle

```math
\boxed{
Possible \neq True \neq PermittedAction
}
```

Un noeud peut etre `IMAGINED` et pourtant utile. Il doit simplement rester etiquete correctement.

## 8.4 Assertions atomiques

Un noeud complexe peut contenir plusieurs assertions ayant des statuts differents :

```yaml
claims:
  - claim_id: "c1"
    statement: "..."
    epistemic_status: "FORMALIZED"
    confidence: 0.6
    evidence: []
  - claim_id: "c2"
    statement: "..."
    epistemic_status: "OBSERVED"
    confidence: 0.9
    evidence: []
```

On evite ainsi de donner un statut unique grossier a tout un document.

---

# 9. Qualite, confiance et incertitude - `Q`

## 9.1 Vecteur de qualite

```math
\mathcal Q=(c,u,k,s,d)
```

avec :

- `c` : confiance ;
- `u` : incertitude ;
- `k` : coherence interne ;
- `s` : support/evidence ;
- `d` : stabilite dans le temps.

## 9.2 Ne pas forcer `confidence + uncertainty = 1`

L'incertitude n'est pas seulement l'inverse de la confiance. Une information peut etre :

- peu fiable ;
- contradictoire ;
- incomplete ;
- non mesurable ;
- simplement inconnue.

Structure :

```yaml
quality:
  confidence: 0.72
  uncertainty: 0.30
  coherence: 0.85
  evidence_strength: 0.55
  temporal_stability: 0.65
  unknown_fields: []
  contradictions: []
```

## 9.3 Valeur inconnue explicite

Le systeme doit distinguer :

```text
false
true
unknown
not_applicable
conflicting
```

et ne jamais transformer `unknown` en `false` par defaut.

---

# 10. Espace d'actions - `A`

## 10.1 Une action associee n'est pas une action autorisee

```math
\mathcal A_N = \{a_1,a_2,\ldots,a_m\}
```

Chaque action candidate porte :

```yaml
action:
  action_id: "a:uuid"
  type: "inspect|expand|compress|link|simulate|execute|ask|export|..."
  description: "..."
  preconditions: []
  expected_effects: []
  side_effects: []
  reversibility: "full|partial|none|unknown"
  risk: 0.0
  estimated_cost: {}
  permission_required: []
  executable: false
```

## 10.2 Action purement conceptuelle vs action externe

On distingue :

```text
NAVIGATION_ONLY
SIMULATION
LOCAL_MUTATION
EXTERNAL_SIDE_EFFECT
HUMAN_DECISION_REQUIRED
```

Une navigation conceptuelle peut etre automatique alors qu'un effet externe exige une porte supplementaire.

---

# 11. Gates, permissions et contraintes - `G`

## 11.1 Porte generale

```math
G_N(a,C_t) \in \{0,1,\bot\}
```

avec :

- `0` : refuse ;
- `1` : autorise ;
- `bot` : information insuffisante.

## 11.2 Porte composee OCCP

```math
G =
G_{coherence}
\land G_{permission}
\land G_{capacity}
\land G_{reversibility}
\land G_{safety}
```

Structure :

```yaml
gates:
  coherence:
    required: true
    status: "pass|fail|unknown"
  permission:
    required: true
    status: "pass|fail|unknown"
  capacity:
    required: true
    status: "pass|fail|unknown"
  reversibility:
    required: false
    status: "pass|fail|unknown"
  safety:
    required: true
    status: "pass|fail|unknown"
```

## 11.3 Permission par acteur et portee

```yaml
permissions:
  owner: "actor:user"
  read: ["actor:user", "agent:aione"]
  mutate: ["actor:user"]
  expand: ["agent:aione"]
  execute_external: []
  share: []
```

Une permission doit avoir une portee, un auteur et eventuellement une expiration.

---

# 12. Budgets et couts - `B`

## 12.1 Budget multi-ressources

```math
B_N=(b_{token},b_{time},b_{ram},b_{vram},b_{depth},b_{branches},b_{human})
```

Structure :

```yaml
budget:
  tokens: 12000
  time_ms: 10000
  ram_mb: 1024
  vram_mb: 2048
  max_depth: 5
  max_branches: 8
  human_attention: 0.5
```

## 12.2 Cout d'ouverture

Pour une expansion `x` :

```math
Cost(x)=
\alpha C_{compute}
+\beta C_{memory}
+\gamma C_{attention}
+\delta C_{risk}
```

L'ouverture est admissible si :

```math
Cost(x) \leq Budget_{available}
```

## 12.3 Budget de complexite

```math
\sum_i c_i x_i \leq B_t
```

Si le budget est depasse, le moteur doit preferer :

1. compresser ;
2. mettre en veille ;
3. resumer ;
4. deferer ;
5. supprimer seulement si explicitement autorise.

---

# 13. Historique, provenance et preuve - `H`

## 13.1 Provenance obligatoire

Pour chaque information importante :

```yaml
provenance:
  - source_type: "user|file|sensor|web|model|derived"
    source_id: "..."
    timestamp: "..."
    extraction_method: "..."
    transformation: "none|summary|inference|calculation"
```

## 13.2 Difference entre source et preuve

Une source n'est pas automatiquement une preuve forte.

```math
Source \neq EvidenceStrength
```

La preuve est une evaluation contextualisee de la capacite d'un element a soutenir une assertion.

## 13.3 Journal de mutation

```yaml
history:
  - event_id: "evt:uuid"
    operation: "create|update|link|split|merge|expand|compress|rollback"
    actor: "..."
    timestamp: "..."
    reason: "..."
    before_hash: "..."
    after_hash: "..."
```

---

# 14. Metadonnees et extension - `M`

Le schema doit etre extensible sans casser le noyau.

```yaml
metadata:
  tags: []
  project: null
  domain: []
  visibility: "private"
  ui:
    icon: null
    preferred_view: "graph"
  extensions: {}
```

Regle : une extension peut ajouter de l'information, mais ne doit pas redefinir silencieusement la semantique d'un champ canonique.

---

# 15. Schema canonique complet

```yaml
ncu:
  identity:
    node_id: "ncu:uuid"
    canonical_key: "..."
    lineage_id: "lineage:uuid"
    version: 1
    aliases: []
    namespace: "user"
    created_at: "..."
    updated_at: "..."

  content:
    label: "..."
    summary: "..."
    description: "..."
    formal_definition: null
    content_types: ["concept"]
    examples: []
    counterexamples: []
    keywords: []
    language: "fr"
    modality: ["textual"]

  scale:
    level: 0
    parents: []
    children: []
    scopes: []
    granularity: 0.5
    abstraction: 0.5

  perspectives: []
  relations: []

  time:
    created_at: "..."
    observed_at: null
    event_time: {start: null, end: null}
    valid_time: {from: null, until: null}
    sequence_index: null
    branch_id: "timeline:main"
    temporal_status: "timeless"

  epistemology:
    status: "FORMALIZED"
    claims: []

  quality:
    confidence: 0.5
    uncertainty: 0.5
    coherence: 0.5
    evidence_strength: 0.0
    temporal_stability: 0.5
    contradictions: []
    unknown_fields: []

  actions: []

  gates:
    coherence: {required: true, status: "unknown"}
    permission: {required: true, status: "unknown"}
    capacity: {required: true, status: "unknown"}
    reversibility: {required: false, status: "unknown"}
    safety: {required: true, status: "unknown"}

  permissions:
    owner: "actor:user"
    read: ["actor:user"]
    mutate: ["actor:user"]
    expand: []
    execute_external: []
    share: []

  budget:
    tokens: null
    time_ms: null
    ram_mb: null
    vram_mb: null
    max_depth: null
    max_branches: null
    human_attention: null

  provenance: []
  history: []

  metadata:
    tags: []
    project: null
    domain: []
    visibility: "private"
    ui: {}
    extensions: {}
```

---

# 16. Etats de cycle de vie

Un noeud suit un cycle de vie explicite :

```text
DRAFT
ACTIVE
STABLE
FROZEN
DEPRECATED
ARCHIVED
```

Ces etats sont operationnels et distincts du statut epistemique.

Par exemple :

- un noeud `STABLE` peut contenir une hypothese `FORMALIZED` ;
- un noeud `DRAFT` peut contenir une observation fortement soutenue ;
- `ARCHIVED` signifie seulement qu'il n'est plus actif dans la navigation courante.

---

# 17. Operations fondamentales sur un NCU

## 17.1 CREATE

```math
Create(C,Context) \rightarrow N
```

Conditions : identite unique, contenu minimal, provenance minimale.

## 17.2 EXPAND

```math
Expand(N,direction,budget) \rightarrow \{N_1,\ldots,N_k\}
```

Expansion possible selon :

- sous-concepts ;
- sur-concepts ;
- perspectives ;
- temporalites ;
- hypotheses ;
- relations ;
- niveaux de preuve.

## 17.3 COMPRESS

```math
Compress(\mathcal G_N) \rightarrow N'
```

`N'` doit conserver les points structurants, la provenance et les divergences importantes.

## 17.4 SPLIT

```math
Split(N) \rightarrow (N_a,N_b,\ldots)
```

Utilise lorsqu'un noeud contient plusieurs concepts incompatibles ou devenus autonomes.

## 17.5 MERGE

```math
Merge(N_a,N_b) \rightarrow N_c
```

Le merge doit :

- conserver les deux sources ;
- creer une nouvelle version ou un nouveau noeud ;
- noter les conflits non resolus ;
- ne jamais supprimer silencieusement les donnees divergentes.

## 17.6 LINK

```math
Link(N_a,N_b,r) \rightarrow r_{ab}
```

## 17.7 ANCHOR

Un noeud ancre devient un repere stable de navigation.

```yaml
anchor:
  active: true
  reason: "reference stable"
  locked_fields: []
```

## 17.8 ROLLBACK

```math
Rollback(N,t_k) \rightarrow N^{(k)}
```

Le rollback est une nouvelle operation historique ; il ne supprime pas les versions ulterieures.

---

# 18. Invariants globaux

Le moteur doit verifier au minimum :

### I1 - Identite

```math
node\_id(N_i)=node\_id(N_j) \Rightarrow N_i,N_j \text{ sont versions du meme objet}
```

### I2 - Provenance

Toute assertion non triviale possede une provenance ou est explicitement marquee comme creation/inference du systeme.

### I3 - Epistemologie

Une hypothese n'est jamais promue en observation sans transition explicite.

### I4 - Permission

```math
CanRepresent(a) \not\Rightarrow CanExecute(a)
```

### I5 - Reversibilite

Toute mutation interne doit soit etre reversible, soit signaler explicitement son irreversibilite.

### I6 - Contradiction

Une contradiction detectee produit un lien de contradiction ; elle ne provoque pas l'effacement automatique d'une branche.

### I7 - Compression

La compression conserve les identifiants des sources significatives.

### I8 - Fractalisation bornee par budget

```math
Depth(N) \leq Budget.max\_depth
```

pour l'exploration active, meme si la profondeur theorique reste ouverte.

### I9 - Separation modele/referent

Une representation n'est jamais consideree comme identique a l'objet externe qu'elle decrit.

### I10 - Journalisation

Toute mutation structurelle produit un evenement d'historique.

---

# 19. Validation de schema

Avant insertion dans le graphe :

```text
VALID
VALID_WITH_WARNINGS
INVALID
```

Exemples d'erreurs invalidantes :

- identifiant manquant ;
- type inconnu dans un champ canonique ferme ;
- relation vers une cible inexistante sans mode `unresolved_reference` ;
- permission contradictoire non resolue ;
- timestamp invalide ;
- mutation sans historique lorsque l'historique est obligatoire.

Exemples de warnings :

- confiance non renseignee ;
- aucune preuve ;
- noeud orphelin ;
- profondeur elevee ;
- nombreuses contradictions ;
- cout estime superieur au budget courant.

---

# 20. Distance conceptuelle

Le NCU doit preparer la future navigation. On definit une distance composite :

```math
D(N_i,N_j)=
\sum_{k=1}^{m} \lambda_k d_k(N_i,N_j)
```

avec par exemple :

```math
D=
\lambda_C d_C
+\lambda_S d_S
+\lambda_P d_P
+\lambda_R d_R
+\lambda_T d_T
+\lambda_E d_E
```

Les poids `lambda` dependent de la question.

Ainsi, deux noeuds peuvent etre :

- proches semantiquement mais eloignes temporellement ;
- proches structurellement mais opposes epistemiquement ;
- proches du point de vue d'un acteur et eloignes du point de vue d'un autre.

Il n'existe donc pas une seule distance universelle : il existe une **metrique contextualisee**.

---

# 21. Coordonnees dynamiques

Au lieu d'imposer un espace fixe de dimension `n`, on utilise :

```math
X_N^{(C_t)}=(x_1,x_2,\ldots,x_{d_t})
```

ou `d_t` depend du contexte.

Le systeme peut activer une nouvelle dimension :

```math
X'=(x_1,\ldots,x_d,x_{d+1})
```

sans reencoder tout le graphe, a condition que cette dimension soit stockee comme feature ou projection supplementaire.

Cela realise la logique :

```math
D_{actives}=Select(D_{possibles},Question,Budget)
```

---

# 22. Exemple concret : "controle cognitif d'une interface"

```yaml
ncu:
  identity:
    node_id: "ncu:control-cognition-interface"
    canonical_key: "research.cognition.interface.control"
    lineage_id: "lineage:cci"
    version: 1

  content:
    label: "Controle cognitif d'une interface"
    summary: "Espace de recherche sur l'utilisation de signaux cognitifs mesurables pour piloter une interface."
    content_types: ["concept", "research_topic"]

  scale:
    parents:
      - "ncu:human-computer-interaction"
    children:
      - "ncu:eeg-input"
      - "ncu:eye-tracking"
      - "ncu:intent-inference"

  epistemology:
    status: "FORMALIZED"

  quality:
    confidence: 0.8
    uncertainty: 0.4
    evidence_strength: 0.5

  actions:
    - action_id: "a:simulate"
      type: "simulate"
      reversibility: "full"
      executable: true
    - action_id: "a:external-control"
      type: "execute"
      reversibility: "partial"
      executable: false
      permission_required: ["human_confirmation"]
```

Le noeud represente le domaine de recherche sans affirmer qu'une methode non mesuree fonctionne.

---

# 23. Exemple fractal : ouverture d'un noeud

Noeud initial :

```text
N0 = "memoire"
```

Expansion selon plusieurs directions :

```text
N0
|- sous-espace biologique
|  |- memoire de travail
|  |- memoire episodique
|  `- memoire procedurale
|- sous-espace informatique
|  |- RAM
|  |- stockage
|  `- cache
|- perspective utilisateur
|- perspective machine
`- axe temporel
   |- trace passee
   |- etat present
   `- anticipation
```

Chaque branche reste liee au meme noeud de depart, mais son statut et sa perspective sont explicites.

---

# 24. Stockage recommande pour prototype

## 24.1 SQLite + JSON

Pour un prototype local :

```text
nodes
relations
claims
perspectives
history
sources
actions
permissions
```

Le coeur structure peut etre en colonnes normales, avec `json` pour les extensions.

## 24.2 Tables minimales

### `nodes`

```sql
id TEXT PRIMARY KEY,
lineage_id TEXT,
version INTEGER,
label TEXT,
summary TEXT,
epistemic_status TEXT,
confidence REAL,
created_at TEXT,
updated_at TEXT,
payload_json TEXT
```

### `relations`

```sql
id TEXT PRIMARY KEY,
source_id TEXT,
target_id TEXT,
type TEXT,
weight REAL,
confidence REAL,
payload_json TEXT
```

### `history`

```sql
id TEXT PRIMARY KEY,
node_id TEXT,
operation TEXT,
actor_id TEXT,
timestamp TEXT,
before_hash TEXT,
after_hash TEXT,
payload_json TEXT
```

## 24.3 Pourquoi ne pas commencer directement par une base graphe

Une base graphe pourra devenir utile plus tard, mais SQLite permet d'abord :

- portabilite ;
- audit ;
- tests faciles ;
- zero service externe ;
- export simple ;
- prototypage rapide.

Le moteur peut exposer une API graphe independamment du stockage physique.

---

# 25. API minimale

```text
POST   /nodes
GET    /nodes/{id}
PATCH  /nodes/{id}
POST   /nodes/{id}/expand
POST   /nodes/{id}/compress
POST   /nodes/{id}/anchor
POST   /nodes/{id}/split
POST   /relations
GET    /nodes/{id}/neighbors
GET    /nodes/{id}/history
POST   /nodes/{id}/rollback
POST   /query/path
POST   /query/similarity
```

Chaque operation mutante doit retourner :

```yaml
result:
  status: "success|warning|rejected"
  node_id: "..."
  version: 2
  gates: {}
  cost: {}
  history_event: "evt:..."
```

---

# 26. Moteur de normalisation

Avant de creer un noeud, le moteur peut chercher :

1. equivalence exacte ;
2. alias ;
3. forte similarite ;
4. relation parent/enfant plausible ;
5. contradiction.

Mais il ne doit pas fusionner automatiquement sur simple similarite.

```math
Similarity(N_a,N_b)>\theta
\not\Rightarrow
Merge(N_a,N_b)
```

Il propose plutot :

```text
LINK_AS_SIMILAR
CREATE_ALIAS
CREATE_NEW_VERSION
KEEP_SEPARATE
REQUEST_REVIEW
```

---

# 27. Moteur d'expansion

Entree :

```yaml
expand_request:
  node_id: "..."
  directions:
    - child
    - parent
    - relation
    - perspective
    - temporal
    - epistemic
  max_branches: 6
  max_depth_delta: 1
  budget: {}
```

Algorithme abstrait :

```text
1. Charger N
2. Calculer dimensions d'ouverture disponibles
3. Appliquer gates
4. Estimer cout
5. Generer hypotheses de branches
6. Evaluer plausibilite / utilite / risque
7. Garder les branches admissibles
8. Creer ou lier les NCU correspondants
9. Journaliser
10. Retourner le delta de graphe
```

Formellement :

```math
Expand(N)=
Gate
\circ Rank
\circ Generate
\circ SelectDimensions(N)
```

---

# 28. Compression et resume multi-niveau

Une structure fractale devient inutilisable si tout reste ouvert. On definit trois niveaux de resume :

```text
L0 : label
L1 : resume court
L2 : resume structurel
L3 : contenu detaille
L4 : sous-graphe complet
```

Le HUD peut afficher L0-L2 ; le moteur charge L3-L4 a la demande.

Une compression doit conserver :

- decisions ;
- contradictions ;
- preuves fortes ;
- incertitudes majeures ;
- noeuds ancres ;
- liens de retour.

---

# 29. Securite conceptuelle

Le systeme doit proteger contre plusieurs classes d'erreurs :

## 29.1 Promotion silencieuse

`hypothese -> fait` sans preuve.

## 29.2 Fusion destructive

Deux concepts voisins deviennent artificiellement un seul concept.

## 29.3 Explosion de branches

Chaque noeud cree trop d'enfants.

## 29.4 Circularite non identifiee

```text
A prouve B
B prouve A
```

sans source externe.

## 29.5 Pollution de contexte

Une information valable dans un espace est propagee hors de sa portee.

## 29.6 Confusion permission/intention

Une intention ou suggestion est interpretee comme autorisation d'action.

## 29.7 Perte de provenance

Un resume devient impossible a retracer vers ses sources.

---

# 30. Tests unitaires conceptuels

Le prototype NCU doit au minimum tester :

1. creation valide ;
2. creation invalide sans ID ;
3. ajout d'un parent ;
4. multi-parent ;
5. ajout d'une perspective contradictoire ;
6. lien de contradiction sans suppression ;
7. expansion sous budget ;
8. rejet d'expansion hors budget ;
9. compression ;
10. round-trip expand/compress ;
11. nouvelle version ;
12. split ;
13. merge non destructif ;
14. rollback ;
15. provenance conservee ;
16. permission refusee ;
17. permission inconnue ;
18. action externe bloquee sans validation ;
19. branche future distincte du present ;
20. serialisation/deserialisation identique.

---

# 31. Proprietes recherche / formalisation avancee

## 31.1 Recursivite

Le NCU peut representer son propre schema :

```math
N_{schema} \in COSMOS-F
```

COSMOS-F devient partiellement reflexif.

## 31.2 Reflexivite bornee

Cette reflexivite doit etre bornee par profondeur :

```math
Reflect^k(N), \quad k \leq k_{max}
```

pour eviter une recursion infinie non productive.

## 31.3 Hypergraphe temporel perspective

Le modele global peut etre decrit :

```math
\mathcal G_t=(V,E,H,\Pi,\Theta)
```

avec :

- `V` : NCU ;
- `E` : relations binaires ;
- `H` : hyper-relations ;
- `Pi` : projections de perspective ;
- `Theta` : structure temporelle.

## 31.4 Graphe actif vs graphe total

```math
\mathcal G^{active}_t \subseteq \mathcal G^{stored}
```

Seul un sous-graphe est actif en memoire de travail. Le reste reste stocke et adressable.

C'est la cle operationnelle de l'"infini potentiel" :

```math
\text{espace extensible} \neq \text{espace entierement materialise}
```

---

# 32. Contrat avec OCCP

Le NCU fournit a OCCP :

- les dimensions disponibles ;
- les risques ;
- les permissions ;
- les couts ;
- la profondeur ;
- l'incertitude ;
- les actions possibles.

OCCP renvoie :

```yaml
regulation:
  allowed_dimensions: []
  max_branching: 0
  max_depth_delta: 0
  actions_allowed: []
  actions_blocked: []
  stabilization_required: false
```

La structure et la regulation restent separees : **NCU decrit**, **OCCP regule**.

---

# 33. Contrat avec TimeWarp

TimeWarp exploite :

- versions ;
- historiques ;
- deltas ;
- branches temporelles ;
- anchors.

Une transition :

```math
(N_t,Context_t)
\xrightarrow{op}
(N_{t+1},Context_{t+1})
```

produit un delta :

```math
\Delta_t=N_{t+1}-N_t
```

qui doit pouvoir etre rejoue ou inverse lorsque l'operation le permet.

---

# 34. Contrat avec Forge / AIONE

Forge peut utiliser le NCU comme unite de planification :

```text
Observation
-> creation/activation de NCU
-> expansion
-> evaluation des branches
-> selection OCCP
-> proposition d'action
-> execution eventuelle
-> trace
-> mise a jour du graphe
```

Un agent ne devrait donc pas manipuler un "prompt plat" seulement. Il manipule un **sous-graphe contextuel adresse**.

---

# 35. Contrat avec HUD

Representations possibles :

- vue graphe ;
- vue arbre locale ;
- vue chronologique ;
- vue perspectives ;
- vue epistemique ;
- vue cout/risque ;
- vue fractale par zoom.

Chaque noeud peut afficher un anneau d'etat :

```text
centre      : contenu
anneau 1    : epistemologie
anneau 2    : perspectives
anneau 3    : relations
anneau 4    : parents/enfants
anneau 5    : actions et gates
```

Le HUD n'est qu'une projection : la structure logique reste independante de l'interface.

---

# 36. Version minimale implementable (MVP)

Pour commencer sans sur-construire :

## MVP-0

Champs obligatoires :

```text
id
label
summary
parents
children
relations
epistemic_status
confidence
provenance
created_at
history
```

Operations :

```text
create
read
link
expand
anchor
history
```

## MVP-1

Ajouter :

- perspectives ;
- actions ;
- permissions ;
- budgets ;
- versioning ;
- rollback.

## MVP-2

Ajouter :

- hyper-relations ;
- metriques dynamiques ;
- branches temporelles ;
- compression multi-niveau ;
- projections HUD.

---

# 37. Definition finale compacte

```math
\boxed{
\begin{aligned}
NCU := (&Identity, Content, Scale, Perspectives, Relations, Time,\\
       &Epistemology, Quality, Actions, Gates, Budget, History, Metadata)
\end{aligned}
}
```

avec les invariants :

```math
\boxed{
\begin{aligned}
Possible &\neq True,\\
Representation &\neq Referent,\\
Relation &\neq Causation,\\
Suggestion &\neq Permission,\\
CanRepresent &\neq CanExecute,\\
StoredGraph &\neq ActiveGraph.
\end{aligned}
}
```

Et la propriete fractale :

```math
\boxed{
N_i \supset \mathcal G_i
\quad\land\quad
N_i\in\bigcap_j U_j
}
```

Le NCU devient ainsi une unite suffisamment petite pour etre manipulee localement, suffisamment riche pour conserver le contexte, et suffisamment extensible pour construire une cartographie conceptuelle ouverte a dimensions variables.

---

# 38. Prochaine structure logique

La suite naturelle est la **Structure 2 - Algebre des operateurs de navigation COSMOS-F** :

```text
DOWN      ouvrir un sous-espace
UP        remonter vers un sur-espace
SHIFT     changer de voisin conceptuel
PERSPECT  changer de perspective
TIME      changer de tranche/branche temporelle
LINK      creer une relation
ANCHOR    stabiliser un repere
RETURN    revenir avec trace
SPLIT     separer des interpretations
MERGE     produire une synthese non destructive
COMPRESS  refermer un sous-graphe
EXPAND    ouvrir un sous-graphe
```

Cette algebre pourra etre definie avec preconditions, effets, couts, inverse, gates et journalisation pour chaque operateur.

---

## Source de continuite

Cette structure etend directement les definitions precedentes de COSMOS-F : cellule `N=(C,S,P,R,T,E)`, sous-espaces et sur-espaces fractals, dimensions actives selectionnees selon question et budget, ainsi que les principes OCCP de permission, budget, reversibilite, stabilisation et separation entre possibilite, croyance et action.

