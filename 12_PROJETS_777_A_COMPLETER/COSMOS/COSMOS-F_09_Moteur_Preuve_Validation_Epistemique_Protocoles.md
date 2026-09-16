# COSMOS-F - Structure 09
## Moteur de preuve, validation epistemique et protocoles experimentaux

**Version :** 0.9  
**Statut :** specification conceptuelle, mathematique, epistemique et technique  
**Dependances :** Structure 01 - NCU ; Structure 02 - Algebre COSMOS-F ; Structure 03 - Moteur de branches concurrentes ; Structure 04 - Zoom fractal ; Structure 05 - Regulateur multi-ressources ; Structure 06 - Stabilisation/recovery ; Structure 08 - Orchestrateur multi-espace.  
**Interface attendue :** Structure 07 - TimeWarp complet pour la provenance temporelle et le replay.

---

## Resume

La Structure 09 definit le systeme qui permet de distinguer explicitement :

```text
idee
hypothese
modele
prediction
simulation
observation
mesure
corroboration
refutation
incertitude
```

Le moteur ne cherche pas a transformer chaque idee en "preuve". Il organise le passage d'une possibilite vers un statut epistemique plus fort **uniquement lorsque les conditions de test, de provenance et de repetition le justifient**.

Le moteur doit pouvoir :

```text
hypothese
-> formalisation
-> predictions discriminantes
-> protocole
-> preregistration
-> collecte
-> controle qualite
-> analyse
-> comparaison aux predictions
-> replication
-> synthese
-> statut epistemique
```

Il doit egalement conserver les resultats negatifs, les inconclusifs, les contre-exemples et les hypotheses alternatives.

Principe central :

```text
explorer une possibilite
!=
la croire vraie
!=
la considerer demontree
```

Deuxieme principe :

```text
la force d'une affirmation
ne doit jamais depasser
la force des preuves qui la soutiennent.
```

# 1. Objet du moteur epistemique

Le moteur epistemique, abrege **MEVEP** - Moteur d'Evaluation, Validation Epistemique et Protocoles - transforme des objets conceptuels en objets testables.

Il prend en entree :

```text
Claim
Hypothesis
Evidence
Protocol
Observation
Simulation
Source
```

et produit :

```text
EpistemicAssessment
```

avec :
- statut ;
- niveau de support ;
- incertitude ;
- dependances ;
- limites ;
- tests restants.


# 2. Separation des niveaux

Le systeme distingue strictement :

```text
CONCEPT
CLAIM
HYPOTHESIS
MODEL
PREDICTION
PROTOCOL
OBSERVATION
INFERENCE
DECISION
ACTION
```

Une erreur de type est un evenement epistemique important.

Exemple :

```text
simulation_result
```

ne peut pas etre caste silencieusement vers :

```text
observation
```


# 3. Claim canonique

Une affirmation est :

```text
Claim = (
  claim_id,
  proposition,
  scope,
  variables,
  qualifiers,
  epistemic_status,
  provenance
)
```

Exemple :

```json
{
  "claim_id": "cl_001",
  "proposition": "X augmente lorsque Y augmente dans le contexte C",
  "scope": ["population:P", "conditions:C"],
  "qualifiers": ["provisional"]
}
```

Les qualificatifs tels que "toujours", "souvent", "dans cet echantillon" font partie du claim.


# 4. Hypothese

Une hypothese est un claim qui genere au moins une consequence testable.

```text
H -> {P1, P2, ..., Pn}
```

Si aucune prediction discriminante ne peut etre derivee, l'hypothese peut rester :
- conceptuelle ;
- speculative ;
- non testable actuellement.

Le moteur ne la supprime pas, mais la classe correctement.


# 5. Hypothese nulle

Pour tout test pertinent, une hypothese nulle ou alternative de reference doit etre definie lorsque possible.

```text
H0
H1
H2
...
```

La presence de `H0` evite de traiter tout signal comme confirmation de l'hypothese favorite.


# 6. Hypotheses concurrentes

Le MBC fournit :

```text
H_set = {H1,...,Hk}
```

MEVEP demande :

```text
"quelle observation differencierait reellement H1 de H2 ?"
```

Une bonne experience ne cherche pas seulement a confirmer H1 ; elle cherche a maximiser la discrimination entre hypotheses.


# 7. Statuts epistemiques

Statuts principaux :

```text
IMAGINED
FORMALIZED
PREDICTIVE
TESTABLE
PREREGISTERED
SIMULATED
OBSERVED
REPLICATED
CORROBORATED
CONTESTED
REFUTED
INCONCLUSIVE
```

Ces statuts ne forment pas toujours une chaine lineaire.

Une hypothese peut passer de `CORROBORATED` a `CONTESTED` apres de nouvelles preuves.


# 8. Statut et confiance

Le statut epistemique est categoriel.

La confiance est un vecteur ou un score distinct :

```text
Confidence = (
  source_quality,
  measurement_quality,
  analysis_robustness,
  replication_strength,
  independence,
  scope_coverage
)
```

Deux hypotheses `OBSERVED` peuvent avoir des confiances tres differentes.


# 9. Types de preuve

Taxonomie :

```text
DIRECT_MEASUREMENT
INSTRUMENT_READING
DOCUMENTED_OBSERVATION
CONTROLLED_EXPERIMENT
NATURAL_EXPERIMENT
SIMULATION
DERIVED_CALCULATION
TESTIMONY
ARCHIVAL_RECORD
REPLICATION
META_EVIDENCE
NEGATIVE_RESULT
COUNTEREXAMPLE
```

Le type influence les controles applicables.


# 10. Objet Evidence

```text
Evidence = (
  evidence_id,
  type,
  content_ref,
  source,
  timestamp,
  method,
  scope,
  quality,
  independence_group,
  supports,
  opposes,
  provenance
)
```

Une preuve n'est pas stockee seulement comme texte ; elle doit etre reliee a une methode et a une source.


# 11. Evidence positive et negative

Pour une hypothese `H_i` :

```text
E_i+ = preuves compatibles
E_i- = preuves incompatibles
```

Les deux ensembles sont conserves.

Le moteur interdit le filtrage silencieux des resultats negatifs.


# 12. Preuve neutre

Une observation peut etre non discriminante.

```text
E0
```

Elle peut etre :
- compatible avec toutes les hypotheses ;
- trop bruitée ;
- hors scope.

Elle n'est pas forcee dans `support` ou `oppose`.


# 13. Source canonique

```text
Source = (
  source_id,
  source_type,
  primary_or_secondary,
  authorship,
  date,
  method,
  access_ref,
  dependency_group,
  quality_metadata
)
```

Le moteur distingue :
- source primaire ;
- reprise ;
- commentaire ;
- synthese.


