# COSMOS-F - Structure 10
## Moteur de modeles, simulation multi-mondes et laboratoire virtuel

**Version :** 1.0  
**Statut :** specification conceptuelle, mathematique et technique  
**Dependances :** Structure 01 - NCU ; Structure 02 - Algebre COSMOS-F ; Structure 03 - Moteur de branches concurrentes ; Structure 04 - Zoom fractal ; Structure 05 - Regulateur multi-ressources ; Structure 06 - Stabilisation/recovery ; Structure 08 - Orchestrateur multi-espace ; Structure 09 - Moteur de preuve et validation epistemique.  
**Interface attendue :** Structure 07 - TimeWarp complet pour historiques, forks temporels et replay.

---

## Resume

La Structure 10 definit un laboratoire virtuel ou COSMOS-F peut construire plusieurs modeles d'un meme phenomene, instancier plusieurs mondes simules, appliquer des interventions, generer des scenarios, tester des contrefactuels, faire varier les parametres et comparer les sorties aux observations.

Le moteur ne simule jamais "la realite elle-meme". Il simule :

```text
un modele
+
des hypotheses
+
des conditions initiales
+
des parametres
+
des regles de transition
```

Le resultat est donc conditionnel.

Le pipeline general est :

```text
question
-> hypotheses
-> modeles candidats
-> monde(s) simule(s)
-> conditions initiales
-> interventions
-> simulation
-> traces
-> mesures
-> comparaison
-> calibration
-> stress tests
-> validation externe
```

Le laboratoire doit supporter :

```text
world models
digital twins conceptuels
multi-worlds
Monte Carlo
counterfactuals
scenario trees
agent-based models
state-space models
dynamical systems
discrete-event simulation
hybrid simulation
ablations
stress tests
sensitivity analysis
calibration
model selection
```

Principe central :

```text
MODEL != WORLD
SIMULATION != OBSERVATION
COUNTERFACTUAL != HISTORY
```

et :

```text
la valeur d'une simulation depend
de la qualite du modele,
de ses hypotheses,
de sa calibration
et de sa validation externe.
```

# 1. Mission de la Structure 10

La Structure 10 repond a :

```text
"Que se passerait-il dans ce modele si... ?"
```

Elle ne repond pas seule a :

```text
"Que s'est-il reellement passe ?"
```

ni :

```text
"Que va-t-il necessairement se passer ?"
```

Elle sert a :
- explorer ;
- comparer ;
- tester ;
- prevoir conditionnellement ;
- generer des experiences ;
- identifier des predictions discriminantes.


# 2. Objet Model

Un modele canonique :

```text
Model = (
  model_id,
  model_type,
  scope,
  state_space,
  parameters,
  transition_rules,
  observation_model,
  assumptions,
  invariants,
  calibration_state,
  validation_state,
  provenance,
  version
)
```

Le modele est versionne.

Une modification de structure produit une nouvelle revision.


# 3. Types de modeles

Taxonomie :

```text
SYMBOLIC
EQUATION_BASED
STATE_SPACE
MARKOV
BAYESIAN
AGENT_BASED
DISCRETE_EVENT
SYSTEM_DYNAMICS
CELLULAR
GRAPH_DYNAMICS
NEURAL
HYBRID
RULE_BASED
SURROGATE
DIGITAL_TWIN
```

Un meme probleme peut utiliser plusieurs types concurrents.


# 4. Modeles concurrents

Pour une question :

```text
M = {M1, M2, ..., Mk}
```

Le moteur ne selectionne pas automatiquement un seul modele.

Chaque modele peut porter :
- hypotheses differentes ;
- resolutions differentes ;
- couts differents ;
- scopes differents.


# 5. Scope d'un modele

Le scope definit :

```text
population
context
time_horizon
spatial_domain
resolution
variables
assumptions
```

Un modele local ne doit pas etre applique hors scope sans avertissement.


# 6. Espace d'etat

Un monde simule possede un etat :

```text
x_t in X
```

avec :

```text
X = X1 x X2 x ... x Xn
```

Les dimensions d'etat peuvent etre :
- continues ;
- discretes ;
- symboliques ;
- hybrides.


# 7. Etat observable et latent

On distingue :

```text
x_t = etat latent/interne
y_t = observation
```

avec :

```text
y_t = h(x_t, epsilon_t)
```

L'observation peut etre bruitée et partielle.


# 8. Transition

Dynamique :

```text
x_{t+1} = f(x_t, u_t, theta, eta_t)
```

ou :
- `u_t` : intervention ;
- `theta` : parametres ;
- `eta_t` : bruit/process noise.

La fonction `f` fait partie du modele, pas d'un fait observe.


# 9. Temps continu

Pour un systeme continu :

```text
dx/dt = F(x,t,u,theta)
```

Le moteur stocke :
- solveur ;
- tolerances ;
- pas ;
- methode d'integration.


# 10. Temps discret

Pour un systeme discret :

```text
x_{k+1} = F(x_k,u_k)
```

Utile pour :
- automates ;
- processus ;
- evenements ;
- simulation informatique.


# 11. Simulation evenementielle

Dans une simulation discrete-event :

```text
EventQueue = priority queue(time)
```

Boucle :

```text
pop next event
advance clock
apply transition
schedule new events
```

Le temps n'avance pas necessairement par pas uniforme.


# 12. Simulation hybride

Combine :
- dynamique continue ;
- evenements discrets ;
- regles symboliques.

Exemple :

```text
continuous process
-> threshold crossing
-> discrete mode switch
```

Les transitions de mode sont explicites.


# 13. Monde simule

Une instance :

```text
World = (
  world_id,
  model_ref,
  initial_state,
  parameters,
  environment,
  seed,
  timeline,
  interventions,
  status
)
```

Plusieurs mondes peuvent partager le meme modele.


# 14. Multi-mondes

```text
W = {W1, W2, ..., Wn}
```

Chaque monde peut varier :
- conditions initiales ;
- parametres ;
- seed ;
- intervention ;
- hypothese de structure.

Le moteur compare les distributions de sorties, pas seulement une trajectoire.


# 15. Monde de reference

`W_ref` contient :
- parametres de reference ;
- conditions initiales de base ;
- aucune intervention speciale.

Les scenarios sont compares a cette baseline.


# 16. Scenario

```text
Scenario = (
  scenario_id,
  baseline_world,
  modifications,
  horizon,
  metrics,
  rationale
)
```

Exemple :

```text
Scenario B = W_ref + intervention U at t=10
```


# 17. Arbre de scenarios

