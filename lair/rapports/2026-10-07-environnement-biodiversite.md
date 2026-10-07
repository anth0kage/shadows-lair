# Produits B2B Environnement & Biodiversité — Faisabilité & scoring

Rapport produit le 2026-10-07. Phase 1 : zéro dépense, zéro compte, zéro publication.
Combine les fiches d'idées Beru (exploration data.gouv.fr) et la notation Tusk (grille 6 critères).

---

## 1. Synthèse exécutive

**Verdict : 3 idées faisables, toutes au-dessus du seuil Tusk de 70/100.**

Trois produits B2B exploitables dès la phase 1 à partir de données publiques françaises :

| Rang | Produit | Score Tusk | Positionnement | Source primaire |
|------|---------|-----------|----------------|-----------------|
| 1 | **ComplyCPE** | 83/100 | Veille réglementaire ICPE & alertes conformité | Base ICPE (fr-lo) |
| 2 | **ZANtrack** | 79/100 | Pilotage Zéro Artificialisation Nette | Consommation espaces Cerema (LO/OL v2) |
| 3 | **BioScreen** | 72/100 | Screening biodiversité pour projets d'aménagement | Espaces protégés (LO/OL v2) |

ComplyCPE est prioritaire (ticket le plus élevé, demande permanente, zéro friction juridique, concurrence inexistante).
ZANtrack est le second (obligation légale avec échéance 2031, marché public captif large, mutualisable avec ComplyCPE).
BioScreen est le plus innovant mais nécessite de clarifier la licence INPN avant d'engager des ressources.

Les trois idées peuvent coexister (cibles distinctes, socles techniques partiellement mutualisables : ETL + SIG + dashboard).

---

## 2. ComplyCPE — Veille réglementaire ICPE & Alertes conformité

### 2.1 Concept

SaaS B2B qui transforme la base des installations classées (ICPE) en outil de veille réglementaire et de gestion de conformité environnementale :

- **Cartographie enrichie** des ~500 000 ICPE françaises (Seveso haut/bas, déclaration, enregistrement, autorisation)
- **Alertes automatiques** sur les échéances : renouvellement d'arrêté, contrôle périodique, récolement
- **Dashboard de conformité** par site, par groupe, par région
- **Croisement risques** : proximité zones inondables, captages d'eau, zones Natura 2000
- **Module inspection** : préparation des visites DREAL/DRIEE, suivi des non-conformités

### 2.2 Sources data.gouv.fr

| Source | Licence | URL |
|--------|---------|-----|
| Base des installations classées (ICPE) — primaire | fr-lo | https://www.data.gouv.fr/datasets/base-des-installations-classees-icpe |
| ICPE France métropolitaine et DROM (BRGM) — géolocalisation | Licence Ouverte (Géorisques) | https://www.data.gouv.fr/datasets/installations-classees-pour-la-protection-de-lenvironnement-icpe-france-metropolitaine-et-drom-3 |
| Base SIRENE (enrichissement exploitants) | LO/OL v2 | https://www.data.gouv.fr/datasets/base-sirene-des-entreprises-et-de-leurs-etablissements-siren-siret |
| Données Sécheresse - VigiEau (croisement risques) | LO/OL v2 | https://www.data.gouv.fr/datasets/donnee-secheresse-vigieau |

### 2.3 Volumétrie estimée

- ~500 000 ICPE actives en France (tous régimes confondus)
- ~1 300 sites Seveso haut, ~2 500 Seveso bas
- Mise à jour quotidienne de la base
- Formats : CSV, JSON, GeoJSON (géolocalisé)
- Enrichissements possibles : inspections DREAL, arrêtés préfectoraux, émissions GEREP, BD REACH

### 2.4 Segmentation tarifaire

| Segment | Ticket mensuel | Nombre de clients potentiels |
|---------|---------------|------------------------------|
| Industriels exploitants ICPE (groupes >10 sites) | 1 500-3 000 €/mois | ~2 000 groupes |
| Bureaux d'études environnement (prestation multi-clients) | 500-1 500 €/mois | ~1 500 cabinets |
| Assureurs risques industriels | 2 000-5 000 €/mois | ~50 acteurs |
| Collectivités avec ICPE sur leur territoire | 300-800 €/mois | ~500 EPCI |

