# League of Legends Meta Analysis - Machine Learning

## Problématique générale

---

> **Comment maximiser ses chances de victoire dans une partie de League of Legends en s'appuyant sur l'analyse des données de jeu et des modèles de machine learning, à travers les choix stratégiques (champions, items, draft) et les performances individuelles en partie ?**

Cette problématique vise à **comprendre**, **quantifier** et **modéliser** les éléments qui influencent la victoire, afin de transformer les données brutes en **recommandations stratégiques exploitables**.

---

## Phase 0 — Préparation et Qualité des Données

### Pourquoi cette phase est cruciale ?
- La qualité des données impacte directement la fiabilité des modèles
- Un nettoyage rigoureux évite les biais et erreurs de prédiction

### Objectifs de la phase
1. **Rassembler et contrôler** : Charger tous les fichiers CSV du dataset
2. **Nettoyer** : Gérer valeurs manquantes, données corrompues, etc.
3. **Échantillonner** : Conserver uniquement les modes CLASSIC et ARAM (exclure les autres)

### Tâches à réaliser
- Import des bibliothèques essentielles 
- Chargement des tables du dataset 
- Filtrage des modes de jeu si besoin
- Exploration initiale des données
- Détection et nettoyage des anomalies
- Segmenter si besoin
- Visualisations de la qualité des données

---

## Phase I — Compréhension de la méta actuelle (ce qui gagne)

### Objectif
Identifier les champions et items qui contribuent le plus aux victoires, par contexte (lane, rank, durée, patch).

### I.1 — Efficacité des champions

#### Question de recherche
> Quels champions présentent les meilleurs taux de victoire en fonction de la lane et du contexte de jeu ?

#### Approches mises en œuvre

**Pipeline d'analyse automatisé :**
- Détection robuste et case-insensitive des colonnes clés dans toutes les tables

**Calculs de performances :**
- Winrates globaux par champion (avec noms lisibles)
- Winrates par champion × lane (lane réelle issue de MatchStatsTbl)
- Filtrage par nombre minimal de parties (≥50 games) pour garantir la robustesse statistique

**Détection des sur/sous-performers :**
- Analyse par lane : identification des champions au-delà de ±1 écart-type de la moyenne de la lane
- Analyse par rank : identification des champions dominants selon le niveau de jeu
- Prise en compte du contexte : un champion peut surperformer dans une lane mais pas dans une autre

**Visualisations interactives :**
- Scatter plots globaux (tous champions confondus) avec winrate, nombre de parties et gradient de couleur
- Scatter plots par lane avec filtrage de fiabilité (≥50 games)
- Top 5 champions par lane (barres horizontales facettées par lane)

#### Résultats attendus
- Identification des champions dominants de la méta actuelle
- Mise en évidence des champions spécialisés par lane
- Détection des champions de niche (winrate élevé mais faible volume)

---

### I.2 — Impact des items

#### Question de recherche
> Quels objets sont les plus efficaces pour maximiser les chances de victoire ?

#### Contexte
Les items dans League of Legends sont l'équivalent d'équipements dans un jeu de rôle : ils améliorent les capacités du champion et influencent directement sa manière de combattre. Le choix des bons items est stratégique et doit s'adapter au contexte de la partie (adversaires, composition, situation).

#### Approches mises en œuvre

**Préparation des données :**
- Afficher les noms et non pas les IDs
- Merge propre
- Harmonisation des types de clés
- Détection robuste des colonnes items

**Analyse des builds gagnants :**
- Remplacement des IDs par noms d'items
- Création d'une clé de build (build_key) : tuple trié d'items non-nuls
- Agrégation par champion × item sur les **victoires uniquement** (Win=1)
- Classification des items :
  - **Core items** : présents dans ≥50% des victoires du champion
  - **Situationnels** : dépenendent du contexte

**Méthodologie :**
- Analyse uniquement sur les parties gagnées pour isoler les builds associés au succès
- Comptage de la fréquence d'apparition de chaque item dans les builds victorieux
- Affichage structuré par champion avec distinction core/situationnel

#### Limites reconnues
> Quelles sont les limites de cette analyse des items ?

---

## Phase II — Modélisation de la victoire

### Objectif
Formaliser la relation entre plusieurs variables et l'issue d'une partie après son déroulement.

### Question de recherche
> Peut-on modéliser la victoire à partir des statistiques de jeu et identifier les facteurs les plus déterminants, toutes choses égales par ailleurs ?

### Approches mises en œuvre

**Sélection des variables (features post-game) :**
- TotalGold : avantage économique
- MinionsKilled : farming (CS)
- DmgDealt : dégâts infligés
- DmgTaken : dégâts subis
- TurretDmgDealt : pression sur objectifs structurels
- VisionScore : contrôle de la vision

**Pipeline de modélisation :**
1. Nettoyage des données (suppression des valeurs manquantes)
2. Séparation train/test (70/30) avec stratification
3. Standardisation des features (StandardScaler) pour la régression logistique
4. Entraînement de deux modèles :
   - **Régression logistique** : modèle linéaire interprétable
   - **Random Forest** : modèle non linéaire pour comparaison

**Évaluation des modèles :**
- Accuracy
- AUC-ROC
- Classification repor

**Analyse des facteurs de victoire :**
- **Régression logistique** : analyse des coefficients standardisés
  