```text
ScenarioTree
```

Chaque noeud :
- etat ;
- decision ;
- evenement ;
- probabilite si definie.

Permet :
- branches futures ;
- contingences ;
- decisions conditionnelles.


# 18. Scenario non probabiliste

Une branche de scenario peut etre simplement :

```text
possible-but-unweighted
```

Le moteur n'invente pas une probabilite si aucune base ne la justifie.


# 19. Counterfactual

Un contrefactuel demande :

```text
"si U avait ete different, que produirait ce modele ?"
```

On compare :

```text
W_actual_modelled
vs
W_counterfactual
```

mais le contrefactuel reste une inference dependante du modele causal.


# 20. Contrefactuel != histoire

Invariant :

```text
counterfactual_output
!=
historical_fact
```

Meme si le modele est bien calibre, le resultat reste une estimation conditionnelle.


# 21. Interventions

Une intervention :

```text
do(U = u)
```

modifie explicitement le mecanisme ou une variable.

Elle se distingue d'une simple observation :

```text
observe(U=u)
```

Cette separation est importante pour causalite.


# 22. Monde causal

Un modele causal peut inclure :

```text
StructuralCausalModel
```

avec equations :

```text
X_i = f_i(PA_i, U_i)
```

Les contrefactuels utilisent ces relations structurelles.


# 23. Twin conceptuel

Un **digital twin conceptuel** represente un systeme par :
- etats ;
- relations ;
- dynamique ;
- mesures ;
- incertitudes.

Il n'implique pas une copie parfaite du monde reel.

Le statut de twin contient :

```text
FidelityLevel
```


# 24. Niveaux de fidelite

```text
F0 schematic
F1 qualitative
F2 calibrated coarse
F3 quantitatively validated
F4 high-fidelity in scoped conditions
```

Aucun niveau ne signifie "identique au monde".


# 25. Twin vivant

Un twin peut recevoir de nouvelles observations :

```text
observe -> assimilate -> update state
```

Le modele et les donnees restent distingues.

Une mise a jour d'etat ne modifie pas necessairement les equations du modele.


# 26. Assimilation de donnees

Methodes :
- Kalman ;
- Extended/Unscented Kalman ;
- particle filter ;
- Bayesian update ;
- optimisation.

Le choix depend du modele.


# 27. Conditions initiales

```text
x_0
```

peut etre :
- mesure ;
- estimation ;
- distribution.

Le moteur conserve la provenance de chaque composante.


# 28. Incertitude initiale

```text
x_0 ~ p(x_0)
```

La propagation de cette incertitude est distincte du bruit du modele.


# 29. Parametres

```text
theta = (theta1,...,thetap)
```

Chaque parametre a :
- domaine ;
- unite ;
- prior/range ;
- provenance ;
- calibration.


# 30. Parametres fixes vs variables

```text
FIXED
CALIBRATED
RANDOM
CONTROL
UNKNOWN
```

Un parametre inconnu n'est pas remplace silencieusement par une constante.


# 31. Hyperparametres

Pour les modeles numeriques/ML :

```text
hyperparameters
```

sont distingues des parametres du phenomene.

Ils sont versionnes.


# 32. Hypotheses structurelles

Exemples :
- linearite ;
- stationnarite ;
- independance ;
- conservation ;
- causalite.

Chaque hypothese structurelle est un objet inspectable.


# 33. Invariants du modele

Exemple :

```text
mass >= 0
probabilities sum to 1
graph identity preserved
```

Une simulation violant un invariant peut etre invalide ou indiquer un bug/model failure.


# 34. Contraintes

Contraintes :

```text
g(x,theta) <= 0
h(x,theta) = 0
```

Le solveur doit rapporter les violations.


# 35. Sources d'incertitude

```text
parameter_uncertainty
initial_state_uncertainty
measurement_uncertainty
process_noise
model_structural_uncertainty
numerical_error
scenario_uncertainty
```

Le moteur les garde separees.


# 36. Incertitude aleatoire vs epistemique

```text
aleatory
```

= variabilite irreductible dans le modele.

```text
epistemic
```

= manque de connaissance/modelisation.

Les politiques de reduction different.


# 37. Ensemble de modeles

Pour l'incertitude structurelle :

```text
ModelEnsemble = {M1,...,Mk}
```

La variance inter-modeles est conservee.


# 38. Monte Carlo

Algorithme :

```text
for i in 1..N:
  sample theta_i
  sample x0_i
  run world_i
collect outputs
```

Sortie :
- distribution ;
- quantiles ;
- probabilites conditionnelles au modele.


# 39. Monte Carlo reproductible

Conserver :
- master seed ;
- seeds enfants ;
- sampler ;
- distributions ;
- version du modele.

Un run doit pouvoir etre reexecute.


# 40. Quasi-Monte Carlo

Utilise sequences a faible discrepancy pour meilleure couverture de l'espace parametrique.

Le moteur distingue la methode d'echantillonnage.


# 41. Latin Hypercube

Pour design parametrique :

```text
LHS
```

peut couvrir plus efficacement les plages qu'un tirage independant simple.


# 42. Sensitivity analysis

Question :

```text
"quels parametres influencent le plus la sortie ?"
```

Methodes :
- local derivatives ;
- Morris ;
- Sobol ;
- variance decomposition.


# 43. Sensibilite locale

```text
S_i = dY/dtheta_i
```

valable autour d'un point.

Elle ne decrit pas forcement les effets globaux.


# 44. Sensibilite globale

Explore l'ensemble du domaine parametrique.

Peut reveler :
- interactions ;
- non-linearites ;
- regions critiques.


# 45. Indices de Sobol

Decomposent la variance :

```text
Var(Y)
```

en contributions de parametres et interactions.

Ils dependent des distributions de parametres choisies.


# 46. Analyse Morris

Screening peu couteux pour nombreux parametres.

Peut identifier :
- effets faibles ;
- effets non lineaires ;
- interactions probables.


# 47. Calibration

Calibration :

```text
theta* = argmin_theta Loss(sim(theta), observations)
```

ou methode bayesienne.

La calibration ajuste le modele a des donnees.


# 48. Calibration != validation

Un modele peut bien fitter les donnees de calibration et echouer hors echantillon.

Donc :

```text
CALIBRATED != VALIDATED
```


# 49. Donnees de calibration et validation

Separer :

```text
D_cal
D_val
D_test
```

si possible.

La fuite entre ensembles est tracee.


# 50. Loss de calibration

```text
L(theta) =
sum_j w_j d(y_sim_j, y_obs_j)
```