# 14. Independances de sources

Deux sources ne sont pas independantes si elles derivent d'une meme origine.

```text
independence_group(E)
```

Exemple :

```text
5 articles
-> 1 meme dataset
```

ne valent pas 5 replications independantes.


# 15. Graphe de dependance

```text
EvidenceGraph
```

relie :
- dataset ;
- article ;
- analyse ;
- reprise ;
- synthese ;
- claim.

On peut calculer :

```text
effective_independent_sources
```

au lieu de compter les documents bruts.


# 16. Qualite d'une source

Vecteur :

```text
Q_source = (
  transparency,
  method_detail,
  data_access,
  reproducibility,
  conflict_disclosure,
  peer_scrutiny,
  correction_history
)
```

Le moteur ne suppose pas qu'un label de publication suffit a garantir la validite.


# 17. Qualite d'une mesure

```text
Q_measure = (
  calibration,
  precision,
  accuracy,
  missingness,
  noise,
  operator_dependence,
  instrument_limits
)
```

Toute mesure doit garder son unite et son incertitude si elles existent.


# 18. Incertitude de mesure

Representation :

```text
x = x_hat +/- u
```

ou plus generalement :

```text
x ~ Distribution(theta)
```

Le moteur conserve l'incertitude plutot que d'utiliser uniquement une valeur ponctuelle.


# 19. Variables

Chaque variable :

```text
Variable = (
  name,
  type,
  operational_definition,
  unit,
  range,
  measurement_method
)
```

Un terme conceptuel ne devient variable experimentale qu'apres definition operationnelle.


# 20. Operationalisation

Exemple abstrait :

```text
"complexite"
```

doit etre traduit en indicateurs mesurables :
- nombre de branches ;
- profondeur ;
- entropie ;
- cout ;
- etc.

Le moteur conserve le lien :

```text
concept -> operationalization
```

et n'identifie pas les deux.


# 21. Scope de validite

Chaque conclusion porte :

```text
scope = population x contexte x temps x methode
```

Une observation locale ne devient pas automatiquement une loi generale.

Le moteur applique une penalite de generalisation lorsque le claim depasse son scope de preuve.


# 22. Prediction

Une prediction :

```text
Prediction = (
  pred_id,
  hypothesis_id,
  condition,
  expected_observation,
  tolerance,
  deadline,
  discriminator_strength
)
```

Elle doit idealement etre formulee avant l'observation.


# 23. Prediction qualitative

Exemple :

```text
si H1, alors A devrait etre plus frequent que B
```

Elle peut etre testable sans valeur numerique exacte.

Le moteur marque son pouvoir discriminant.


# 24. Prediction quantitative

Exemple :

```text
Y in [a,b]
```

ou :

```text
E[Y|H1] = mu +/- sigma
```

Plus la prediction est precise, plus un resultat incompatible peut etre informatif.


# 25. Prediction retrodictive

Une hypothese peut expliquer des donnees deja connues.

Cela est utile mais moins discriminant qu'une prediction preregistree.

Le moteur distingue :

```text
POST_HOC_FIT
```

de :

```text
PRE_SPECIFIED_PREDICTION
```


# 26. Falsifiabilite operationnelle

Le moteur demande :

```text
WhatResultWouldCountAgainst(H)?
```

Si la reponse est :

```text
"aucun resultat possible"
```

alors l'hypothese n'est pas falsifiable dans le protocole actuel.

Elle peut rester philosophique ou speculative, mais pas etre marquee comme experimentalement testee.


# 27. Conditions de refutation

```text
RefutationCriterion = (
  observation_pattern,
  tolerance,
  quality_requirements,
  replication_requirements
)
```

Le critere doit etre defini avant l'analyse lorsque possible.


# 28. Conditions de confirmation

Le moteur evite le mot "preuve definitive".

Il definit plutot :

```text
SupportCriterion
```

avec :
- amplitude ;
- qualite ;
- independance ;
- replication.

La conclusion est `supported/corroborated` dans un scope.


# 29. Protocole canonique

```text
Protocol = (
  protocol_id,
  question,
  hypotheses,
  variables,
  sample,
  controls,
  procedure,
  predictions,
  analysis_plan,
  stop_rules,
  exclusion_rules,
  risk,
  provenance
)
```

Une experience est une instance d'un protocole.


# 30. Versioning de protocole

```text
Protocol v1
Protocol v2
```

Toute modification apres debut de collecte est journalisee.

Le moteur peut marquer :

```text
POST_START_MODIFICATION
```

pour les analyses affectees.


# 31. Preregistration

Objet :

```text
Preregistration = hash(protocol + predictions + analysis_plan)
```

Objectif :
- fixer les predictions principales ;
- distinguer confirmatoire/exploratoire.

La preregistration n'empeche pas l'exploration ulterieure ; elle la classe correctement.


# 32. Confirmatoire vs exploratoire

```text
CONFIRMATORY
EXPLORATORY
```

Une analyse exploratoire peut devenir la base d'une nouvelle etude confirmatoire.

Le moteur evite de reetiqueter apres coup une exploration comme prediction initiale.


# 33. Plan d'analyse

Contient :
- transformations ;
- statistiques ;
- seuils ;
- exclusions ;
- gestion des donnees manquantes ;
- comparaisons multiples.

Il est versionne.


# 34. Stop rules

Le protocole definit :

```text
stop_if
```

Exemples :
- taille cible atteinte ;
- budget epuise ;
- risque ;
- precision suffisante.

Eviter de s'arreter uniquement lorsque le resultat devient favorable.


# 35. Exclusion rules

Les exclusions doivent etre predefinies lorsque possible.

Chaque exclusion reelle est tracee :

```text
excluded_observation
reason
rule_id
```

Le moteur peut recalculer une analyse de sensibilite avec et sans exclusions.


# 36. Randomisation

Quand pertinente :

```text
RandomizationPlan
```

stocke :
- seed ;
- methode ;
- blocs ;
- contraintes.

Le seed permet la reproductibilite.


# 37. Controle

Types :

```text
NEGATIVE_CONTROL
POSITIVE_CONTROL
PLACEBO_CONTROL
BASELINE
SHAM
REFERENCE
```

Le type depend du domaine.

Un protocole sans controle pertinent doit justifier pourquoi.


# 38. Blinding

Lorsque applicable :

```text
SINGLE_BLIND
DOUBLE_BLIND
ANALYST_BLIND
```

Le moteur stocke qui connaissait quoi et quand.


# 39. Confounders

Liste :

```text
ConfounderCandidate = (
  variable,
  causal_path,
  observability,
  control_strategy
)
```

Le moteur peut demander une branche alternative :

```text
H_confounded
```

plutot que supposer causalite directe.


# 40. Causalite vs correlation

