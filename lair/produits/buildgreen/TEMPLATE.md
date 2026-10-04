# BuildGreen Analytics — Template HTML

## Template choisi : Landed (HTML5 UP)

- **Nom** : Landed
- **Auteur** : @ajlkn (HTML5 UP)
- **URL démo** : https://html5up.net/landed
- **URL téléchargement** : https://html5up.net/landed/download
- **Licence** : Creative Commons Attribution 3.0 (usage commercial libre, modification libre, attribution requise)
- **Type** : Template HTML5/CSS3 responsive, single-page, multi-sections
- **Date de sélection** : 2026-10-03
- **Validé par** : Bellion

## Justification du choix

Landed est le template HTML5 UP le plus adapté à un SaaS B2B comme BuildGreen Analytics :

1. **Structure multi-sections complète** : banner (hero) → 3 sections spotlight (gauche/droite alternées, avec images) → grille de 6 cartes feature → CTA avec formulaire → footer
2. **Conçu pour les landing pages produit** : chaque section spotlight combine un grand visuel et un texte d'accroche, idéal pour présenter un produit data-driven avec des captures d'écran
3. **Grille de features** : 6 emplacements en grid 3×2, parfaits pour détailler les fonctionnalités (analyse DPE, scoring patrimoine, rapports réglementaires, etc.)
4. **Images et captures acceptées** : chaque spotlight utilise une image pleine largeur ; les cartes feature utilisent des icônes mais peuvent être enrichies de captures
5. **Professionnalisme B2B** : design sobre et structuré, sans fioritures, adapté à une cible professionnelle (bailleurs, syndics, bureaux d'études)
6. **Responsive natif** : HTML5/CSS3, fonctionne sur mobile et desktop sans modification

## Structure des sections (Landed)

| Section | ID HTML | Usage BuildGreen |
|---------|---------|------------------|
| Bannière | `#banner` | Hero : titre + sous-titre + CTA « Voir une démo » ou « Essai gratuit » |
| Spotlight 1 | `#one` (bottom) | Comment ça marche : import de données → analyse → plan d'action |
| Spotlight 2 | `#two` (right) | Cibles : bailleurs sociaux, copropriétés, collectivités |
| Spotlight 3 | `#three` (left) | Conformité & ROI : échéances DPE, économies chiffrées |
| Features | `#four` (6 cartes) | Fonctionnalités clés : scoring, rapports, alertes, API, etc. |
| CTA | `#five` | Formulaire de contact ou inscription newsletter |
| Footer | `#footer` | Liens légaux, mentions, copyright |

## Adaptation au kit BuildGreen

### Palette (remplacer les couleurs dans main.css)

```css
/* Couleurs Landed → BuildGreen */
/* Ancien bleu (#4b7bec) → Navy #1a2332 */
/* Ancien accent → Teal #2d8a7a */
/* Fond → Stone #f5f6f8 */

:root {
  --color-primary: #1a2332;
  --color-accent: #2d8a7a;
  --color-bg: #f5f6f8;
}
```

### Typographies (remplacer dans main.css + <head>)

```html
<!-- Google Fonts -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@500;700&family=Inter:wght@400;500&display=swap" rel="stylesheet">
```

```css
/* Titres : DM Sans (remplace la police par défaut des h1-h4) */
h1, h2, h3, h4 { font-family: 'DM Sans', sans-serif; }
h1, h2 { font-weight: 700; }
h3, h4 { font-weight: 500; }

/* Corps : Inter */
body, p, li, a, input, textarea, button { font-family: 'Inter', sans-serif; }
```

### Logo dans le header

Remplacer le texte « Landed » dans `#logo` par le logo BuildGreen :

```html
<h1 id="logo">
  <a href="index.html">
    <img src="assets/images/logo-buildgreen.svg" alt="BuildGreen Analytics" height="40">
  </a>
</h1>
```

Le fichier logo.svg du kit (/home/monarch/lair-sites/buildgreen/kit/logo.svg) est à copier dans `assets/images/` et à référencer comme ci-dessus.

### Navigation

Adapter les liens du menu `#nav` :
- Fonctionnalités → `#four`
- Cas d'usage → `#two`
- Tarifs → `#pricing` (section à créer si nécessaire, ou lien vers page externe)
- Contact → `#five`

### Images

- **Spotslights** (`#one`, `#two`, `#three`) : utiliser des captures d'écran du dashboard BuildGreen, pas de photos banque d'images génériques
- **Bannière** (`#banner`) : fond uni Navy `#1a2332` ou capture large du produit
- **Icônes features** (`#four`) : icônes simples (Font Awesome ou SVG inline) en Teal

### Pages légales (obligatoires)

Créer 3 pages liées en pied de page :
- `mentions-legales.html`
- `politique-confidentialite.html`
- `conditions-generales.html`

### Contenu éditorial

Respecter le ton de voix BuildGreen (cf. kit/README.md) :
- Précis et chiffré (pas de superlatifs vides)
- B2B : vocabulaire DPE, copropriété, maîtrise d'ouvrage
- Sobre : pas de « sauver la planète »
- Direct : une phrase = une information
- Bannir : « révolutionnez », « boostez », « solution innovante », « n'hésitez pas »

## Instructions pour Iron

1. Télécharger le template Landed depuis https://html5up.net/landed/download
2. Remplacer la palette CSS (--color-primary: #1a2332, --color-accent: #2d8a7a, --color-bg: #f5f6f8)
3. Remplacer les polices par DM Sans (titres) + Inter (corps) via Google Fonts
4. Intégrer le logo BuildGreen dans le header (kit/logo.svg → assets/images/)
5. Remplir chaque section avec le contenu BuildGreen (textes fournis par Igris dans la spec)
6. Remplacer les images placeholder par des captures réelles du produit
7. Créer les 3 pages légales et les lier en footer
8. Ne pas coder de thème Solo Leveling, ne pas utiliser d'illustrations IA, pas de dégradés violet-bleu néon

## Validation du kit Kaisel (par Bellion, le 2026-10-03)

Le kit d'identité visuelle (kit/README.md + kit/logo.svg) est **validé sans correction**.

Conformité charte qualité :
- [x] Palette : 3 couleurs max (Navy #1a2332, Teal #2d8a7a, Stone #f5f6f8)
- [x] Typographies : 2 polices Google Fonts réelles (DM Sans + Inter)
- [x] Logo : symbole géométrique vectoriel (3 barres rectangulaires) + texte en vraie typographie (DM Sans), aucun texte dessiné par IA
- [x] Ton de voix : 5 règles alignées avec la charte (précis, B2B, sobre, direct, utile)
- [x] Aucun élément interdit : pas de dégradé violet-bleu, pas d'objet 3D, pas d'illustration IA, pas d'émoji dans les titres
- [x] Usage universel : kit applicable à tous les supports (site, réseaux, documents, dashboard)