Les poids et distances sont documentes.


# 51. Calibration multi-objectifs

Plusieurs metriques :

```text
fit_A
fit_B
fit_C
```

peuvent etre en tension.

Conserver front Pareto des parametres.


# 52. Equifinalite

Plusieurs jeux de parametres peuvent produire des sorties proches.

```text
theta_a != theta_b
but
Y(theta_a) approx Y(theta_b)
```

Le moteur ne pretend pas identifier un parametre unique.


# 53. Identifiabilite

Un parametre est identifiable si les observations le contraignent suffisamment.

Le moteur peut signaler :

```text
NON_IDENTIFIABLE
```

ou `WEAKLY_IDENTIFIABLE`.


# 54. Calibration bayesienne

```text
p(theta|D) proportional p(D|theta)p(theta)
```

Sortie :
- posterior ;
- correlations ;
- uncertainty.

Le prior reste explicite.


# 55. Approximate Bayesian Computation

Si vraisemblance inaccessible :

```text
ABC
```

compare des statistiques resumees.

Le seuil de tolerance affecte fortement la posterior approximative.


# 56. Surrogate model

Une simulation couteuse peut etre approximee par :

```text
M_surrogate
```

Exemples :
- Gaussian Process ;
- neural surrogate ;
- polynomial chaos.

Le surrogate a son propre domaine de validite.


# 57. Surrogate fidelity

Le moteur mesure :

```text
error_surrogate
```

sur un ensemble de validation.

Il ne remplace le modele lourd que si l'erreur reste acceptable.


# 58. Multi-fidelity

Combiner :
- modele rapide grossier ;
- modele couteux precis.

Strategie :

```text
coarse search
-> refine interesting regions
```

Compatible avec Structure 05.


# 59. Emulateur

Un emulateur apprend la fonction entree-sortie d'un simulateur.

Il est different d'un twin si la dynamique interne n'est pas representee.


# 60. Agent-based model

ABM :

```text
Agents = {a_i}
Rules(a_i)
Environment
Interactions
```

Les comportements emergents sont des sorties du modele, pas des preuves du monde.


# 61. Agent heterogeneity

Les agents peuvent varier :
- parametres ;
- strategie ;
- memoire ;
- role.

Les distributions sont explicites.


# 62. Regles d'interaction

```text
Interact(a_i,a_j,context)
```

doit etre versionnee.

Une modification de regle change le modele.


# 63. System dynamics

Stocks/flows :

```text
dStock/dt = inflows - outflows
```

Utile pour boucles de feedback a echelle agregée.


# 64. Boucles de feedback

```text
reinforcing
balancing
```

Le moteur peut analyser :
- gain ;
- delai ;
- stabilite.


# 65. Markov model

Transition :

```text
P(X_{t+1}=j | X_t=i)
```

Hypothese Markov explicite.

Si memoire plus longue necessaire, changer le modele.


# 66. Hidden Markov

Etat latent + observations.

Utilisable pour inference de regimes cachés.


# 67. State-space model

Generalise :

```text
x_{t+1}=f(x_t)+noise
y_t=h(x_t)+noise
```

Compatible assimilation.


# 68. Graph dynamics

Etat sur graphe :

```text
x_v(t)
```

avec propagation via voisins/hyperaretes.

Utile pour COSMOS-F lui-meme.


# 69. Cellular automata

Regles locales :

```text
cell_{t+1}=F(neighborhood_t)
```

Emergence et sensibilite aux conditions initiales peuvent etre etudiees.


# 70. Modeles symboliques

Regles logiques :

```text
IF condition THEN transition
```

Permettent explicabilite forte mais peuvent etre rigides.


# 71. Neural world models

Un modele neuronal peut apprendre :

```text
latent dynamics
```

Il doit etre traite comme modele statistique avec validation, pas comme oracle.


# 72. Hybrid symbolic-neural

Combiner :
- contraintes symboliques ;
- approximation neuronale ;
- solveurs.

Chaque sous-partie a sa provenance.


# 73. Observation model

Le simulateur peut generer un etat "vrai du modele" puis simuler l'instrument :

```text
y = h(x) + epsilon
```

Cela permet de comparer au type de donnees reellement observe.


# 74. Instrument virtuel

Objet :

```text
VirtualInstrument = (
  resolution,
  noise,
  bias,
  saturation,
  sampling_rate
)
```

Utile pour tester l'effet des limites de mesure.


# 75. Missingness simulee

Le laboratoire peut injecter :
- pertes de mesures ;
- latence ;
- dropout.

Permet de tester robustesse du pipeline.


# 76. Bruit

Types :
- Gaussian ;
- Poisson ;
- correlated ;
- colored ;
- empirical.

Le choix de bruit fait partie des hypotheses.


# 77. Perturbations

```text
Perturbation = delta applied to state/parameter/environment
```

Utilisee pour :
- robustness ;
- recovery ;
- stress.


# 78. Stress test

Question :

```text
"jusqu'ou le modele reste-t-il stable ?"
```

On explore :
- extremes ;
- combinaisons ;
- adversarial conditions ;
- resource limits.


# 79. Stress surface

Construire :

```text
FailureRegion(theta,u)
```

avec :
- stable ;
- degraded ;
- failed.

La frontiere est elle-meme une sortie.


# 80. Boundary search

Algorithme adaptatif pour chercher la frontiere de panne.

Plus efficace qu'un grid exhaustif.


# 81. Adversarial scenario generation

Generer des scenarios qui maximisent :
- erreur ;
- violation ;
- instabilite.

Sous contraintes realistes ou definies.


# 82. Robustness margin

```text
margin = distance to failure boundary
```

dans une metrique declaree.

Le sens du margin depend du modele.


# 83. Ablation

Supprimer/neutraliser un composant :

```text
M \ component_k
```

Comparer les sorties.

Permet d'evaluer contribution modellee.


# 84. Ablation causale prudente

Dans un simulateur, une ablation montre la consequence **dans le modele**.

Elle ne prouve pas automatiquement la causalite dans le monde.


# 85. Parameter sweep

```text
grid/random/adaptive sweep
```

avec limite de budget.

Les resultats sont indexables dans l'atlas multi-echelle.


# 86. Adaptive sweep

Le moteur densifie les regions :
- de transition ;
- de forte incertitude ;
- de fort gradient.

Economise des runs.


# 87. Active learning

Choisit prochain point de simulation pour maximiser information sur la fonction.

Utile avec surrogate.


# 88. Bayesian optimization

Pour optimiser une sortie couteuse :

```text
acquisition(x)
```

