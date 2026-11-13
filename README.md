# README de rendu — Projet noté — Site vitrine d'un événement

> Pour chaque exigence, l'emplacement est indiqué sous la forme `fichier:ligne`.

## Groupe

| | |
|---|---|
| Nom du groupe | indie-game-fest |
| Membres (et rôle référent) | Clément Desbuisson : travail individuel, donc référent des trois rôles (architecture, mise en page, thème/animations/accessibilité) |
| Événement / thème | **Indie Game Fest** : salon du jeu vidéo indépendant, 5 & 6 décembre 2026, Lille Grand Palais |
| Lien du dépôt Git (si Git) | Pas de dépôt Git : rendu par archive `groupe-indie-game-fest.zip` |
| Convention de nommage | **BEM** : `.bloc`, `.bloc__element`, `.bloc--variante` (ex. `.card`, `.card__media`, `.btn--ghost`) ; utilitaires préfixés `u-` (`.u-visually-hidden`) ; tokens primitifs nommés d'après leur valeur (`--color-plum-950`, `--space-4`), tokens sémantiques d'après leur rôle (`--color-surface`, `--space-section`) |

## Localisation des exigences

| Exigence | Élément attendu | Fichier:ligne | Commentaire (ce que ça fait, pourquoi) |
|---|---|---|---|
| 1. Architecture | Déclaration de l'ordre des `@layer` | `css/main.css:16` | `@layer reset, base, layout, components, utilities;`, déclaré une seule fois avant les `@import`. Chaque fichier est enveloppé dans son layer (ex. `css/reset.css:9`). Aucune règle hors layer. |
| 1. Architecture | Tokens primitifs | `css/tokens.css:22` | Palette oklch (prune, violet, lavande, sarcelle), échelle d'espacements, rayons, durées, courbe, familles de polices (`tokens.css:17-65`). |
| 1. Architecture | Tokens sémantiques | `css/tokens.css:74` | Rôles : `--color-surface`, `--color-text`, `--color-action`, `--color-highlight`, `--font-size-*`, `--space-section`, `--radius-card`, `--duration-fast`… (`tokens.css:67-157`). Espacements par rôle (`tokens.css:120-143`) : `--space-inset-*` (marges intérieures), `--space-gap-*` (écarts), `--space-scroll-offset`, `--size-logo`… Les composants n'utilisent que ceux-là : aucun primitif (`--space-4`, `--color-plum-*`…) dans `layout.css`, `components.css` ni `base.css`. |
| 1. Architecture | Nesting natif (exemple représentatif) | `css/components.css:71-96` | `.site-nav__link` → `&::after` → `&:is(:hover, :focus-visible)` → `&::after` : 3 niveaux au maximum, `@media`/`@container` imbriqués dans le composant (ex. `layout.css:83`). |
| 2. Mise en page | `grid-template-areas` (mobile → desktop) | `css/layout.css:118-132` (programme) et `css/layout.css:72-94` (hero) | Programme : `"titre" "jour1" "jour2"` → `"titre titre" "jour1 jour2"`. Hero : 5 zones (`titre`, `infos`, `actions`, `affiche`, `visuel`) en colonne sur mobile → 2 colonnes en desktop. Seuls `grid-template-columns` et `grid-template-areas` changent, les enfants gardent leur `grid-area`. |
| 2. Mise en page | `subgrid` | `css/layout.css:165-169` + `css/components.css:354-374` (cartes) ; `css/layout.css:181-185` + `css/components.css:458` (offres) ; `css/components.css:306` (créneaux) | Cartes : chaque `<li>` occupe 4 lignes (`grid-row: span 4`) et les transmet à la carte (`grid-template-rows: subgrid`). Image, nom, bio et lien sont alignés d'une carte à l'autre. Démo : allonger une seule bio. Offres : prix et boutons alignés. Créneaux : colonne horaire commune à toute la journée. |
| 2. Mise en page | Flexbox pour un composant | `css/layout.css:41-63` (en-tête, nav), `css/components.css:252` (groupe de boutons), `css/components.css:387` (en-tête de carte), `css/components.css:609` (réseaux entre deux filets), `css/layout.css:189-214` (footer, colonnes en `flex: 1 1 15rem`) | Flexbox pour l'unidimensionnel avec `gap`, `flex-wrap`, raccourci `flex`. Grid pour le bidimensionnel. |
| 3. Responsive | Première media query mobile-first | `css/layout.css:61` | Styles de base pour 320 px, puis `@media (width >= 48rem)` pour élargir (aussi `layout.css:83`, `layout.css:126`). |
| 3. Responsive | Typographie fluide `clamp()` | `css/tokens.css:112-122` | Échelle complète dans les tokens : texte courant, chapeau, h1, h2, h3, prix, espacement de section. La part en `rem` garde le zoom actif. |
| 3. Responsive | Container query (`container-type` + `@container`) et ses 2 contextes | Conteneurs : `css/layout.css:160` (liste `.speakers`) et `css/layout.css:98` (bandeau `.hero__spotlight`). Requête : `css/components.css:440` | Le composant `.card` est compact (image à gauche) par défaut et devient vertical si son conteneur `card-host` dépasse 40rem. **Contexte 1** : la grille des intervenants, large en desktop, d'où des cartes verticales. **Contexte 2** : le bandeau « Invitée d'honneur » du hero (≈ 27rem), où la même carte reste compacte à 1440 px. Le conteneur n'est pas le `<li>`, car `container-type` impose une containment de layout qui annulerait le `subgrid`. |
| 3. Responsive | Test 320 px sans défilement horizontal (correctif principal) | `css/reset.css:35-49`, `css/layout.css:15-18` | Vérifié à 320 px (iframe de 320 px, `scrollWidth` = `clientWidth`). Correctifs : `.container` en `min(100% - 2 * gouttière, 72rem)`, `minmax(min(100%, 18rem), 1fr)` dans les grilles, images en `max-inline-size: 100%`, `overflow-wrap: break-word` pour les adresses mail. |
| 4. Sélecteurs | `:has()` pertinent | `css/components.css:477` | `.offer:has(.offer__badge)` : l'offre qui contient un badge est mise en avant automatiquement. Déplacer le badge dans le HTML change l'offre vedette. Sans `:has()`, il faudrait une classe ajoutée à la main. Autre usage : la bascule de thème (`tokens.css:178`). |
| 4. Sélecteurs | `:is()` / `:where()` | `css/base.css:32`, `css/reset.css:22`, `css/components.css:41`, `css/components.css:423` | `:where(h1, h2, h3)` en base : spécificité nulle, facile à surcharger. `:is(.site-header, .site-footer)` et `:is(.card__badge, .offer__badge)` factorisent des styles communs (spécificité d'une classe). |
| 5. Thème | `light-dark()` / `prefers-color-scheme` + `color-scheme` | `css/tokens.css:71` et `css/tokens.css:74-104` | `color-scheme: light dark` + `light-dark()` dans tous les tokens sémantiques de couleur. Le thème suit le système. En-tête et pied de page restent prune dans les deux thèmes, comme bandeaux de marque. |
| 5. Thème (bonus technique) | Bascule CSS-only | `css/tokens.css:178-186`, `index.html:42-45` | Une case `#theme-toggle` (`role="switch"`) **inverse** le thème du système : `:root:has(#theme-toggle:checked)` passe `color-scheme` à `dark`, ou à `light` si le système est déjà sombre. Tous les `light-dark()` basculent d'un coup. Aucun JavaScript. |
| 6. Animations | Transition n°1 | `css/components.css:169` | Boutons : `translate` + `background-color` + `color`, durées `--duration-fast` et courbe `--ease-out`. |
| 6. Animations | Transition n°2 | `css/components.css:364` | Cartes : `translate` au survol et au `:focus-within`. Autres : soulignement de la nav en `scale` (`:86`), icône de FAQ en `rotate` (`:576`), interrupteur de thème (`:134`). |
| 6. Animations | `@keyframes` | `css/components.css:699-717` (utilisés `:664-697`) | `rise-in` : apparition en cascade du hero et des réponses de FAQ. `pixel-pop` : apparition en `steps()` façon pixel art. `cta-pulse` : 3 pulsations du bouton principal, pas une boucle infinie. Seuls `opacity`, `translate` et `scale` sont animés. |
| 6. Animations | `prefers-reduced-motion` | `css/tokens.css:165-171`, `css/components.css:664`, `css/base.css:11` | Les `@keyframes` ne sont appliqués que sous `no-preference`. Sous `reduce`, les durées sémantiques passent à `0s`, donc toutes les transitions deviennent instantanées. Les keyframes ne définissant qu'un état `from`, aucun contenu ne reste invisible. Test : DevTools > Rendu > Émuler `prefers-reduced-motion`. |
| 6. Animations (bonus technique) | Scroll-driven (`@supports`), View Transitions, `@starting-style`/popover… | — | Non revendiqué (seul bonus : la bascule CSS-only). |
| 7. Accessibilité | Landmarks + hiérarchie de titres | `index.html:2` (`lang="fr"`), `:25` header, `:32` nav, `:49` main, `:400` footer | Un seul `h1` (`:57`). Sections titrées par `aria-labelledby`. Hiérarchie h1 → h2 → h3 → h4 continue. Lien d'évitement (`:20`). FAQ en `<details>/<summary>` (`:348`). Listes `<ul>/<ol>` pour les listes. |
| 7. Accessibilité | `:focus-visible` | `css/base.css:69`, `css/components.css:48` | Contour de 3px partout, en violet sur fond clair et en sarcelle sur fond sombre. Il est surchargé en sarcelle sur les bandeaux prune pour garder le contraste. Cibles d'au moins 24 px (boutons de 2.75rem, interrupteur de 1.5rem). |
| 7. Accessibilité | Contrastes AA vérifiés (outil + paires de couleurs testées, 2 thèmes) | voir section « Contrastes » ci-dessous | Ratios WCAG calculés à partir des valeurs oklch (script de conversion oklch → sRGB) : toutes les paires ≥ 6,4:1. |
| 7. Accessibilité | Images avec `alt` | `index.html:83`, `:201`, `:214`, `:227`, `:240`, `:253`, `:266` | `alt` descriptif sur chaque photo et sur l'illustration du Collectif Bitmap. Grand logo du hero décoratif (`alt=""`, le `h1` donne déjà le nom). Logo : son `alt` nomme le lien. |
| 8. Qualité | Résultat du validateur W3C | voir section « Avertissements du validateur » | Validé le 13/11/2026 : **aucune erreur réelle**. Les 20 signalements sont des syntaxes récentes non reconnues par le validateur, ou un plantage interne de l'outil (`light-dark()`), détaillés ligne par ligne ci-dessous. |
| 8. Qualité | `!important` (aucun, ou utilitaire justifié) | — | **Aucun** `!important` : les conflits sont réglés par l'ordre des layers. |

## Contrastes vérifiés (WCAG 2.1)

| Paire (thème) | Ratio |
|---|---|
| Texte `plum-950` / surface `lavender-50` (clair) | 18,45:1 |
| Texte atténué `plum-700` / `lavender-100` (clair) | 9,11:1 |
| Lien/accent `violet-600` / `lavender-50` (clair) | 7,09:1 |
| Bouton : blanc / `violet-600` (clair) | 7,53:1 |
| Badge/horaire : blanc / `teal-700` (clair) | 8,15:1 |
| Texte `lavender-50` / surface `plum-950` (sombre) | 18,45:1 |
| Texte `lavender-50` / carte `plum-800` (sombre) | 13,83:1 |
| Texte atténué `lavender-200` / `plum-800` (sombre) | 10,42:1 |
| Accent `violet-300` / `plum-800` (sombre) | 7,02:1 |
| Bouton : `violet-600` / `lavender-100` (sombre) | 6,46:1 |
| Badge/horaire : `plum-950` / `teal-300` (sombre) | 11,76:1 |
| Bandeaux : `lavender-50` / `plum-800` ; focus `teal-300` / `plum-800` | 13,83:1 ; 8,82:1 |
| Hero sur photo, pire cas (voile 85 % sur pixel noir en clair / blanc en sombre) : texte, texte atténué, accent, sarcelle | clair 13,06 / 7,08 / 5,02 / 5,43:1 ; sombre 12,55 / 9,45 / 6,37 / 8,00:1 |

## Avertissements du validateur W3C non corrigés

Validation effectuée le 13/11/2026 sur https://jigsaw.w3.org/css-validator/ (profil CSS niveau 3 + SVG), fichier par fichier. **Aucune erreur réelle** : les 20 « erreurs » signalées concernent toutes des syntaxes récentes que le validateur ne connaît pas encore, ou un plantage interne de l'outil.

| Fichier:ligne | Message du validateur | Pourquoi c'est valide |
|---|---|---|
| `css/main.css:18-19` | « @import n'est pas autorisé s'il apparaît après toute déclaration autre que @charset et @import » | Depuis Cascade Layers (Baseline 2022), une instruction `@layer` déclarative est explicitement autorisée **avant** les `@import`. C'est même la méthode demandée par l'énoncé. |
| `css/tokens.css:74-99` (13 lignes) | « Cannot invoke "org.w3c.css.values.CssValue.getType()" because "<parameter2>" is null » | Exception Java interne du validateur, déclenchée par `light-dark()` (CSS Color 5, Baseline 2024). Ce n'est pas une erreur CSS : les lignes concernées sont exactement les tokens en `light-dark()`. |
| `css/layout.css:16`, `:21` | « min(100% - 2 * var(--space-gutter), …) n'est pas une valeur de inline-size » | `min()` avec un calcul contenant `var()` (CSS Values 4, Baseline 2020). Le validateur ne sait pas évaluer `var()` dans un calcul. |
| `css/layout.css:98`, `:160` | « La propriété container n'existe pas » | Raccourci `container` des container queries (Baseline 2023). |
| `css/components.css:440` | « La règle-arobase @container n'est pas reconnue » | Container queries (Baseline 2023). |
| `css/reset.css:18` | Avertissement : « -webkit-text-size-adjust est une extension propriétaire » | Préfixe volontaire, nécessaire pour iOS Safari (doublé de la propriété standard `text-size-adjust`). |
| `css/tokens.css:184`, `css/layout.css:89`, `:130`, `css/components.css:65`, `:264`, `:675`, `:679`, `:708` | Avertissements « Redéfinition de la propriété … » | Redéfinitions **voulues** : valeur modifiée dans une media query imbriquée (mobile-first), dans la bascule de thème ou dans une étape de `@keyframes`. |
| tous les fichiers | Avertissements « les variables CSS ne sont pas vérifiées statiquement », « pas de couleur de fond définie avec votre couleur », « proposer une famille générique » | Le validateur ne résout pas les custom properties. Les couleurs de fond sont fixées par les tokens, et les familles génériques sont présentes dans les tokens (`--font-family-pixel`, `--font-family-sans`). |

Le nesting natif, `:has()`, `color-mix()`, `text-wrap` et `content: "+" / ""` n'ont déclenché aucune erreur. `utilities.css` est valide sans aucun avertissement.

## Bonus technique revendiqué

| Fonctionnalité | Fichier:ligne | Explication (et repli prévu pour les navigateurs qui ne la gèrent pas) |
|---|---|---|
| Bascule de thème CSS-only via `:has()` | `css/tokens.css:178-186`, `index.html:42-45` | La case `#theme-toggle` inverse le thème du système en changeant `color-scheme` sur `:root`. Les tokens `light-dark()` suivent sans aucun JS. Sans support de `:has()`, la case n'a aucun effet et le site garde le thème du système, entièrement lisible. |

## JavaScript (facultatif, ≤ 20 lignes)

| Fichier:lignes | Ce que fait le code | Pourquoi ce n'était pas faisable en CSS |
|---|---|---|
| — | **Aucun JavaScript.** | — |

## Déclaration d'usage d'outils d'IA

| Outil | Pour quoi faire (textes, explication d'une erreur, revue…) | Parties du code concernées |
|---|---|---|
| Claude Code (Anthropic) | Rédaction de la structure HTML et des textes fictifs (bios, programme, FAQ) en collaboration et selon mes instructions, afin de disposer d'une architecture BEM propre avant d'écrire mon CSS ; rédaction des commentaires CSS et explications du fonctionnement de certaines notions vues en cours et en TP; demande de rédaction de style rapide d'une card pour un gain de temp dans le css basique; recadrage des photos ; calcul des contrastes ; assistance à la vérification responsive à 320 px et à la validation W3C. | `index.html`, commentaires de `css/*.css`, `img/*`, ce README |

> Rappel : chaque membre doit pouvoir expliquer chaque ligne en soutenance (question individuelle, critère H).

## Qui a fait quoi

| Membre | Principales contributions |
|---|---|
| Clément Desbuisson | Travail individuel : choix du thème et de l'identité (nom, dates, lieu, palette de couleurs, logo, motif d'icônes), sélection des photos, co-conception du HTML, rédaction complète des feuilles de style CSS, tests navigateur, vérifications d'accessibilité et validation W3C. |

## Crédits

- **Photos** (licence Unsplash, libres de droits), recadrées en 640×800 : Inglés para Devs (streamer), Brooke Cagle (développeur indé), Zulfugar Karimov (développeuse indé), Jelly Marketing (game designer), Look Studio (joueuse pro). Les noms des intervenants sont fictifs, photo du hero : Maxime Dufour.
- **Logo Indie Game Fest, motif d'icônes** (en-tête et pied de page) **et icônes des réseaux** (SVG du footer) : créations originales pour le projet.
- **Illustration pixel art** du Collectif Bitmap : créée pour le projet, en SVG, logo & pattern de indie game fest crée par Clément Desbuisson pour le projet.
- **Polices** : Pixelify Sans (Stefie Justprince) et Inter (Rasmus Andersson), Google Fonts, licence SIL Open Font License.
