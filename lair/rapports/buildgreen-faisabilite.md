# BuildGreen Analytics — Faisabilité technique & architecture

Rapport produit le 2026-10-03. Phase 1 : zéro dépense, zéro compte.

---

## 1. Synthèse exécutive

**Verdict : Faisable.** La BDNB fournit un socle de données suffisamment riche (32M+ bâtiments, 170+ attributs open data, prédictions DPE couvrant 95% du parc) pour construire un SaaS B2B de scoring énergétique dès la phase 1. Les données sont sous Licence Ouverte v2 (réutilisation commerciale autorisée avec attribution). L'API CSTB permet un accès gratuit jusqu'à 10 000 requêtes/mois pour le prototypage.

Le positionnement B2B pur (rénovateurs, diagnostiqueurs, bailleurs sociaux, syndics) est cohérent avec les offres API Expert du CSTB (200-10 000 EUR/an selon volume). La différenciation clé est l'exploitation exhaustive de la BDNB, le croisement DVF pour le ROI rénovation, et une interface orientée gestion de portefeuille.

---

## 2. La BDNB en détail

### 2.1 Présentation

| Attribut | Valeur |
|----------|--------|
| Nom complet | Base de Données Nationale des Bâtiments |
| Éditeur | CSTB (Centre Scientifique et Technique du Bâtiment) |
| Volume | 32+ millions de bâtiments (résidentiels et tertiaires) |
| Attributs | 400+ informations (170+ en open data) |
| Sources | Croisement géospatial d'une cinquantaine de bases publiques |
| Licence | Licence Ouverte v2 (Etalab) — réutilisation commerciale OK |
| Mise à jour | Semestrielle (dernier millésime : 2026-02.a, mai 2026) |
| Formats | GeoPackage, CSV, dump SQL PostGIS, tuiles vectorielles MVT, Parquet |
| API | REST (api.bdnb.io), 3 niveaux : Open / Open Plus / Expert |
| Page data.gouv.fr | https://www.data.gouv.fr/datasets/base-de-donnees-nationale-des-batiments/ |
| Documentation | https://bdnb.io/documentation/ |
| GitLab | https://gitlab.com/BDNB/base_nationale_batiment |

### 2.2 Modèle de données

La BDNB est structurée autour de 3 niveaux de bâtiments :

- **Construction** : ensemble constructif cohérent, potentiellement plusieurs bâtiments séparés par des murs porteurs.
- **Bâtiment** : aligné sur les bâtiments fiscaux (DGFiP), assimilable à l'ensemble des logements accessibles depuis une entrée.
- **Groupe de bâtiment** : regroupement de constructions d'une parcelle quand l'identification individuelle est impossible.

Pour les maisons individuelles, ces trois niveaux correspondent au même contour géométrique.

### 2.3 Principales catégories de données (BDNB Open, 170+ attributs)

| Catégorie | Attributs clés |
|-----------|---------------|
| **Morphologie** | Emprise au sol, hauteur, volume, surface habitable, nombre de niveaux, géométrie 2.5D, orientation des façades, surface de toiture |
| **Usage** | Usage niveau 1 (résidentiel/tertiaire/agricole/etc.), usage niveau 2 (maison, appartement, bureau, commerce…), nombre de logements |
| **Matériaux** | Matériau des murs, matériau de la toiture |
| **Année de construction** | Période de construction (par décennies), année estimée |
| **Performance énergétique** | Étiquettes DPE (A-G) prédites, consommation énergie primaire/finale, émissions GES, probabilités par étiquette, incertitude, gisement de gain |
| **Systèmes énergétiques** | Type de chauffage (individuel/collectif), type de générateur (25 types), type ECS, type ventilation |
| **Enveloppe** | Coefficients U (murs, planchers, baies), type d'isolation, surface déperditive, facteur solaire, masques solaires |
| **Raccordement** | Potentiel de raccordement aux réseaux de chaleur, potentiel ENR |
| **Données socio-éco** | Ratio HLM, ratio location, ratio vacance, ratio résidences secondaires |
| **Certifications** | Labels et certifications des bâtiments (nouveauté 2026-02.a) |
| **Adresses & cadastre** | Lien BAN (Base Adresse Nationale), parcelles cadastrales, lien IGN |
| **Valeur verte** | Décote/surcote immobilière liée au DPE, calculée par croisement DVF × DPE |
| **Surchauffe estivale** | Indicateur ISB-DH (Îlots de Chaleur Bâtiment) |