choisit prochain run.

La fonction objectif et contraintes sont explicites.


# 89. Optimisation != explication

Trouver une configuration optimale du modele ne dit pas pourquoi le monde reel fonctionne ainsi.

Invariant important.


# 90. Design d'experience virtuel

Le laboratoire peut chercher :

```text
which real experiment would best discriminate models?
```

Il utilise les mondes pour estimer gain d'information.


# 91. Virtual trial

Tester d'abord un protocole dans plusieurs modeles pour :
- detecter ambiguite ;
- verifier sensibilite ;
- choisir metriques.

Le trial virtuel ne remplace pas un essai reel.


# 92. Scenario replay

Rejouer le meme scenario avec :
- autre version modele ;
- autre seed ;
- autre parametre.

TimeWarp stockera les revisions.


# 93. Deterministic replay

Possible si :
- model version ;
- seed ;
- inputs ;
- solver ;
- environment
sont fixes.

Sinon statut :

```text
STATISTICALLY_REPRODUCIBLE
```


# 94. Numerical reproducibility

Sur GPU/parallelisme, le bitwise exact peut etre impossible.

Le moteur peut definir tolerances :

```text
abs_error <= eps_abs
rel_error <= eps_rel
```


# 95. Solver error

Distinguer :
- erreur de modele ;
- erreur numerique.

Convergence tests :
- pas plus petit ;
- precision accrue ;
- solveur alternatif.


# 96. Convergence

Une simulation numerique peut exiger :

```text
result(h) -> limit as h -> 0
```

Le moteur peut estimer ordre de convergence.


# 97. Stability numerique

Un solveur instable peut produire explosion artificielle.

Le moteur associe des diagnostics numeriques.


# 98. Conservation numerique

Comparer invariants :
- energie ;
- masse ;
- probabilite ;
- identites.

Derive excessive signale un probleme.


# 99. Model diagnostics

Diagnostics :
- residuals ;
- autocorrelation ;
- bias ;
- heteroscedasticity ;
- calibration ;
- failure modes.


# 100. Residual analysis

```text
r_t = y_obs - y_sim
```

Structure des residuals peut reveler :
- mecanisme manquant ;
- drift ;
- mauvaise observation model.


# 101. Residual branch

Une structure residuelle persistante devient une nouvelle branche d'hypothese dans le MBC.

Le laboratoire nourrit donc la Structure 03.


# 102. Calibration curve

Pour predictions probabilistes :
- bins ;
- predicted vs observed.

Un modele peut etre discriminant mais mal calibre.


# 103. Forecast skill

Comparer a baseline :

```text
skill = 1 - error_model/error_baseline
```

La baseline est explicite.


# 104. Backtesting

Simuler une prediction sur donnees historiques en respectant :

```text
only information available at time t
```

Evite leakage temporel.


# 105. Walk-forward validation

Cycle :

```text
train/calibrate until t
predict t+1
advance
```

Adapté aux processus temporels.


# 106. Hindcasting

Predire des periodes passees non utilisees dans calibration.

Utile pour validation de modeles dynamiques.


# 107. Out-of-distribution

Tester regions eloignees du domaine calibration.

Le moteur calcule un `OOD_score`.


# 108. Extrapolation warning

Si :

```text
query outside validated domain
```

sortie marquee :

```text
EXTRAPOLATION
```

avec confiance reduite.


# 109. Domain of validity

Carte :

```text
ValidDomain(M)
```

contient :
- parametric region ;
- conditions ;
- horizon ;
- resolution.


# 110. Horizon de prediction

L'incertitude croit souvent avec horizon.

```text
U(t+h)
```

Le moteur peut definir un horizon utile.


# 111. Predictability horizon

Premier horizon ou :
- intervalle trop large ;
- skill sous baseline ;
- divergence modeles trop forte.

Au-dela, sortie "scenario" plutot que prediction precise.


# 112. Chaos et sensibilite initiale

Pour systemes chaotiques :

```text
delta x_0 -> exponential divergence
```

Le moteur peut estimer Lyapunov-like sensitivity.


# 113. Ensemble forecasts

Lancer plusieurs conditions initiales/parametres.

Sortie :
- mediane ;
- spread ;
- extremes.

L'ensemble represente une incertitude conditionnelle.


# 114. Model averaging

Combiner plusieurs modeles si politique justifiee.

Les poids :
- uniformes ;
- performance ;
- posterior.

La moyenne ne doit pas cacher une bimodalite importante.


# 115. Mixture output

Conserver distributions multi-modales si plusieurs modeles divergent.

Eviter de resumer uniquement par moyenne.


# 116. Model selection

Critere :
- predictive performance ;
- complexity ;
- calibration ;
- robustness ;
- interpretability ;
- cost.

Selection peut etre contexte-dependante.


# 117. Occam penalty

Un modele plus complexe doit apporter un gain suffisant.

Mais "plus simple" ne signifie pas automatiquement "plus vrai".


# 118. Information criteria

AIC/BIC-like selon modeles compatibles.

Le moteur stocke assumptions.


# 119. Cross-model consistency

Tester les conclusions qui restent stables a travers plusieurs modeles.

```text
robust_across_models
```

peut augmenter confiance dans une prediction qualitative.


# 120. Structural disagreement

Si les modeles donnent des sorties opposees :
- ne pas moyenner silencieusement ;
- chercher experience discriminante.


# 121. Model discrepancy

Ajouter un terme :

```text
y_real = y_model + delta_model + noise
```

pour reconnaitre que le modele peut etre structurellement imparfait.


# 122. Calibration with discrepancy

Evite de forcer les parametres a absorber toutes les erreurs de structure.


# 123. Digital twin sync

Boucle :

```text
observe
-> compare
-> assimilate
-> diagnose discrepancy
-> maybe recalibrate
```

Les recalibrations sont versionnees.


# 124. Drift detection

Si residuals changent :
- data drift ;
- concept drift ;
- model drift.

Le moteur peut de-promouvoir le twin.


# 125. Twin health

```text
TwinHealth = (
  data_freshness,
  calibration_error,
  residual_bias,
  sync_latency,
  model_version
)
```

Statuts :
- HEALTHY ;
- DEGRADED ;
- STALE ;
- INVALID.


# 126. Sandbox

Toute simulation s'execute dans :

```text
SimulationSandbox
```

avec :
- fichiers isoles ;
- ressources ;
- permissions ;
- seed ;
- limites.

Aucune sortie ne devient action externe sans orchestration/gates.


# 127. Resource contract

Chaque run declare :