### 2.5 Concurrence

- **Ecomesure** : capteurs IoT qualité de l'air, pas de couverture réglementaire ICPE globale
- **Enablon (Wolters Kluwer)** : EHS global, lourd et coûteux, pas spécifique France/ICPE
- **Atmotrack** : monitoring air, pas de conformité administrative
- **Bureaux d'études traditionnels** : prestation manuelle, pas de SaaS
- **Opportunité** : pas d'acteur SaaS positionné spécifiquement sur la veille réglementaire ICPE France

### 2.6 Contraintes légales

- **Base ICPE** : fr-lo (Licence Ouverte v2). Réutilisation commerciale libre avec mention de source. Note : vérifier les conditions Géorisques (licence ouverte confirmée).
- **Données personnelles** : Aucune dans la base ICPE (données administratives sur les installations, pas sur les personnes).
- **RGPD** : Non applicable.

---

## 3. ZANtrack — Pilotage Zéro Artificialisation Nette

### 3.1 Concept

SaaS B2B qui aide les collectivités territoriales et les aménageurs à suivre, piloter et anticiper leur trajectoire ZAN (Zéro Artificialisation Nette). La loi Climat et Résilience impose -50 % d'artificialisation d'ici 2031 et zéro artificialisation nette en 2050. Chaque commune/EPCI doit intégrer ces objectifs dans ses documents d'urbanisme (PLU, PLUi, SCOT).

- **Tableau de bord ZAN** : consommation d'espaces communale, tendances, projections
- **Simulateur de scénarios** : impact de nouveaux projets sur le quota ZAN, densification vs extension
- **Comparaison intercommunale** : benchmarking entre territoires comparables
- **Rapportage réglementaire** : génération automatique des bilans ZAN pour les documents d'urbanisme
- **Module foncier** : identification des friches et dents creuses mobilisables pour la densification

### 3.2 Sources data.gouv.fr

| Source | Licence | URL |
|--------|---------|-----|
| Consommation d'espaces NAF 2009-2024 (Cerema) — primaire | LO/OL v2 | https://www.data.gouv.fr/datasets/consommation-despaces-naturels-agricoles-et-forestiers-du-1er-janvier-2009-au-1er-janvier-2024 |
| Consommation d'espaces NAF 2011-2025 (Cerema) — millésime récent | LO/OL v2 | https://www.data.gouv.fr/datasets/consommation-despaces-naturels-agricoles-et-forestiers-du-1er-janvier-2011-au-1er-janvier-2025 |
| RPG (Registre Parcellaire Graphique) — occupation agricole | LO/OL v2 | https://www.data.gouv.fr/datasets/rpg |
| Données carroyées consommation d'espace (DREAL ARA) — exemple infra | non spécifié | https://www.data.gouv.fr/datasets/consommation-despace-socle-dindicateurs-du-millesime-2022-sous-forme-de-carroyage |

### 3.3 Volumétrie estimée

