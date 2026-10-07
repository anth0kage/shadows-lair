# ComplyCPE

Identifiant : complycpe (tableau kanban : complycpe)
Niveau : 1 (lancé, pas encore de client payant)

## Le produit
- Promesse en une phrase : Veille réglementaire ICPE et alertes conformité environnementale pour les industriels et bureaux d'études.
- Cible : Industriels exploitants ICPE (>10 sites), bureaux d'études environnement, assureurs risques industriels, collectivités avec ICPE sur leur territoire.
- Données utilisées (lien data.gouv) et licence : Base ICPE (fr-lo), ICPE BRGM géolocalisée (Licence Ouverte), Base SIRENE (LO/OL v2), VigiEau sécheresse (LO/OL v2). https://www.data.gouv.fr/datasets/base-des-installations-classees-icpe
- Prix et offre : Industriel 1 500-3 000 €/mois, Bureau d'études 500-1 500 €/mois, Assureur 2 000-5 000 €/mois, Collectivité 300-800 €/mois.

## La marque (Kaisel, validée par Bellion)

### Nom de domaine
- Domaine principal : **complycpe.fr** (disponible, ~8-10 $/an sur Porkbun, dans le budget 15 $/an)
- Domaine de réserve : complycpe.com (disponible, ~10-13 $/an)
- Achat par Igris selon règle trésorerie : un domaine par produit validé, 15 $/an max.

### Ton de voix
Voix de l'expert réglementaire qui informe et alerte, ne vend pas.

- **Sérieux** : vocabulaire technique précis (arrêté, récolement, non-conformité, régime), jamais de jargon marketing.
- **Fiable** : chaque affirmation est datée et sourcée (texte de loi, arrêté préfectoral, base ICPE).
- **Technique** : assume le vocabulaire métier ICPE sans le vulgariser — le client est un industriel ou un bureau d'études, pas un novice.
- **Concis** : phrases courtes, chiffrées. Une information par phrase.
- **Proactif** : « Votre arrêté du 12/03/2023 arrive à échéance dans 45 jours. » Pas « Pensez à vérifier vos échéances. »

Interdits : superlatifs creux, guillemets décoratifs, points d'exclamation, « solution innovante », « boostez », « révolutionnez ».

### Palette de couleurs
3 couleurs maximum (charte qualité) + 2 déclinaisons claires pour les fonds.

| Rôle | Nom | Hex | Usage |
|------|-----|-----|-------|
| Principale | Vert ICPE | `#0F4C3A` | Logos, titres, boutons principaux, barre de navigation |
| Secondaire | Bleu réglementaire | `#1C2838` | Textes, sous-titres, icônes, data visualisation |
| Tertiaire | Ambre alerte | `#E87D22` | Badges, alertes critiques, CTA secondaires, liens |
| Fond clair 1 | Vert brume | `#ECF2EF` | Arrière-plans de section, cartes |
| Fond clair 2 | Bleu brume | `#F0F3F6` | Arrière-plans alternatifs, tableaux |

Règle : jamais plus de 3 couleurs sur un même écran. Les fonds clairs ne comptent pas comme des couleurs (déclinaisons).

### Typographies
Deux polices, Google Fonts (SIL Open Font License), usage commercial libre.

| Rôle | Police | Graisses | Usage |
|------|--------|----------|-------|
| Titres | **Inter** | 600 (semibold), 700 (bold) | H1-H4, navigation, boutons |
| Corps | **IBM Plex Sans** | 400 (regular), 500 (medium) | Paragraphes, tableaux, données, légendes, labels |

Hiérarchie :
- H1 : Inter Bold 700, 2.5rem
- H2 : Inter Semibold 600, 1.75rem
- H3 : Inter Semibold 600, 1.25rem
- Corps : IBM Plex Sans Regular 400, 1rem / 1.6 line-height
- Données/chiffres : IBM Plex Sans Medium 500 (meilleur rendu des chiffres)

### Comptes et accès (noms exacts dans le coffre) :

## Les chiffres (Kamish, chaque semaine)
- Visiteurs, inscrits, clients payants, revenu mensuel, coûts :

## FAQ clients

## Parcours d'achat
- Spécification complète : /home/monarch/lair-sites/complycpe/SPEC.md (rédigée par Iron le 2026-10-07, en attente de validation Igris, tâche t_5faa4671).
- Résumé : page offres → 4 étapes guidées (activité, périmètre de veille, structure, récapitulatif avec aperçu) → Stripe Checkout (lien de paiement par offre, abonnement mensuel HT) → page succès vérifiée côté serveur → accès par lien magique + facture. Sur-mesure : formulaire uniquement, sans paiement. Remboursement : quête pour le Monarque.
- 2026-10-07 : SPEC.md rédigée. En attente de validation par Igris. Aucun lien Stripe créé. Les boutons d'achat seront désactivés tant que le service v1 n'est pas prêt (cf. section 0 de la spec).

## Journal des décisions
- 2026-10-07 : produit créé. Score Tusk 83/100 (prioritaire). ~4 000 clients potentiels, demande permanente, zéro friction juridique, concurrence SaaS inexistante.