### 2.4 Qualité des prédictions DPE

Le CSTB a développé **bat2vec-Energie**, un modèle de deep learning combinant :
- **CVAE** (Conditional Variational Auto-Encoder) pour la génération probabiliste
- **Architecture Transformer** (TabTransformer + FTTransformer) pour le traitement des données tabulaires
- Entraîné sur 2,3 millions de DPE arrêté 2021 filtrés (opposables, cohérents)

**Performances du modèle :**
- **86%** de prédictions correctes à ±1 étiquette près
- **45%** de prédictions exactes (estimation par validation croisée sur 87 000 DPE)
- Couvre **95%** des logements sans DPE réel
- Inclut un **indicateur de fiabilité** par bâtiment (score 1-5)
- Scénario de rénovation globale inclus (isolation BBC rénovation + PAC)
- 5 millions de passoires thermiques (F/G) en France (coefficient énergie primaire 1.9)
- Prédictions à l'échelle du groupe de bâtiments ET de l'appartement (position dans l'immeuble)

**Limites :**
- Effets de seuil non reproduits dans les simulations → proportion plus élevée de F/G simulés vs DPE réels
- Pas de prise en compte de l'hétérogénéité intra-étage (orientation au sein d'un même étage)
- Modèle probabiliste : résultats à interpréter avec les intervalles de confiance
- Millésime semestriel → latence de 6 mois maximum sur les données

### 2.5 Tables clés pour BuildGreen

| Table BDNB | Contenu | Usage BuildGreen |
|-----------|---------|-----------------|
| `batiment_groupe_simulations_dpe` | Score DPE prédit par bâtiment (état actuel + rénové), consommations, gisement | Scoring principal |
| `local_simulations_dpe` | Score DPE par appartement (position : RDC, étage, toiture) | Scoring fin pour immeubles |
| `batiment_groupe_dpe_representatif_logement` | DPE réel représentatif du bâtiment (quand disponible) | Calibration avec données réelles |
| `dpe_logement` | 100 attributs techniques par DPE (isolation, systèmes, matériaux) | Détail technique, audit |
| `batiment_groupe_delimitation_enveloppe` | Géométrie des façades, orientation, masques solaires | Calculs thermiques, potentiel solaire |
| `prediction_enveloppe_et_systemes` | 100 tirages Monte Carlo (Parquet, 2 milliards de lignes) | Analyse de sensibilité, intervalles de confiance |

---

## 3. Architecture du SaaS

### 3.1 Vue d'ensemble

```
┌─────────────────────────────────────────────────────────────────┐
│                    BuildGreen Analytics                          │
├─────────────────────────────────────────────────────────────────┤
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────────┐ │
│  │ Dashboard │  │ API REST  │  │ Exports  │  │ Portefeuille     │ │
│  │ Web       │  │ publique  │  │ PDF/CSV  │  │ Bailleurs/Syndics│ │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────────┬─────────┘ │
│       └──────────────┴─────────────┴───────────────┘           │
│                          │                                      │
│               ┌──────────┴──────────┐                           │
│               │  API Gateway /      │                           │
│               │  Authentification    │                           │
│               └──────────┬──────────┘                           │
│                          │                                      │
│  ┌───────────────────────┼───────────────────────────────┐      │
│  │               Backend Services                         │      │
│  │  ┌────────┐ ┌────────┐ ┌──────────┐ ┌──────────────┐  │      │
│  │  │Scoring │ │Portfolio│ │Rénovation│ │ Conformité    │  │      │
│  │  │Engine  │ │Manager │ │Simulator │ │ Loi Climat    │  │      │
│  │  └───┬────┘ └───┬────┘ └────┬─────┘ └──────┬───────┘  │      │
│  └──────┼──────────┼──────────┼───────────────┼──────────┘      │
│         │          │          │               │                  │
│  ┌──────┴──────────┴──────────┴───────────────┴──────────┐      │
│  │              Couche d'accès aux données                │      │
│  │  ┌──────────┐  ┌──────────┐  ┌────────────────────┐   │      │
│  │  │ Cache    │  │ BDNB     │  │ Calculs            │   │      │
│  │  │ Local    │  │ Mirror   │  │ thermiques         │   │      │
│  │  │(PostGIS) │  │(PostGIS) │  │ (Python/C++)       │   │      │
│  │  └──────────┘  └──────────┘  └────────────────────┘   │      │
│  └───────────────────────────────────────────────────────┘      │
└─────────────────────────────────────────────────────────────────┘
                          │
        ┌─────────────────┼──────────────────┐
        │                 │                  │
   ┌────┴────┐     ┌──────┴──────┐    ┌──────┴──────┐
   │  BDNB   │     │   ADEME     │    │    DVF      │
   │  API    │     │  DPE API    │    │ (DGFiP/     │
   │ (CSTB)  │     │             │    │  CEREMA)    │
   └─────────┘     └─────────────┘    └─────────────┘
```