```text
SimulationCost = (
  CPU,
  GPU,
  RAM,
  VRAM,
  time,
  tokens,
  storage
)
```

La Structure 05 admet ou degrade le run.


# 128. Checkpoint de simulation

Pour run long :
- state snapshot ;
- RNG state ;
- clock ;
- pending events.

Permet pause/reprise.


# 129. Resume

`RESUME_SIM` verifie :
- model version ;
- state schema ;
- seed state ;
- compatibility.

Sinon migration ou restart.


# 130. Failure handling

Types :
- numerical ;
- resource ;
- invalid state ;
- invariant violation ;
- code error.

Le run produit un `SimulationIncident`.


# 131. Recovery simulation

QuickResolution/Doctor peuvent reparer l'environnement du simulateur sans modifier l'hypothese scientifique.


# 132. Crash consistency

Un run partial est marque :

```text
PARTIAL_RUN
```

Ses resultats ne sont pas utilises comme run complet sans politique.


# 133. Simulation ledger

Chaque run :

```text
RunRecord = (
  run_id,
  model_version,
  inputs,
  seed,
  solver,
  environment,
  outputs,
  metrics,
  status,
  provenance
)
```

Append-only.


# 134. Experiment campaign

Une campagne :

```text
Campaign = {
  runs,
  design,
  objective,
  budget,
  stop_rules
}
```

Exemple :
- sweep ;
- Monte Carlo ;
- calibration ;
- stress.


# 135. Campaign scheduler

Priorise :
- information gain ;
- frontier regions ;
- failures ;
- replication.

Compatible orchestration 08.


# 136. Stop rules de campagne

Arret si :
- precision atteinte ;
- budget ;
- convergence ;
- aucun gain ;
- risque ;
- bug.

Stop reason journalise.


# 137. Early stopping

Une campagne peut s'arreter avant N si la distribution est suffisamment stabilisee selon critere preregistre.


# 138. Sequential design

Les prochains runs dependent des precedents.

Chaque adaptation est tracee afin de distinguer design adaptatif d'un grid fixe.


# 139. Parallel runs

Runs independants peuvent etre parallelises.

Le regulateur evite saturation.


# 140. Deterministic partitioning

Attribuer seeds/ranges sans duplication entre workers.


# 141. Distributed simulation

Si distribue :
- task IDs ;
- idempotence ;
- result checksums ;
- retry.

Le coeur peut rester local-first.


# 142. Result aggregation

L'agregateur collecte :
- runs valides ;
- failed ;
- censored ;
- partial.

Il ne jette pas silencieusement les echecs.


# 143. Failure rate as output

Le taux de runs qui echouent peut etre scientifiquement/techniquement informatif.

Il est conserve.


# 144. Visualisation des mondes

Le HUD peut afficher :
- timelines ;
- trajectories ;
- parameter maps ;
- distributions ;
- scenario tree ;
- failure regions.


# 145. Vue comparee

```text
World A | World B | Observed
```

avec :
- differences ;
- metrics ;
- confidence ;
- model version.


# 146. Carte de parametres

Heatmap/atlas :
- x = theta1 ;
- y = theta2 ;
- color/value = metric.

Les zones non simulees restent distinctes des zones faibles.


# 147. Navigation multi-echelle

La Structure 04 permet :
- zoom sur une region parametrique ;
- ouvrir un run ;
- ouvrir un timestep ;
- remonter vers campagne.

Chaque niveau garde le chemin.


# 148. NCU et simulation

Un model, world, run, intervention ou metric peut etre represente comme NCU.

Relations :
- INSTANCE_OF ;
- SIMULATES ;
- DERIVED_FROM ;
- COMPARES_TO.


# 149. MBC et simulation

Chaque modele peut correspondre a une branche.

Resultats :
- augmentent support ;
- diminuent support ;
- generent nouvelles hypotheses.

Mais la Structure 09 decide le statut epistemique.


# 150. Structure 09 et validation

Pipeline :

```text
simulation prediction
-> real protocol
-> observation
-> compare
```

La simulation aide a concevoir les tests ; elle ne remplace pas l'observation.


# 151. Structure 08 et campagnes

Orchestrateur :
- decomposes goal ;
- planifie campagnes ;
- alloue agents ;
- synthese les sorties.

Le simulateur execute.


# 152. Structure 06 et checkpoints

Avant modifications de modele/campagne :
- checkpoint ;
- version ;
- rollback.

Les anciens runs restent lies a l'ancienne version.


# 153. Structure 05 et degradation

Si VRAM faible :
- moins de parallelisme ;
- surrogate ;
- precision reduite ;
- smaller batch.

Toute degradation est visible.


# 154. TimeWarp interface

TimeWarp doit pouvoir repondre :
- quel modele etait actif a t ?
- quel seed ?
- quelle calibration ?
- quel scenario ?

Cela permet replay historique.


# 155. Versioning modele

```text
M1.0
M1.1
M2.0
```

Changement structurel majeur -> major version.

Parametre calibre -> calibration revision distincte.


# 156. Semantic versioning scientifique

Possible convention :
- major : equations/structure ;
- minor : nouvelles sorties/instruments ;
- patch : bug implementation sans changement theorique.

Un bug corrige peut invalid er les runs precedents.


# 157. Run invalidation

Si bug :
- identifier versions affectees ;
- marquer `INVALIDATED`;
- ne pas supprimer ;
- re-run prioritaire.


# 158. Regression simulation

Comparer sortie avant/apres changement de code.

Differences attendues vs inattendues.


# 159. Golden scenarios

Ensemble de scenarios de reference avec resultats attendus/tolerances.

Utiles pour detecter regressions.


# 160. Unit tests du modele

Tester :
- equations locales ;
- invariants ;
- edge cases ;
- dimensions/unités.


# 161. Property tests

Generer etats aleatoires respectant contraintes et verifier invariants.


# 162. Metamorphic tests

Exemples :
- symetrie ;
- permutation ;
- scaling ;
- conservation.

Particulierement utiles sans oracle.


# 163. Cross-solver validation

Executer deux solveurs independants sur cas compatibles.

Divergence signale investigation.


# 164. Analytical benchmark

Si solution analytique existe pour cas simple :
- comparer au simulateur.

Tres utile pour verification.


# 165. Dimensional analysis

Verifier unites :

```text
[L]/[T]
```

et coherence des equations.

Peut detecter erreurs avant simulation.


# 166. Symbolic checks

Si expressions symboliques :
- simplification ;
- derivatives ;
- limits ;
- invariants.

