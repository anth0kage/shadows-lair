# BuildGreen Analytics — Feuille de route produit

Rapport rédigé par Igris le 2026-10-04. Classe les fonctions par valeur client et effort technique, identifie l'indispensable avant ouverture des ventes.

---

## 1. État des lieux

- **Site** : construit, publié sur buildgreen.app, parcours d'achat spécifié (SPEC.md v1)
- **Paiement** : Stripe en mode test, activation live bloquée par l'absence d'espace client v1
- **Données** : 1 département (13 — Bouches-du-Rhône) en cours d'import par Iron
- **Monarque** : a testé l'espace client actuel, deux défauts relevés :
  1. Adresse non trouvée → affiche "0/100" et "N/C" au lieu d'un message clair
  2. Les fiches affichent un identifiant interne au lieu de l'adresse lisible
- **Offres** : Artisan 250 €/mois, Bureau d'études 600 €/mois, Institutionnel sur devis (réunion 2026-10-03)

Contrainte : VPS 13 Go libres / 40 Go. Import départemental progressif uniquement.

---

## 2. Classement des fonctions

Chaque fonction est notée sur deux axes :
- **Valeur** : Indispensable / Forte / Moyenne / Faible (évaluation Igris basée sur faisabilité, SPEC, scoring Tusk)
- **Effort** : Faible (1-2 h) / Moyen (demi-journée) / Lourd (1-3 jours) / Très lourd (1+ semaine)

### 2.1 Fonctions existantes (déjà spécifiées dans SPEC §4 et §8)

| # | Fonction | Valeur | Effort | Source BDNB / Note |
|---|----------|--------|--------|---------------------|
| 1 | Score BuildGreen 0-100 (5 composantes) | Indispensable | Moyen | Cœur du produit. Pondérations 35/25/15/15/10 déjà décidées. |
| 2 | Étiquette DPE estimée (A-G) | Indispensable | Faible | Colonne `etiquette_dpe` dans `batiment_groupe_simulations_dpe` |
| 3 | Indice de fiabilité (1-5) | Indispensable | Faible | Colonne `fiabilite` — crédibilité obligatoire |
| 4 | Fiche bâtiment (agrégat 1+2+3+sources+millésime) | Indispensable | Moyen | Vue unifiée, déjà spécifiée |
| 5 | Recherche par adresse (BAN) | Indispensable | Moyen | API BAN gratuite + matching PostGIS |
| 6 | Export CSV | Forte | Faible | Déjà dans la spec existante |
| 7 | Quota mensuel et suivi | Forte | Faible | Logique métier (compteur + date) |
| 8 | Gestion du périmètre | Forte | Faible | Déjà dans la spec existante |

### 2.2 Nouvelles fonctions — Artisans

| # | Fonction | Valeur | Effort | Source BDNB / Note |
|---|----------|--------|--------|---------------------|
| 9 | Recherche bâtiments F et G dans le périmètre | **Indispensable** | Moyen | Requête SQL : `WHERE etiquette_dpe IN ('F','G') AND ST_Within(...)`. Cas d'usage principal de l'artisan. |
| 10 | Export liste F et G | Forte | Faible | Extension de l'export CSV existant (filtre F/G) |
| 11 | Fiche PDF client (avec logo) | Forte | Moyen | wkhtmltopdf ou WeasyPrint. Logo stocké dans le compte. Template HTML/CSS. |
| 12 | Travaux prioritaires recommandés | Forte | Lourd | Logique métier experte (isolation → chauffage → ventilation). Données partielles : BDNB donne types de murs/toiture/chauffage mais pas l'état. |
| 13 | Gain estimé après travaux | Forte | **Faible** | `gisement_gain_conso_finale_total` déjà dans la table importée. Conversion € = kWh × 0,20 €. |
| 14 | Échéances Loi Climat | Forte | **Faible** | Table statique : G → 2025, F → 2028, E → 2034, D → 2050. Afficher l'échéance selon l'étiquette. |

### 2.3 Nouvelles fonctions — Bureaux d'études