### 3.2 Composants détaillés

#### 3.2.1 Stockage et mirroring BDNB

La BDNB Open est disponible en téléchargement intégral (France entière ou par département). Un mirroring local en **PostgreSQL + PostGIS** est recommandé pour :

- Éviter les quotas de l'API CSTB (10K requêtes/mois en gratuit → insuffisant en production)
- Permettre des jointures spatiales complexes (trouver tous les bâtiments F/G dans un rayon de 500m)
- Offrir des temps de réponse < 200ms pour le dashboard
- Coût : ~200-500 Go de stockage pour la France entière (selon millésime et tables chargées)

**Stratégie de chargement :**
1. Téléchargement initial : France entière ou top 10 départements (phase MVP)
2. Chargement incrémental à chaque millésime (semestriel)
3. Tables à charger en priorité : `batiment_groupe_simulations_dpe`, `batiment_groupe_dpe_representatif_logement`
4. Index spatiaux (GiST) sur les géométries, index B-tree sur les identifiants

#### 3.2.2 Scoring Engine

Module central qui produit un **score BuildGreen** par bâtiment, agrégé à partir de :

| Composante | Poids | Source |
|-----------|-------|--------|
| Étiquette DPE (A=7 à G=1) | 35% | BDNB `batiment_groupe_simulations_dpe` |
| Potentiel de gain énergétique (kWh/m²/an) | 25% | BDNB `gisement_gain_conso_finale_total` |
| Fiabilité de la prédiction (1-5) | 15% | BDNB `etiquette_dpe_initial_inc` |
| Conformité Loi Climat (calendrier interdiction location) | 15% | Calculé à partir de l'étiquette et du millésime |
| Sur-risque canicule (ISB-DH, optionnel) | 10% | BDNB `batiment_groupe_predictions_isb` |

Le score est normalisé sur 0-100 et dégradé en 5 classes : Critique / Prioritaire / À surveiller / Correct / Excellent.

**Algorithmes additionnels :**
- Détection des **passoires thermiques** : probabilité cumulée F+G > 50%
- Détection des **indécences énergétiques** : consommation > 450 kWh/m²/an
- Calcul du **ROI rénovation** : croisement BDNB (gain DPE) × DVF (valeur au m² par IRIS) × données CSTB valeur verte
- **Priorisation de portefeuille** : score composite triant les bâtiments par ordre d'intervention optimal

#### 3.2.3 Portfolio Manager

Gestion de portefeuilles (bailleurs sociaux, syndics, collectivités) :

- Import de SIRET / liste d'adresses / parcellaire → matching BDNB
- Dashboard agrégé : distribution DPE, % passoires, budget rénovation estimé
- Drill-down par bâtiment → fiche détaillée → scénarios de rénovation
- Alertes réglementaires : échéances Loi Climat par étiquette
- Export PDF/CSV/GeoJSON des portefeuilles

#### 3.2.4 Renovation Simulator

Basé sur les données BDNB de simulation post-rénovation :

