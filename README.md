# butterfly-effect

Skill Claude Code qui propage un changement **partout** dans un projet : code, textes, traductions, docs, config, tests.

## Ce qu'il fait
- Cartographie toutes les occurrences (variantes de casse, pluriels, textes UI, i18n, docs, config, tests).
- Montre le plan avant d'éditer, applique partout, puis re-vérifie qu'il ne reste aucun ancien terme.
- Lance build / lint / tests et rend un rapport court.
- **Sans argument**, il retrouve seul les dernières modifications (conversation, `git diff`, ou fichiers récents) et vérifie leur propagation.

## Installation
Copier le dossier `skills/butterfly-effect` dans `~/.claude/skills/` (global) ou `.claude/skills/` (projet).

## Utilisation
```
/butterfly-effect "Panier" → "Cart"
/butterfly-effect
```

Pour un lancement quasi automatique, ajouter dans `~/.claude/CLAUDE.md` :
> Après tout renommage ou changement de texte, de valeur ou de règle, invoque le skill `butterfly-effect`.

## Licence
MIT
