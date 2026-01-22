# Travail réalisé par Denis POPESCU et Rostom SAMAR - M1 Expert IA

# League of Legends - Analyse des facteurs de victoire et du draft
## Problème étudié
Dans League of Legends, la victoire dépend à la fois :
- de choix effectués avant la partie (draft, champions, matchups, contexte),
- et de décisions prises pendant la partie (gestion des ressources, objectifs, combats).
Ce projet cherche à analyser, à partir de données réelles de parties classées :
- quels facteurs sont associés à la victoire,
- à quel moment ces facteurs interviennent,
- et dans quelle mesure les choix pré-game permettent d’estimer les chances de succès.
L’objectif est d’identifier des **tendances statistiques** et non de prédire de manière certaine l’issue d’une partie.
---
## Phases du projet
### Phase 0 — Exploration et cadrage des données
- Prise en main des fichiers de données
- Identification des entités principales (matchs, équipes, champions, statistiques)
- Vérification de la cohérence et de la complétude des données
But : comprendre ce que les données permettent réellement d’étudier.
---
### Phase I — Analyses descriptives
- Calcul de statistiques globales (fréquences, winrates)
- Analyse par champion, par lane et par rank
- Mise en évidence des tendances générales du méta
But : obtenir une première lecture non modélisée des données.
---
### Phase II — Modélisation de la victoire (post-game)
- Construction d’un dataset basé sur des statistiques in-game
- Entraînement de modèles de classification
- Comparaison de modèles linéaires et non linéaires
- Analyse des performances et des variables explicatives
But : identifier les facteurs expliquant la victoire a posteriori.
---
### Phase III — Analyse et modélisation pré-game (draft)
- Construction d’un dataset strictement pré-game
- Étude des matchups champion contre champion
- Analyse des performances relatives par lane
- Modélisation de l’impact du draft sur la probabilité de victoire
But : évaluer ce qu’il est possible d’estimer avant le début de la partie.
---
### Phase IV — Interprétation et limites
- Comparaison entre résultats pré-game et post-game
- Mise en évidence d’un signal pré-game réel mais limité
- Discussion des limites méthodologiques et interprétatives
But : replacer les résultats dans un cadre probabiliste et non déterministe.
---
## Résumé
- Les facteurs in-game expliquent fortement l’issue d’une partie.
- Les choix pré-game influencent les probabilités, sans déterminer le résultat.
- Les modèles capturent des tendances de méta exploitables comme aide à la décision.
---
## Organisation du dépôt
- Dataset : fichiers de données
- Code : notebooks d’analyse et de modélisation
- Documents : problématique et supports écrits
- Presentation : slides de restitution
