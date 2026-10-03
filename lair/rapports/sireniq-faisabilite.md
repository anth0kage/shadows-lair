# SirenIQ — Faisabilité technique & architecture

*Rapport d'analyse pour Tusk — 3 octobre 2026*
*Auteur : Beru (ombre de la recherche)*

---

## 1. Résumé exécutif

La **Base SIRENE** de l'INSEE est le registre national exhaustif des entreprises et établissements français. Elle contient ~32 millions d'unités légales et ~50 millions d'établissements depuis 1973, mise à jour mensuellement, sous **Licence Ouverte v2** (réutilisation commerciale autorisée). Le volume des données (2,2 Go en Parquet pour les seuls établissements actifs) impose une architecture robuste, mais le format Parquet + l'écosystème open source rendent un SaaS B2B d'intelligence économique techniquement faisable en Phase 1 (zéro dépense, zéro compte).

**Verdict : faisable. Risque principal : RGPD et concurrence établie.**

---

## 2. Analyse détaillée de la source SIRENE

### 2.1 Identification

| Champ | Valeur |
|-------|--------|
| Nom | Base Sirene des entreprises et de leurs établissements (SIREN, SIRET) |
| ID data.gouv.fr | `5b7ffc618b4c4169d30727e0` |
| Producteur | INSEE (Institut national de la statistique) |
| Licence | **Licence Ouverte v2** (LO/OL v2) — réutilisation commerciale libre |
| API Sirene v3 | https://portail-api.insee.fr (semi-ouvert, inscription obligatoire) |
| Fréquence | Mensuelle (stocks) + quotidienne via API |
| Couverture temporelle | 1973-01-01 → aujourd'hui |
| Dernière mise à jour (stock) | 1er octobre 2026 |
| Téléchargements cumulés | 3 573 317 |
| Réutilisations | 208 |
| Abonnés | 203 |

### 2.2 Structure des données : les 6 fichiers stock

#### Fichier 1 : StockUniteLegale (unités légales — état courant)
- **35 champs**, 934 Mo (ZIP) / 681 Mo (Parquet)
- SIREN, dénomination, nom/prénom, sigle, catégorie juridique, code APE/NAF, tranche d'effectifs, date de création, état administratif, ESS, société à mission, caractère employeur, NAF2025 anticipé, statut de diffusion (RGPD)

#### Fichier 2 : StockEtablissement (établissements — état courant)
- **54 champs**, 2 746 Mo (ZIP) / 2 119 Mo (Parquet)
- SIRET, NIC, adresse complète (numéro, voie, code postal, commune), coordonnées Lambert, enseigne, code APE/NAF, tranche d'effectifs, qualité siège, état administratif, adresse secondaire, NAF2025 anticipé, statut de diffusion

#### Fichier 3 : StockUniteLegaleHistorique (historique des unités légales)
- **28 champs**, 1 207 Mo (ZIP) / 823 Mo (Parquet)
- Périodes historiques avec indicateurs de changement (changementNom, changementDenomination, changementActivitePrincipale, etc.)

#### Fichier 4 : StockEtablissementHistorique (historique des établissements)
- **18 champs**, 1 187 Mo (ZIP) / 837 Mo (Parquet)
- Périodes historiques avec indicateurs de changement (état administratif, enseigne, activité)

#### Fichier 5 : StockEtablissementLiensSuccession (liens de succession)
- **6 champs**, 115 Mo (ZIP) / 114 Mo (Parquet)
- SIRET prédécesseur → successeur, date, transfert de siège, continuité économique

#### Fichier 6 : StockDoublons (doublons SIREN)
- **1 Mo** (ZIP/Parquet)
- Liste des SIREN en doublon avec date de dernier traitement

### 2.3 Volumétrie estimée

