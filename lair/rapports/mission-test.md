# Rapport de mission — Évaluation de 5 idées de produits B2B data.gouv.fr

**Date** : 3 octobre 2026
**Méthode** : Grille Tusk (100 points)
**Seuil de recommandation** : >= 70/100
**Produits actifs maximum** : 3

---

## Grille d'évaluation

| Critère | Poids | Description |
|---------|-------|-------------|
| Demande réelle | 25 | Réalité et taille de la demande marché |
| Capacité à payer | 20 | Budget et disposition à payer des clients cibles |
| Concurrence | 15 | Score élevé = marché peu concurrentiel |
| Coût de construction | 15 | Score élevé = coût faible, complexité modérée |
| Automatisation | 15 | Potentiel d'automatisation du produit |
| Risque juridique | 10 | Score élevé = risque faible |

---

## Résumé des scores

| # | Produit | Score | Statut |
|---|---------|-------|--------|
| 1 | **SirenIQ** — Intelligence B2B | **78/100** | RECOMMANDÉ (actif) |
| 2 | **BuildGreen Analytics** — Rénovation énergétique | **75/100** | RECOMMANDÉ (actif) |
| 3 | **ImmoData Pro** — Marché immobilier | **75/100** | RECOMMANDÉ (actif) |
| 4 | MarchéScan — Marchés publics | 72/100 | Recommandé (réserve) |
| 5 | ClimaRisk Pro — Risques climatiques | 68/100 | REJETÉ |

---

## 1. SirenIQ — 78/100 RECOMMANDÉ (actif)

