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

### 3. Codes personnels
Chaque membre se connecte avec **son propre code** (format `XXXX-XXXX-XXXX`), créé par l'admin du site.

- **Migration d'un crew existant** (une seule fois) : publier d'abord les règles de `firestore.rules`,
  puis l'admin ouvre `https://<ton-pseudo>.github.io/tablette-crew/#migration` sur l'appareil où il utilisait le site.
  Il choisit l'admin, et tous les codes sont créés. Les OP, bilans, coffre et formations sont conservés.
- L'admin retrouve les codes dans l'onglet **Codes d'accès** : bouton « Lien » pour envoyer un lien qui connecte directement,
  « Nouveau code » pour désactiver un code qui a fuité.

## Droits
| Grade | Peut faire |
|---|---|
| Membre, Lieutenant | Consulter, s'inscrire à une formation, cocher le coffre quand c'est son tour |
| Exécutant (élite) | Lancer les OP, tirer la roue, valider les formations, passer un membre Lieutenant ou Membre, virer un Lieutenant ou un Membre |
| Patron, Co-Patron | Tout sauf les codes d'accès : ajouter des membres, tous les grades, paramètres, supprimer un bilan |
| Admin du site | Tout, y compris les codes d'accès |

Ces droits sont vérifiés par Firebase (fichier `firestore.rules`), pas seulement par le site.

## Sécurité
- Avec son code, un membre agit sous son nom et avec son grade : il ne doit le donner à personne.
- Un membre viré ou dont le code est changé ne peut plus rien modifier.
- Quelqu'un qui récupère l'identifiant interne du crew (stocké dans le navigateur d'un membre) peut lire les données, mais pas les modifier.

## Limites gratuites Firebase
50 000 lectures et 20 000 écritures par jour : largement suffisant pour un crew.