- Données communales : ~35 000 communes, séries 2009-2025
- Mise à jour annuelle par le Cerema (juin de chaque année)
- Formats : CSV, GeoJSON, GeoPackage
- Granularité : communale, infra-communale (carroyage 1 km² pour les données avancées)
- Enrichissements possibles : PLU/PLUi numérisés (Géoportail de l'Urbanisme), données DVF (transactions foncières), Loi Littoral/Montagne

### 3.4 Segmentation tarifaire

| Segment | Ticket mensuel | Nombre de clients potentiels |
|---------|---------------|------------------------------|
| EPCI (intercommunalités) | 500-1 500 €/mois | ~1 250 EPCI |
| Communes >10 000 hab. | 300-800 €/mois | ~1 000 communes |
| Agences d'urbanisme | 800-2 000 €/mois | ~50 agences |
| Promoteurs/aménageurs (stratégie foncière) | 600-1 500 €/mois | ~300 acteurs |
| DDT/DREAL (services déconcentrés État) | 500-1 200 €/mois | ~100 services |

### 3.5 Concurrence

- **Portail de l'artificialisation (Cerema)** : site public gratuit, visualisation basique, pas de simulation ni pilotage
- **Mon Territoire Numérique** : outils open source État/collectivités, pas de SaaS commercial clé en main
- **UrbALab** : conseil en urbanisme, pas de logiciel standardisé
- **Géovendée / géoportails locaux** : visualisation uniquement, pas d'aide à la décision
- **Opportunité** : le créneau du pilotage ZAN est porté par une obligation légale récente (2021) et des sanctions à venir. Demande forte, concurrence SaaS quasi inexistante.

### 3.6 Contraintes légales

- **Consommation d'espaces (Cerema)** : LO/OL v2. Réutilisation commerciale libre avec mention de source.
- **RPG** : LO/OL v2. Réutilisation commerciale libre.
- **Fichiers fonciers** : données DGFiP, accessibles via le Cerema sous conditions (habilitation). Alternative : données open data communales publiées par le Cerema sur data.gouv.fr.
- **Données personnelles** : Aucune (données agrégées à la commune).
- **RGPD** : Non applicable.

---

## 4. BioScreen — Screening biodiversité pour projets d'aménagement

### 4.1 Concept

SaaS B2B qui automatise le pré-screening biodiversité pour tout projet de construction ou d'aménagement. L'outil croise l'emprise du projet avec les zonages environnementaux (espaces protégés, ZNIEFF, Natura 2000, zones humides, habitats d'espèces protégées) et génère un rapport de sensibilité écologique, préalable à l'étude d'impact réglementaire.

- **Analyse spatiale automatisée** : upload d'une emprise (GeoJSON/shapefile) → rapport de sensibilité
- **Score de risque biodiversité** par parcelle : probabilité de présence d'espèces protégées, contraintes réglementaires
- **Checklist réglementaire** : déclencheurs étude d'impact, dossier Loi sur l'Eau, dérogation espèces protégées, compensation
- **Bibliothèque séquence ERC** : mesures types Éviter-Réduire-Compenser par type de milieu et de projet

### 4.2 Sources data.gouv.fr

| Source | Licence | URL |
|--------|---------|-----|
| Espaces naturels protégés — primaire | LO/OL v2 | https://www.data.gouv.fr/datasets/espaces-naturels-proteges |
| INPN - Données du programme Espaces Protégés | other-open | https://www.data.gouv.fr/datasets/inpn-donnees-du-programme-espaces-proteges |
| Biodiversité - Observations ponctuelles | LO/OL v2 | https://www.data.gouv.fr/datasets/biodiversite-observations-ponctuelles |
| Cartographie des habitats naturels CarHab | Licence Ouverte | https://ecologie.data.gouv.fr/datasets/66601a40fc2fb8f5969fc8f4 |
| RPG (occupation des sols agricole) — contexte paysager | LO/OL v2 | https://www.data.gouv.fr/datasets/rpg |

### 4.3 Volumétrie estimée

