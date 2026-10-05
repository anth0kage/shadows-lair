# Produits B2B sur données de marchés publics — Faisabilité & scoring

Rapport produit le 2026-10-05. Phase 1 : zéro dépense, zéro compte, zéro publication.
Combine les fiches d'idées Beru (exploration data.gouv.fr) et la notation Tusk (grille 6 critères).

---

## 1. Synthèse exécutive

**Verdict : 3 idées faisables, toutes au-dessus du seuil Tusk de 70/100.**

Trois produits B2B exploitables dès la phase 1 à partir de données publiques françaises :

| Rang | Produit | Score Tusk | Positionnement | Source primaire |
|------|---------|-----------|----------------|-----------------|
| 1 | **MarchéIntel** | 86/100 | Intelligence concurrentielle post-attribution | DECP (LO/OL v2) |
| 2 | **SubvenScope** | 82/100 | Matching aides/subventions pour cabinets comptables | Aides-entreprises (fr-lo) |
| 3 | **AppelDO** | 72/100 | Veille appels d'offres avec score prédictif | BeauAMP (cc-by-sa) + BOAMP (fr-lo) |

MarchéIntel est prioritaire (ticket élevé 499-2 999 €/mois, concurrence quasi nulle sur l'analyse post-attribution).
SubvenScope est le plus rapide à lancer (55-70 j/h, risque juridique nul, marché vierge).
AppelDO nécessite une clarification juridique préalable du CC-BY-SA de BeauAMP avant d'engager des ressources.

Les trois idées peuvent coexister (cibles distinctes, socles techniques partiellement mutualisables).

---

## 2. MarchéIntel — Intelligence concurrentielle post-attribution

### 2.1 Concept

SaaS B2B qui transforme les données d'attribution des marchés publics en intelligence concurrentielle actionnable :

- **Qui achète quoi ?** Cartographie des acheteurs publics par secteur, région, montant.
- **Qui sont mes concurrents et à quel prix gagnent-ils ?** Tracking des attributions par titulaire, prix moyens par secteur.
- **Quels contrats arrivent à échéance ?** Détection des fins de contrat (dureeRestanteMois → 0) pour anticiper les renouvellements.

La valeur clé : les données d'attribution sont publiées mais dispersées et inexploitables sans consolidation. MarchéIntel les agrège, les nettoie (montants rationalisés, anomalies filtrées), les enrichit (catégories acheteur/titulaire, labels, géolocalisation) et les rend interrogeables.

### 2.2 Sources data.gouv.fr

| Source | Licence | URL |
|--------|---------|-----|
| DECP consolidées tabulaires (Colmo) — source primaire | LO/OL v2 | https://www.data.gouv.fr/datasets/donnees-essentielles-de-la-commande-publique-consolidees-format-tabulaire |
| DECP consolidées (Ministère) — source secondaire | fr-lo | https://www.data.gouv.fr/datasets/donnees-essentielles-de-la-commande-publique-fichiers-consolides |
| Base SIRENE — enrichissement | LO/OL v2 | https://www.data.gouv.fr/datasets/base-sirene-des-entreprises-et-de-leurs-etablissements-siren-siret |

### 2.3 Volumétrie

- Fichier Parquet : ~250 Mo (compressé), CSV : ~2,6 Go
- Période : 2018-2026 (mise à jour quotidienne automatique)
- Estimation : **5 à 10 millions de marchés** sur la période, **~500 K à 1 M nouveaux marchés/an**
- ~50 colonnes enrichies : acheteur, titulaire, montant, CPV, NAF, géolocalisation, durée, offres reçues, labels (RGE, ESS, Bio, etc.), catégorie PME/ETI/GE, considérations sociales et environnementales, sous-traitance, groupements

### 2.4 Licence

**Licence Ouverte v2 (LO/OL v2)** : réutilisation commerciale libre, modification, création de produits dérivés autorisée. Obligation de mentionner la source. Aucune restriction B2B, aucune donnée personnelle.

### 2.5 Faisabilité technique Phase 1 (3 mois)

| Composant | Détail |
|-----------|--------|
| Ingestion | Téléchargement quotidien du Parquet (250 Mo), chargement dans PostgreSQL/PostGIS |
| API | FastAPI exposant ~10 endpoints (recherche CPV, SIRET titulaire, acheteur, région, alertes échéance) |
| Frontend | Dashboard Next.js avec cartographie (Leaflet/MapLibre), tableaux de bord sectoriels, fiches acheteur/titulaire |
| Effort | 60-80 j/h développeur full-stack senior |
| Infra Phase 1 | Serveur 4 vCPU/16 Go RAM, PostgreSQL + Parquet → ~50-100 €/mois (Scalingo/OVH) |

### 2.6 Positionnement B2B

| Cible | Volume | Besoin | Ticket envisagé |
|-------|--------|--------|-----------------|
| Directeurs commerciaux / business developers ETI/PME vendant au secteur public (IT, BTP, conseil, facility, fournitures) | ~50 000 fournisseurs réguliers | Cartographie acheteurs, tracking concurrents, anticipation renouvellements | 499-2 999 €/mois |
| Cabinets de conseil en stratégie, fusions-acquisitions | ~500 cabinets | Due diligence marchés publics | 1 000-2 000 €/mois |

**Concurrence :** VecteurPlus, FranceMarchés, Klekoon (veille appels d'offres, pas d'analyse post-attribution). MégaDonnées, OpenDataSoft (plates-formes généralistes). Aucun acteur ne combine analyse attributions + prédiction renouvellements + intelligence concurrentielle.

**Différenciation :** Données rationalisées (montants corrigés), enrichissement labels (RGE, ESS), prédiction de renouvellement, API-first.

### 2.7 Score Tusk détaillé : 86/100 ✅ LANCER (prioritaire)

| Critère (poids) | Note | Justification |
|-----------------|------|---------------|
| Demande réelle (25) | 22/25 | ~50 000 fournisseurs réguliers. Pain point clair : données dispersées et inexploitables. 3 cas d'usage concrets. |
| Capacité à payer (20) | 18/20 | Ticket 499-2 999 €/mois. Outils comparables (VecteurPlus) prouvent la disposition à payer. Marché secondaire cabinet de conseil. |
| Concurrence (15) | 13/15 | Concurrents focalisés veille AO, pas post-attribution. Aucun concurrent direct sur le triptyque attributions + prédiction + intelligence. |
| Coût de construction (15) | 10/15 | 60-80 j/h, infra 50-100 €/mois. Complexité moyenne : ingestion Parquet 250 Mo/jour, 10 M enregistrements. Volume = difficulté principale. |
| Automatisation (15) | 14/15 | Pipeline ETL quotidien, alertes échéance, dashboards entièrement automatisables. Supervision occasionnelle pour qualité des données. |
| Risque juridique (10) | 9/10 | LO/OL v2 : réutilisation commerciale libre. Données publiques DECP + SIRENE. Aucune donnée personnelle. |

---

## 3. SubvenScope — Matching aides/subventions pour cabinets comptables

### 3.1 Concept

Plateforme SaaS B2B2B qui permet aux **cabinets d'expertise comptable** de proposer à leurs clients PME un service automatisé de matching d'aides publiques et subventions :

- Agrège et nettoie la base de référence des aides aux entreprises (~9 000 dispositifs)
- Croise automatiquement le profil d'une entreprise (NAF, effectif, localisation, âge, projet) avec les critères d'éligibilité
- Génère un **rapport d'éligibilité** en marque blanche pour le cabinet comptable
- Alerte le cabinet quand de nouvelles aides sont publiées correspondant aux profils de leurs clients
- Suit les demandes (tableau de bord multi-clients)

### 3.2 Sources

| Source | Licence | URL |
|--------|---------|-----|
| Base de données des Aides aux Entreprises (DGE) — source primaire | fr-lo | https://www.data.gouv.fr/datasets/base-de-donnees-des-aides-aux-entreprises |
| Base SIRENE — enrichissement | LO/OL v2 | https://www.data.gouv.fr/datasets/base-sirene-des-entreprises-et-de-leurs-etablissements-siren-siret |

**Fichiers composant la base aides-entreprises :**

| Fichier | Taille | Contenu |
|---------|--------|---------|
| aides.csv | 6 Mo | ~9 000 dispositifs (nom, objet, conditions, montant, bénéficiaires) |
| territoires.csv | 7,6 Mo | Lien aide ↔ territoires éligibles |
| contacts.csv | 2,2 Mo | Contacts par aide |
| financeurs.csv | 194 Ko | ~2 000 financeurs |
| domaines.csv, natures.csv, profils.csv, projets.csv, niveaux.csv | < 1 Mo | Vocabulaires contrôlés |

**Note :** L'API Data.Subvention (DINUM) est RESTREINTE aux agents publics. Les subventions attribuées (montants réels) ne sont pas disponibles en open data consolidé au niveau national.

### 3.3 Volumétrie

- aides.csv : 6 Mo, ~9 000 aides
- territoires.csv : 7,6 Mo
- Volume total < 50 Mo en base
- Mise à jour quotidienne (republié depuis data.aides-entreprises.fr)

### 3.4 Licence

**Licence Ouverte (fr-lo)** : réutilisation commerciale libre avec mention de source. Aucune restriction B2B, aucune donnée personnelle. Données republiées par la DGE (Direction Générale des Entreprises).

### 3.5 Faisabilité technique Phase 1 (3 mois)

| Composant | Détail |
|-----------|--------|
| Ingestion | Téléchargement quotidien des CSV, parsing, chargement PostgreSQL |
| Moteur de matching | Règles booléennes (NAF exact/sous-classe, effectif min-max, codes géographiques, type de projet). Pas de ML nécessaire en Phase 1 |
| API | FastAPI — endpoint matching (POST profil entreprise → liste aides éligibles), endpoint alertes |
| Frontend | Dashboard Next.js — import CSV clients du cabinet, rapport PDF par client, configuration alertes |
| Effort | 55-70 j/h (matching rules = essentiel du travail — parsing du langage naturel des conditions) |
| Infra Phase 1 | Légère — 2 vCPU/8 Go, 30-60 €/mois |
| Point d'attention | Les conditions d'éligibilité sont en texte libre. Parsing NLP/NER simple (spaCy) nécessaire pour extraire critères structurés (effectif, âge, secteur). La qualité du matching dépend de ce parsing |

### 3.6 Positionnement B2B

| Cible | Volume | Besoin | Ticket envisagé |
|-------|--------|--------|-----------------|
| Cabinets d'expertise comptable (marque blanche) | 21 000 cabinets (dont ~15 000 indépendants), marché adressable 3 000-5 000 | Service automatisé de détection d'aides pour leurs clients TPE/PME | 149-499 €/mois (freemium 10 clients) |
| CCI, CMA, agences de développement économique | ~200 structures | Accompagnement des entreprises du territoire | 300-800 €/mois |

**Concurrence :**
- aides-entreprises.fr (gratuit, officiel, recherche manuelle)
- Les-aides.fr (CMA, recherche manuelle)
- Bpifrance (guichet unique, pas de matching automatique)
- **Aucun SaaS de matching automatique multi-clients en marque blanche pour cabinets comptables**

**Limite :** Le matching donne l'éligibilité théorique, pas une garantie d'obtention. Les données d'attribution réelles ne sont pas en open data. Le produit est un outil de **détection d'opportunités**, pas de suivi de succès.

### 3.7 Score Tusk détaillé : 82/100 ✅ LANCER

| Critère (poids) | Note | Justification |
|-----------------|------|---------------|
| Demande réelle (25) | 19/25 | 21 000 cabinets, 15 000 indépendants. Marché adressable 3 000-5 000 cabinets. Besoin réel mais limité à la détection (pas de suivi d'attribution). |
| Capacité à payer (20) | 14/20 | Ticket 149-499 €/mois, freemium. Cabinets ont revenus récurrents mais budgets plus contraints que les ETI. Abonnement refacturable au client final. |
| Concurrence (15) | 14/15 | aides-entreprises.fr (gratuit, manuel), Les-aides.fr, Bpifrance. Aucun SaaS de matching automatique multi-clients en marque blanche. Marché vierge. |
| Coût de construction (15) | 12/15 | 55-70 j/h, infra 30-60 €/mois. Complexité faible-moyenne : règles booléennes + NLP léger. Pas de ML en Phase 1. |
| Automatisation (15) | 13/15 | Ingestion, matching, rapports PDF, alertes automatisables. Parsing NLP nécessite ajustements pour nouvelles aides aux formulations inédites. |
| Risque juridique (10) | 10/10 | Licence Ouverte (fr-lo), données DGE publiques. Aucune donnée personnelle. Risque nul. |

---

## 4. AppelDO — Veille intelligente d'appels d'offres avec prédiction d'issue

### 4.1 Concept

SaaS de veille d'appels d'offres publics qui ne se contente pas d'alerter, mais **prédit les chances de succès** en croisant les avis (BeauAMP/BOAMP) avec l'historique des attributions (DECP) :

- Détecte les appels d'offres correspondant au secteur du client (matching CPV × NAF)
- Affiche l'historique de l'acheteur : avec qui a-t-il déjà travaillé ? À quel prix ?
- Prédit un **score de compétitivité** : nombre probable d'offres, prix médian attendu, probabilité acteur local
- Alerte en temps réel (email, Slack, webhook)

### 4.2 Sources data.gouv.fr

| Source | Licence | URL |
|--------|---------|-----|
| BeauAMP — source primaire | **CC-BY-SA 4.0** ⚠️ | https://www.data.gouv.fr/datasets/base-etendue-amelioree-et-unifiee-des-annonces-des-marches-publics |
| BOAMP — source primaire 2 | fr-lo | https://www.data.gouv.fr/datasets/boamp |
| DECP consolidées tabulaires — source secondaire (prédiction) | LO/OL v2 | https://www.data.gouv.fr/datasets/donnees-essentielles-de-la-commande-publique-consolidees-format-tabulaire |
| API BOAMP — flux temps réel | fr-lo | https://www.data.gouv.fr/dataservices/api-bulletin-officiel-des-annonces-des-marches-publics-boamp |

### 4.3 Volumétrie

- BeauAMP : ~96 fichiers quotidiens (CSV/Parquet), 100-350 Ko/jour, cumul 2015-2025
- BOAMP : fichiers XML annuels, couvre 2015-2035
- Estimation : **20 000 à 30 000 annonces/an** BeauAMP (marchés > seuils JOUE), **100 000+ annonces/an** BOAMP
- Croisement DECP : 5-10 M de marchés historiques pour entraîner les modèles prédictifs

### 4.4 Licence

| Source | Licence | Compatibilité B2B |
|--------|---------|-------------------|
| BeauAMP | **CC-BY-SA 4.0** | ⚠️ Zone grise : obligation ShareAlike sur données transformées. Le dataset croisé avis × attributions pourrait devoir être sous licence compatible. Avis juridique nécessaire avant lancement. |
| BOAMP | fr-lo | ✅ Compatible sans restriction |
| DECP | LO/OL v2 | ✅ Compatible sans restriction |

### 4.5 Faisabilité technique Phase 1 (3 mois)

| Composant | Détail |
|-----------|--------|
| Ingestion quotidienne | Téléchargement CSV BeauAMP + parsing XML BOAMP, chargement PostgreSQL |
| Matching CPV × NAF | Table de correspondance CPV→NAF (fournie avec les DECP Colmo : `probabilites-naf-cpv.csv`) |
| Moteur de scoring | Modèle statistique simple (régression logistique ou forêt aléatoire) sur données historiques DECP → prix médian, nombre d'offres, probabilité acteur local |
| Système d'alertes | File d'attente Redis + envoi email/Slack/webhook |
| Frontend | Dashboard Next.js — flux d'avis, fiches enrichies, configuration alertes |
| Effort | 70-90 j/h (dont ~20 j/h pour parsing XML BOAMP et matching) |
| Infra Phase 1 | Similaire MarchéIntel, 50-150 €/mois |

### 4.6 Positionnement B2B

| Cible | Volume | Besoin | Ticket envisagé |
|-------|--------|--------|-----------------|
| Services commerciaux PME/ETI fournisseurs du secteur public sans veille dédiée | ~50 000 entreprises | Détection des opportunités pertinentes, score de compétitivité | 299-1 499 €/mois (freemium 5 alertes) |
| Centrales d'achat, groupements de commandes, fédérations professionnelles | ~200 structures | Offre marque blanche | 500-1 000 €/mois |

**Concurrence :** Marché ENCOMBRÉ — VecteurPlus, FranceMarchés, Megalis, achatpublic.com, Klekoon, AlertInfo. La couche prédictive est différenciante mais le terrain est occupé par des acteurs établis.

**Différenciation :** Le croisement avis × attributions historiques est la barrière à l'entrée. Les concurrents historiques n'ont pas construit cette couche analytique.

### 4.7 Score Tusk détaillé : 72/100 ✅ LANCER (après clarification juridique)

| Critère (poids) | Note | Justification |
|-----------------|------|---------------|
| Demande réelle (25) | 21/25 | Même cible que MarchéIntel. Besoin : PME sans veille dédiée perdent des opportunités. Score prédictif = proposition unique. Demande forte mais moins universelle que l'intelligence post-attribution. |
| Capacité à payer (20) | 17/20 | Ticket 299-1 499 €/mois, freemium. Marché mature de l'alerte prouve la disposition à payer. Ticket plus bas que MarchéIntel. |
| Concurrence (15) | 8/15 | Marché encombré par des acteurs établis. La couche prédictive différencie mais le terrain est occupé. |
| Coût de construction (15) | 8/15 | 70-90 j/h, infra 50-150 €/mois. Complexité élevée : parsing XML BOAMP (20 j/h), matching CPV×NAF, modèle ML. Maintenance du ML = risque additionnel. |
| Automatisation (15) | 12/15 | Ingestion et alertes automatisables. Modèle ML nécessite réentraînement périodique (dérive des patterns, nouveaux acheteurs). |
| Risque juridique (10) | 6/10 | BeauAMP en CC-BY-SA 4.0 : obligation ShareAlike sur données transformées = zone grise. Avis juridique nécessaire avant d'engager des ressources. |

---

## 5. Tableau comparatif

| Critère | MarchéIntel | SubvenScope | AppelDO |
|---------|-------------|-------------|---------|
| Score Tusk | **86/100** | 82/100 | 72/100 |
| Source primaire | DECP Colmo (lov2) | Aides-entreprises (fr-lo) | BeauAMP (cc-by-sa) + BOAMP (fr-lo) |
| Volume données | ~250 Mo/jour, ~10 M marchés | ~50 Mo total, ~9 000 aides | ~100-350 Ko/jour, ~20-30 K avis/an |
| Complexité technique | Moyenne (ETL + API) | Faible-moyenne (matching + NLP) | Élevée (XML + matching + ML) |
| Concurrence | Faible (post-attribution) | Aucune (SaaS matching B2B) | Forte (alerte), nulle (prédiction) |
| Go-to-market | Direct ETI/PME | Indirect via cabinets comptables | Direct ETI/PME |
| Ticket moyen | 1 000-2 000 €/mois | 200-400 €/mois | 500-1 000 €/mois |
| Barrière à l'entrée | Consolidation et enrichissement | Parsing NLP conditions | Croisement avis × attributions |
| Risque juridique | Nul (lov2) | Nul (fr-lo) | **Zone grise (cc-by-sa)** |
| Effort Phase 1 | 60-80 j/h | 55-70 j/h | 70-90 j/h |
| Infra mensuelle | 50-100 € | 30-60 € | 50-150 € |
| Décision | ✅ LANCER prioritaire | ✅ LANCER rapide | ✅ LANCER après clarification |

---

## 6. Synthèse des risques

### 6.1 Risques juridiques

| Produit | Risque | Sévérité | Mitigation |
|---------|--------|----------|------------|
| AppelDO | CC-BY-SA 4.0 de BeauAMP : obligation ShareAlike sur données transformées | Élevée | Avis juridique externe avant développement. Alternative : utiliser uniquement BOAMP (fr-lo) en Phase 1, quitte à perdre les enrichissements BeauAMP |
| MarchéIntel | Aucun | - | LO/OL v2 et fr-lo : réutilisation commerciale libre |
| SubvenScope | Aucun | - | fr-lo : réutilisation commerciale libre |

### 6.2 Risques techniques

| Produit | Risque | Sévérité | Mitigation |
|---------|--------|----------|------------|
| MarchéIntel | Volume de données (250 Mo/jour, 10 M enregistrements) | Moyenne | PostgreSQL + indexation appropriée, partitionnement par année |
| AppelDO | Parsing XML BOAMP instable, maintenance du modèle ML | Moyenne | 20 j/h dédiés au parsing en Phase 1. Modèle simple (régression logistique) avant ML complexe |
| SubvenScope | Qualité du parsing NLP des conditions d'éligibilité | Moyenne | Approche hybride : règles booléennes + NLP. Tests sur échantillon de 500 aides avant mise en production |

### 6.3 Risques business

| Produit | Risque | Sévérité | Mitigation |
|---------|--------|----------|------------|
| MarchéIntel | VecteurPlus/FranceMarchés étendent vers le post-attribution | Moyenne | Avance au démarrage, enrichissement labels (RGE, ESS) difficile à répliquer rapidement |
| AppelDO | Marché mature et encombré, difficile de se différencier uniquement sur l'alerte | Moyenne | Le score prédictif est la différenciation. Sans lui, pas de lancement |
| SubvenScope | Taille de marché limitée (3 000-5 000 cabinets équipables) | Faible | Ticket bas compensé par volume. Extension possible aux CCI, CMA, réseaux d'accompagnement |

---

## 7. Recommandations stratégiques

### Ordre de lancement

1. **MarchéIntel en priorité** — meilleur équilibre marché × technique × juridique. Ticket élevé (499-2 999 €/mois), différenciation forte, risque nul. 60-80 j/h pour un MVP.

2. **SubvenScope ensuite** — exécution la plus rapide (55-70 j/h), coût d'infrastructure le plus bas (30-60 €/mois), marché vierge. Permet de générer un premier revenu rapidement pendant que MarchéIntel monte en puissance.

3. **AppelDO en dernier** — nécessite une clarification juridique préalable du CC-BY-SA. Si l'avis est favorable, le produit est faisable mais le marché est encombré. Si l'avis est défavorable, pivoter sur BOAMP uniquement (données fr-lo) quitte à perdre les enrichissements BeauAMP.

### Mutualisation possible

- **Socle commun MarchéIntel / AppelDO :** ingestion DECP, base PostgreSQL partagée, composants frontend (cartographie, tableaux de bord)
- **Compétences :** Python/FastAPI/PostgreSQL/Next.js, réutilisables sur les 3 produits
- **Infrastructure :** Un seul serveur 4 vCPU/16 Go peut héberger les 3 produits en Phase 1 (150-250 €/mois)

### Prochaines étapes

1. Commande d'un avis juridique sur la réutilisation commerciale de BeauAMP (CC-BY-SA 4.0) pour AppelDO
2. Début du POC MarchéIntel : ingestion DECP Parquet sur les 12 derniers mois, validation du modèle de données enrichi
3. Début du POC SubvenScope : parsing NLP des conditions d'éligibilité sur un échantillon de 500 aides

---

## 8. Sources

- DECP consolidées tabulaires (Colmo) : https://www.data.gouv.fr/datasets/donnees-essentielles-de-la-commande-publique-consolidees-format-tabulaire
- DECP consolidées (Ministère) : https://www.data.gouv.fr/datasets/donnees-essentielles-de-la-commande-publique-fichiers-consolides
- Base SIRENE : https://www.data.gouv.fr/datasets/base-sirene-des-entreprises-et-de-leurs-etablissements-siren-siret
- BeauAMP : https://www.data.gouv.fr/datasets/base-etendue-amelioree-et-unifiee-des-annonces-des-marches-publics
- BOAMP : https://www.data.gouv.fr/datasets/boamp
- API BOAMP : https://www.data.gouv.fr/dataservices/api-bulletin-officiel-des-annonces-des-marches-publics-boamp
- Aides aux entreprises (DGE) : https://www.data.gouv.fr/datasets/base-de-donnees-des-aides-aux-entreprises
- Fiches idées Beru : idees-marches-publics-b2b.md (tâche t_5cf6bec5)
- Notation Tusk : notation-tusk-3-idees.md (tâche t_e53ac45a)