Le formalisme peut etre verifie avant code.


# 167. Precision arithmetic

Choix :
- float32 ;
- float64 ;
- arbitrary precision.

Le moteur peut tester sensibilite numerique.


# 168. Floating-point audit

Detecter :
- overflow ;
- underflow ;
- catastrophic cancellation ;
- NaN/Inf.

Un run avec NaN ne devient pas donnees valides.


# 169. Units registry

Chaque variable declare unite.

Conversions via registry.

Evite melange implicite.


# 170. Coordinate frames physiques

Si simulation spatiale :
- frame ;
- origine ;
- orientation ;
- units.

Transformation explicite, analogue Structure 04 mais typée physique.


# 171. Coordinate frames conceptuelles

Pour modeles non physiques :
- axes descriptifs ;
- embeddings ;
- scales.

Ne pas les appeler dimensions physiques sans justification.


# 172. Multi-resolution simulation

Modeles a plusieurs echelles :
- macro ;
- meso ;
- micro.

Couplage explicite.


# 173. Coarse graining

Passage micro -> macro avec fonction :

```text
C(x_micro)=x_macro
```

et perte mesuree.


# 174. Refinement

Macro -> micro n'est pas inverse exact.

Il genere un ensemble de micro-etats compatibles.


# 175. Couplage multi-echelle

Echanges :
- boundary conditions ;
- aggregate variables ;
- feedback.

Le risque de double comptage est controle.


# 176. Co-simulation

Plusieurs simulateurs :

```text
SimA <-> SimB
```

avec synchronisation temporelle et contrat d'interface.


# 177. Master algorithm

Le co-sim master gere :
- clock ;
- data exchange ;
- step negotiation ;
- rollback si supporte.


# 178. Algebraic loops

Deux simulateurs peuvent dependre instantanement l'un de l'autre.

Strategies :
- iteration ;
- lag ;
- solver conjoint.

Le choix affecte la sortie.


# 179. Discrete-continuous co-sim

Exemple :
- controller discret ;
- plant continu.

Synchronisation evenement/pas explicitée.


# 180. Real-time simulation

Contrainte :

```text
compute_time_per_step <= wall_time_step
```

Sinon degradation ou offline.


# 181. Faster-than-real-time

Utile pour prediction/scenario.

Facteur :

```text
RTF = simulated_time / wall_time
```


# 182. Slower-than-real-time

Mode haute fidelite.

Le HUD ne doit pas faire croire a une prediction immediate.


# 183. Human-in-the-loop simulation

Un humain peut intervenir :
- decisions ;
- classification ;
- control.

Les interventions sont journalisees.


# 184. Agent-in-the-loop

Un agent IA peut prendre des decisions dans le monde simule.

Ses politiques/version sont partie du modele.


# 185. Policy evaluation

Comparer politiques :

```text
pi1, pi2, ...
```

sur memes seeds/scenarios pour variance controlee.


# 186. Common random numbers

Utiliser memes seeds pour comparer politiques peut reduire variance de difference.

Le design est documente.


# 187. Control variates

Technique Monte Carlo pour reduction de variance lorsque reference connue.


# 188. Importance sampling

Echantillonner davantage regions rares.

Les poids sont conserves pour estimation correcte.


# 189. Rare event simulation

Methodes specialisees pour pannes tres rares.

Le moteur ne conclut pas "zero risque" parce qu'aucun evenement n'apparait dans peu de runs.


# 190. Reliability estimation

```text
P(failure before T)
```

conditionnel au modele.

Intervals et nombre d'events sont affiches.


# 191. Survival simulation

Temps jusqu'a evenement.

Peut utiliser hazard functions ou processus simules.


# 192. Queue simulation

Modeles de files :
- arrivals ;
- service ;
- capacity.

Mesures :
- waiting time ;
- utilization ;
- overflow.


# 193. Network simulation

Noeuds/liens :
- latence ;
- capacity ;
- failures ;
- routing.

Peut etre utilise pour architecture logicielle.


# 194. Software-system twin

Twin logiciel :
- services ;
- resources ;
- dependencies ;
- requests ;
- failures.

Permet tester :
- load ;
- recovery ;
- scaling ;
- fault injection.


# 195. Fault injection virtuel

Injecter :
- service down ;
- packet loss ;
- disk full ;
- GPU OOM ;
- corrupted config.

Tester Doctor/ConnectBusBot/Immune en sandbox.


# 196. Recovery simulation

Comparer politiques de recovery :
- restart ;
- rollback ;
- failover ;
- quarantine.

Mesurer :
- MTTR ;
- data loss ;
- blast radius.


# 197. Security simulation prudent

Le moteur peut simuler des defaillances et politiques de protection dans un environnement controle.

La simulation reste centree sur resilience, configuration et tests autorises.


# 198. Economic scenario model

Modeles cout/ressource :
- budget ;
- demande ;
- capacité.

Les sorties dependent fortement des hypotheses et ne sont pas des garanties.


# 199. Decision simulation

Comparer decisions :

```text
ExpectedUtility(pi)
```

avec distributions.

La fonction d'utilite doit etre explicite.


# 200. Multi-criteria outcomes

Conserver :
- cout ;
- qualite ;
- risque ;
- latence ;
- impact.

Pas de scalarisation obligatoire.


# 201. Pareto worlds

Identifier scenarios/configurations non domines.

Utile pour explorer compromis.


# 202. Scenario narratives

Le moteur peut generer une description lisible d'un monde :

```text
assumptions
key events
outputs
uncertainties
```

Mais la narration reference toujours le run.


# 203. Explanation trace

Pour une sortie :

```text
why did metric Y change?
```

Le moteur peut produire :
- parameter sensitivity ;
- event chain ;
- intervention effects.

Selon explicabilite du modele.


# 204. Causal trace in simulation

Les evenements peuvent avoir :
- causation IDs ;
- dependencies.

Permet remonter des chaines internes au modele.


# 205. Attribution

Methodes :
- ablation ;
- Shapley-like ;
- derivatives ;
- causal interventions.

Le sens depend du type de modele.


# 206. Explainability limits

Une explication du modele n'est pas automatiquement une explication du monde reel.

Toujours indiquer :

```text
"within model M"
```


# 207. Simulation artifact package

```text
SimulationPackage/
  model/
  manifest.json
  scenarios/
  runs/
  seeds/
  outputs/
  metrics/
  calibration/
  validation/
  plots/
  limitations.md
  provenance/
```

Exportable vers Structure 09.


# 208. Manifest