Invariant :

```text
correlation != causation
```

Le moteur exige pour une assertion causale :
- design causal ;
- controles ;
- modele causal ;
- ou qualification explicite.


# 41. DAG causal

```text
CausalDAG
```

represente :
- causes ;
- effets ;
- confounders ;
- mediators ;
- colliders.

Le DAG est un modele testable, pas une observation.


# 42. Donnees brutes

Le moteur conserve :

```text
raw_data
processed_data
derived_data
```

avec lineage.

Les donnees transformees ne remplacent pas silencieusement les brutes.


# 43. Integrity manifest

Un dataset peut avoir :

```text
DatasetManifest = (
  files,
  hashes,
  rows,
  columns,
  schema,
  missingness,
  generated_at
)
```

Cela permet de verifier la stabilite d'une analyse.


# 44. Nettoyage des donnees

Chaque operation :

```text
cleaning_step
```

est journalisee.

Exemple :
- correction de type ;
- suppression de doublons ;
- normalisation.

Le moteur produit un rapport de transformation.


# 45. Donnees manquantes

Types possibles :

```text
MCAR
MAR
MNAR
UNKNOWN
```

Le moteur ne suppose pas automatiquement que les valeurs manquantes sont aleatoires.


# 46. Outliers

Un outlier n'est pas supprime automatiquement.

Options :
- conserver ;
- robust estimator ;
- analyse avec/sans ;
- exclusion preregistree.

La decision est tracee.


# 47. Controle qualite automatique

Checks :
- schema ;
- plages ;
- duplications ;
- timestamps ;
- incoherences ;
- unités ;
- missingness.

Un `QC_FAIL` bloque l'analyse confirmatoire si le protocole l'exige.


# 48. Provenance des donnees

```text
DataProvenance = source -> acquisition -> transform -> analysis
```

Chaque fleche a :
- code/version ;
- parametres ;
- timestamp.


# 49. Analyse descriptive

Avant test :
- distributions ;
- effectifs ;
- valeurs manquantes ;
- dispersion.

La description ne doit pas etre confondue avec un test d'hypothese.


# 50. Taille d'effet

Le moteur privilegie :

```text
effect_size
```

avec intervalle d'incertitude.

Un petit p-value ne suffit pas a decrire l'importance pratique.


# 51. Intervalles

Selon methode :

```text
confidence_interval
credible_interval
prediction_interval
```

Le type doit etre nomme correctement.


# 52. Tests statistiques

Le choix depend :
- variable ;
- distribution ;
- dependance ;
- plan.

Le moteur peut stocker :

```text
TestAssumptions
```

et refuser un test manifestement incompatible.


# 53. Multiplicite

Lorsque de nombreux tests sont realises :

```text
multiple_comparisons
```

Le moteur enregistre :
- nombre de tests ;
- correction ;
- famille d'hypotheses.

Cela limite les faux positifs par exploration massive.


# 54. p-value

Une p-value n'est pas :

```text
P(H0 is true)
```

Le moteur interdit cette interpretation.

Il l'associe a :
- test ;
- modele ;
- donnees ;
- hypothese nulle.


# 55. Analyse bayesienne

Le moteur peut representer :

```text
Prior
Likelihood
Posterior
```

mais doit garder les priors explicites.

Une forte conclusion ne doit pas masquer sa sensibilite au prior.


# 56. Bayes factor

Peut comparer :

```text
BF_12 = P(Data|H1)/P(Data|H2)
```

Il depend des modeles choisis.

Le moteur stocke ces modeles.


# 57. Model comparison

Plus generalement :

```text
CompareModels({M_i})
```

metriques possibles :
- predictive accuracy ;
- calibration ;
- complexity penalty ;
- residual structure.

Un modele performant n'est pas necessairement une explication causale vraie.


# 58. Cross-validation

Separations :
- train ;
- validation ;
- test.

Le test final ne doit pas etre reutilise pour regler le modele sans reclassification.


# 59. Leakage

Le moteur detecte autant que possible :

```text
DATA_LEAKAGE
TARGET_LEAKAGE
TEMPORAL_LEAKAGE
```

Un resultat avec leakage est degrade epistemiquement.


# 60. Replication

Une replication est :

```text
Replication = (
  protocol_ref,
  independent_team_or_run,
  new_data,
  deviations,
  outcome
)
```

Le moteur distingue :
- exact/close replication ;
- conceptual replication.


# 61. Repetition vs replication

Reexecuter le meme code sur le meme dataset :

```text
REPRODUCTION
```

n'est pas une replication empirique independante.

Les deux sont utiles mais differents.


# 62. Reproductibilite computationnelle

Exigences :
- code ;
- versions ;
- seed ;
- environnement ;
- donnees accessibles ou manifest.

Le moteur peut generer un bundle reproductible.


# 63. Replication negative

Un echec de replication ne refute pas automatiquement.

On compare :
- puissance ;
- protocole ;
- population ;
- mesures ;
- deviations.

Le statut peut devenir `CONTESTED`.


# 64. Meta-analyse locale

Plusieurs etudes peuvent etre combinees si leurs effets sont comparables.

Le moteur conserve :
- heterogeneite ;
- dependances ;
- biais potentiels.

Une meta-analyse de sources dependantes est marquee.


# 65. Heterogeneite

Le moteur mesure/qualifie :

```text
between-study heterogeneity
```

Une moyenne globale peut masquer des effets opposes selon les contextes.


# 66. Biais de publication

Le moteur considere :
- resultats manquants ;
- preregistrations non publiees ;
- asymetrie.

L'absence de resultats negatifs visibles n'est pas prise comme confirmation.


# 67. Sensibilite

Analyse :

```text
SensitivityAnalysis
```

varie :
- exclusions ;
- modeles ;
- priors ;
- seuils ;
- operationalisations.

Un claim robuste doit rester raisonnablement stable.


# 68. Robustesse

```text
Robustness = stability across plausible analyses
```

Le moteur distingue robustesse analytique et corroboration externe.


# 69. Specification curve

Pour plusieurs choix analytiques plausibles :

```text
analysis_1 ... analysis_n
```

on observe la distribution des resultats.

Utile pour detecter un resultat dependant d'un choix particulier.


# 70. Resultat inconclusif

Le statut :

```text
INCONCLUSIVE
```

est explicite.

Causes :
- faible precision ;
- donnees insuffisantes ;
- mesures faibles ;
- predictions non discriminantes.

Le moteur ne force pas une decision binaire.


# 71. Resultat nul

Un resultat non significatif ne prouve pas automatiquement l'absence d'effet.

Le moteur demande :
- intervalle ;
- puissance/precision ;
- taille d'effet compatible.


# 72. Equivalence

Si le but est montrer que l'effet est suffisamment petit :

