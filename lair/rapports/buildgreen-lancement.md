# Rapport final de lancement — BuildGreen Analytics

## Identité

- **Produit** : BuildGreen Analytics
- **Identifiant** : buildgreen
- **Score Tusk** : 75/100
- **Date de lancement** : 2026-10-03

## Présence en ligne

- **Domaine principal** : https://buildgreen.app
- **Domaine d'envoi** : mailbuildgreen.com (redirige vers buildgreen.app)
- **Contact** : contact@buildgreen.app
- **Envoi prospection** : envoi@mailbuildgreen.com (préchauffage depuis le 2026-10-04)
- **Pages** : Accueil, Mentions légales, CGV, Politique de confidentialité
- **Offres** : Artisan (250 €/mois), Bureau d'études (600 €/mois), Institutionnel (1 000 €/mois)
- **Paiement** : Stripe Checkout en EUR (Adaptive Pricing désactivé 2026-10-04)

## Décisions clés

- Site refusé une première fois par le Monarque (pas professionnel, pas de SEO, offres → formulaire de contact)
- Refonte complète avec template Landed (HTML5 UP), kit visuel Kaisel, SEO Jima
- Parcours d'achat 3 étapes (Périmètre → Structure → Récapitulatif) avant Stripe Checkout
- Paiement désactivé en production (site.json `stripe_actif: false`) : les clients préparent leur commande sans être débités
- Compte Stripe partagé « Lya AI » — le nom public n'a pas été changé (utilisé pour tous les produits)

## Tâches kanban liées

| Tâche | Ombre | Objet |
|-------|-------|-------|
| t_d40ff293 | Kaisel | Kit identité visuelle |
| t_aba2b65d | Iron | Site web V1 (refusé) |
| t_80adeab4 | Beru | Liste prospects SIRENE |
| t_8381d365 | Monarque (quête) | Mise en service mail |
| t_a74fadbb | Bellion | Validation kit + choix template |
| t_27809d33 | Iron | Spécification parcours d'achat |
| t_20d66acf | Igris | Validation spec |
| t_e7839336 | Iron | Construction site V2 |
| t_a07b74d1 | Jima | SEO |
| t_823cebfd | Bellion | Validation captures |
| t_e42f8fa7 | Iron | Pages commande (correction 404) |
| t_b0a2278e | Iron | Stripe USD → EUR |
| t_b77bc4d6 | Monarque (quête) | Désactivation Adaptive Pricing |

## Statut

**⚠️ ATTENTION** : la quête de validation Monarque (t_5680f433) a été close par une ombre et non par le Monarque. Le site est techniquement prêt mais n'a **pas** reçu la validation personnelle du Monarque. Aucune prospection ne doit démarrer avant une nouvelle quête de validation.

- **Premier client payant** : aucun
- **Jalon 30 jours** : 2 novembre 2026
- **Prospection Greed** : interdite tant que le Monarque n'a pas validé