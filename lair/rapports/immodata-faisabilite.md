# ImmoData Pro — Faisabilité technique & architecture

Rapport de recherche — Phase 1 (zéro dépense, zéro compte)
Date : 2026-10-03
Profil : Beru (recherche)

---

## 1. Synthèse

Le dataset DVF (Demandes de Valeurs Foncières) de la DGFiP est publiable, mûr, et structurellement adapté à un SaaS B2B d'estimation immobilière. Il couvre 5 années de transactions (2021-2025), est mis à jour semestriellement (avril et octobre), et est sous Licence Ouverte v2 permettant la réutilisation commerciale. Les contraintes légales (RGPD, interdiction de ré-identification et d'indexation moteur de recherche) sont gérables techniquement avec un système d'authentification et un robots.txt approprié. Une version géolocalisée enrichie (DVF géolocalisées, par Etalab/Cerema) ajoute les coordonnées WGS-84, facilitant l'usage cartographique. Une API REST "Données foncières" (Cerema, en beta) fournit un accès structuré aux données enrichies. Le marché est compétitif (MeilleursAgents, Yanport, Castorus) mais aucun acteur ne combine DVF + enrichissements multi-sources + modèles ML avancés dans une API SaaS B2B pure.

---

## 2. Analyse du dataset DVF

### 2.1 Source et producteur

- **Producteur** : Direction Générale des Finances Publiques (DGFiP), Ministères économiques et financiers
- **Page data.gouv.fr** : https://www.data.gouv.fr/fr/datasets/demandes-de-valeurs-foncieres/
- **Identifiant** : `5c4ae55a634f4117716d5656`
- **Création** : 2019-01-25
- **Dernière modification** : 2026-05-18
- **Dernière mise à jour des données** : 2026-04-05
- **Licence** : Licence Ouverte / Open Licence v2.0 (LO/OL v2) — réutilisation commerciale autorisée
- **Origine légale** : Décret n°2018-1350 du 28 décembre 2018, article L.112 A du Livre des Procédures Fiscales
- **Finalité légale** : "Concourir à la transparence des marchés fonciers et immobiliers"

### 2.2 Volumétrie

| Millésime | Format | Taille compressée | URL |
|-----------|--------|-------------------|-----|
| 2025 | txt.zip | 66,4 MB | valeursfoncieres-2025.txt.zip |
| 2024 | txt.zip | 62,3 MB | valeursfoncieres-2024.txt.zip |
| 2023 | txt.zip | 68,4 MB | valeursfoncieres-2023.txt.zip |
| 2022 | txt.zip | 83,2 MB | valeursfoncieres-2022.txt.zip |
| 2021 | txt.zip | 82,8 MB | valeursfoncieres-2021.txt.zip |

