# Rapport de validation — Onglet QUÊTES V2

**Date :** 2026-10-03
**Validateur :** Bellion (revue de code)
**Branche :** `lair-dev` (commit `1e7172e`)
**Fichiers modifiés :** 6 fichiers, +363 lignes

## Verdict global : REFUSÉ — 0/6 critères satisfaits

Le code livré est une V1 fonctionnelle mais incomplète : l'onglet QUÊTES affiche les quêtes sous forme de liste accordéon avec un bouton « Valider la quête ». Les 6 fonctionnalités demandées pour la V2 sont absentes.

---

## Critère 1 : Split view desktop (liste gauche, détail droite)

**Statut :** ÉCHEC

**Constat :** `QuestsPanel.tsx` utilise un pattern accordéon (`expandedQuestId`) : la liste occupe toute la largeur, le détail s'ouvre en dessous de la carte. Aucun layout deux colonnes.

**Correction attendue :**
- Sur écran ≥ 1024px : `grid grid-cols-[minmax(280px,30%)_1fr]` ou flex row avec panneau gauche fixe
- Liste des quêtes dans le panneau gauche, détail de la quête sélectionnée dans le panneau droit
- Navigation entre quêtes = clic dans la liste → mise à jour du détail droit, sans refermer/rouvrir

---

## Critère 2 : Mobile — quête en plein écran avec bouton retour

**Statut :** ÉCHEC

**Constat :** Aucune adaptation mobile. Le même rendu est utilisé quelle que soit la largeur d'écran. Pas de bouton retour, pas de mode plein écran.

**Correction attendue :**
- Sur écran < 768px : la liste des quêtes s'affiche normalement
- Au clic sur une quête : la quête occupe tout l'écran (`fixed inset-0 z-40`) avec :
  - Un bouton « ← Retour » ou « ← Quêtes » en haut (retour à la liste)
  - Le contenu de la quête en pleine largeur
  - Le bouton d'action (valider/demander changements) en bas

---

## Critère 3 : Bouton « Demander des changements »

**Statut :** ÉCHEC

**Constat :** Seul un bouton « Valider la quête » existe (ligne 270). Il ouvre `ValidateModal` qui marque la tâche `status: "done"` via `updateGatewayTask`. Aucun mécanisme de demande de changements.

**Correction attendue :**
- Ajouter un bouton « Demander des changements » à côté de « Valider la quête » (dans la zone expandée, ligne 261-272)
- Au clic : ouvrir une modale avec :
  - Un champ `textarea` obligatoire pour le commentaire
  - Un bouton « Envoyer »
- À la soumission :
  1. Mettre à jour la quête avec le commentaire (notes)
  2. Fermer la quête (changer son statut ou l'archiver)
  3. Créer une nouvelle tâche dans le kanban assignée à `default` (Igris) avec le commentaire en description

---

## Critère 4 : Texte 16px minimum dans le corps des quêtes

**Statut :** ÉCHEC

**Constat :** Le corps est rendu en `text-[11px]` (ligne 253). Le titre est en `text-xs` (12px).

**Correction attendue :**
- Corps de la quête : `text-base` (16px) minimum au lieu de `text-[11px]`
- Conserver `font-mono` et `whitespace-pre-wrap` pour la mise en forme
- Adapter la taille du titre si nécessaire pour la hiérarchie visuelle (ex. `text-sm` ou `text-base`)

---

## Critère 5 : Liens cliquables dans le corps

**Statut :** ÉCHEC

**Constat :** `renderQuestBody()` (ligne 122-129) ne fait que supprimer le formatage markdown. Le résultat est injecté dans une balise `<pre>` sans parsing d'URL. Aucun `<a href>` généré.

**Correction attendue :**
- Remplacer le rendu `<pre>` par un rendu qui détecte les URLs et les transforme en liens
- Approche recommandée : parser le texte avec une regex URL pour découper le contenu en segments texte + liens
- Exemple de rendu :
```tsx
function renderQuestBodyWithLinks(body: string): ReactNode {
  const urlRegex = /(https?:\/\/[^\s<>"]+)/g;
  const parts = body.split(urlRegex);
  return parts.map((part, i) =>
    urlRegex.test(part) ? (
      <a key={i} href={part} target="_blank" rel="noopener noreferrer"
         className="text-cyan-300 underline hover:text-cyan-100">
        {part}
      </a>
    ) : part
  );
}
```
- Utiliser `whitespace-pre-wrap` sur le conteneur pour conserver les retours à la ligne

---

## Critère 6 : Onglets sans chevauchement sur mobile 360px

**Statut :** ÉCHEC

**Constat :** Les onglets sont passés de `grid-cols-4` à `grid-cols-5`. Sur 360px, chaque colonne fait 72px. Avec `text-[11px]` + `tracking-[0.18em]` + `uppercase`, « PLAYBOOKS » ≈ 117px de rendu, soit un débordement certain.

**Correction attendue :**
- Option A : Réduire la taille de police sur mobile (`text-[9px]` ou `text-[10px]`) avec `tracking-[0.1em]`
- Option B : Remplacer `grid-cols-5` par un défilement horizontal (`overflow-x-auto flex`) sur mobile
- Option C : Abréger les labels sur mobile (ex. « INB », « HIST », « KAN », « QTE », « PLB »)
- Recommandation : Option A + media query `@media (max-width: 400px)` avec `text-[9px]` et `tracking-[0.08em]`

---

## Points positifs (à conserver)

- L'intégration dans le QG est correcte : l'onglet est bien positionné entre KANBAN et PLAYBOOKS
- La pastille de comptage fonctionne (`quetesCount`)
- Le tri par priorité décroissante est correct
- La modale de validation a une validation de formulaire (commentaire obligatoire)
- Les états loading / error / vide sont gérés
- Les nouveaux champs (`priority`, `board`, `body`, `result`) sont propagés proprement dans les types et le contrôleur

---

## Action demandée

Retour à Iron pour implémentation des 6 corrections détaillées ci-dessus. La branche `lair-dev` nécessite un second commit avec les fonctionnalités V2 avant nouvelle validation.