- Scénario standard : BBC rénovation + PAC (fourni par BDNB)
- Scénarios personnalisables : isolation partielle, changement de chauffage uniquement
- Estimation de coûts travaux via ratios forfaitaires (€/m² par type de paroi)
- Calcul ROI : coût travaux / gain énergétique annuel / plus-value verte
- Les scénarios avancés nécessiteraient un moteur de calcul thermique adossé à la table `batiment_groupe_delimitation_enveloppe` (géométries des façades)

#### 3.2.5 Conformité Loi Climat et Résilience

Module de suivi réglementaire :

- Calendrier d'interdiction de location (2025 : G+, 2028 : G, 2034 : F, etc.)
- Identification des bâtiments du portefeuille concernés par chaque échéance
- Suivi des obligations de travaux (audit énergétique, DPE collectif)
- Calcul du taux de conformité du portefeuille

---

## 4. API externes nécessaires

### 4.1 APIs obligatoires (phase MVP)

| API | Fournisseur | Usage | Licence/Coût |
|-----|-----------|-------|-------------|
| **BDNB API Expert** | CSTB | Données enrichies, prédictions DPE, simulations rénovation | Payant : 500-10 000 EUR/an selon volume |
| **BDNB API Open** | CSTB | Données open data pour prototypage | Gratuit (10K req/mois) |
| **ADEME DPE** | ADEME | DPE réels (arrêté 2021) pour calibration | Open data, gratuit |
| **BAN (Base Adresse Nationale)** | Etalab | Géocodage des adresses → coordonnées BDNB | Open data, gratuit |

### 4.2 APIs recommandées (phase 2)

| API | Fournisseur | Usage | Licence/Coût |
|-----|-----------|-------|-------------|
| **DVF (Demandes de Valeurs Foncières)** | DGFiP / data.gouv.fr | Prix de transaction pour calcul ROI rénovation, valeur verte | Open data, gratuit |
| **Fichiers Fonciers (MAJIC)** | CEREMA | Données fiscales détaillées (surface, propriétaire) | Ayant-droit uniquement |
| **Météo-France** | Météo-France | Degrés-jours de chauffage, données climatiques locales | Open data (stations), payant (modèles fins) |
| **BDTopo IGN** | IGN | Géométries précises des bâtiments, contexte urbain | Open data (partiel), payant (complet) |
| **INSEE (données carroyées)** | INSEE | Données socio-démographiques à 200m | Open data, gratuit |

### 4.3 APIs optionnelles (phase 3)

| API | Fournisseur | Usage |
|-----|-----------|-------|
| **MaPrimeRénov' / ANAH** | ANAH | Subventions disponibles pour financement rénovation |
| **RNIC (Registre National Immeubles Copropriétés)** | Ministère Logement | Données copropriétés |
| **RPLS (Répertoire Logements Sociaux)** | Ministère Logement | Parc social (ayant-droit) |
| **OpenStreetMap** | OSM | Points d'intérêt, transports (enrichissement valeur verte) |

### 4.4 API CSTB : grille tarifaire détaillée

| Offre | Prix annuel HT | Requêtes/mois | Requêtes/min | Données |
|-------|---------------|--------------|-------------|---------|
| API Open | Gratuit | 10 000 | 120 | Open data uniquement |
| Open Plus (500 appels/an) | 200 EUR | — | 1 200 | Open data |
| Open Plus (100K appels/an) | 1 000 EUR | — | 1 200 | Open data |
| Expert (500 appels/an) | 500 EUR | — | 1 200 | Toutes données BDNB |
| Expert (100K appels/an) | 5 000 EUR | — | 1 200 | Toutes données BDNB |
| Expert (1M appels/an) | 10 000 EUR | — | 1 200 | Toutes données BDNB |
| Expert (>1M) | +3 000 EUR/tranche 1M | — | 1 200 | Toutes données BDNB |

**Stratégie recommandée pour BuildGreen :**
- Phase MVP : API Open (gratuite) + mirroring BDNB Open en local
- Phase commerciale : API Expert (500-5 000 EUR/an selon volume) + données expertes en export
- Éviter la dépendance temps réel à l'API CSTB en production : mirroring PostGIS + cache applicatif