```text
equivalence_margin
```

doit etre defini.

Un test d'equivalence est different d'un simple echec a rejeter H0.


# 73. Non-inferiorite

Dans les contextes pertinents :

```text
non_inferiority_margin
```

est preregistree.

Le moteur conserve la justification de la marge.


# 74. Contre-exemple

Pour un claim universel :

```text
forall x, P(x)
```

un contre-exemple valide peut etre fortement informatif.

Le moteur donne une priorite speciale aux observations qui violent une prediction universelle.


# 75. Claims universels vs statistiques

```text
"toujours"
```

demande une logique differente de :

```text
"plus probable"
```

Le moteur adapte le critere de refutation au quantificateur.


# 76. Promotion epistemique

Une promotion :

```text
FORMALIZED -> TESTABLE
```

exige :
- variables ;
- prediction ;
- protocole possible.

```text
OBSERVED -> REPLICATED
```

exige une replication conforme aux criteres.

Aucune promotion n'est automatique uniquement parce qu'un score depasse un seuil.


# 77. De-promotion

Nouveaux elements peuvent entrainer :

```text
CORROBORATED -> CONTESTED
OBSERVED -> INVALIDATED
```

Le moteur conserve l'histoire des statuts.


# 78. Refutation

Une hypothese devient `REFUTED` dans un scope si :
- critere de refutation preregistree satisfait ;
- qualite de mesure suffisante ;
- analyses alternatives pertinentes traitees.

Une refutation locale ne signifie pas invalidite dans tout contexte.


# 79. Corroboration

`CORROBORATED` signifie :
- plusieurs supports ;
- suffisamment independants ;
- qualite acceptable ;
- predictions compatibles ;
- scope explicite.

Cela ne signifie pas certitude metaphysique.


# 80. Score de support

On peut calculer :

```text
SupportVector = (
  prediction_match,
  source_quality,
  measurement_quality,
  replication,
  independence,
  robustness,
  scope_fit
)
```

Un score scalaire peut aider au tri mais ne remplace pas le vecteur.


# 81. Fonction de support

Exemple :

```text
Q_support =
GM(
  prediction_match,
  measurement_quality,
  independence,
  replication
)
```

Une moyenne geometrique penalise une dimension quasi nulle.

Mais le moteur conserve les composantes.


# 82. Penalite de dependance

Si les preuves partagent une origine :

```text
effective_support < naive_sum_support
```

Le moteur peut utiliser un facteur de dependance.


# 83. Penalite de scope

Si le claim depasse les donnees :

```text
scope_penalty > 0
```

Le moteur propose une reformulation plus prudente.


# 84. Reformulation epistemique

Exemple :

```text
"X cause Y"
```

peut etre reformule en :

```text
"Dans ce protocole, X est associe a Y"
```

si les preuves ne supportent pas la causalite.

Le moteur conserve la formulation originale comme claim non valide.


# 85. Niveau de langage

Verbes controles :

```text
speculate
suggest
support
observe
replicate
corroborate
refute
```

Chaque verbe correspond a des conditions epistemiques.


# 86. Claim linting

Un linter detecte :
- causalite non supportee ;
- generalisation excessive ;
- certitude absolue ;
- source unique presentee comme consensus ;
- simulation presentee comme observation.

Sortie :

```text
EPISTEMIC_WARNING
```


# 87. Evidence gate

Avant une action fondee sur un claim :

```text
G_evidence
```

peut exiger un niveau minimal.

Mais le seuil depend du risque :
- faible risque : evidence plus faible acceptable ;
- fort risque : evidence plus forte requise.


# 88. Evidence-risk coupling

Principe :

```text
required_evidence_strength
increases with
potential_harm + irreversibility
```

Le moteur relie ainsi Structure 09 a OCCP.


# 89. Protocol generator

A partir de `H` :

```text
GenerateProtocol(H, constraints)
```

produit :
- variables ;
- predictions ;
- controls ;
- procedure ;
- analysis ;
- stop rules.

Le resultat reste une proposition a valider.


# 90. Protocol critic

```text
CritiqueProtocol(P)
```

cherche :
- confounders ;
- ambiguities ;
- impossible measures ;
- circular definitions ;
- weak discriminator ;
- data leakage.


# 91. Adversarial protocol review

Un reviewer genere :

```text
"comment ce protocole pourrait produire un faux support ?"
```

Cela cree des branches de biais possibles.


# 92. Preuve adversariale

Pour une hypothese forte, le moteur peut prioriser la recherche de :
- contre-exemples ;
- replications negatives ;
- alternatives.

But :
- reduire confirmation bias.


# 93. Predictions rivales

Table :

```text
Observation | H1 | H2 | H3
O1          | +  | -  | 0
O2          | +  | +  | -
```

Le moteur prefere tester `O1` si elle discrimine mieux.


# 94. Gain d'information experimentale

```text
IG(test) =
H(prior hypotheses)
-
E[H(posterior hypotheses | outcome)]
```

Le moteur choisit les tests qui devraient reduire le plus l'incertitude.


# 95. Cout d'une experience

```text
C_exp = (
  temps,
  compute,
  argent,
  charge_humaine,
  risque,
  irreversibilite
)
```

Le meilleur test maximise :

```text
IG / (1 + C_exp)
```

sous contraintes.


# 96. Sequence experimentale

Plan :

```text
cheap_screen
-> discriminating_test
-> replication
-> stress_test
```

Le moteur n'utilise pas directement le test le plus couteux si un test simple peut eliminer des branches.


# 97. Pilot study

Une etude pilote :
- teste faisabilite ;
- estime variabilite ;
- affine instrument.

Elle n'est pas automatiquement une validation principale.


# 98. Calibration

Avant mesure :

```text
CALIBRATE
```

avec reference connue.

Le certificat/calibration event est stocke dans la provenance.


# 99. Instrument drift

Des mesures longues peuvent etre affectees par :

```text
drift(t)
```

Le protocole peut inclure des recalibrations.


# 100. Observateur

Lorsque l'observateur peut influencer la mesure, le protocole documente :
- interaction ;
- expectations ;
- blinding ;
- automatisation.

Le terme "observateur" reste operationnel, pas metaphysique par defaut.


# 101. Auto-experimentation

Pour des tests personnels ou locaux :
- petite portee ;
- mesures explicites ;
- journal ;
- comparaison baseline ;
- prudence de generalisation.

Une observation sur un utilisateur ne devient pas population-wide.


# 102. Simulations

Une simulation :

```text
SimResult = Model + Inputs + Parameters
```

supporte une conclusion sur le comportement du modele.

Elle ne prouve pas directement le comportement du monde reel.


# 103. Validation de simulation

