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

### 3. Comptes (identifiant + mot de passe)
1. Dans Firebase : **Authentication > Commencer > Mode de connexion > E-mail/Mot de passe > Activer**.
2. Publier les règles de `firestore.rules` (Firestore Database > Règles).
3. Migration d'un crew existant (une seule fois) : l'admin ouvre `https://<ton-pseudo>.github.io/tablette-crew/#migration`
   sur l'appareil où il utilisait déjà le site, entre son nom et choisit son mot de passe. Tous les comptes sont créés ;
   les OP, bilans, coffre et formations sont conservés.
4. Onglet **Comptes** (admin) : bouton « Copier » pour envoyer à chacun son lien, son identifiant et son mot de passe de départ,
   « Réinitialiser » en cas d'oubli ou de fuite.
5. Chaque membre peut changer son mot de passe dans **Paramètres**.

## Droits
| Grade | Peut faire |
|---|---|
| Membre, Lieutenant | Consulter, s'inscrire à une formation, déposer/retirer dans le coffre, cocher la roue quand c'est son tour, changer son mot de passe |
| Exécutant (élite) | Lancer les OP, tirer la roue, valider les formations, sanctionner et gérer les Lieutenants/Membres, gérer les objets du coffre |
| Patron, Co-Patron | Tout sauf les comptes : ajouter des membres, tous les grades, paramètres, annuler une sanction, supprimer un bilan |
| Admin du site | Tout, y compris les comptes |

Ces droits sont vérifiés par Firebase (fichier `firestore.rules`), pas seulement par le site.

## Sanctions
Rappel à l'ordre (ne compte pas) ou avertissement. **2 avertissements = viré automatiquement.**

## Sécurité
- Les mots de passe sont gérés par Firebase Authentication : ils ne sont jamais enregistrés dans les données du crew.
- Seuls les membres connectés et actifs peuvent lire les données.
- Un membre viré ou réinitialisé perd l'accès immédiatement.

## Limites gratuites Firebase
50 000 lectures et 20 000 écritures par jour : largement suffisant pour un crew.