| Fichier | Lignes estimées | Taille ZIP | Taille Parquet |
|---------|----------------|------------|-----------------|
| StockUniteLegale | ~32 M | 934 Mo | 681 Mo |
| StockEtablissement | ~50 M | 2 746 Mo | 2 119 Mo |
| StockUniteLegaleHistorique | ~80 M | 1 207 Mo | 823 Mo |
| StockEtablissementHistorique | ~100 M | 1 187 Mo | 837 Mo |
| StockEtablissementLiensSuccession | ~5 M | 115 Mo | 114 Mo |
| StockDoublons | ~100 k | 1 Mo | 1 Mo |
| **Total** | **~267 M lignes** | **~6,2 Go** | **~4,8 Go** |

Note : ~10 M d'unités légales actives, ~20 M d'établissements actifs (estimation).

### 2.4 Qualité et complétion

| Critère | Évaluation |
|---------|-----------|
| Fraîcheur | Excellente — stock mis à jour mensuellement, API quotidienne |
| Complétude des champs | Élevée pour les champs administratifs ; adresses parfois incomplètes pour les personnes physiques ayant fait opposition |
| Taux de géocodage | Coordonnées Lambert présentes pour la majorité des établissements |
| Données à caractère personnel | **Oui** (nom, prénom, adresse des personnes physiques) → obligations RGPD |
| Statut de diffusion | Champ `statutDiffusion` : `O` = diffusion publique, `P` = diffusion partielle (opposition) |
| Évolution 2027 | NAF 2025 remplace NAF Rev2 à partir de janvier 2027 ; CSV déprécié au profit de Parquet au S2 2027 |

### 2.5 Contraintes légales

1. **Licence Ouverte v2** : réutilisation commerciale libre, obligation de mentionner la source (INSEE) et la date de mise à jour.
2. **RGPD** : les données des personnes physiques (entrepreneurs individuels) sont soumises au RGPD. Le champ `statutDiffusionUniteLegale = 'P'` signale une opposition : les données d'identité et de géolocalisation doivent être masquées. Obligation de tenir compte du statut de diffusion le plus récent.
3. **Article R.123-232 du Code de commerce** : les données des représentants légaux ne sont pas diffusées en Open Data.
4. **Pas de restriction d'usage commercial** sous LO v2.

---

## 3. Concurrence

| Produit | Positionnement | Données SIRENE | Forces | Faiblesses |
|---------|---------------|----------------|--------|-----------|
| **societe.com** | Fiches entreprises + scoring | Oui | Marque connue, SEO massif | Modèle freemium/publicitaire |
| **Pappers** | Fiches entreprises + documents légaux | Oui | Gratuit, documents légaux, API | Monétisation encore jeune |
| **Ellisphere** (ex-Coface) | Scoring crédit B2B | Oui | Scoring historique enrichi | Payant, orienté risque |
| **Infogreffe** | Registre du commerce | Oui | Documents officiels (KBIS) | Interface datée, payant |
| **Manageo** | Annuaires + prospection B2B | Oui | Listes qualifiées, export | Pas de dashboard avancé |
| **Verif.com** | Surveillance d'entreprises | Oui | Alertes de modification | Interface datée |
| **Kompass** | Annuaires B2B mondial | Partiel | International | Payant, données vieillissantes |
| **Annuaire des Entreprises** (DINUM) | Recherche SIRET gratuite | Oui | Officiel, gratuit | Pas de fonctionnalités B2B avancées |

**Opportunité différenciante** : un SaaS centré sur l'**analyse de portefeuille B2B** (dashboard prospect, scoring personnalisé, veille concurrentielle sectorielle, enrichissement CRM), avec des données fraîches et une UX moderne, n'existe pas encore de façon satisfaisante. Les acteurs existants sont soit généralistes (societe.com), soit orientés conformité (Infogreffe), soit trop chers pour les PME (Ellisphere).

---

## 4. Architecture technique proposée

### 4.1 Principes
- **Phase 1** : autohébergement, stack 100% open source, zéro dépense
- **Données fraîches** : ingestion mensuelle du stock + API Sirene pour les delta quotidiens
- **Recherche full-text** : dénominations, enseignes, codes NAF
- **Géospatial** : filtrage par zone géographique (coordonnées Lambert → WGS84)
- **API REST** : exposée aux clients B2B
- **Dashboard** : analytics sectoriel, cartographie, alertes

### 4.2 Schéma architectural

