# Tablette du Crew

Site du crew : lancement des OP (postes, véhicules), bilans d'activité prêts pour Discord, roue du coffre et formations.
Toutes les modifications apparaissent en direct chez tous les membres (base de données Firebase).

## Mise en route (une seule fois, par le patron)

### 1. Créer la base de données Firebase (gratuit)
1. Va sur https://console.firebase.google.com et clique sur **Créer un projet** (nom au choix, Google Analytics inutile).
2. Dans le menu de gauche : **Créer > Firestore Database > Créer une base de données**.
   Choisis une région en Europe (ex. `eur3` ou `europe-west`) et le **mode production**.
3. Onglet **Règles** : remplace tout par le contenu du fichier `firestore.rules`, puis **Publier**.
4. Roue crantée **Paramètres du projet > Général > Vos applications** : clique sur l'icône **Web `</>`**,
   donne un nom, puis copie le bloc `firebaseConfig`.
5. Colle ces valeurs dans `config.js` à la place de `COLLE_ICI` (et des champs vides).

### 2. Mettre le site en ligne avec GitHub Pages
Dans le dépôt GitHub : **Settings > Pages > Source : Deploy from a branch > Branch : `main` / `(root)`**.
Le site est ensuite disponible sur `https://<ton-pseudo>.github.io/tablette-crew/`.

### 3. Créer le crew
1. Ouvre le site, entre un **code du crew** d'au moins 16 caractères (lettres, chiffres, `-` ou `_`).
2. Clique sur **Créer le crew avec ce code** : les 13 membres sont ajoutés automatiquement.
3. Partage le lien du site et le code **uniquement** sur le Discord du crew.

Chaque membre ouvre le lien, entre le code, puis choisit son nom.

## Sécurité
- Le code du crew sert de mot de passe : toute personne qui l'a peut lire et modifier les données.
  Si le code fuite, crée un nouveau crew avec un nouveau code (les données ne suivent pas).
- Le choix du nom dans le site n'est pas protégé : c'est de la confiance entre membres.

## Limites gratuites Firebase
50 000 lectures et 20 000 écritures par jour : largement suffisant pour un crew.
