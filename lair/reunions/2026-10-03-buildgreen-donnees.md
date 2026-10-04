# Réunion : mise en service des données BuildGreen — Samedi 3 octobre 2026

Présidence : Igris. Objet : débloquer l'ouverture des paiements en remplaçant le mode dégradé
par des données BDNB réelles.

---

## 1. Constat

- Aucune table `batiments` dans la base : `bdnb.py` retombe en mode dégradé, scores inventés à
  partir de moyennes départementales codées en dur (fiabilité 1). Inacceptable pour un service
  vendu 250-1000 €/mois.
- Le score de l'app (5 composantes de 20 points = 100) ne correspond pas à la pondération
  publiée sur le site : étiquette estimée 35 %, gain énergétique 25 %, fiabilité 15 %,
  échéance Loi Climat 15 %, surchauffe 10 %.
- VPS : 13 Go libres sur 40 Go. La BDNB France entière fait 200-500 Go. Le périmètre national
  de l'offre Institutionnel n'est pas tenable.

---

## 2. Décisions

### 2.1 Import de la BDNB

Stratégie : import par département, progressif.

- Iron télécharge **1 département test** (Bouches-du-Rhône, 13 — département urbain
  représentatif avec ~400K bâtiments) depuis data.gouv.fr, tables `batiment_groupe_simulations_dpe`
  et `batiment_groupe_dpe_representatif_logement`, format GeoPackage.
- Il mesure le volume des tables pertinentes pour BuildGreen (pas toutes les tables BDNB).
- Si le volume tient (< 4 Go/département en moyenne), il importe **5 départements**
  (13, 69, 75, 33, 59) pour couvrir l'offre Bureau d'études (3 départements au choix du client
  dans un catalogue).
- Si le volume est trop élevé (> 5 Go/département), il importe **3 départements** et le
  catalogue Bureau d'études est limité à ces 3.
- La table `batiments` est créée avec le schéma défini dans `bdnb.py` (bdnb_id, adresse,
  departement, etiquette_dpe, conso_energie, gaz_effet_serre, surface_habitable,
  annee_construction, millesime, fiabilite) + les colonnes supplémentaires nécessaires
  (gisement_gain_conso_finale_total pour le gain énergétique 25 %, indicateur surchauffe ISB-DH
  pour le 10 %).
- L'import passe par un script `buildgreen-import-bdnb` (shell + Python) dans le dossier `app/`.

### 2.2 Offre Institutionnel

**Institutionnel (1 000 €/mois, national illimité) est mis « sur devis »** tant que la
couverture nationale n'est pas livrable. Sur la page offres, le bouton affiche
« Sur devis — Nous consulter » et ouvre le formulaire Sur-mesure avec une présélection
« Offre Institutionnel ». Dès que 30 départements sont importés, Igris pourra décider
de l'ouvrir.

L'offre Sur-mesure existante reste inchangée.

### 2.3 Calendrier d'ouverture

- **Artisan** (250 €/mois, 1 département) : ouvert dès que 3 départements sont importés et le
  scoring fonctionne sur données réelles.
- **Bureau d'études** (600 €/mois, 3 départements) : ouvert dès que 5 départements sont
  importés (ou 3 si le volume l'impose).
- **Institutionnel** : sur devis.

---

## 3. Tâche créée

Tâche `t_<id>` : « BuildGreen — Import BDNB + moteur de score (SPEC §4 et §8) », assignée à
Iron. Contient :
- Estimation du volume d'un département (13 — Bouches-du-Rhône)
- Import des tables BDNB prioritaires
- Correction du calcul du score (pondérations réelles : 35/25/15/15/10)
- Fiche bâtiment avec score réel
- Aperçu réel à l'étape 3 du récapitulatif (SPEC §4)

---

## 4. Prochaine étape

Iron exécute la tâche. À la livraison, Igris vérifie que :
- Le score est calculé sur données réelles (fiabilité > 1)
- Les pondérations correspondent à la page offres
- L'aperçu réel fonctionne sur un bâtiment public du département
- Le volume disque reste sous 10 Go utilisés (3 Go de marge)