On peut valider :
- code ;
- equations ;
- numerique ;
- calibration ;
- comparaison empirique.

Le statut d'une simulation depend aussi de la validite du modele.


# 104. Verification vs validation

```text
verification:
"avons-nous implemente correctement le modele ?"

validation:
"le modele correspond-il suffisamment au phenomene vise ?"
```

Les deux sont separees.


# 105. Tests metamorphiques

Lorsque oracle exact absent :

```text
MetamorphicRelation
```

exemple :
- symetrie ;
- invariance ;
- monotonicite.

Le moteur peut tester les proprietes attendues du modele.


# 106. Tests de proprietes

Pour les formalismes :

```text
property-based testing
```

explore de nombreux inputs.

Un contre-exemple minimal est conserve comme evidence technique.


# 107. Validation croisee theorie/code

Pour une equation implementee :
- calcul symbolique ;
- reference numerique ;
- implementation ;
- comparaison.

Le pipeline distingue erreur conceptuelle et bug.


# 108. Evidence package

Livrable :

```text
EvidencePackage/
  manifest.json
  claims/
  hypotheses/
  protocols/
  preregistrations/
  data_manifests/
  analyses/
  figures/
  replications/
  provenance/
  limitations.md
  README.md
```

Ce paquet est concu pour audit et publication.


# 109. Manifest du paquet

```json
{
  "package_version": "1.0",
  "claims": ["cl_1"],
  "protocols": ["p_1"],
  "datasets": ["d_1"],
  "status": "CORROBORATED",
  "scope": ["..."],
  "known_limitations": ["..."]
}
```


# 110. Export paper

Le moteur peut produire des sections :
- Methods ;
- Results ;
- Limitations ;
- Evidence table ;
- Reproducibility statement.

Il ne doit pas inventer des donnees manquantes pour remplir un paper.


# 111. Evidence table

Colonnes :

```text
Claim
Evidence
Type
Source
Independent?
Quality
Supports/Opposes
Scope
Status
```

Cette table rend le support inspectable.


# 112. Claim-evidence matrix

```text
M_ij =
+1 support
0 neutral
-1 oppose
```

avec force et confiance separees.

Elle permet de voir quels claims dependent d'une seule preuve.


# 113. Evidence graph HUD

Vue :

```text
Claim
 |\
 | Evidence A
 | Evidence B
 \ Counterexample C
```

Filtres :
- source ;
- independance ;
- statut ;
- date ;
- methode.


# 114. Protocol HUD

Affiche :
- hypotheses ;
- predictions ;
- procedure ;
- progression ;
- deviations ;
- QC ;
- analyses preregistrees.

Une deviation est visible en temps reel.


# 115. Replication dashboard

Resume :
- nombre de replications ;
- independantes ;
- compatibles ;
- negatives ;
- heterogeneite.

Aucune reduction a un simple compteur.


# 116. Integration NCU

Chaque claim/hypothese peut etre un NCU ou etre lie a un NCU.

Le NCU stocke :
- identite ;
- contenu.

MEVEP stocke :
- statut epistemique ;
- supports ;
- protocoles.


# 117. Integration MBC

MBC :
- genere branches.

MEVEP :
- propose tests ;
- met a jour support ;
- refute/corroborate dans un scope.

Une branche faible n'est pas supprimee automatiquement ; elle peut rester archivee.


# 118. Integration Structure 04

Les protocoles peuvent ouvrir des sous-espaces :
- variables ;
- instrument ;
- contexte ;
- populations.

La validation peut etre naviguee par echelle.


# 119. Integration Structure 05

Le regulateur alloue :
- calcul ;
- temps ;
- branches ;
- replications.

Une replication independante peut etre prioritaire par rapport a une 20e simulation similaire.


# 120. Integration Structure 06

Avant experience ou analyse importante :
- checkpoint ;
- version des donnees ;
- code ;
- protocole.

En cas d'erreur :
- rollback technique sans reecrire les observations historiques.


# 121. Integration Structure 08

L'orchestrateur peut demander :

```text
VALIDATE claim:X
```

Structure 09 retourne :
- protocole ;
- taches ;
- evidence gaps ;
- seuil de statut.

Structure 08 coordonne l'execution.


# 122. Interface TimeWarp

TimeWarp doit conserver :
- moment de formulation du claim ;
- moment des predictions ;
- moment de preregistration ;
- moment des observations ;
- revisions.

Cela permet de verifier ce qui etait connu avant chaque analyse.


# 123. Event types

```text
CLAIM_CREATED
HYPOTHESIS_FORMALIZED
PREDICTION_REGISTERED
PROTOCOL_REGISTERED
DATA_COLLECTED
QC_COMPLETED
ANALYSIS_RUN
RESULT_RECORDED
REPLICATION_RECORDED
STATUS_CHANGED
```


# 124. Immutabilite historique

Une prediction modifiee apres observation devient une nouvelle revision.

Le moteur conserve :

```text
prediction_original
prediction_revised
```

et indique laquelle est confirmatoire.


# 125. Audit epistemique

Questions :
- quelles predictions etaient preregistrees ?
- combien de tests ont ete lances ?
- quelles exclusions ont change ?
- quelles preuves sont dependantes ?
- quelles analyses sont exploratoires ?

Sortie : `EpistemicAuditReport`.


# 126. Detection de HARKing

Le systeme peut signaler :

```text
HYPOTHESIS_AFTER_RESULTS_KNOWN
```

si une prediction est formulee apres observation mais presentee comme antecedente.

Il ne juge pas l'intention ; il classe la temporalite.


# 127. Detection de p-hacking

Signaux possibles :
- grand nombre de tests ;
- changements d'exclusion ;
- stop rules adaptatifs ;
- seules analyses favorables conservees.

Le moteur produit un warning, pas une accusation automatique.


# 128. Detection de cherry-picking

Comparer :
- evidence inventory ;
- evidence used.

Si des preuves pertinentes opposees sont omises :

```text
SELECTIVE_EVIDENCE_WARNING
```


# 129. Negative result registry

Les resultats negatifs/inconclusifs sont indexes.

Cela reduit la repetition de tests identiques et le biais de publication local.


# 130. Failed protocol registry

Un protocole rate peut etre utile.

On stocke :
- cause ;
- limites ;
- ce qui a ete appris ;
- modifications proposees.


# 131. Evidence debt

```text
EvidenceDebt =
claims_high_strength
- evidence_strength_available
```

Si dette elevee :
- limiter les formulations fortes ;
- prioriser validation.


# 132. Replication debt

```text
ReplicationDebt =
important_claims_without_independent_replication
```

Le moteur peut proposer un plan de replication.


# 133. Measurement debt

Si les variables sont mal operationalisees :

```text
MeasurementDebt
```

priorite :
- ameliorer instrument avant multiplier les analyses.