```
┌──────────────────────────────────────────────────────────────┐
│                        DATA INGESTION                        │
│                                                              │
│  data.gouv.fr (stock mensuel)    API Sirene (delta quotidien)│
│         │ Parquet/CSV                    │ JSON/XML          │
│         ▼                               ▼                   │
│  ┌─────────────┐              ┌──────────────────┐           │
│  │   DuckDB    │──────────────│  Python (FastAPI) │           │
│  │ (ETL/Parquet│              │   scheduler       │           │
│  │  parsing)   │              │   (APScheduler)   │           │
│  └──────┬──────┘              └────────┬─────────┘           │
│         │                              │                     │
│         ▼                              ▼                     │
│  ┌─────────────────────────────────────────────┐             │
│  │          PostgreSQL 16 + PostGIS             │             │
│  │  • sirene.unites_legales                     │             │
│  │  • sirene.etablissements                     │             │
│  │  • sirene.unites_legales_historique          │             │
│  │  • sirene.etablissements_historique          │             │
│  │  • sirene.liens_succession                   │             │
│  │  • Index: GIN trigram (dénomination),        │             │
│  │    GiST (géospatial), B-tree (SIREN/SIRET)   │             │
│  └───────────────────┬─────────────────────────┘             │
│                      │                                       │
│                      ▼                                       │
│  ┌─────────────────────────────────────────────┐             │
│  │              Redis (cache)                    │             │
│  │  Cache de fiches entreprise, résultats        │             │
│  │  de recherche, rate limiting API              │             │
│  └───────────────────┬─────────────────────────┘             │
│                      │                                       │
│         ┌────────────┴────────────┐                          │
│         ▼                         ▼                          │
│  ┌──────────────┐        ┌──────────────────┐                │
│  │  FastAPI      │        │  React / Next.js  │               │
│  │  (REST API)   │        │  (Dashboard SPA)   │               │
│  │               │        │                    │               │
│  │  /api/v1/     │        │  • Recherche       │               │
│  │  entreprises/ │        │  • Fiches          │               │
│  │  recherche/   │        │  • Cartographie    │               │
│  │  statistiques/│        │  • Alertes         │               │
│  │  alertes/     │        │  • Export          │               │
│  └──────┬───────┘        └──────────────────┘                │
│         │                                                    │
│         ▼                                                    │
│  ┌──────────────┐                                            │
│  │  Keycloak     │  Authentification OAuth2/OIDC              │
│  │  (multi-      │                                            │
│  │   tenant)     │                                            │
│  └──────────────┘                                            │
│                                                              │
│         ┌─────────────────────────┐                          │
│         │  Nginx (reverse proxy)   │                          │
│         │  + Let's Encrypt (SSL)   │                          │
│         └─────────────────────────┘                          │
└──────────────────────────────────────────────────────────────┘
```

### 4.3 Briques open source retenues

| Brique | Rôle | Justification |
|--------|------|---------------|
| **DuckDB** | ETL Parquet → PostgreSQL | Lit le Parquet directement, pas besoin de Spark |
| **PostgreSQL 16 + PostGIS** | Base principale | Full-text search (pg_trgm), géospatial, JSONB, mature |
| **Redis** | Cache + rate limiting | Simple, performant, pas d'alternative nécessaire |
| **FastAPI** | API REST | Python, async, OpenAPI auto-généré, écosystème data science |
| **React + Vite** (ou htmx) | Dashboard frontend | React pour SPA riche, htmx si on veut rester simple |
| **Keycloak** | Authentification | Multi-tenant, OIDC, RBAC, self-hosted gratuit |
| **Nginx** | Reverse proxy + SSL | Standard, Let's Encrypt intégré |
| **APScheduler** | Planification ingestion | Léger, intégré dans l'app FastAPI |
| **Leaflet.js / Maplibre** | Cartographie | Open source, gratuit, tuiles OSM |
| **Apache Superset** (optionnel) | Analytics dashboard | Alternative si le dashboard custom est trop lourd |

### 4.4 Dimensionnement PostgreSQL estimé

