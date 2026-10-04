# BuildGreen Analytics

Identifiant : buildgreen (tableau kanban : buildgreen)
Niveau : 1 (lancé, pas encore de client payant)

## Le produit
- Promesse en une phrase : Scoring énergétique B2B pour prioriser les rénovations et garantir la conformité réglementaire.
- Cible : Entreprises de rénovation énergétique, diagnostiqueurs DPE, bailleurs sociaux (HLM), syndics de copropriété, collectivités territoriales.
- Données utilisées (lien data.gouv) et licence : Base de Données Nationale des Bâtiments (BDNB) — CSTB — Licence Ouverte v2. https://www.data.gouv.fr/datasets/base-de-donnees-nationale-des-batiments/
- Prix et offre : Artisan 250 €/mois, Bureau d'études 600 €/mois, Institutionnel 1 000 €/mois.

## La marque (Kaisel, validée par Bellion)
- Ton de voix, palette, typographies : à définir par Kaisel.
- Comptes et accès (noms exacts dans le coffre) :
- Domaine : buildgreen.app (Porkbun, ID 590282806/590282810, pointe vers 149.202.62.48)
- Domaine d'envoi : mailbuildgreen.com (Porkbun, ID 590284336, redirige vers buildgreen.app)
- Mail contact : contact@buildgreen.app (quête t_8381d365)
- Mail envoi : envoi@mailbuildgreen.com (quête t_8381d365, préchauffage à dater de la mise en service)

## Les chiffres (Kamish, chaque semaine)
- Visiteurs, inscrits, clients payants, revenu mensuel, coûts :

## FAQ clients

*Aucune question reçue à ce jour.*

## Journal des décisions
- 2026-10-03 : produit créé.
- 2026-10-03 : site reconstruit (Landed + kit). Parcours d'achat ouvert pour les 3 offres, paiement Stripe désactivé (site.json stripe_actif=false) en attendant les clés Stripe (t_4d712fd5) et les données BDNB (t_4abcdf32). Espace Ressources (5 guides sourcés) ajouté pour le SEO.
## Parcours d'achat
- Spécification complète : /home/monarch/lair-sites/buildgreen/SPEC.md (rédigée par Iron le 2026-10-03, en attente de validation Igris, tâche t_20d66acf).
- Résumé : page offres → 3 étapes guidées (périmètre, structure, récapitulatif avec aperçu) → Stripe Checkout (lien de paiement par offre, abonnement mensuel HT) → page succès vérifiée côté serveur → accès par lien magique + facture. Sur-mesure : formulaire uniquement, sans paiement. Remboursement : quête pour le Monarque.