# 134. Protocol debt

Accumulation :
- tests ad hoc ;
- procedures non versionnees ;
- exclusions ambiguës.

Le moteur demande consolidation avant promotion epistemique.


# 135. Decision sous incertitude

Une decision peut etre necessaire avant corroboration complete.

Le moteur fournit :

```text
DecisionEvidenceProfile
```

avec :
- support ;
- risque ;
- alternatives ;
- reversibilite.

La decision reste distincte du statut de verite.


# 136. Seuils dependants du risque

Exemple conceptuel :

```text
low-risk reversible exploration
-> threshold low/moderate

high-risk irreversible action
-> threshold high
```

Les seuils sont politiques, pas lois universelles.


# 137. Epistemic stop rule

Arreter l'exploration si :
- claim suffisamment contraint pour l'objectif ;
- gain d'information marginal faible ;
- budget epuise ;
- methode inadequate ;
- nouvelles donnees necessaires.

Le moteur ne simule pas une certitude.


# 138. Questions ouvertes

Une synthese peut produire :

```text
OpenQuestions = {
  Q1,
  Q2,
  ...
}
```

Chaque question est reliee a :
- claim ;
- lacune de preuve ;
- protocole possible.


# 139. Research backlog

Priorite :

```text
Priority(Q) =
importance
* information_gain
* feasibility
/
(1 + cost + risk)
```

Le backlog devient une feuille de route scientifique.


# 140. Protocoles iteratifs

Cycle :

```text
P0 pilot
-> P1 confirmatory
-> P2 replication
-> P3 stress-test
```

Chaque cycle repond a une question plus precise.


# 141. Stress testing d'une theorie

Le moteur cherche les zones ou la theorie devrait echouer.

Exemples :
- valeurs extremes ;
- autre population ;
- autre instrument ;
- perturbations.

Une theorie robuste accepte d'etre testee hors de sa zone la plus favorable.


# 142. Boundary mapping

Plutot que seulement demander :

```text
"est-ce vrai ?"
```

demander :

```text
"ou, quand, pour qui et jusqu'a quel niveau cela tient-il ?"
```

Le moteur construit une carte de validite.


# 143. Carte de validite

```text
ValidityMap = {
  regions_supported,
  regions_contested,
  regions_refuted,
  regions_unknown
}
```

Compatible avec le zoom multi-echelle de Structure 04.


# 144. Generalisation

Generaliser demande :
- diversite de contextes ;
- replication ;
- mecanisme plausible ;
- stabilite.

Le moteur affiche un `GeneralizationLevel`.


# 145. Transfer

Un modele valide dans un domaine peut etre teste dans un autre.

```text
TransferTest
```

La similitude conceptuelle seule ne suffit pas.


# 146. Analogies experimentales

Une analogie peut generer une hypothese :

```text
A analogous B
-> predicted shared property P?
```

Puis test.

La Structure 09 convertit ainsi une analogie en proposition falsifiable.


# 147. Evidence hierarchy contextuelle

Il n'existe pas une hierarchie universelle unique.

Le moteur peut definir :

```text
EvidencePolicy(domain)
```

Exemple :
- logiciel ;
- physique ;
- sciences sociales ;
- recherche qualitative.

Les criteres changent, mais provenance et transparence restent centrales.


# 148. Qualitatif

Pour donnees qualitatives :
- codage ;
- saturation ;
- triangulation ;
- traces ;
- contre-exemples ;
- reflexivite methodologique.

Le moteur ne force pas toutes les preuves dans un format statistique.


# 149. Triangulation

Plusieurs methodes differentes peuvent converger.

```text
Triangulation = distinct_methods -> compatible_claim
```

La diversite de methode augmente la robustesse si leurs biais ne sont pas identiques.


# 150. Inter-rater

Pour codages humains :

```text
inter_rater_agreement
```

avec protocole de resolution des desaccords.

Le consensus final ne remplace pas les taux de desaccord initiaux.


# 151. Reflexivite methodologique

Le protocole peut documenter :
- choix du chercheur ;
- attentes ;
- changements ;
- limites.

Cela augmente la tracabilite sans pretendre eliminer toute subjectivite.


# 152. Evidence qualitative canonique

```text
QualEvidence = (
  corpus_ref,
  sampling,
  coding_scheme,
  coder_provenance,
  excerpts_refs,
  negative_cases,
  saturation_notes
)
```

Les extraits restent references, pas inventes.


# 153. Simulation Monte Carlo

Pour incertitude :

```text
theta ~ distributions
-> simulate outcomes
```

Le moteur conserve :
- distributions ;
- seeds ;
- hypotheses.

La sortie de Monte Carlo reste conditionnelle au modele.


# 154. Analyse de puissance

Avant collecte, on peut estimer :
- taille necessaire ;
- precision ;
- effet minimal detectable.

Le moteur garde les assumptions.


# 155. Precision-first design

Au lieu de viser un seuil p, on peut viser :

```text
interval_width <= target
```

Le moteur peut choisir cette strategie.


# 156. Sequential testing

Si collecte sequentielle :
- methode adaptee ;
- stop rules explicites.

Le moteur evite les tests repetes non corriges a chaque nouvelle observation.


# 157. Online experimentation

Pour flux continus :
- version des modeles ;
- fenetres temporelles ;
- drift ;
- guardrails.

Le TimeWarp devient essentiel pour savoir quelle configuration etait active.


# 158. Concept drift

Si la relation change avec le temps :

```text
P(Y|X,t)
```

peut evoluer.

Le moteur peut reduire la validite temporelle d'un claim ancien.


# 159. Data drift

Distribution des inputs change :

```text
P_t(X) != P_{t+1}(X)
```

Cela peut invalider une generalisation sans refuter le mecanisme initial.


# 160. Calibration de confiance

Pour predictions probabilistes :

```text
predicted_probability
vs
observed_frequency
```

Le moteur mesure calibration separement de discrimination.


# 161. Benchmarking

Un benchmark :
- dataset ;
- protocole ;
- metriques ;
- versions ;
- limites.

Les scores ne sont comparables que si les conditions sont compatibles.


# 162. Baselines

Toute nouvelle methode peut etre comparee a :
- baseline simple ;
- systeme actuel ;
- hasard si pertinent.

Une methode complexe doit justifier son gain.


# 163. Ablation studies

Pour un systeme complexe :

```text
remove component C
-> compare outcome
```

Cela aide a identifier quelles briques contribuent reellement.


# 164. Controles negatifs logiciels

Exemple :
- test ou aucun effet ne devrait apparaitre.

Si le pipeline detecte quand meme un "effet", cela signale un probleme de methode.


# 165. Reproductibilite d'environnement

