# Vide-maison — instructions pour Claude Code

Catalogue en ligne d'un vide-maison familial, publié avec GitHub Pages :
https://geraldcousi.github.io/vide-maison/

Propriétaire : Gérald. Il travaille en français, débute en développement et préfère des instructions
pas à pas, clic par clic, avec la raison de chaque étape et des exemples concrets.

## Architecture

- `index.html` : toute l'application (HTML, CSS, JS), sans dépendance ni étape de build.
- `data/lots.json` : `{ meta, lots }`. C'est la base de données, modifiée en direct depuis le site.
- `photos/` : photos des lots (`<lotId>-<id>.jpg` en 1600 px, `-t.jpg` en miniature 640 px).
- Lecture publique : `fetch("data/lots.json?t=…")` depuis GitHub Pages.
- Mode gestion : écrit dans le dépôt via l'API Contents de GitHub avec un jeton à accès précis
  (Contents : Read and write, ce dépôt uniquement), stocké dans le `localStorage` de l'appareil
  de l'administrateur. Chaque enregistrement relit le fichier (sha), applique la modification, puis écrit.
  Chaque enregistrement crée donc un commit « Lot NN enregistré. ».

## Règles de travail

- **Ne jamais écraser `data/lots.json`** : Gérald saisit l'inventaire en continu depuis son iPhone.
  Avant toute modification : `git pull --ff-only`, puis modifier, commiter, `git fetch` + `git rebase origin/main`, et pousser.
  Pour changer `meta`, faire une modification ciblée en Python sur le JSON fraîchement récupéré.
- Garder les valeurs par défaut côté code (`categories()`, `pieces()`, `statuts()`, `profils()`, `nomsResa()`)
  pour que l'application fonctionne même si une clé `meta` manque.
- Tester avant de pousser : `node --check` sur le script extrait, puis Playwright sur
  `python3 -m http.server`, en simulant `api.github.com` avec `context.route`.
- Ne jamais écrire de jeton GitHub dans le dépôt : il est public.

## Modèle de données

`meta` : `titre`, `intro`, `infos` (texte de retrait / réservation), `contacts` [{nom, tel}],
`delaiJours` (délai du lien public), `categories` [], `pieces` [], `nomsResa` [],
`statuts` [{id, label, type: dispo|reserve|parti}],
`profils` [{id, nom, delai, code}].

Lot : `id`, `no`, `titre`, `categorie`, `piece`, `description`, `prix` ("" = faire une offre, 0 = gratuit),
`statut`, `reservePour`, `photos` [{id, t}], `dispoLe` (ms ; null = brouillon),
`reserveFoyer` (réservé à la Famax), `reserveAcheteurs` (réservé aux acheteurs maison).

## Accès par profil

- Le lien d'un profil est `…/vide-maison/?acces=<code>`. Le code reste dans l'adresse (rafraîchissement,
  icône d'écran d'accueil iPhone). Les anciens liens `#code` sont convertis automatiquement.
- Le profil `foyer` (« la Famax », le foyer vendeur) voit tout dès la mise à disposition, et seul lui voit les lots
  `reserveFoyer`. Il voit aussi `reservePour`.
- Le profil `acheteurs` ne voit que les lots `reserveAcheteurs` (qui l'emportent sur `reserveFoyer`).
- Les autres profils voient un lot à `dispoLe + delai` jours ; le lien public à `dispoLe + meta.delaiJours`.
- Rendre disponible un lot réservé (bouton désactivé, ou « Rendre disponible maintenant ») remet
  `dispoLe` à l'instant présent : le calendrier normal repart de là.

## Formulaire de lot

- Obligatoires : titre, catégorie, pièce. Description facultative.
- La catégorie est proposée pendant la saisie (`devinerCategorie`) d'après le titre et la description,
  à partir du nom des catégories, du dictionnaire `KW` et des lots déjà classés. Un choix manuel n'est plus remplacé.
- Nouveau lot : « Réservé à la Famax » activé par défaut, « Réservé aux acheteurs maison » désactivé.
- Réservation des visiteurs : par SMS (pas de réservation en ligne), enregistrée ensuite par l'administrateur.