```json
{
  "model_id": "M10",
  "version": "1.2.0",
  "campaign": "stress-001",
  "runs": 500,
  "solver": "solver-x",
  "validated_scope": ["..."],
  "known_limitations": ["..."]
}
```


# 209. Model card

Chaque modele possede :
- purpose ;
- scope ;
- equations/rules ;
- parameters ;
- data ;
- calibration ;
- validation ;
- known failures ;
- computational cost.


# 210. World card

Chaque monde :
- model version ;
- initial state ;
- interventions ;
- seed ;
- horizon ;
- status.

Permet comparaison reproducible.


# 211. Campaign report

Sections :
- objective ;
- design ;
- run count ;
- failures ;
- distributions ;
- sensitivity ;
- conclusions ;
- limitations ;
- next experiments.


# 212. API

```text
POST /models
GET  /models/{id}
POST /models/{id}/validate
POST /worlds
POST /runs
POST /campaigns
POST /campaigns/{id}/start
POST /campaigns/{id}/pause
POST /campaigns/{id}/resume
GET  /campaigns/{id}/results
POST /counterfactuals
POST /calibration
POST /sensitivity
POST /stress
POST /packages/simulation
```


# 213. DSL

```text
MODEL LOAD M1
WORLD CREATE baseline FROM M1
WORLD FORK stress FROM baseline
SET stress.parameter:load = 1.8
INTERVENE stress.service:A = down AT t=30
RUN baseline,stress HORIZON 120
COMPARE metric:latency
SENSITIVITY parameters all
MONTE_CARLO N=1000
EXPORT SIMULATION_PACKAGE
```


# 214. Schema SQLite

```sql
models(
  model_id TEXT,
  version TEXT,
  type TEXT,
  scope_json TEXT,
  manifest_json TEXT,
  PRIMARY KEY(model_id, version)
);

worlds(
  world_id TEXT PRIMARY KEY,
  model_id TEXT,
  model_version TEXT,
  initial_state_json TEXT,
  parameters_json TEXT
);

runs(
  run_id TEXT PRIMARY KEY,
  world_id TEXT,
  seed TEXT,
  status TEXT,
  solver_json TEXT,
  started_at TEXT,
  ended_at TEXT
);

run_metrics(
  run_id TEXT,
  metric TEXT,
  value_json TEXT
);

campaigns(
  campaign_id TEXT PRIMARY KEY,
  design_json TEXT,
  status TEXT
);

model_validations(
  validation_id TEXT PRIMARY KEY,
  model_id TEXT,
  model_version TEXT,
  payload_json TEXT
);
```


# 215. Architecture logicielle

```text
SimulationLab
├── ModelRegistry
├── WorldManager
├── ScenarioManager
├── InterventionEngine
├── SolverAdapter
├── CampaignManager
├── MonteCarloEngine
├── SensitivityEngine
├── CalibrationEngine
├── CounterfactualEngine
├── StressEngine
├── SurrogateManager
├── ValidationEngine
├── RunLedger
├── PackageBuilder
├── RegulatorAdapter
├── RecoveryAdapter
├── MBCAdapter
├── EpistemicAdapter
├── TimeWarpAdapter
└── HUDAdapter
```


# 216. Invariants

```text
M1. Model != world.
M2. Simulation != observation.
M3. Counterfactual != history.
M4. Every run references an immutable model version.
M5. Every stochastic run records its seed strategy.
M6. Calibration != validation.
M7. Failed runs are preserved.
M8. Invalidated runs are never silently reused.
M9. Extrapolation is marked.
M10. Solver/model errors are distinguished when possible.
M11. Any real-world claim leaves the simulation layer through Structure 09.
M12. Sandbox outputs cannot trigger external effects without Structure 08/OCCP.
```


# 217. Tests unitaires

```text
test_world_references_model_version
test_seed_reproducibility
test_counterfactual_label
test_failed_run_preserved
test_invariant_violation
test_solver_tolerance
test_calibration_validation_split
test_extrapolation_warning
test_checkpoint_resume
test_campaign_stop_rule
test_invalidated_run
test_package_manifest
```


# 218. Tests de proprietes

```text
P1. Same deterministic input/version produces same output within tolerance.
P2. Every output is traceable to model, world, run and seed.
P3. A run from invalid model version cannot be promoted as valid.
P4. No simulation record is typed OBSERVATION.
P5. Every model version change preserves historical runs.
```


# 219. Chaos tests

Injecter :
- solver crash ;
- GPU OOM ;
- corrupt checkpoint ;
- missing model ;
- NaN propagation ;
- worker duplicate ;
- network loss in co-sim.

Verifier :
- detection ;
- no duplicate output ;
- recovery ;
- provenance.


# 220. Performance tests

Mesurer :
- runs/sec ;
- simulated time/sec ;
- RAM/VRAM ;
- scaling ;
- checkpoint overhead ;
- aggregation time.


# 221. Validation benchmarks

Maintenir :
- simple analytical systems ;
- known queue ;
- known oscillator ;
- deterministic automaton.

Ils servent de tests de verification du moteur.


# 222. Observabilite

Metriques :

```text
models_registered
worlds_active
runs_completed
runs_failed
simulation_rate
gpu_hours
mean_solver_error
campaign_progress
calibration_error
validation_error
extrapolation_count
counterfactual_count
stress_failure_count
```


# 223. HUD du laboratoire

Vue :

```text
Model: M1 v1.2
Worlds: 8
Campaign: MonteCarlo-04
Runs: 732/1000
Failures: 4
Calibration: PASS
Validation: DEGRADED
Current load: YELLOW
```

Actions :
- fork world ;
- intervene ;
- run ;
- compare ;
- calibrate ;
- stress ;
- export.


# 224. Vue World Tree

```text
baseline
├─ intervention-A
│  ├─ seed-1
│  └─ seed-2
└─ intervention-B
```

Chaque noeud est ouvrable.


# 225. Vue timeline

Afficher :
- interventions ;
- events ;
- state variables ;
- metrics.

Zoom temporel via interface Structure 07/TimeWarp.


# 226. Vue uncertainty

Afficher :
- ensembles ;
- intervals ;
- distributions ;
- model spread.

Eviter une seule courbe donnant illusion de precision.


# 227. Exemple A - logiciel local

Construire un twin conceptuel d'une stack :
- UI ;
- API ;
- model router ;
- Ollama ;
- GPU ;
- storage.

Injecter :
- provider absent ;
- GPU saturation ;
- config mismatch.

Comparer politiques de recovery.


# 228. Exemple B - espace conceptuel