**Dataset** : [Base Sirene des entreprises (SIREN, SIRET)](https://www.data.gouv.fr/datasets/base-sirene-des-entreprises-et-de-leurs-etablissements-siren-siret/) — INSEE
**Cible** : Directions commerciales B2B, services conformité/KYC banques et assurances, cabinets de recrutement, sociétés de leasing
**Prix** : 150 € – 500 € / mois

| Critère | Score | Justification |
|---------|-------|---------------|
| Demande réelle | 22/25 | Marché éprouvé — la prospection B2B et le KYC sont des besoins permanents et réglementés |
| Capacité à payer | 17/20 | Budgets commerciaux et conformité existent, 150-500€/mois est très abordable en B2B |
| Concurrence | 8/15 | Marché occupé par des acteurs historiques (Societe.com, Ellisphere, Altares, Dun & Bradstreet) aux interfaces datées et aux tarifs opaques — barrière à l'entrée réelle mais faiblesse exploitable |
| Coût construction | 9/15 | Ingestion et nettoyage de millions d'enregistrements SIRENE, infrastructure d'alertes temps réel, double interface API+dashboard — complexité modérée |
| Automatisation | 13/15 | Pipeline de données, alertes, enrichissement CRM — cœur de valeur automatisable |
| Risque juridique | 9/10 | Licence Ouverte v2 claire, attribution simple, pas de données personnelles |

---

## 2. BuildGreen Analytics — 75/100 RECOMMANDÉ (actif)

**Dataset** : [Base de Données Nationale des Bâtiments (BDNB)](https://www.data.gouv.fr/datasets/base-de-donnees-nationale-des-batiments/) — CSTB
**Cible** : Entreprises de rénovation énergétique, diagnostiqueurs DPE, bailleurs sociaux (HLM), syndics de copropriété, collectivités territoriales (PCAET)
**Prix** : 250 € – 1 000 € / mois

| Critère | Score | Justification |
|---------|-------|---------------|
| Demande réelle | 21/25 | Fort vent réglementaire — Loi Climat et Résilience impose la rénovation des passoires thermiques, le DPE est obligatoire. Marché en croissance garantie |
| Capacité à payer | 16/20 | Mix de clients bien financés (bailleurs sociaux, collectivités, bureaux d'études) et de plus petits acteurs (artisans, diagnostiqueurs) |
| Concurrence | 12/15 | GoRénove, Effy, Heero, QuelleEnergie sont quasi exclusivement B2C — l'angle B2B pur est nettement moins encombré, vrai avantage différenciant |
| Coût construction | 7/15 | BDNB complexe (27M+ bâtiments, nombreux attributs), modélisation énergétique experte, croisement DVF — développement lourd |
| Automatisation | 11/15 | Scoring, gestion de portefeuille, rapports automatisables, mais crédibilité nécessite validation experte |
| Risque juridique | 8/10 | Licence Ouverte v2, données parfois modélisées (pas toujours DPE réel) — transparence requise, pas de données personnelles |

---

## 3. ImmoData Pro — 75/100 RECOMMANDÉ (actif)

**Dataset** : [Demandes de valeurs foncières (DVF)](https://www.data.gouv.fr/fr/datasets/demandes-de-valeurs-foncieres/) — DGFiP
**Cible** : Agences immobilières, banques (services crédit), assurances (expertise), notaires, promoteurs immobiliers, investisseurs professionnels
**Prix** : 100 € – 400 € / mois

| Critère | Score | Justification |
|---------|-------|---------------|
| Demande réelle | 20/25 | Besoin permanent des professionnels de l'immobilier pour l'estimation, l'octroi de crédit et l'expertise. Marché établi |
| Capacité à payer | 17/20 | Agences, banques, notaires ont des budgets dédiés ; ticket d'entrée à 100€/mois très accessible |
| Concurrence | 6/15 | Point faible majeur — MeilleursAgents Pro, SeLoger Pro, Yanport, PriceHubble, Castorus dominent avec des marques fortes et des données enrichies. Marché très encombré |
| Coût construction | 10/15 | Données DVF relativement simples (transactions), AVM standard, cartographie — complexité modérée, le plus accessible des 5 |
| Automatisation | 13/15 | Estimations, rapports, API livrables sans intervention humaine |
| Risque juridique | 9/10 | Données anonymisées par conception, Licence Ouverte v2 claire |

---

## 4. MarchéScan — 72/100 RECOMMANDÉ (réserve)

**Dataset** : [Données essentielles de la commande publique (DECP)](https://www.data.gouv.fr/datasets/donnees-essentielles-de-la-commande-publique-fichiers-consolides/) — DAJ / Ministère de l'Économie
**Cible** : Entreprises soumissionnant aux marchés publics (BTP, conseil, IT, services), cabinets d'avocats spécialisés, chambres de commerce, fédérations professionnelles
**Prix** : 200 € – 800 € / mois

| Critère | Score | Justification |
|---------|-------|---------------|
| Demande réelle | 18/25 | Besoin réel mais niche — limité aux entreprises qui soumissionnent activement aux marchés publics (~200Md€/an, mais clientèle concentrée) |
| Capacité à payer | 16/20 | Entreprises établies, ticket 200-800€/mois raisonnable |
| Concurrence | 10/15 | monflair.fr, vecteurplus.fr, Klekoon, France Marchés — marché fragmenté sans leader écrasant, opportunité d'entrée |
| Coût construction | 8/15 | Données DECP complexes (formats hétérogènes, nombreux champs), croisement SIRENE nécessaire, prédiction de renouvellement ajoute de la complexité |
| Automatisation | 11/15 | Ingestion, alertes, dashboards automatisables mais l'analyse prédictive demande du calibrage régulier |
| Risque juridique | 9/10 | Licence Ouverte v2, données publiques, rares exclusions sécurité nationale |

**Statut** : Recommandé mais 4e au classement — limite de 3 produits actifs atteinte. Marché de niche et fragmentation des concurrents rendent l'entrée possible, mais la demande est plus étroite et le coût de construction supérieur à ImmoData Pro. Candidat de remplacement solide si un produit actif est retiré.

---

## 5. ClimaRisk Pro — 68/100 REJETÉ

**Dataset** : [Données climatologiques de base — quotidiennes](https://www.data.gouv.fr/datasets/donnees-climatologiques-de-base-quotidiennes/) — Météo-France
**Cible** : Compagnies d'assurance IARD et réassureurs, mutuelles agricoles (Groupama, Pacifica), coopératives agricoles, gestionnaires d'infrastructures
**Prix** : 500 € – 2 000 € / mois

| Critère | Score | Justification |
|---------|-------|---------------|
| Demande réelle | 19/25 | Marché émergent porté par la pression réglementaire (Solvency II) et la hausse des sinistres climatiques — besoin croissant mais pas encore mature |
| Capacité à payer | 18/20 | Les assureurs ont des poches profondes, ticket 500-2000€/mois acceptable en entreprise |
| Concurrence | 10/15 | Météo-France Pro, CCR, AXA Climate sont des institutionnels chers et peu agiles — place pour un outsider, mais barrières de crédibilité |
| Coût construction | 6/15 | Modélisation climatique et actuarielle très exigeante, CatNat scoring complexe, pipeline de données météo lourd — le plus coûteux à construire des 5 |
| Automatisation | 9/15 | Ingestion et alertes automatisables, mais les modèles probabilistes nécessitent une maintenance experte permanente — ne peut pas tourner seul |
| Risque juridique | 6/10 | Les données radar/modèles avancés de Météo-France sont payants (limite la différenciation), le scoring assurantiel tombe sous surveillance ACPR, et la fiabilité des prédictions expose à un risque de responsabilité |

**Raison du rejet** : Score sous le seuil de 70. Coût de construction prohibitif pour un premier portefeuille, dépendance à des données Météo-France payantes qui contredisent le positionnement open data, et risque juridique modéré (ACPR, responsabilité des prédictions).

---

## Portefeuille recommandé

Les 3 produits actifs couvrent des marchés distincts sans cannibalisation :

| Produit | Marché | Ticket | Atout clé |
|---------|--------|--------|-----------|
| **SirenIQ** | Intelligence B2B | 150–500 € | Marché éprouvé, faiblesse exploitable des concurrents historiques |
| **BuildGreen Analytics** | Rénovation énergétique | 250–1 000 € | Vent réglementaire fort, angle B2B différenciant |
| **ImmoData Pro** | Marché immobilier | 100–400 € | Ticket d'entrée le plus bas, construction la plus accessible |

**Réserve** : MarchéScan (72/100) — candidat de remplacement si un produit actif est retiré.

**Rejeté** : ClimaRisk Pro (68/100) — trop complexe pour un premier portefeuille, dépendance aux données payantes Météo-France.

---

*Rapport généré automatiquement à partir de la grille Tusk — 5 idées évaluées, 4 recommandées, 3 activées, 1 rejetée.*