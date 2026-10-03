# Registre des traitements — Lya AI (éditeur : voir modeles/legal/EDITEUR.md)

## Prospection commerciale B2B par email
- Finalité : proposer les produits du Lair à des professionnels concernés par leur activité.
- Base légale : intérêt légitime (prospection B2B en lien avec l'activité du destinataire).
- Données : raison sociale, SIREN, secteur, ville, site web, adresse email professionnelle
  publiée par l'entreprise, date et source de collecte, historique des échanges.
- Sources : répertoire SIRENE (API Recherche d'entreprises) et site web de l'entreprise.
- Durée : 3 ans après le dernier contact émanant du prospect, puis suppression.
- Droits : opposition immédiate (« STOP »), accès, rectification et effacement par email.
- Stockage : VPS OVH (France), sauvegarde chiffrée. Jamais dans le dépôt git.

## Clients
- Finalité : fournir le service acheté, facturer, assurer le support.
- Base légale : exécution du contrat ; obligations comptables pour les factures.
- Données : identité professionnelle, email, échanges de support ; paiements gérés par Stripe.
- Durée : durée du contrat, puis archives comptables 10 ans.
