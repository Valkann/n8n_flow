# itchio-jdr-aleatoire

Workflow n8n qui tire au hasard un JdR français sans IA sur itch.io.

## Fonctionnement

1. Récupère le flux RSS `https://itch.io/physical-games/newest/lang-fr/tag-no-ai.xml`, page par page (36 jeux par page, 21 pages au dernier relevé).
2. Convertit le XML en JSON.
3. Choisit un jeu au hasard et renvoie `title`, `link`, `summary` et `hashtags` (`#JdR #itchio`).

## Import

Dans n8n, copie le contenu de `workflow.json` et colle-le sur le canvas (`Ctrl+V`).

## Points d'attention

- Le header `User-Agent` est réglé sur `python-requests/2.32.3`. Avec un User-Agent de navigateur, itch.io répond en 429.
- La pagination s'arrête sur la première page vide ou en erreur (`Never Error` activé, 25 pages au maximum, 500 ms entre les requêtes).
- Évite les exécutions en rafale : chaque lancement fait environ 22 requêtes.