| Table | Lignes | Taille estimée (~150 o/ligne + index) |
|-------|--------|--------------------------------------|
| unites_legales | 32 M | ~8 Go |
| etablissements | 50 M | ~15 Go |
| unites_legales_historique | 80 M | ~18 Go |
| etablissements_historique | 100 M | ~20 Go |
| liens_succession | 5 M | ~1 Go |
| doublons | 100 k | ~20 Mo |
| **Total** | **~267 M** | **~62 Go** (avec index) |

Serveur recommandé : 16 Go RAM, 8 vCPU, 200 Go SSD — type VPS milieu de gamme (~40-60 €/mois en production, mais Phase 1 en local).

---

## 5. Plan d'ingestion

### 5.1 Script ETL (DuckDB → PostgreSQL)

```python
# Pseudocode du pipeline d'ingestion mensuel
import duckdb
import psycopg2

def ingest_stock():
    # 1. Télécharger les Parquet depuis data.gouv.fr (URL stables)
    # 2. DuckDB : lire Parquet, nettoyer, convertir Lambert→WGS84
    # 3. DuckDB : diff avec le mois précédent (INSERT/UPDATE/DELETE)
    # 4. PostgreSQL : UPSERT par lot avec ON CONFLICT
    # 5. Mettre à jour les index, VACUUM ANALYZE
```

### 5.2 Fréquence
- **Stock complet** : 1x/mois (le 2 du mois, automatique)
- **Delta via API Sirene** : 1x/jour (pour les enregistrements modifiés)
- **API Sirene v3** nécessite un compte INSEE (Phase 1 possible via inscription gratuite)

---

## 6. Risques techniques

| Risque | Probabilité | Impact | Mitigation |
|--------|------------|--------|------------|
| **Volume PostgreSQL** — 60+ Go, performances requêtes | Élevée | Moyen | Partitionnement par département/année, index GIN trigram, cache Redis, read replicas |
| **RGPD — statut de diffusion** | Élevée | Élevé | Filtrer `statutDiffusion='P'` systématiquement ; purge journalière des données de personnes physiques opposées |
| **NAF 2025** — double codification + bascule | Certaine (janvier 2027) | Moyen | Gérer les deux codes en parallèle ; prévoir la migration du schéma |
| **Dépréciation CSV → Parquet** | Certaine (S2 2027) | Faible | Déjà en Parquet ; pas d'impact si le pipeline est conçu pour Parquet |
| **API Sirene — quotas/throttling** | Moyenne | Moyen | Cache agressif, ingestion la nuit, fallback sur les stocks mensuels |
| **Concurrence établie** (Pappers, societe.com) | Élevée | Élevé | Positionnement différencié : analytics B2B prospect vs annuaires généralistes |
| **Évolution du schéma INSEE** | Moyenne | Faible | Tests automatisés de validation du schéma avant ingestion |
| **Coûts d'hébergement en production** | Moyenne | Moyen | Optimiser les index ; commencer petit ; scale vertical avant horizontal |

---

## 7. Prochaines étapes (Phase 1 — zéro dépense)

1. **PoC d'ingestion** : script DuckDB → PostgreSQL local avec le fichier StockEtablissement d'octobre 2026
2. **Schéma PostgreSQL** : création des tables, index, contraintes
3. **API FastAPI minimale** : `/entreprises/{siren}`, `/recherche?q=...`
4. **Maquette dashboard** : 3 écrans (recherche, fiche entreprise, carte)
5. **Analyse de rentabilité** : personas, pricing, TAM/SAM/SOM

---

## 8. Sources

- Base SIRENE sur data.gouv.fr : `https://www.data.gouv.fr/datasets/5b7ffc618b4c4169d30727e0`
- API Sirene v3 : `https://portail-api.insee.fr/catalog/api/2ba0e549-5587-3ef1-9082-99cd865de66f`
- Schémas CSV des variables : fichiers `dessin-de-fichier` sur la page data.gouv.fr
- Licence Ouverte v2 : `https://www.etalab.gouv.fr/licence-ouverte-open-licence`
- RGPD / CNIL : `https://www.cnil.fr/fr/reglement-europeen-protection-donnees`