- **Total compressé** : ~363 MB (5 millésimes)
- **Estimation décompressée** : ~1-2 GB par fichier, soit ~5-10 GB total
- **DVF géolocalisées (fichier unique 2021-2025)** : 523 MB compressé (csv.gz), ~3,5 GB décompressé (d'après `analysis:content-length`)
- **Métriques data.gouv.fr** : 2 039 065 vues, 436 688 téléchargements, 261 réutilisations, 137 followers

### 2.3 Fraîcheur

- **Fréquence de mise à jour** : Semestrielle (avril et octobre)
- **Couverture temporelle** : 5 années glissantes (2021-2025 actuellement)
- **Délai de publication** : Une transaction enregistrée au SPF avant le 31/12 apparaît dans la mise à jour d'avril ; une transaction enregistrée avant le 30/06 apparaît dans celle d'octobre. Délai typique : 4 à 10 mois après la transaction.
- **Important** : Tous les millésimes sont réactualisés à chaque mise à jour (des publications tardives peuvent concerner des années antérieures). Le dernier millésime est toujours incomplet.

### 2.4 Couverture géographique

- **Territoire couvert** : France métropolitaine + DOM-TOM
- **Exclusions** : Alsace (Bas-Rhin, Haut-Rhin), Moselle, Mayotte
- **Granularité spatiale** : Point d'intérêt (adresse), parcelle cadastrale
- **DVF géolocalisées** ajoute : coordonnées WGS-84 (latitude/longitude) à la parcelle

### 2.5 Schéma des colonnes (43 colonnes)

Les colonnes sont décrites dans la notice officielle de la DGFiP. Voici une classification fonctionnelle :

**Identification de la mutation (colonnes 1-9)**
- Code service CH, Référence document
- Colonnes 3-7 : Non publiées (décret)
- N° de disposition (plusieurs mutations possibles par acte)
- Date de mutation (format ISO-8601 depuis octobre 2019)
- Nature de la mutation : Vente / VEFA / Vente terrain à bâtir / Adjudication / Expropriation / Échange

**Valeur (colonne 11)**
- Valeur foncière (prix net vendeur, hors frais de notaire, hors meubles ; inclut TVA et frais d'agence si à charge du vendeur)

**Adresse (colonnes 12-20)**
- N° de voie, B/T/Q (indice de répétition), Type de voie, Code voie (Rivoli)
- Libellé voie, Code postal, Commune, Code département, Code commune (INSEE)

**Références cadastrales (colonnes 21-23)**
- Préfixe de section, Section, N° de plan

**Lots de copropriété (colonnes 24-35)**
- 5 premiers lots : N° de volume, Surface Carrez
- Nombre total de lots

**Descriptif du bien bâti (colonnes 36-40)**
- Code type local : 1=Maison, 2=Appartement, 3=Dépendance (isolée), 4=Local industriel/commercial
- Type local (libellé)
- Identifiant local (non publié)
- Surface réelle bâti (m²)
- Nombre de pièces principales

**Terrain (colonnes 41-43)**
- Code nature culture (ex: AB=terrain à bâtir, B=bois, J=jardins, T=terres, S=sols...)
- Nature culture spéciale (ex: JARD, VIGNE, PECHE, ABRIC... — 100+ codes)
- Surface terrain (m²)

### 2.6 DVF géolocalisées (version enrichie)

Dataset dérivé publié par Etalab/Cerema : https://www.data.gouv.fr/fr/datasets/demandes-de-valeurs-foncieres-geolocalisees/

**Améliorations par rapport au DVF brut :**
- CSV avec séparateur virgule et encodage UTF-8
- Coordonnées WGS-84 (latitude/longitude) à la parcelle
- Dates au format ISO-8601 (YYYY-MM-DD)
- Codes INSEE et codes postaux normalisés
- Codes voie normalisés (FANTOIR, 4 caractères)
- Identifiant de parcelle compatible fichiers cadastraux Etalab
- Jointure avec les tables de référence (natures de culture)
- Valeurs décimales normalisées (point comme séparateur)
- Libellés de communes riches (accentués)
- Disponible en fichier unique national ou par département/commune
- Colonnes renommées pour traitement informatique (ex: `id_mutation`, `date_mutation`, `valeur_fonciere`, `type_local`, `surface_reelle_bati`, `nombre_pieces_principales`, `longitude`, `latitude`...)

**Volumétrie du fichier unique 2021-2025 :** 523 MB compressé (csv.gz), ~3,5 GB décompressé.

---

## 3. Contraintes légales et réglementaires

### 3.1 Licence

**Licence Ouverte v2.0 (LO/OL v2)** — https://www.etalab.gouv.fr/licence-ouverte-v20

- Réutilisation commerciale : **autorisée**
- Modification, adaptation : **autorisées**
- Obligation : mention de la source (DGFiP) et date de mise à jour
- Pas de royalties, pas de restriction de territoire
- Compatible ODbL, CC-BY, OGL

### 3.2 Protection des données personnelles (RGPD)

Les fichiers DVF contiennent des données à caractère personnel (adresses, parcelles). Contraintes :

1. **Interdiction de ré-identification** (art. R112 A-3 LPF) : Les traitements ne peuvent avoir ni pour objet ni pour effet de permettre la ré-identification des personnes concernées, y compris par recoupement indirect avec d'autres sources.

2. **Interdiction d'indexation par les moteurs de recherche** (art. R112 A-3 LPF) : Les données ne peuvent être indexées sur des moteurs de recherche externes. Mesures requises : robots.txt + captcha ou mécanisme équivalent pour s'assurer que l'émetteur d'une requête est un internaute.

3. **Finalité limitée** : La réutilisation n'est autorisée que pour "concourir à la transparence des marchés fonciers et immobiliers".

4. **RGPD + Loi Informatique et Libertés** : Information des personnes, droit d'accès/rectification/effacement, durée de conservation définie.

### 3.3 Implications pour le SaaS

- **Authentification obligatoire** : Toute API ou dashboard exposant les données doit être derrière une couche d'authentification. Pas d'accès public sans login.
- **Robots.txt + noindex** : Toutes les pages exposant des données individuelles doivent être protégées.
- **Agrégation sans identification** : Les estimations et statistiques agrégées (prix/m² par quartier) sont autorisées et constituent l'usage normal.
- **Pas de croisement nominatif** : Ne pas croiser avec d'autres sources permettant de nommer les propriétaires.
- **Mention de source** : "Données DGFiP/DVF, mise à jour [date]" sur chaque écran.

---

## 4. APIs et sources de données externes

### 4.1 API Données foncières (Cerema) — GRATUIT, accès restreint

- **URL** : https://www.data.gouv.fr/dataservices/api-donnees-foncieres
- **Portail d'accès** : https://portaildf.cerema.fr
- **Documentation** : https://datafoncier.cerema.fr/
- **Statut** : Beta
- **Accès** : Restreint. Collectivités et administrations : oui. Entreprises/associations : sous condition (demande d'habilitation). Privés : non.
- **Contact** : datafoncier@cerema.fr
- **Données exposées** : Transactions foncières et immobilières issues de DVF, indicateurs de territoires enrichis par le Cerema et la DGALN
- **Jeux de données liés** : 3 datasets

**Stratégie d'accès** : La demande d'habilitation "sous condition" pour les entreprises est une piste. Un positionnement "service d'intérêt général pour la transparence du marché" pourrait faciliter l'obtention. Alternative : utiliser les fichiers DVF en téléchargement direct (toujours disponibles sans restriction).

### 4.2 API Adresse (BAN) — GRATUIT, ouvert

- **URL** : https://api-adresse.data.gouv.fr/
- **Fonction** : Géocodage et reverse géocodage des adresses françaises
- **Licence** : Ouverte
- **Usage** : Conversion adresse → coordonnées pour les données DVF brutes (non géolocalisées), normalisation des adresses

### 4.3 API Cadastre (IGN Géoportail) — GRATUIT, ouvert

- **URL** : https://www.geoportail.gouv.fr/
- **Services** : WMS/WFS pour les parcelles cadastrales
- **Fonction** : Récupération des contours de parcelles, surfaces exactes, géométrie
- **Licence** : Etalab Ouverte pour les données, clé API gratuite nécessaire pour les services

### 4.4 API Notaires de France / PERVAL — PAYANT, accès professionnel

- **URL** : https://www.immobilier.notaires.fr/
- **Base** : PERVAL (base notariale des transactions)
- **Contenu** : Prix de vente, indices Notaires-INSEE, volumes de transactions
- **Accès** : Réservé aux professionnels de l'immobilier, partenariats notariaux
- **Coût estimé** : Variable selon usage, probablement 1 000-5 000 €/an pour un accès API
- **Valeur ajoutée** : Transactions plus récentes que DVF (pas de délai de publication), données sur les biens non couverts par DVF (Alsace-Moselle, Mayotte), typologies plus fines

### 4.5 API BDNB (Base de Données Nationale des Bâtiments) — GRATUIT/ouvert

- **URL** : https://bdnb.io/
- **Contenu** : Caractéristiques des bâtiments (surface, usage, étages, matériaux, DPE, consommation énergétique)
- **Licence** : Ouverte
- **Usage** : Enrichissement des estimations avec des attributs bâtimentaires absents de DVF

### 4.6 API DPE (Diagnostics de Performance Énergétique) — GRATUIT/ouvert

- **URL** : https://data.ademe.fr/datasets/dpe-v2-logements-existants
- **Contenu** : ~8 millions de DPE (étiquette énergie, consommation, GES)
- **Licence** : Ouverte
- **Usage** : Enrichissement des modèles d'estimation avec la performance énergétique

### 4.7 Immo API (service tiers) — PAYANT

- **URL** : https://immoapi.app/
- **Contenu** : API unifiée d'accès aux transactions immobilières en France
- **Cible** : Développeurs SaaS, proptech
- **Coût** : Probablement freemium/abonnement

---

## 5. Architecture technique proposée

### 5.1 Vue d'ensemble

```
┌─────────────────────────────────────────────────────────────┐
│                    SOURCES DE DONNÉES                        │
├──────────┬──────────┬─────────┬────────┬──────────┬─────────┤
│ DVF brut │ DVF géo  │ BAN     │ BDNB   │ DPE      │ Cadastre│
│ (txt.zip)│ (csv.gz) │ (API)   │ (csv)  │ (csv)    │ (WFS)   │
└────┬─────┴────┬─────┴────┬────┴───┬────┴────┬─────┴────┬────┘
     │          │          │        │         │          │
     ▼          ▼          ▼        ▼         ▼          ▼
┌─────────────────────────────────────────────────────────────┐
│              INGESTION & ETL (Apache Airflow)                │
│  - Téléchargement semestriel des fichiers                    │
│  - Parsing CSV/TXT, normalisation, contrôle qualité          │
│  - Jointure spatiale parcelles/cadastre                      │
│  - Enrichissement BDNB/DPE/BAN                              │
│  - Calcul d'indicateurs dérivés (prix/m², tendances)        │
└─────────────────────────────────────────────────────────────┘
     │
     ▼
┌─────────────────────────────────────────────────────────────┐
│          DATA WAREHOUSE (PostgreSQL + PostGIS)               │
│  - Transactions DVF (table principale)                       │
│  - Référentiel géographique (communes, IRIS, quartiers)      │
│  - Indicateurs agrégés pré-calculés                          │
│  - Cache des enrichissements externes                        │
│  - Index géospatiaux, full-text search                       │
└─────────────────────────────────────────────────────────────┘
     │
     ▼
┌─────────────────────────────────────────────────────────────┐
│              API REST (FastAPI ou Go/Gin)                    │
│  - /api/v1/estimate — Estimation d'un bien                   │
│  - /api/v1/transactions — Recherche de transactions          │
│  - /api/v1/indicators — Indicateurs de marché (prix/m²,...)  │
│  - /api/v1/geo — Données géographiques                      │
│  - Authentification JWT + rate limiting                      │
│  - Mise en cache Redis                                       │
└─────────────────────────────────────────────────────────────┘
     │
     ▼
┌─────────────────────────────────────────────────────────────┐
│            DASHBOARD B2B (React/Next.js)                     │
│  - Carte interactive (Leaflet/MapLibre)                      │
│  - Moteur d'estimation paramétrable                          │
│  - Rapports PDF exportables                                  │
│  - Alertes de marché personnalisées                          │
│  - Gestion multi-comptes (agences, équipes)                  │
└─────────────────────────────────────────────────────────────┘
```

### 5.2 Stack technique recommandé

| Couche | Technologie | Justification |
|--------|------------|---------------|
| Ingestion | Apache Airflow ou Prefect | Orchestration des téléchargements semestriels, qualité pipeline |
| Parsing/ETL | Python (Pandas, Dask pour >10 GB) | CSV parsing, jointures, calculs statistiques |
| Base de données | PostgreSQL 16 + PostGIS | Spatial indexes, requêtes géographiques, robustesse |
| Cache | Redis | Cache des résultats d'API et requêtes fréquentes |
| API | FastAPI (Python) ou Go + Gin | Performances, typage, async |
| Frontend | Next.js + MapLibre GL JS | SSR pour SEO (page marketing), carte performante |
| Hébergement | Scaleway/OVH (souveraineté FR) ou Hetzner | Conformité RGPD, rapport perf/coût |
| Conteneurisation | Docker + Docker Compose (phase 1), Kubernetes (scale) | Reproductibilité, déploiement simplifié |

### 5.3 Dimensionnement estimé (phase 1)

| Ressource | Estimation |
|-----------|------------|
| Stockage BDD | 20-50 GB (5 ans DVF + enrichissements + index) |
| RAM | 8-16 GB (PostgreSQL cache, traitement ETL) |
| CPU | 4-8 vCPU (traitements batch semestriels) |
| Bande passante | 100-500 GB/mois selon nombre d'utilisateurs API |
| Coût hébergement | 80-200 €/mois (VPS/cloud phase 1) |

### 5.4 Pipeline de mise à jour

1. **Trigger** : Cron semestriel (avril et octobre) ou webhook data.gouv.fr
2. **Download** : Téléchargement des 5 fichiers millésimés + DVF géolocalisées
3. **Validation** : Checksums SHA1, contrôle des colonnes, détection d'anomalies
4. **Parsing** : CSV → tables PostgreSQL (bulk insert, ~10-20 min pour 5 fichiers)
5. **Jointures** : Croisement avec BDNB, DPE, cadastre
6. **Recalculs** : Indicateurs agrégés (prix/m² par commune/quartier/type de bien), tendances trimestrielles
7. **Swap** : Remplacement atomique des tables (transaction DDL) pour éviter les downtime
8. **Purge cache** : Invalidation Redis des clés concernées

### 5.5 Modèle d'estimation (approche)

**Méthode hédonique** (recommandée) :
- Régression sur les caractéristiques : surface, nombre de pièces, type de local, étage, année de construction (BDNB), DPE, localisation géographique (k plus proches voisins)
- Segmentation par zone géographique (IRIS/quartier) et type de bien
- Correction temporelle (indice trimestriel local)

**Enrichissements possibles** :
- Proximité transports en commun (GTFS/OpenStreetMap)
- Proximité écoles, commerces (OpenStreetMap)
- Bruit (cartes de bruit stratégiques)
- Historique des permis de construire (data.gouv.fr)

---

## 6. Paysage concurrentiel

### 6.1 Acteurs établis

| Acteur | Positionnement | Accès API | Données utilisées | Forces | Faiblesses |
|--------|---------------|-----------|-------------------|--------|------------|
| **MeilleursAgents** (AVIV Group) | Estimation grand public + pro | Non (portail propriétaire) | DVF + notaires + MLS | Marque forte, volume de données | Fermé, pas d'API B2B |
| **Yanport** | Intelligence de marché pro | Oui (API payante) | DVF + annonces web + données propriétaires | API mature, scoring leads | Coûteux, boîte noire |
| **Castorus** | Historique des annonces | Non (plugin navigateur) | Annuaires immobiliers | Détection de baisses de prix | Données DVF limitées |
| **Immo-Data** | Visualisation DVF | Non | DVF + parcellaire | UX simple, gratuit | Pas d'estimation, pas d'API |
| **app.dvf.etalab.gouv.fr** | Consultation DVF grand public (officiel) | Non | DVF géolocalisées | Gratuit, officiel | Pas d'API, pas pro |
| **explore.data.gouv.fr/immobilier** | Exploration DVF | Non | DVF | Gratuit, dataviz | Pas d'estimation |

### 6.2 Positionnement proposé : ImmoData Pro

**Différenciation** :
1. API REST B2B pure — les pros de l'immo intègrent nos estimations dans leurs propres outils (CRM, ERP, portails)
2. Modèle transparent — documentation du modèle d'estimation, intervalles de confiance, données sources citées
3. Multi-source — DVF + BDNB + DPE + cadastre = estimation plus riche que les concurrents qui se limitent à DVF
4. Tarification prédictible — abonnement mensuel basé sur le volume de requêtes, pas de frais cachés
5. RGPD-native — authentification, pas de ré-identification, pas d'indexation = conforme par conception

**Cibles B2B** :
- Agences immobilières indépendantes (estimation AVANT mandat)
- Plateformes SaaS immobilières (intégration API pour enrichir leurs produits)
- Cabinets d'expertise foncière (accès rapide aux comparables)
- Promoteurs (analyse de marché pour projets neufs)
- Banques/assurances (valorisation de patrimoine, scoring crédit)

---

## 7. Opportunités et risques

### 7.1 Opportunités

- **Données de qualité, gratuites, légales** : le DVF est un actif public unique en Europe, maintenu par l'État
- **Marché immobilier en tension** : besoin croissant d'estimations fiables et rapides
- **Écosystème d'enrichissement en croissance** : BDNB, DPE, cadastre ouvert — de plus en plus de données publiques viennent enrichir DVF
- **Absence d'API B2B pure sur données publiques** : Yanport utilise ses propres données, MeilleursAgents est fermé
- **Licence Ouverte v2** : aucune restriction commerciale, pas de royalties

### 7.2 Risques

- **Conformité RGPD** : le principal risque. Un manquement à l'interdiction de ré-identification ou d'indexation peut entraîner des sanctions (amende CNIL jusqu'à 4% du CA). Mettre en place une gouvernance stricte dès le jour 1.
- **Données non exhaustives sur le dernier millésime** : le fichier le plus récent est toujours incomplet (délai de publication). L'estimation sur les 12 derniers mois sera moins fiable.
- **Absence de l'Alsace-Moselle et Mayotte** : ~3% de la population. Les notaires (PERVAL) peuvent combler ce trou mais c'est payant.
- **Concurrence des géants** : AVIV (MeilleursAgents + SeLoger) pourrait ouvrir une API. Leur volume de données est bien supérieur.
- **Évolution réglementaire** : un changement du cadre légal (restriction d'accès DVF) est improbable mais pas impossible.

---

## 8. Prochaines étapes (Phase 2 — PoC)

1. **Téléchargement et parsing** : Récupérer les fichiers DVF géolocalisées (1 fichier csv.gz), le charger dans PostgreSQL+PostGIS. Effort : 2-3 jours.
2. **Indicateurs de base** : Calculer les prix/m² médians par commune et type de bien, tendances trimestrielles. Effort : 2 jours.
3. **API minimale** : `/api/v1/estimate` (entrée : adresse + caractéristiques, sortie : prix estimé + intervalle). Modèle hédonique simple ou KNN. Effort : 3-5 jours.
4. **Dashboard prototype** : Carte interactive de démonstration avec quelques villes. Effort : 3 jours.
5. **Validation légale** : Consulter un juriste spécialisé données personnelles pour valider le dispositif de conformité RGPD. Effort : 1 jour + honoraires.
6. **Entretiens utilisateurs** : 5-10 agences immobilières pour valider le besoin et la proposition de valeur. Effort : 1 semaine.

---

## 9. Sources

- DVF data.gouv.fr : https://www.data.gouv.fr/fr/datasets/demandes-de-valeurs-foncieres/ (API consultée le 2026-10-03)
- DVF géolocalisées : https://www.data.gouv.fr/fr/datasets/demandes-de-valeurs-foncieres-geolocalisees/ (API consultée le 2026-10-03)
- Notice descriptive DVF (PDF DGFiP, 2022-10-17)
- FAQ DVF (PDF DGFiP, 2022-10-17)
- CGU DVF (PDF DGFiP, 2020-10-16)
- Décret n°2018-1350 du 28 décembre 2018
- Article L.112 A du Livre des Procédures Fiscales
- API Données foncières (Cerema) : https://www.data.gouv.fr/dataservices/api-donnees-foncieres
- Licence Ouverte v2.0 : https://www.etalab.gouv.fr/licence-ouverte-v20
- Documentation Datafoncier Cerema : https://datafoncier.cerema.fr/
- Portail Données Foncières : https://portaildf.cerema.fr
- Base Adresse Nationale : https://api-adresse.data.gouv.fr/
- BDNB : https://bdnb.io/
- DPE ADEME : https://data.ademe.fr/datasets/dpe-v2-logements-existants
- Notaires de France : https://www.immobilier.notaires.fr/