Simuler l'ouverture COSMOS-F :
- branches ;
- dimensions ;
- budget.

Tester :
- seuils de pruning ;
- vitesse d'ouverture ;
- stabilisation.

La simulation aide a choisir des politiques avant implementation.


# 229. Exemple C - protocole scientifique

Deux modeles predicts transitions differentes.

Le laboratoire :
- calibre ;
- simule ;
- trouve region ou predictions divergent le plus ;
- propose une experience reelle.

Structure 09 gere ensuite la validation.


# 230. Exemple D - stress recovery

World baseline :
- tous services OK.

Scenarios :
- service down ;
- bus disconnected ;
- Doctor unavailable.

Comparer :
- QuickResolution ;
- ConnectBusBot ;
- Immune.

Mesures :
- MTTR ;
- rollback ;
- data loss.


# 231. Exemple E - Monte Carlo

Parametres incertains :

```text
theta1 ~ ...
theta2 ~ ...
```

1000 runs.

Sortie :
- distribution de metric ;
- sensitivity ;
- rare failures.

Conclusion limitee au modele et distributions choisies.


# 232. Exemple F - contrefactuel

Observation historique `O`.

Modele causal calibre.

Question :

```text
"si intervention U n'avait pas eu lieu ?"
```

Le moteur produit distribution contrefactuelle et intervalle, jamais un fait historique alternatif.


# 233. Exemple G - ablation

Retirer composant `C`.

Comparer performance.

Si forte baisse dans simulation :
- C est important dans le modele.

Pour claim reel :
- demande validation externe.


# 234. Exemple H - multi-fidelity

Modele coarse explore 100k configurations.

Modele high-fidelity reevalue top 200.

Surrogate apprend la region interessante.

Le regulateur alloue budget.


# 235. Equation maitresse

Modele :

```text
M = (X,U,Theta,F,H,A)
```

Monde :

```text
W_i = (M, x0_i, theta_i, seed_i, interventions_i)
```

Evolution :

```text
x_{t+1}^{(i)}
=
F_M(
  x_t^{(i)},
  u_t^{(i)},
  theta_i,
  eta_t^{(i)}
)
```

Observation simulee :

```text
y_t^{(i)}
=
H_M(
  x_t^{(i)},
  epsilon_t^{(i)}
)
```

Campagne :

```text
C = {W_1,...,W_N}
```

Evaluation contre donnees :

```text
L(M)
=
Distance(
  Simulated(M),
  Observed
)
```

avec la condition :

```text
L(M) faible
does not imply
M is uniquely true
```


# 236. Equation de choix d'experience

Pour un test reel candidat `e` :

```text
Value(e) =
ExpectedModelDiscrimination(e)
* Feasibility(e)
/
(1 + Cost(e) + Risk(e))
```

Le laboratoire sert a estimer ce terme avant Structure 09.


# 237. Equation de robustesse

```text
Robustness(M, region)
=
1
-
FailureMeasure(region)
```

selon une mesure declaree.

Le choix de region est partie du claim.


# 238. Equation de fidelite twin

```text
Fidelity =
w1*state_match
+w2*dynamics_match
+w3*forecast_skill
+w4*calibration
-w5*drift
```

Le vecteur detaille reste conserve.


# 239. Equation d'incertitude totale

Conceptuellement :

```text
U_total =
U_initial
+ U_parameter
+ U_process
+ U_measurement
+ U_numerical
+ U_structural
```

La combinaison exacte depend du modele.

Le moteur ne suppose pas une simple addition si les termes sont dependants.


# 240. Equation de priorite de run

```text
Priority(run) =
ExpectedInformationGain
* DecisionRelevance
* Novelty
/
(1 + ComputeCost + Risk + Redundancy)
```

Permet campagnes adaptatives.


# 241. MVP experimental

### M0 - Model/World/Run
- registry ;
- versions ;
- seeds ;
- ledger.

### M1 - Solvers
- discrete ;
- continuous ;
- event.

### M2 - Campaigns
- sweep ;
- Monte Carlo ;
- compare.

### M3 - Calibration/Validation
- datasets ;
- loss ;
- residuals ;
- scope.

### M4 - Counterfactual/Stress
- interventions ;
- forks ;
- failure maps.

### M5 - Multi-fidelity
- surrogate ;
- adaptive sampling.

### M6 - Integration
- MBC ;
- Structure 09 ;
- Orchestrator ;
- Regulator ;
- Recovery ;
- TimeWarp.

### M7 - HUD/Export
- world tree ;
- timelines ;
- distributions ;
- SimulationPackage.


# 242. Criteres d'acceptation

```text
A1. Un modele est versionne et separé des mondes.
A2. Un monde reference conditions initiales, parametres et seed.
A3. Une simulation n'est jamais typee comme observation.
A4. Un contrefactuel est explicitement etiquete.
A5. Plusieurs modeles concurrents peuvent etre compares.
A6. Monte Carlo produit distributions et provenance.
A7. Les parametres incertains ont des ranges/distributions explicites.
A8. Calibration et validation utilisent des etapes distinctes.
A9. Les runs echoues restent visibles.
A10. Les extrapolations sont signalees.
A11. Les simulations longues sont checkpointables.
A12. Le moteur peut reprendre apres crash.
A13. Le stress testing peut cartographier une region de panne.
A14. Une ablation reste qualifiee "dans le modele".
A15. Un SimulationPackage exportable peut etre genere.
A16. Un run invalidé n'est jamais reutilise silencieusement.
A17. Le moteur conserve erreur numerique et erreur de modele separement lorsque possible.
A18. Le laboratoire peut proposer une experience discriminante pour Structure 09.
A19. Le regulateur peut degrader proprement une campagne.
A20. Le HUD affiche modele, monde, run, validation et incertitude.
```


# 243. Suite logique vers Structure 11

La suite naturelle peut etre un **moteur de decision, controle et politiques d'action sous incertitude**.

Il pourrait formaliser :
- decision theory ;
- utilities ;
- multi-criteria decision analysis ;
- policies ;
- control loops ;
- MPC ;
- POMDP ;
- receding horizon ;
- safe exploration ;
- action gates ;
- value of information ;
- regret ;
- robust control ;
- policy testing in Structure 10 ;
- validation des politiques par Structure 09 ;
- execution orchestree par Structure 08.

La Structure 10 dit :

```text
"voici ce que plusieurs modeles prevoient sous plusieurs mondes."
```

La Structure 11 dirait :

```text
"compte tenu de ces mondes, preuves, risques et permissions,
quelle politique d'action est admissible et preferable ?"
```
