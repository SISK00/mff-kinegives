# 002 — Restreindre les effets de survol aux vrais pointeurs

- **Status**: DONE
- **Commit**: N/A (pas de dépôt git dans ce projet)
- **Severity**: MEDIUM
- **Category**: Accessibilité
- **Estimated scope**: 1 fichier (`MFF Kinegives/index.html`), quelques règles `:hover` regroupées sous un media query

## Problème

Les effets de survol qui **déplacent** des éléments ne sont pas gardés par `@media (hover: hover)`. Sur un écran tactile, le premier tap déclenche l'état `:hover` (qui reste « collé » jusqu'au tap suivant) avant le clic — l'élément saute puis reste dans son état survolé, ce qui paraît cassé.

Emplacements verbatim :

```css
/* MFF Kinegives/index.html:468 */
      transform: translateY(-6px);      /* .card:hover */
/* :586 */
    .praticien:hover .praticien-photo img { transform: scale(1.04); }
/* :1171 */
      transform: translateX(4px);       /* .rdv-contact:hover */
/* :1210 */
      transform: translateX(4px);       /* .rdv-contact:hover .rdv-contact-arrow */
```

(Les hovers de simple changement de couleur — liens de nav, footer — n'ont pas besoin d'être gardés : pas de déplacement, pas de gêne tactile.)

## Target

Envelopper les blocs `:hover` **porteurs de mouvement** dans `@media (hover: hover) and (pointer: fine)`. Les couleurs de fond au survol peuvent rester dans les cartes (elles ne dérangent pas), mais le déplacement `translateY`/`translateX`/`scale` doit être conditionnel.

Approche recommandée : ne PAS réécrire chaque règle en place (risque d'erreur), mais **neutraliser le mouvement sur non-hover** via un media query dédié en fin de `<style>` :

```css
    /* ─── TOUCH: neutraliser le mouvement au "hover" fantôme ─── */
    @media (hover: none), (pointer: coarse) {
      .card:hover { transform: none; }
      .praticien:hover .praticien-photo img { transform: none; }
      .rdv-contact:hover { transform: none; }
      .rdv-contact:hover .rdv-contact-arrow { transform: none; }
      .btn-primary:hover,
      .nav-cta:hover { transform: none; }
    }
```

Cela laisse le comportement desktop intact et supprime uniquement le déplacement sur appareils sans survol réel.

## Repo conventions to follow

- CSS inline unique ; ajouter ce bloc en fin de `<style>`, à côté du bloc reduced-motion du plan 001 (si 001 est déjà appliqué, placer juste après).
- Garder le commentaire cartouche `/* ─── … ─── */`.

## Steps

1. Repérer la fin du `<style>` (après `.reveal-delay-*`, ≈ L1268).
2. Coller le bloc `@media (hover: none), (pointer: coarse)` de la section **Target** verbatim.
3. Ne pas modifier les règles `:hover` d'origine.

## Boundaries

- Ne PAS retirer les changements de **couleur/fond** au survol (ils sont sans danger tactile).
- Ne PAS toucher au JS ni au HTML.
- Ne PAS ajouter de dépendance.
- Si un sélecteur diffère du code réel, STOP et signaler.

## Verification

- **Mécanique** : pas d'erreur console.
- **Feel check** :
  - DevTools → mode responsive/tactile (ou vrai téléphone) : taper une carte « Soins » → elle ne se soulève plus et ne reste pas « collée » en état survolé ; le clic/scroll fonctionne normalement.
  - Sur desktop (souris) : survol des cartes, boutons, contacts RDV → les mouvements d'origine (lift, translate, zoom photo) sont **intacts**.
- **Done when** : aucun état de survol « collant » sur tactile ; comportement desktop inchangé.
