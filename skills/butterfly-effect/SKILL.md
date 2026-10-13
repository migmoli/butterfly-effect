---
name: butterfly-effect
description: Propager un changement PARTOUT dans le projet (code, textes, docs, config, tests, traductions). À utiliser dès qu'on renomme, change une valeur, un texte, un prix, un nom, une règle, une couleur, une URL, ou qu'on modifie un comportement qui peut exister à plusieurs endroits. Sans argument, vérifie et corrige automatiquement les dernières modifications.
---

# Butterfly effect — un changement, zéro oubli

Objectif : quand quelque chose change, TOUT ce qui en dépend change aussi, dans tout le projet. Ne jamais s'arrêter au premier fichier trouvé.

## 0. Sans argument : détecter les dernières modifications
Si l'utilisateur lance le skill sans préciser le changement, ne PAS lui demander : le retrouver soi-même, dans cet ordre :
1. **Conversation en cours** : les éditions faites dans cette session (fichiers touchés, ancien → nouveau).
2. **Git** (si dépôt) : `git status` + `git diff` (non commité) puis `git diff HEAD~1` / `git log -5 --stat` si rien de non commité.
3. **Pas de git** : fichiers du projet triés par date de modification (les plus récents, ex. dernières heures), en comparant avec le contenu environnant pour déduire ce qui a changé.

Pour chaque modification trouvée, en déduire ANCIEN → NOUVEAU (nom, texte, valeur, règle, comportement), puis traiter chacune comme un changement à propager (étapes 2 à 6). Faire un check général : ne pas se limiter à un seul changement, couvrir tous les récents. Si une modification est vraiment ambiguë (impossible de déduire l'intention), la lister et demander seulement pour celle-là.

## 1. Définir le changement
Écrire en une ligne : ANCIEN → NOUVEAU (nom, texte, valeur, comportement, règle). Si ambigu, poser une question avant de toucher quoi que ce soit.

## 2. Cartographier AVANT d'éditer
Chercher dans TOUT le projet (hors node_modules, .git, dist, build, lockfiles) :
- **Variantes de forme** : camelCase, PascalCase, snake_case, kebab-case, MAJUSCULES, pluriel/singulier, avec/sans accents, avec/sans espaces ou tirets.
- **Textes** : libellés UI, titres, boutons, messages d'erreur, placeholders, emails, notifications, meta/SEO, alt, aria-label, README, docs, changelog, commentaires, CLAUDE.md.
- **Traductions** : tous les fichiers de langues (i18n/locales/.json/.po/.arb/.strings), toutes les langues, pas seulement le français.
- **Fichiers non-code** : config, .env.example, JSON/YAML/TOML, SQL/migrations, seeds, fixtures, mocks, snapshots, CI/CD, Docker, scripts, manifests.
- **Références indirectes** : imports, routes/URLs, clés de config, noms de fichiers/dossiers, chaînes construites dynamiquement, constantes dupliquées, types/interfaces/schémas.
- **Tests** : unitaires, e2e, snapshots, données de test.
- **Valeurs dérivées** : si une valeur change (prix, limite, version, date), chercher aussi ses équivalents calculés ou écrits en toutes lettres.
- **Concepts sans le mot exact** : chercher aussi par sens (synonymes, description du comportement), pas seulement par le terme littéral.

## 3. Montrer le plan
Lister fichier par fichier ce qui sera modifié (et ce qui sera volontairement laissé, avec la raison : historique, changelog passé, citation, données utilisateur). Si > 20 fichiers ou changement destructif, demander confirmation.

## 4. Appliquer partout
Éditer tous les endroits listés, sans en sauter. Garder le style et le ton du texte environnant (tutoiement/vouvoiement, majuscules, ponctuation). Renommer aussi fichiers/dossiers si nécessaire.

## 5. Vérifier (obligatoire)
- Re-grep l'ANCIEN terme dans toutes ses variantes : il ne doit plus rester que les exceptions justifiées.
- Lancer build / typecheck / lint / tests si disponibles et corriger les cassures.
- Relire les endroits où du texte a changé pour la cohérence (grammaire, accords, genre, pluriels).

## 6. Rapport court
- Nombre de fichiers modifiés et liste groupée (code / textes / docs / config / tests).
- Exceptions laissées et pourquoi.
- Résultat build/tests.
- Risques restants (ce qu'un grep ne peut pas voir : données en base, services externes, caches, déploiements).

## Règles
- Ne jamais déclarer « fait partout » sans l'étape 5.
- Ne pas modifier l'historique (changelog passé, migrations déjà appliquées, git) sauf demande explicite.
- En cas de doute sur un endroit, le signaler plutôt que le modifier en silence.


## Git (si le projet est un dépôt)
- **Avant** : `git status`. Si des modifs non commitées sans rapport existent, le signaler pour ne pas les mélanger avec la propagation (proposer un commit ou un `git stash`, ne jamais le faire sans accord).
- **Pendant** : `git diff` / `git log -5 --stat` pour retrouver les dernières modifications (étape 0) ; `git grep` pour la cartographie (respecte .gitignore, rapide).
- **Après** : `git diff --stat` pour résumer ce qui a été propagé et vérifier qu'aucun fichier inattendu n'a bougé.
- **Commit** : proposer un message clair (`Propagation : ANCIEN → NOUVEAU`) mais ne commiter que si l'utilisateur le demande. Ne jamais push, reset --hard, rebase ni réécrire l'historique sans demande explicite.
- **Retour arrière** : en cas de problème, indiquer comment annuler (`git restore <fichier>` ou `git diff` pour relire), sans l'exécuter seul.
- **Pas de dépôt** : suggérer `git init` pour que le prochain check soit plus précis.