| # | Fonction | Valeur | Effort | Source BDNB / Note |
|---|----------|--------|--------|---------------------|
| 15 | Caractéristiques détaillées (année, surface, logements, chauffage, matériaux) | **Indispensable** | Moyen | Colonnes : `annee_construction`, `surface_habitable`, `nb_logements`, `type_chauffage`, `materiau_murs`, `materiau_toiture`. Toutes disponibles en BDNB Open. |
| 16 | DPE réels et leur date | Forte | **Faible** | Table `batiment_groupe_dpe_representatif_logement` déjà importée. Couvre ~10-15% du parc seulement. |
| 17 | Analyse d'adresses en lot (batch) | **Indispensable** | Moyen | Upload CSV/liste + file d'attente + page de résultats. 500 max par lot (quota Bureau d'études). |
| 18 | Export Excel (.xlsx) | Forte | **Faible** | openpyxl, gratuit. Template avec en-têtes de colonnes et largeurs automatiques. |

### 2.4 Communs à tous les segments

| # | Fonction | Valeur | Effort | Source BDNB / Note |
|---|----------|--------|--------|---------------------|
| 19 | Carte de localisation | Forte | Moyen | Géométrie BDNB → GeoJSON → Leaflet/OSM. PostGIS `ST_AsGeoJSON()`. |
| 20 | Explication en clair de chaque note | Moyenne | **Faible** | Texte statique par composante (bulle d'aide ou infobulle). Réutiliser le contenu du guide de démarrage. |

### 2.5 Fonctions supplémentaires (rapport de faisabilité)

| # | Fonction | Valeur | Effort | Source BDNB / Note |
|---|----------|--------|--------|---------------------|
| 21 | Simulations de rénovation (BBC+PAC) | Forte (BE) | Lourd | Scénario déjà dans BDNB (`simulation_rge`) mais calcul complexe. À réserver à la phase 2. |
| 22 | Calcul ROI rénovation (DVF) | Forte (BE) | Lourd | Croisement DVF non importé. Nécessite import DVF + jointure spatiale IRIS. Phase 2. |
| 23 | API REST publique | Forte (Inst.) | Lourd | Authentification, rate-limiting, documentation. Institutionnel uniquement. Phase 2. |
| 24 | Alertes réglementaires | Moyenne | **Faible** | Cron + email. Vérifie les échéances Climat pour le portefeuille. |
| 25 | Gestion de portefeuille | Forte (Inst.) | Très lourd | Multi-bâtiments, dashboard agrégé, drill-down. Phase 3. |
| 26 | Export GeoJSON | Moyenne | **Faible** | PostGIS → GeoJSON natif. Une ligne de code. |

---

## 3. Corrections immédiates (défauts Monarque)

Avant TOUTE nouvelle fonction, deux corrections sont obligatoires :

| Défaut | Correctif | Effort | Tâche |
|--------|-----------|--------|-------|
| Adresse non trouvée → "0/100, N/C" | Remplacer par "Adresse non trouvée" + pistes (vérifier l'orthographe, essayer sans numéro, la BDNB couvre X% des adresses du département) | Faible | À créer |
| Fiches affichent un identifiant interne | Afficher l'adresse BAN (colonne `adresse` dans la table `batiments`) au lieu du `bdnb_id` | Faible | À créer |

Ces deux défauts sont rédhibitoires : aucun client payant ne tolérera "0/100" ou un identifiant technique. Ils doivent être corrigés avant toute prospection.

---

## 4. Ordre de construction recommandé

### Phase 0 — Corrections (immédiat, < 2 h)
```
C1. [Faible]  Adresse non trouvée → message clair
C2. [Faible]  Fiches → adresse lisible au lieu de l'identifiant
```
→ Assigné à Iron. Livrable : les deux défauts corrigés et visibles sur le VPS.

### Phase 1 — Indispensable avant ouverture Artisan (3-5 jours)
```
P1-1. [Moyen]  Score 0-100 réel (pondérations 35/25/15/15/10) — déjà en cours par Iron
P1-2. [Moyen]  Recherche par adresse BAN + fiche bâtiment complète — déjà spécifié
P1-3. [Faible] Étiquette DPE, fiabilité, gain estimé — colonnes BDNB directes
P1-4. [Moyen]  Recherche F et G (filtre spatial + étiquette)
P1-5. [Faible] Export CSV (existant) + filtre F/G (extension)
P1-6. [Faible] Échéances Loi Climat (table statique)
P1-7. [Faible] Explication en clair de chaque note
```
→ Livrable : espace client Artisan fonctionnel avec données BDNB réelles sur 3 départements.
→ Jalon : ouverture des ventes Artisan (250 €/mois).

### Phase 2 — Bureau d'études (3-5 jours après Phase 1)
```
P2-1. [Moyen]  Caractéristiques détaillées (15 colonnes BDNB)
P2-2. [Faible] DPE réels et date (table déjà importée)
P2-3. [Moyen]  Analyse en lot (upload CSV → file d'attente → résultats)
P2-4. [Faible] Export Excel (.xlsx)
P2-5. [Moyen]  Carte de localisation (Leaflet + GeoJSON)
```
→ Livrable : espace client Bureau d'études sur 5 départements.
→ Jalon : ouverture des ventes Bureau d'études (600 €/mois).

### Phase 3 — Différenciants commerciaux (après rentrée des premiers clients)
```
P3-1. [Moyen]  Fiche PDF client avec logo (WeasyPrint + template HTML)
P3-2. [Faible] Alertes réglementaires (cron + email)
P3-3. [Faible] Export GeoJSON
```
→ Livrable : fiches PDF et alertes pour les clients existants.

### Phase 4 — Fonctions avancées (post-premiers revenus, ~50+ clients)
```
P4-1. [Lourd]  Travaux prioritaires recommandés (logique experte)
P4-2. [Lourd]  Simulations de rénovation (scénario BBC+PAC)
P4-3. [Lourd]  Calcul ROI rénovation (import DVF)
P4-4. [Lourd]  API REST publique (Institutionnel)
P4-5. [Très lourd] Gestion de portefeuille multi-bâtiments
```

---

## 5. Tableau de synthèse valeur × effort

```
VALEUR
  Indispensable  │  2 3 13 14 16      │  1 4 5 9 15 17       │               │
                 │                     │                       │               │
  Forte          │  6 7 8 10 18 20     │  11 19                │  12 21 22 23  │  25
                 │  24 26              │                       │               │
  Moyenne        │                     │                       │               │
                 │                     │                       │               │
  Faible         │                     │                       │               │
                 └─────────────────────┴───────────────────────┴───────────────┴──────────────
                    Faible (1-2h)         Moyen (0.5j)            Lourd (1-3j)    Très lourd (1+s)
                                                                                        EFFORT
```

Lecture : les fonctions en haut à gauche sont les "quick wins" — forte valeur, faible effort.

---

## 6. Ce qui est INDISPENSABLE avant d'ouvrir les ventes

Pour l'offre **Artisan** (première à ouvrir) :
1. ✅ Corrections C1 et C2 (message adresse + affichage adresse)
2. Score 0-100 sur données BDNB réelles (#1)
3. Recherche par adresse + fiche bâtiment (#4, #5)
4. Recherche bâtiments F et G (#9)
5. Export CSV avec filtre F/G (#6, #10)
6. Échéances Loi Climat (#14)
7. Explication du score (#20)

Pour l'offre **Bureau d'études** (seconde à ouvrir), en plus de ce qui précède :
8. Caractéristiques détaillées (#15)
9. Analyse en lot (#17)
10. Export Excel (#18)

---

## 7. Prochaines étapes

1. **Ce rapport** est soumis au Monarque pour validation de l'ordre de construction.
2. **Iron** reçoit la tâche de construction dans l'ordre ci-dessus, phase par phase.
3. **Tusk et Beru** pourront affiner l'évaluation valeur/effort dans leurs tâches respectives (t_c6f6a22b, t_7e07a1db) — ce rapport constitue la base, leurs retours seront intégrés en révision.
4. **Après chaque phase**, Bellion teste et le Monarque valide par une quête d'achat de contrôle.

---

*Sources : buildgreen-faisabilite.md, SPEC.md, réunion 2026-10-03-buildgreen-donnees.md, scoring Tusk (mission-test.md).*