---

## 5. Contraintes légales et conformité

### 5.1 Licence des données

| Source | Licence | Conditions |
|--------|---------|-----------|
| BDNB Open | Licence Ouverte v2 (Etalab) | Réutilisation commerciale libre, attribution obligatoire (« Source : CSTB / BDNB ») |
| ADEME DPE | Licence Ouverte v2 | Attribution obligatoire (« Source : ADEME ») |
| DVF | Licence Ouverte v2 | Attribution obligatoire (« Source : DGFiP ») |
| BAN | Licence Ouverte v2 | Attribution obligatoire |
| BDNB Expert | Contrat commercial CSTB | Conditions contractuelles spécifiques |

### 5.2 RGPD

- **Aucune donnée personnelle** au niveau bâtiment/DPE : les données sont agrégées à l'échelle du bâtiment.
- Le DPE ne contient pas d'information nominative sur le propriétaire ou l'occupant.
- Les données DVF sont anonymisées (pas de nom d'acheteur/vendeur).
- **Risque nul** pour le scoring énergétique standard.
- **Attention** : si croisement avec des données de SIRENE (identification de bailleurs sociaux), les données d'établissement sont publiques (pas de RGPD).

### 5.3 Propriété intellectuelle du score

- Le **score BuildGreen** et les algorithmes de priorisation sont la propriété intellectuelle de BuildGreen Analytics.
- Les données sources restent la propriété de leurs émetteurs (CSTB, ADEME, DGFiP).
- Pas de clause share-alike sur la Licence Ouverte v2 → les produits dérivés ne sont pas contaminés.

### 5.4 Mentions obligatoires

Toute publication, export ou écran du produit devra inclure :

> « Données sources : CSTB / Base de Données Nationale des Bâtiments, ADEME, DGFiP, Etalab — sous Licence Ouverte v2. Score et analyses : BuildGreen Analytics. »

---

## 6. Modèle économique et positionnement

### 6.1 Cibles B2B

| Segment | Taille estimée (France) | Besoin principal | Prix acceptable |
|---------|------------------------|------------------|-----------------|
| Entreprises de rénovation énergétique | ~15 000 | Ciblage des passoires thermiques, estimation gains travaux | 250-500 EUR/mois |
| Diagnostiqueurs DPE certifiés | ~8 000 | Pré-diagnostic, données enrichies, rapports automatiques | 100-300 EUR/mois |
| Bailleurs sociaux (HLM) | ~600 organismes | Gestion de portefeuille, planification rénovation, conformité Loi Climat | 500-1 000 EUR/mois |
| Syndics de copropriété | ~3 000 cabinets | DPE collectif, PPPT (Plan Pluriannuel de Travaux), audit énergétique | 300-600 EUR/mois |
| Collectivités territoriales | ~1 200 EPCI | PCAET (Plan Climat), repérage passoires, politique de l'habitat | 500-1 000 EUR/mois |
| Bureaux d'études thermiques | ~2 000 | Accès aux données BDNB enrichies, exports, calculs thermiques | 300-600 EUR/mois |

### 6.2 Grille tarifaire proposée

| Formule | Prix/mois HT | Inclus |
|---------|-------------|--------|
| **Artisan** | 250 EUR | Ciblage local (1 département), 100 bâtiments/mois, scoring + étiquette |
| **Bureau d'études** | 600 EUR | Analyses avancées (3 départements), exports CSV/GeoJSON, simulations rénovation |
| **Institutionnel** | 1 000 EUR | Portefeuille illimité, API, alertes réglementaires, marque blanche |
| **Sur-mesure** | Sur devis | Intégration SI, données ayant-droit, modèles personnalisés |

### 6.3 Concurrence et différenciation

| Concurrent | Positionnement | Faiblesse | Différenciation BuildGreen |
|-----------|---------------|---------|---------------------------|
| **GoRénove Pro** (CSTB) | B2C + B2B, adossé à la BDNB | Interface générique, pas de gestion de portefeuille avancée | Focus portefeuille, scoring composite, API-first |
| **Effy** | B2C, marketplace rénovation | Pas B2B, pas d'accès DPE prédictif | B2B pur, exploitation exhaustive des prédictions BDNB |
| **Heero** | B2C, accompagnement rénovation | Scoring basique, pas d'API | Modèle probabiliste, intervalles de confiance |
| **QuelleEnergie** | B2C, simulateur travaux | Interface grand public | Gestion de portefeuille, conformité réglementaire |
| **URBS / observatoires régionaux** | Institutionnel, données locales | Silotés par région, pas d'API | France entière, API REST, tarification SaaS |

### 6.4 Avantage compétitif structurel

Le CSTB est à la fois fournisseur de données (BDNB, GoRénove) et concurrent potentiel. BuildGreen doit se positionner comme **couche de valeur ajoutée** plutôt que comme simple repackager de données BDNB :

1. **Score composite propriétaire** (pas juste l'étiquette DPE)
2. **Algorithmes de priorisation** de portefeuille (quel bâtiment rénover en premier pour maximiser le ROI)
3. **Tableaux de bord orientés métier** (bailleur ≠ diagnostiqueur ≠ bureau d'études)
4. **Intégrations CRM** (pousser les leads passoires vers les CRM des rénovateurs)
5. **API-first** : permettre à des tiers d'intégrer le scoring BuildGreen dans leurs propres outils

---

## 7. Feuille de route technique phase 1 (zéro dépense)

### Étape 1 : Mirroring BDNB (semaine 1-2)

- Télécharger le millésime 2026-02.a pour 1-2 départements tests (Paris, Lyon)
- Charger dans PostgreSQL + PostGIS (instance Docker locale, gratuite)
- Valider les jointures spatiales, les index
- Charger les tables prioritaires : `batiment_groupe_simulations_dpe`, `batiment_groupe_dpe_representatif_logement`

### Étape 2 : Scoring Engine POC (semaine 3-4)

- Implémenter l'algorithme de scoring composite (Python)
- Valider sur le département test : distribution des scores, corrélation avec les DPE réels
- Calibrer les poids du scoring
- Développer le calculateur de ROI rénovation (croisement DVF)

### Étape 3 : API et dashboard minimal (semaine 5-6)

- API REST (FastAPI/Flask) : `/score?lat=...&lon=...` et `/portfolio/analyze`
- Dashboard web minimal (Streamlit ou Svelte) : carte interactive, recherche par adresse
- Authentification basique (email/mot de passe, Flask-Login ou Supabase Auth gratuit)

### Étape 4 : Tests utilisateurs (semaine 7-8)

- Identifier 3-5 beta-testeurs (rénovateurs, syndics) via réseau ou LinkedIn
- Tests du POC, recueil des retours
- Itération sur le scoring et l'interface

### Stack technique proposée (100% open source, zéro licence)

| Couche | Technologie | Coût |
|--------|-----------|------|
| Base de données | PostgreSQL 16 + PostGIS 3 | 0 EUR |
| Cache | Redis (optionnel en phase 1) | 0 EUR |
| Backend API | Python 3.12 + FastAPI + SQLAlchemy + GeoAlchemy2 | 0 EUR |
| Calculs thermiques | Python (numpy, pandas) | 0 EUR |
| Frontend | SvelteKit ou Streamlit (MVP) | 0 EUR |
| Cartographie | Leaflet / MapLibre GL JS + tuiles OSM | 0 EUR (OSM gratuit) |
| Hébergement | Scaleway Stardust (2,80 EUR/mois) ou Hetzner CX22 (3,99 EUR/mois) | ~3-4 EUR/mois |
| Déploiement | Docker Compose | 0 EUR |
| CI/CD | GitHub Actions (compute gratuit 2000 min/mois) | 0 EUR |

**Coût total mensuel phase MVP : < 5 EUR HT** (hébergement uniquement).

---

## 8. Risques et points d'attention

### 8.1 Risques techniques

| Risque | Probabilité | Impact | Mitigation |
|--------|-----------|--------|-----------|
| Qualité insuffisante des prédictions DPE pour certains usages métier | Moyenne | Élevé | Indicateur de fiabilité visible, avertissements clairs, ne pas remplacer un vrai DPE |
| Volume de données trop important pour un mirroring local complet | Moyenne | Moyen | Commencer par 5-10 départements, montée en charge progressive |
| Changement de schéma BDNB entre millésimes | Élevée | Faible | Scripts de migration automatisés, tests de non-régression à chaque millésime |
| Disponibilité de l'API CSTB | Faible | Moyen | Mirroring local, pas de dépendance temps réel en production |

### 8.2 Risques business

| Risque | Probabilité | Impact | Mitigation |
|--------|-----------|--------|-----------|
| GoRénove Pro (CSTB) absorbe le marché B2B | Moyenne | Élevé | Différenciation sur le scoring composite, l'API-first, les intégrations CRM |
| Les concurrents B2C (Effy, Heero) pivotent vers le B2B | Faible | Moyen | Avance au démarrage, focus sur les portefeuilles et la conformité réglementaire |
| Réglementation DPE évolue et rend le modèle obsolète | Moyenne | Élevé | Architecture modulaire, modèle bat2vec adaptable (le CSTB le fait déjà pour les nouveaux coefficients) |
| Vente de données rendue payante par le CSTB | Faible | Élevé | La licence ouverte est irrévocable pour les données déjà publiées. Les nouvelles données pourraient être restreintes. |

### 8.3 Risques réglementaires

- Le DPE prédit **ne se substitue pas** au DPE réglementaire : communication claire obligatoire.
- La Loi Climat et Résilience évolue : veille juridique nécessaire.
- Les données DVF ont une fréquence semestrielle avec 18-24 mois de décalage → les prix affichés ne sont pas « temps réel ».

---

## 9. Conclusion et recommandations

**BuildGreen Analytics est techniquement faisable dès la phase 1.** Le socle BDNB est mature, documenté, et accessible gratuitement en open data pour le prototypage. Le modèle de prédiction DPE (bat2vec-Energie) offre une couverture de 95% du parc avec une précision à ±1 étiquette pour 86% des bâtiments, ce qui est suffisant pour du ciblage commercial et de la planification de portefeuille.

**Recommandations stratégiques :**

1. **Démarrer par un POC sur 2 départements** (Paris + Lyon) pour valider le scoring composite et le mirroring PostGIS
2. **Cibler en priorité les bailleurs sociaux** (600 organismes, besoin réglementaire urgent, budget disponible)
3. **Ne pas concurrencer GoRénove frontalement** : se positionner en surcouche de valeur ajoutée (portefeuille, priorisation, intégration CRM)
4. **API-first dès le jour 1** : permettre aux rénovateurs et diagnostiqueurs d'intégrer le scoring BuildGreen dans leurs outils existants
5. **Investir dans les algorithmes de priorisation** : le vrai avantage compétitif est l'ordre d'intervention optimal, pas l'étiquette DPE brute
6. **Coût d'infrastructure minimal en phase 1** (< 5 EUR/mois) → aucun risque financier

**Prochaine étape :** valider le marché par 5-10 interviews de bailleurs sociaux et rénovateurs avant de lancer le développement du POC.

---

## 10. Sources

- BDNB data.gouv.fr : https://www.data.gouv.fr/datasets/base-de-donnees-nationale-des-batiments/
- BDNB documentation : https://bdnb.io/documentation/
- BDNB modèle de données interactif : https://bdnb.io/schema/latest/
- BDNB prédictions DPE : https://bdnb.io/documentation/predictions_dpe/
- BDNB traitement DPE ADEME : https://bdnb.io/documentation/methode_traitement_dpe/
- BDNB offres API : https://bdnb.io/services/services_api/
- BDNB GitLab : https://gitlab.com/BDNB/base_nationale_batiment
- ADEME DPE arrêté 2021 : https://data.ademe.fr/datasets/dpe-v2-logements-existants
- DVF data.gouv.fr : https://www.data.gouv.fr/datasets/demandes-de-valeurs-foncieres/
- Licence Ouverte v2 : https://www.etalab.gouv.fr/licence-ouverte-v2-0