- ~3 500 espaces naturels protégés (parcs nationaux, réserves, arrêtés de biotope, etc.)
- ~18 000 ZNIEFF (Zones Naturelles d'Intérêt Écologique Faunistique et Floristique)
- ~1 700 sites Natura 2000 en France
- Millions d'observations d'espèces (SINP/INPN)
- Formats : GeoJSON, Shapefile, GeoPackage
- Mise à jour variable selon les sources (annuelle à temps réel)

### 4.4 Segmentation tarifaire

| Segment | Ticket mensuel | Nombre de clients potentiels |
|---------|---------------|------------------------------|
| Promoteurs immobiliers (groupes nationaux) | 800-2 000 €/mois | ~200 groupes |
| Bureaux d'études environnement (écologues) | 400-1 200 €/mois | ~2 000 cabinets |
| Aménageurs publics (EPA, SEM, SPL) | 500-1 500 €/mois | ~300 structures |
| BTP / Infrastructures (screening avant appel d'offres) | 600-1 800 €/mois | ~500 entreprises |
| Avocats droit environnement (contentieux) | 200-600 €/mois | ~500 cabinets |

### 4.5 Concurrence

- **Geoptis** : outil d'aide à la décision aménagement, mais focalisé urbanisme réglementaire (PLU), pas biodiversité
- **Ecosphère / Biotope / CERFA** : leaders des études d'impact en bureau d'études, prestation manuelle, pas de SaaS
- **GéoPaysages** : atlas cartographique, pas d'automatisation de screening
- **Opportunité** : le marché du screening automatisé est quasi vierge en France. Les bureaux d'études facturent 3 000-15 000 € par étude manuelle.

### 4.6 Contraintes légales

- **Espaces naturels protégés** : LO/OL v2. Réutilisation commerciale libre.
- **INPN - Espaces Protégés** : « other-open » — vérifier les conditions exactes. Les données INPN sont sous licence ouverte pour les usages non-commerciaux par défaut. Pour un usage B2B, contacter le MNHN (producteur). Alternative : les données SINP (Système d'Information sur la Nature et les Paysages) sont sous licence ouverte.
- **Données personnelles** : Aucune (données sur les espèces et les espaces, pas sur les personnes).
- **RGPD** : Non applicable.

---

## 5. Notation Tusk

Grille : demande réelle (25), capacité à payer (20), concurrence (15), coût de construction (15), automatisation possible (15), risque juridique (10). Total sur 100.

### 5.1 Tableau de notation

| Critère (poids) | ComplyCPE | ZANtrack | BioScreen |
|---|---|---|---|
| **Demande réelle** (/25) | 22 | 23 | 20 |
| **Capacité à payer** (/20) | 17 | 14 | 15 |
| **Concurrence** (/15) | 13 | 13 | 14 |
| **Coût de construction** (/15) | 10 | 9 | 7 |
| **Automatisation possible** (/15) | 12 | 12 | 10 |
| **Risque juridique** (/10) | 9 | 8 | 6 |
| **TOTAL** | **83/100** | **79/100** | **72/100** |
| **Verdict** | RECOMMANDÉ | RECOMMANDÉ | RECOMMANDÉ |

### 5.2 Analyse détaillée

#### ComplyCPE — 83/100 (PRIORITAIRE)

- **Demande réelle (22/25)** : Marché permanent et obligatoire. 500 000 ICPE en France, ~4 000 clients potentiels. Les contrôles DREAL et les sanctions pour non-conformité créent une demande non discrétionnaire. Ticket élevé (500-5 000 €/mois).
- **Capacité à payer (17/20)** : Les industriels et assureurs ont des budgets conformité structurés. Ticket aligné sur ce que le marché paie déjà en prestations manuelles.
- **Concurrence (13/15)** : Pas d'acteur SaaS dédié ICPE en France. Enablon est global et non spécifique. Ecomesure/Atmotrack sont sur le monitoring, pas la conformité.
- **Coût de construction (10/15)** : ETL + SIG, complexité moyenne. Base ICPE bien structurée. Compétence métier ICPE nécessaire (réglementaire).
- **Automatisation possible (12/15)** : Ingestion quotidienne, alertes, dashboard, croisement risques SIG : tout automatisable. La préparation aux inspections nécessite une part d'humain.
- **Risque juridique (9/10)** : Base ICPE en fr-lo, réutilisation commerciale libre. Zéro donnée personnelle. RGPD non applicable.

**Forces** : Ticket élevé, demande non discrétionnaire, zéro concurrence SaaS, données ouvertes sans friction juridique.

**Faiblesses** : Expertise métier nécessaire, ticket très variable selon segment.

#### ZANtrack — 79/100 (FORTEMENT RECOMMANDÉ)

- **Demande réelle (23/25)** : Obligation légale (Loi Climat 2021) avec échéances 2031 et 2050. ~2 700 clients potentiels. Sanctions à venir. Demande non discrétionnaire et croissante.
- **Capacité à payer (14/20)** : Ticket modéré (300-2 000 €/mois). Secteur public : budgets contraints mais ZAN est une obligation → des lignes budgétaires seront créées.
- **Concurrence (13/15)** : Portail Cerema gratuit mais basique (visualisation seule). Mon Territoire Numérique est open source, pas SaaS clé en main.
- **Coût de construction (9/15)** : ETL + dashboard + simulation + reporting. Complexité moyenne. Données Cerema bien structurées.
- **Automatisation possible (12/15)** : Dashboard, projections, benchmarking, rapportage : tout automatisable.
- **Risque juridique (8/10)** : Données Cerema en LO/OL v2 (OK). RPG en LO/OL v2 (OK). Zéro donnée personnelle.

**Forces** : Obligation légale avec échéances, marché public large, données ouvertes, concurrence quasi nulle.

**Faiblesses** : Ticket public modéré, données foncières fines partiellement restreintes.

#### BioScreen — 72/100 (LANÇABLE, points de vigilance)

- **Demande réelle (20/25)** : Forte mais projet-dépendante (non permanente). ~3 500 clients potentiels. Le marché des études d'impact manuelles pèse plusieurs centaines de M€, ce SaaS en capterait une fraction.
- **Capacité à payer (15/20)** : Ticket modéré (200-2 000 €/mois). Les BE facturent déjà 3 000-15 000 € par étude manuelle → le SaaS est très compétitif.
- **Concurrence (14/15)** : Quasi vierge. Geoptis couvre l'urbanisme réglementaire, pas la biodiversité. BE leaders en prestation manuelle.
- **Coût de construction (7/15)** : Élevé. Moteur SIG avancé, modèle de scoring écologique calibré par experts, ingestion de millions d'observations.
- **Automatisation possible (10/15)** : L'analyse spatiale et le rapport sont automatisables. Le scoring de risque biodiversité demande une calibration experte continue.
- **Risque juridique (6/10)** : Point de vigilance majeur. Données INPN en « other-open » — usage commercial à vérifier auprès du MNHN. Alternative SINP (licence ouverte) mais périmètre plus restreint. Ce risque doit être levé avant le lancement.

**Forces** : Marché vierge, besoin réel, ticket très compétitif vs prestation manuelle.

**Faiblesses** : Licence INPN incertaine (bloquant), coût de construction élevé, demande non récurrente.

---

## 6. Recommandations

### 6.1 Classement de priorité

1. **ComplyCPE (83/100) — LANCER EN PRIORITÉ**
   Ticket le plus élevé, demande permanente, zéro friction juridique, concurrence inexistante. Le plus rapide à rentabiliser.

2. **ZANtrack (79/100) — LANCER EN SECOND**
   Obligation légale avec urgence temporelle (2031), marché captif public large. Ticket plus modeste mais volume compense. Bonne complémentarité avec ComplyCPE (même stack technique ETL+SIG).

3. **BioScreen (72/100) — LANCER EN DERNIER, SOUS RÉSERVE**
   Le plus innovant et différenciant, mais bloqué par l'incertitude sur la licence INPN. Lancer après avoir clarifié la licence INPN auprès du MNHN ou sécurisé l'alternative SINP. Coût de construction le plus élevé des trois.

### 6.2 Synergies techniques

ComplyCPE et ZANtrack partagent une architecture commune (ETL + SIG + dashboard + alertes), ce qui permet de mutualiser les développements. BioScreen ajoute une couche de scoring spatial plus complexe.

### 6.3 Règle des 3 produits actifs

Les 3 idées passent le seuil de 70/100. Toutes peuvent être activées simultanément (maximum 3 produits actifs). Si une seule doit être lancée : ComplyCPE.

---

*Sources : fiches idées Beru (idees-environnement-biodiversite.md, tâche t_3700b48a), notation Tusk (notation-tusk-environnement.md, tâche t_29c34814).*