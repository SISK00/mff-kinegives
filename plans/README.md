# Plans d'amélioration des animations — MFF Kinégives

Généré par le skill `improve-animations`. Cible : `MFF Kinegives/index.html` (page one-pager, tout le CSS inline dans un `<style>`).

Chaque plan est autonome : chemins, code actuel et valeurs cibles exacts. Un exécutant (agent ou humain) peut les appliquer sans contexte de la conversation.

| # | Titre | Sévérité | Catégorie | Status |
|---|-------|----------|-----------|--------|
| [001](001-reduced-motion.md) | Respecter `prefers-reduced-motion` | HIGH | Accessibilité | ✅ DONE |
| [002](002-hover-gating.md) | Restreindre les survols aux vrais pointeurs | MEDIUM | Accessibilité | ✅ DONE |
| [003](003-press-feedback.md) | Retour au press (`:active`) | MEDIUM | Physicalité | ✅ DONE |

## Ordre d'exécution recommandé

1. **001** (le plus fort levier — accessibilité, boucles infinies)
2. **002** (accessibilité tactile)
3. **003** (polissage physique)

## Dépendances

Aucune dépendance fonctionnelle entre les plans. Les trois ajoutent chacun un bloc CSS **en fin de `<style>`** — les appliquer dans l'ordre 001 → 002 → 003 pour que les blocs se suivent proprement au même endroit. Aucun ne modifie les règles existantes ; ils ne font que surcharger/compléter.

## Non retenus (audit)
- Finding #4 (`.reveal` à 800 ms) : LOW, marketing — laissé de côté sauf demande.
- Missed opportunities (crossfade entre scènes 360°, menu mobile absent < 600px, emphase ligne « aujourd'hui ») : à discuter séparément, additifs.