Manifest :
- OS ;
- dependencies ;
- hardware ;
- model versions.

Les resultats computationnels sensibles a l'environnement sont marques.


# 166. Containerisation

Une experience peut fournir :
- Dockerfile ;
- lockfile ;
- environment spec.

Le paquet de preuve peut les referencer.


# 167. Determinisme et seeds

Si un composant est stochastic :

```text
seed
```

est conserve lorsque possible.

On peut executer plusieurs seeds pour mesurer variabilite.


# 168. Regression testing epistemique

Lorsqu'un code d'analyse change :

```text
old_result vs new_result
```

Le moteur detecte les claims impactes.

Un changement de pipeline peut de-promouvoir temporairement des conclusions.


# 169. Claim dependency graph

Un claim peut dependre d'autres claims.

```text
C3 <- C1, C2
```

Si C1 est refute, C3 doit etre reevalue.


# 170. Propagation de changement

```text
status_change(C1)
-> invalidate_dependents
-> recompute assessments
```

Le moteur ne laisse pas des conclusions dependantes obsoletes.


# 171. Evidence freshness

Certaines preuves vieillissent :
- logiciel ;
- mesures operationnelles ;
- populations changeantes.

Le moteur stocke un `freshness_policy`.


# 172. Expiration de preuve

Une preuve peut devenir :

```text
STALE
```

sans etre fausse.

Le claim peut necessiter revalidation.


# 173. Confidence decay

Dans les domaines dynamiques :

```text
confidence(t)
```

peut decroitre selon la fraicheur.

La fonction doit etre justifiee par domaine.


# 174. Consensus scientifique

Le moteur peut representer :
- nombre de sources ;
- qualite ;
- heterogeneite ;
- controverses.

Il ne derive pas un consensus uniquement d'un comptage de pages.


# 175. Expert disagreement

Les positions d'experts sont stockees comme evidence/testimony avec provenance.

Un desaccord d'experts reste un desaccord, pas une moyenne.


# 176. Source authority vs evidence

Autorite peut influencer la credibilite initiale d'une source, mais ne remplace pas la methode et les donnees.

Le moteur separe :

```text
authority_prior
```

et :

```text
empirical_support
```


# 177. Open science metadata

Le paquet peut contenir :
- licence ;
- DOI ;
- preregistration ID ;
- repository ;
- ORCID ;
- version.

Si absents, ils restent vides.


# 178. Reproducibility statement

Generation automatique possible :

```text
Data: available/not available
Code: available/not available
Environment: specified/not specified
Seeds: ...
```

Aucun statut n'est invente.


# 179. Limitations generator

Le moteur derive les limites depuis :
- scope ;
- sample ;
- mesure ;
- dependances ;
- replication ;
- analyses.

Il ne genere pas une liste generique de limitations sans lien avec le protocole.


# 180. Future work

Les prochaines experiences sont classees par :
- lacune ;
- gain d'information ;
- cout ;
- risque.

Le "future work" devient un backlog trace.


# 181. Epistemic API

```text
POST /claims
GET  /claims/{id}
POST /claims/{id}/assess
POST /hypotheses/{id}/predictions
POST /protocols/generate
POST /protocols/{id}/preregister
POST /evidence
POST /replications
GET  /claims/{id}/evidence-map
GET  /claims/{id}/audit
POST /packages/evidence
```


# 182. DSL epistemique

```text
CLAIM CREATE "X increases Y" SCOPE C
HYPOTHESIS FROM claim:1
PREDICT IF H1 THEN metric:Y > baseline
PROTOCOL GENERATE FOR H1,H0
PREREGISTER protocol:3
EVIDENCE ADD source:dataset1 TYPE measurement
ASSESS claim:1
SHOW COUNTEREVIDENCE claim:1
EXPORT EVIDENCE_PACKAGE claim:1
```


# 183. Schema SQLite

```sql
claims(
  claim_id TEXT PRIMARY KEY,
  proposition TEXT,
  scope_json TEXT,
  status TEXT,
  created_at TEXT
);

hypotheses(
  hypothesis_id TEXT PRIMARY KEY,
  claim_id TEXT,
  formalization TEXT,
  status TEXT
);

predictions(
  prediction_id TEXT PRIMARY KEY,
  hypothesis_id TEXT,
  payload_json TEXT,
  preregistered INTEGER
);

protocols(
  protocol_id TEXT,
  version INTEGER,
  payload_json TEXT,
  PRIMARY KEY(protocol_id, version)
);

evidence(
  evidence_id TEXT PRIMARY KEY,
  type TEXT,
  source_id TEXT,
  payload_json TEXT
);

claim_evidence(
  claim_id TEXT,
  evidence_id TEXT,
  polarity INTEGER,
  strength REAL
);

assessments(
  assessment_id TEXT PRIMARY KEY,
  claim_id TEXT,
  status TEXT,
  vector_json TEXT,
  timestamp TEXT
);
```


# 184. Architecture logicielle

```text
EpistemicEngine
├── ClaimRegistry
├── HypothesisManager
├── PredictionEngine
├── ProtocolGenerator
├── PreregistrationManager
├── EvidenceRegistry
├── SourceDependencyGraph
├── DataQualityEngine
├── AnalysisRunner
├── ReplicationManager
├── RobustnessEngine
├── ClaimLinter
├── StatusEvaluator
├── EvidencePackageBuilder
├── AuditEngine
├── MBCAdapter
├── OrchestratorAdapter
├── TimeWarpAdapter
└── HUDAdapter
```


# 185. Invariants

```text
E1. Simulation n'est jamais observation.
E2. Une prediction post-hoc est marquee post-hoc.
E3. Une source derivee ne compte pas comme independante de sa source primaire.
E4. Les resultats negatifs restent conserves.
E5. Une conclusion porte un scope.
E6. Une promotion de statut est tracee.
E7. Une de-promotion est possible.
E8. Un protocole modifie apres collecte change de revision.
E9. Les exclusions sont tracees.
E10. Une action a risque fort demande un niveau de preuve plus eleve.
E11. Une generalisation ne peut etre plus forte que son evidence sans warning.
E12. Un paquet de preuve conserve provenance et limites.
```


# 186. Tests unitaires

```text
test_simulation_not_observation
test_posthoc_prediction_marked
test_source_dependency
test_negative_evidence_preserved
test_protocol_versioning
test_preregistration_hash
test_scope_penalty
test_claim_linter_causality
test_replication_independence
test_status_depromotion
test_evidence_package_manifest
test_claim_dependency_reassessment
```


# 187. Tests de proprietes

```text
P1. No claim can have stronger status than its mandatory evidence gates permit.
P2. All status changes are append-only events.
P3. Every evidence item has provenance.
P4. Every confirmatory result references a preregistered prediction or is marked otherwise.
P5. Removing dependent duplicate sources never increases effective independent support.
```


