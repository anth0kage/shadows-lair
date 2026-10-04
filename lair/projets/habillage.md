# Cahier des charges — Habillage de Shadow's Lair

Code : ~/hermes3d-dev, branche lair-dev (dépôt git@github-ui:anth0kage/shadows-lair-ui.git).
Tests : npm run build && systemctl --user restart lair-ui-dev, puis
https://shadows-lair.tailb52592.ts.net:4443. Ne jamais toucher à ~/hermes3d ni au service
lair-ui : c'est le Lair en production.

## Fonctions, par ordre de priorité
1. Onglet QUÊTES dans le QG, à côté d'INBOX, HISTORY, KANBAN et PLAYBOOKS.
   - Liste les tâches dont le titre commence par « Quête : », sur tous les tableaux, triées
     par priorité puis par date, avec leur corps affiché comme un guide pas à pas.
   - Bouton « Valider la quête » : champ « compte rendu » obligatoire, puis clôture de la
     tâche avec ce compte rendu (équivalent de hermes kanban complete <id> --result).
   - Pastille sur l'onglet avec le nombre de quêtes en attente.
   - **[PRIORITAIRE — avant la fonction 2]** Améliorations de l'onglet QUÊTES :
     a) **Bouton « Demander des changements »** à côté de « Valider la quête ».
        Commentaire obligatoire, ajouté à la quête. La quête est close avec ce commentaire.
        Une tâche est créée pour default (Igris) avec la demande. Igris prépare les
        corrections et une nouvelle quête de validation.
     b) **Lecture confortable** :
        - Sur ordinateur : panneau large avec liste des quêtes à gauche et détail de la
          quête sélectionnée à droite (split view).
        - Sur mobile : la quête occupe tout l'écran quand elle est ouverte.
        - Texte du corps des quêtes en 16 px minimum (actuellement 11 px dans `<pre>`).
        - Liens dans le corps des quêtes automatiquement cliquables (actuellement en texte
          brut dans `<pre>`).
        - Onglets qui ne se chevauchent plus (les 5 onglets tiennent sur une ligne sans
          débordement ni troncature, sur mobile ≥ 360 px).
2. Personnages au travail : quand une tâche du kanban est en cours pour un profil, son
   personnage est affiché au travail (animation et compteur « working »), puis redevient libre
   à la fin. Source : le kanban d'Hermes, interrogé toutes les 15 secondes par le pont.
3. Une salle par produit de ~/.hermes/lair/produits/INDEX.md, à son nom, où s'installent le
   chef et le support de son escouade quand ils existent.
4. Tableau des rapports : le tableau d'affichage de la salle de réunion ouvre la liste des
   rapports (~/.hermes/lair/rapports) et des comptes rendus (~/.hermes/lair/reunions),
   lisibles dans l'interface.

## Ambiance (Kaisel propose, Bellion valide)
- Dark fantasy originale : sols et murs sombres, lueurs violettes, portails, panneaux et
  notifications façon « fenêtre système » bleue translucide.
- Interdit : reproduire des personnages, logos, polices, images ou interfaces officiels de
  Solo Leveling (manhwa, anime ou jeu). Tout est créé pour le Lair.

## Méthode
- Une fonction à la fois, en commits sur lair-dev poussés sur GitHub.
- Modifications aussi isolées que possible (nouveaux fichiers plutôt que réécritures), pour
  pouvoir récupérer les mises à jour de Hermes3D (remote upstream).
- Après chaque fonction : Bellion valide sur captures de l'atelier, ordinateur et mobile.
- En fin de chantier : quête « Déployer l'Habillage » pour le Monarque, avec les captures et
  l'identifiant du dernier commit validé.
- Arrête l'atelier (systemctl --user stop lair-ui-dev) à la fin de chaque session de test.
