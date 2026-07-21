# 003 — Ajouter un retour au press (`:active`) sur les éléments cliquables

- **Status**: DONE
- **Commit**: N/A (pas de dépôt git dans ce projet)
- **Severity**: MEDIUM
- **Category**: Physicalité
- **Estimated scope**: 1 fichier (`MFF Kinegives/index.html`), ajout de règles `:active`

## Problème

Les éléments principaux d'action se soulèvent au survol mais **n'accusent pas le clic** : aucun état `:active`. Le seul `:active` du fichier concerne le curseur de pannellum (L697). Un bouton qui ne « s'enfonce » pas au press paraît moins tangible (cf. AUDIT §3 : `scale(0.97)` sur `:active`, subtil, 0.95–0.98).

Éléments concernés (code verbatim) :

```css
/* MFF Kinegives/index.html:252-255 */
      transition: transform 0.2s var(--ease), background 0.25s;
    }
    .btn-primary:hover { transform: translateY(-2px); background: var(--plum-900); }
/* :169-174 */
      transition: transform 0.2s var(--ease), box-shadow 0.2s;
    }
    /* ... */
    .nav-cta:hover { transform: translateY(-1px); box-shadow: 0 6px 20px rgba(0,0,0,0.25); }
/* .btn-ghost et .tour-scene-btn : hover présent, pas d':active */
```

## Target

Ajouter un feedback de press subtil (`scale(0.97)`) sur les éléments cliquables. Durée courte (AUDIT §2 : feedback de bouton 100–160 ms). Le `transition` de ces éléments inclut déjà `transform`, donc le retour est fluide.

```css
    /* ─── PRESS FEEDBACK ─── */
    .btn-primary:active,
    .btn-ghost:active,
    .nav-cta:active,
    .tour-scene-btn:active {
      transform: scale(0.97);
      transition: transform 0.12s var(--ease);
    }
```

Note : pour `.btn-primary` et `.nav-cta`, l'état survol applique un `translateY`. Au press (souris enfoncée = encore en hover), `scale(0.97)` remplace la translation le temps du clic — c'est le comportement voulu (l'élément s'enfonce). Vérifier au feel check que la transition reste douce.

## Repo conventions to follow

- Réutiliser le token d'easing existant `--ease` (défini L43 : `cubic-bezier(0.32, 0.72, 0, 1)`).
- CSS inline unique ; placer le bloc en fin de `<style>`, à côté des autres ajouts (plans 001/002).
- Commentaire cartouche `/* ─── … ─── */`.

## Steps

1. Repérer la fin du `<style>` (≈ L1268, après `.reveal-delay-*`).
2. Coller le bloc `PRESS FEEDBACK` de la section **Target** verbatim.
3. Ne pas modifier les règles `:hover` existantes.

## Boundaries

- Garder le press subtil (`scale(0.97)`, pas plus bas que 0.95).
- Ne PAS ajouter d'`:active` aux liens de simple navigation (nav-links, footer) — inutile.
- Ne PAS toucher au JS ni au HTML.
- Ne PAS ajouter de dépendance.
- Si les sélecteurs ne correspondent pas, STOP et signaler.

## Verification

- **Mécanique** : pas d'erreur console.
- **Feel check** :
  - Cliquer (et maintenir) « Prendre rendez-vous », « Prendre RDV » (nav), « Rencontrer votre futur kiné », et les boutons de scène 360° : chaque élément **s'enfonce légèrement** pendant l'appui puis revient au relâchement.
  - DevTools → Animations, réduire la vitesse à 10 % : la bascule vers `scale(0.97)` est douce, sans à-coup.
  - Le hover d'origine (lift) reste inchangé quand on ne clique pas.
- **Done when** : les 4 groupes d'éléments réagissent visiblement au press, subtilement, sans casser leurs états de survol.