# 188. Tests de chaos epistemique

Injecter :
- dataset change ;
- missing file ;
- different seed ;
- corrupted protocol ;
- contradictory replication ;
- stale evidence ;
- dependency graph error.

Verifier :
- de-promotion ;
- warnings ;
- pas de faux statut stable.


# 189. Observabilite

Metriques :

```text
claims_by_status
evidence_items
independent_source_count
protocols_preregistered
replication_count
negative_result_count
epistemic_debt
replication_debt
measurement_debt
status_change_rate
claim_lint_warning_count
```


# 190. HUD epistemique

Vue :

```text
Claim: X -> Y
Status: CONTESTED
Support: 0.71
Independent sources: 3
Replications: 2 positive / 1 negative
Scope: C
Open issues: measurement drift
```

Boutons :
- evidence map ;
- protocol ;
- replication ;
- audit ;
- export package.


# 191. Exemple A - hypothese technique

Claim :

```text
"le changement de configuration C augmente la stabilite du service"
```

Plan :
- baseline ;
- metrics ;
- prediction ;
- rollout controle ;
- comparaison ;
- rollback disponible ;
- replication sur plusieurs sessions.

Conclusion possible :

```text
supported in this environment
```

pas :

```text
universally proven
```


# 192. Exemple B - simulation mathematique

Une equation predit une transition.

Etapes :
- verifier implementation ;
- balayer parametres ;
- simulation ;
- identifier zone de transition ;
- formuler prediction empirique si un systeme mesurable existe.

La simulation soutient d'abord le modele, pas directement le monde.


# 193. Exemple C - observation perceptive

Une experience subjective peut etre documentee comme :

```text
self_report
```

avec :
- conditions ;
- heure ;
- procedure ;
- repetition ;
- controle perceptif si possible.

Elle peut generer une hypothese, mais le statut reste adapte au type de mesure.


# 194. Exemple D - comparaison de deux hypotheses

H1 et H2 expliquent les memes donnees historiques.

Le moteur cherche une prediction :

```text
P*
```

ou :

```text
P(P*|H1) >> P(P*|H2)
```

L'experience cible `P*` plutot que refaire des observations non discriminantes.


# 195. Exemple E - replication contradictoire

Etude A : positive.
Etude B : negative.

Le moteur examine :
- protocol deviations ;
- precision ;
- populations ;
- instrument ;
- heterogeneity.

Statut :

```text
CONTESTED
```

jusqu'a meilleure resolution.


# 196. Exemple F - evidence package pour paper

Le moteur exporte :
- claims ;
- hypotheses ;
- protocol ;
- raw data manifest ;
- analysis ;
- figures ;
- limitations ;
- provenance.

Le paper peut alors citer le package comme support reproductible.


# 197. Formule maitresse

Pour un claim `C` :

```text
H = Formalize(C)
P = DerivePredictions(H)
R = RegisterProtocol(P)
D = Collect(R)
Q = QualityControl(D)
A = Analyze(Q, R.analysis_plan)
E = BuildEvidence(A)
Rep = Replicate(E)
```

Evaluation :

```text
Assessment(C) =
Evaluate(
  prediction_match,
  evidence_quality,
  independence,
  robustness,
  replication,
  scope_fit,
  counterevidence
)
```

Transition de statut :

```text
Status_{t+1}
=
Transition(
  Status_t,
  Assessment_t,
  policy
)
```

avec :

```text
strength(claim_language)
<=
strength(assessment)
```


# 198. Equation d'admission epistemique

Pour une conclusion candidate `c` :

```text
G_epi(c) =
G_provenance
AND G_scope
AND G_measurement
AND G_analysis
AND G_dependency
AND G_language
```

Si un gate echoue :

```text
DOWNGRADE
or
INCONCLUSIVE
```

plutot que masquer l'echec.


# 199. Equation de priorite de recherche

Pour une experience `e` :

```text
Priority(e) =
Importance(claim)
* ExpectedInformationGain(e)
* Feasibility(e)
/
(1 + Cost(e) + Risk(e))
```

avec bonus possible pour :
- replication ;
- contre-hypothese ;
- dette epistemique elevee.


# 200. MVP experimental

### M0 - Claims/Evidence
- claims ;
- hypotheses ;
- evidence ;
- statuses.

### M1 - Predictions/Protocols
- prediction schema ;
- protocol versioning ;
- preregistration.

### M2 - Data QC
- manifests ;
- hashes ;
- transformations.

### M3 - Assessment
- evidence vector ;
- scope ;
- counterevidence ;
- status transitions.

### M4 - Replication
- independence groups ;
- replication records ;
- robustness.

### M5 - Evidence package
- manifest ;
- methods ;
- results ;
- limitations ;
- provenance.

### M6 - Integration
- MBC ;
- Orchestrator ;
- TimeWarp ;
- HUD.


# 201. Criteres d'acceptation

```text
A1. Un claim peut etre relie a plusieurs hypotheses.
A2. Chaque hypothese peut produire des predictions explicites.
A3. Les predictions post-hoc sont distinguees des preregistrees.
A4. Les protocoles sont versionnes.
A5. Les donnees possedent provenance et manifest.
A6. Evidence positive, negative et neutre sont conservees.
A7. Les sources dependantes sont identifiees.
A8. Simulation et observation restent separees.
A9. Le scope de chaque conclusion est explicite.
A10. Une replication independante peut modifier le statut.
A11. Une contradiction peut de-promouvoir un claim.
A12. Les exclusions sont tracees.
A13. Un audit epistemique peut reconstruire l'histoire.
A14. Les formulations causales non supportees sont signalees.
A15. Le moteur peut retourner INCONCLUSIVE.
A16. Un evidence package complet peut etre exporte.
A17. Les limitations sont derivees du protocole.
A18. Le moteur conserve les resultats negatifs.
A19. L'evidence requise augmente avec le risque de l'action.
A20. Les prochaines experiences sont classees par gain d'information.
```


# 202. Suite logique vers Structure 10

La prochaine structure logique peut etre le **moteur de modeles, simulation multi-mondes et laboratoire virtuel**.

Il formaliserait :
- world models ;
- hypotheses de dynamique ;
- simulateurs ;
- environnements ;
- digital twins conceptuels ;
- scenarios ;
- Monte Carlo ;
- counterfactuals ;
- comparaison prediction/observation ;
- calibration ;
- model selection ;
- ablations ;
- stress tests ;
- sandbox d'experiences.

La Structure 09 definit **comment une affirmation gagne ou perd du statut epistemique**.
La Structure 10 definirait **comment construire des mondes simulables pour tester ces affirmations avant ou en parallele d'experiences reelles**.
