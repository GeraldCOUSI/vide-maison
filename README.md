# Vide-maison

Catalogue en ligne du vide-maison, publié avec GitHub Pages : https://geraldcousi.github.io/vide-maison/

- `index.html` : l'application (une seule page, sans dépendance).
- `data/lots.json` : les lots et les réglages (titre, textes, délai d'exclusivité famille, contacts, code du lien famille).
- `photos/` : les photos des lots (`<lot>-<id>.jpg` en grand format, `-t.jpg` en miniature).

Les visiteurs consultent le site sans compte. Le mode gestion (lien « Gestion » en bas de page) écrit directement
dans ce dépôt grâce à une clé GitHub personnelle à accès précis (Contents : Read and write, limitée à ce dépôt),
conservée uniquement dans le navigateur de l'administrateur. Chaque enregistrement crée un commit ;
le site public se met à jour une à deux minutes plus tard.
