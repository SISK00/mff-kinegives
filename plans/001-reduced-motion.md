# 001 — Respecter `prefers-reduced-motion`

- **Status**: DONE
- **Commit**: N/A (pas de dépôt git dans ce projet)
- **Severity**: HIGH
- **Category**: Accessibilité
- **Estimated scope**: 1 fichier (`MFF Kinegives/index.html`), un seul bloc CSS ajouté

## Problème

Aucune règle `@media (prefers-reduced-motion: reduce)` n'existe dans tout le `<style>` du fichier. Quatre animations **infinies** tournent en permanence, plus les révélations au scroll et l'apparition mot-par-mot du hero. Un utilisateur ayant activé « Réduire les animations » (OS) subit tout cela — nausée/vertige potentiels, et c'est un manquement d'accessibilité.

Emplacements verbatim :

```css
/* MFF Kinegives/index.html:329 */
      animation: heroZoom 20s ease-out infinite alternate;
/* :353 */
      animation: pulse 2s ease-in-out infinite;
/* :398 */
      animation: scrollLine 2.4s ease-in-out infinite;
/* :718 */
      animation: hotspotPulse 2s ease-out infinite;
/* :218 (apparition des mots du hero) */
      animation: revealWord 0.9s var(--ease) forwards;
/* :1262-1263 (révélation au scroll) */
      transform: translateY(24px);
      transition: opacity 0.8s var(--ease), transform 0.8s var(--ease);
```

## Target

Ajouter, **juste avant la fermeture `</style>`**, un bloc qui : coupe les boucles infinies, neutralise les déplacements des révélations (garde l'opacité comme feedback de compréhension), et rend les mots du hero visibles immédiatement. On ne met pas « 0 animation » — on retire le mouvement, on garde les fondus.

```css
    /* ─── REDUCED MOTION ─── */
    @media (prefers-reduced-motion: reduce) {
      /* Couper toutes les boucles infinies et neutraliser les déplacements */
      .hero-visual-img,
      .hero-badge-dot,
      .hero-scroll-line::after,
      .tour-hotspot::before {
        animation: none !important;
      }
      /* Mots du hero : visibles d'emblée, sans translation */
      .hero h1 .reveal-word {
        animation: none !important;
        opacity: 1;
        transform: none;
      }
      /* Révélations au scroll : garder le fondu, supprimer le glissement */
      .reveal {
        transition: opacity 0.3s ease;
        transform: none;
      }
      .reveal.in { transform: none; }
      /* Neutraliser le zoom permanent du hero sur son état de base */
      .hero-visual-img { transform: scale(1.04); }
    }
```

## Repo conventions to follow

- Tout le CSS est inline dans un seul `<style>` en `<head>`. Ajouter le bloc **à la fin de ce `<style>`**, après les règles `.reveal-delay-*` (autour de la ligne 1268) et avant `</style>`.
- Le fichier utilise déjà des commentaires de section en cartouche `/* ─── NOM ─── */` (voir L. « NAVBAR », « HERO »). Réutiliser ce style de commentaire (fait ci-dessus).
- Les courbes sont tokenisées (`--ease`, `--ease-spring`) ; ici on utilise `ease` simple pour un fondu neutre, ce qui est volontaire.

## Steps

1. Ouvrir `MFF Kinegives/index.html`, repérer les règles `.reveal-delay-1/2/3` (≈ L1266-1268).
2. Juste après ces règles et avant la fin du `<style>`, coller le bloc `@media (prefers-reduced-motion: reduce)` de la section **Target** verbatim.
3. Ne rien changer d'autre.

## Boundaries

- Ne PAS supprimer ni modifier les `@keyframes` existants ni les déclarations d'animation d'origine — on les surcharge uniquement dans le media query.
- Ne PAS toucher au JS (observer de scroll, navbar).
- Ne PAS ajouter de dépendance.
- Si les sélecteurs ne correspondent pas au code trouvé (dérive), STOP et signaler plutôt qu'improviser.

## Verification

- **Mécanique** : ouvrir le fichier dans un navigateur, aucune erreur console.
- **Feel check** :
  - DevTools → Rendering → « Emulate CSS prefers-reduced-motion: reduce ».
  - Recharger : le hero s'affiche **sans** zoom lent, les mots du titre sont visibles immédiatement (pas d'apparition décalée), le point vert du badge ne pulse plus, la ligne « Défiler » ne scintille plus, les anneaux de hotspot 360° ne pulsent plus.
  - En scrollant, les sections apparaissent en **fondu léger** sans glisser vers le haut.
  - Repasser en motion normal : toutes les animations d'origine reviennent.
- **Done when** : en mode reduced-motion, aucun mouvement de position ni boucle infinie ne subsiste, mais les fondus d'opacité restent.