- **Random Forest** : analyse des importances de variables

**Visualisations :**
- Barplot des coefficients de régression logistique
- Comparaison des importances entre modèles
- Interprétation contextuelle des résultats

### Limites reconnues
> Quelles sont les limites de cette modélisation ?

---

## Phase III — Analyse pré-game (draft et stratégies)

### Objectif
Analyser l'impact des choix **pré-game** (champion, lane, matchup, rank, patch) sur la victoire, **sans utiliser les statistiques post-game** (gold, dégâts, CS, items).

### Positionnement
Les choix pré-game ne déterminent pas directement la victoire, mais fixent un cadre initial qui influence les probabilités de succès. Cette phase vise à :
- Identifier les tendances de méta par contexte
- Analyser les matchups favorables/défavorables
- Estimer des probabilités de victoire conditionnelles au draft

### Question de recherche principale
> Peut-on prédire l'issue d'une partie à partir des choix stratégiques pré-game ?

### Approches mises en œuvre

**Construction du dataset pré-game :**
- Variables strictement pré-game uniquement(EnemyChampionName)
  - Contexte : Patch, Rank, QueueType, GameDuration
- Variable cible : Win (0/1)
- **Exclusion totale** des statistiques post-game (or, dégâts, CS, items, runes, sorts)

**Jointures et nettoyage :**
- Merge propre
- Harmonisation des types de clés
- Intégration des noms lisibles
- Suppression des valeurs manquantes sur les colonnes critiques

**Analyses descriptives :**

1. **Winrates par champion × lane :**
   - Calcul du winrate par champion dans chaque lane
   - Filtrage par nombre minimal de parties (≥50 games)
   - Identification des top performers par rôle

2. **Analyse des matchups :**
   - Winrate champion vs champion dans chaque lane
   - Filtrage par volume minimal
   - Identification des avantages structurels de draft (counters)

3. **Proxy de méta par lane :**
   - Combinaison winrate × popularité × volume
   - Score meta 
   - Identification des champions stables et performants

**Modélisation prédictive pré-game :**
- Encodage catégoriel (OneHotEncoder) de toutes les features
- Modèles de regression logistique et Random Forest
- Évaluation des performances
- Visualisation

### Résultats et interprétations
**Résultats attendus**
> Quels sont les champions, lanes et matchups les plus favorables avant le début de la partie 

**Limites et portée :**
> Quelles sont les limites de cette analyse pré-game ?

---

## Conclusion générale du projet

### Synthèse des résultats

Ce projet met en évidence une **distinction fondamentale** entre ce qui est observable avant une partie et ce qui détermine réellement son issue.

**Phase 0** a permis de construire un dataset propre et fiable, en éliminant les biais liés aux anomalies et en structurant les données selon les modes de jeu pertinents (CLASSIC et ARAM).

**Phase I** a identifié les champions et items dominants de la méta, tout en soulignant l'importance du contexte (lane, rank, matchup). Ces analyses descriptives fournissent des tendances exploitables mais ne constituent pas des recommandations absolues.

**Phase II** a démontré que les facteurs post-game (or, objectifs, dégâts) expliquent largement la victoire, mais avec un biais d'interprétation : ces statistiques sont des conséquences du déroulement de la partie plutôt que des causes directes.

**Phase III** a montré que les choix pré-game contribuent à orienter l'issue d'une partie, mais dans une logique **probabiliste** et non déterministe. Les modèles capturent des signaux exploitables pour l'aide à la décision en phase de draft, sans prétendre remplacer le jeu lui-même.

### Réponse à la problématique

> **Comment maximiser ses chances de victoire dans League of Legends ?**

Les données permettent de :
- **Identifier** les tendances de méta et les configurations favorables
- **Quantifier** l'impact relatif des choix stratégiques
- **Modéliser** des probabilités conditionnelles d'issue de partie

Mais elles ne permettent **pas** de :
- Prédire avec certitude l'issue d'une partie
- Remplacer les décisions humaines et l'exécution in-game
- Ignorer le rôle central de la coordination et de l'adaptation

### Portée et valeur du projet

La valeur de ce travail réside moins dans la recherche d'un score prédictif maximal que dans la **compréhension des frontières entre information stratégique et incertitude**.

Les modèles développés constituent des outils d'aide à la décision qui :
- Éclairent les choix de draft
- Structurent la réflexion stratégique
- Identifient des leviers d'amélioration

Mais ils ne remplacent ni le jeu lui-même ni les choix humains qui en façonnent l'issue.

### Limites assumées

- **Absence de causalité stricte** : les modèles identifient des associations, pas des relations de cause à effet
- **Données post-game** : induisent un biais d'interprétation dans la Phase II
- **Facteurs non observables** : coordination, timing, erreurs individuelles ne sont pas capturés
- **Nature probabiliste** : les prédictions pré-game restent des estimations conditionnelles

Ces limites n'invalident pas le projet, mais **encadrent strictement sa portée** et garantissent une interprétation rigoureuse des résultats.

---

## Perspectives d'amélioration

- Intégration de données de bans pour affiner l'analyse de draft
- Analyse temporelle fine par patch pour détecter l'évolution de la méta
- Modèles séquentiels (early game → mid game → late game) pour capturer la dynamique temporelle
- Intégration de données de coordination (wards placés, objectifs contestés) si disponibles