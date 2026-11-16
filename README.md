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

### 3. Mots de passe
Pas besoin d'activer quoi que ce soit dans Firebase : il suffit de publier les règles de `firestore.rules`.

- **Identifiant** = le nom du membre dans le crew. **Mot de passe** = donné par l'admin, modifiable dans **Paramètres**.
- Crew qui utilisait les anciens codes `XXXX-XXXX-XXXX` : l'admin se connecte avec son ancien code (dans « Mot de passe ») ;
  le site lui demande une **mise à jour de sécurité** qui crée un mot de passe pour chacun et désactive tous les anciens codes.
- Crew qui n'avait qu'un code de crew : l'admin ouvre `https://<ton-pseudo>.github.io/tablette-crew/#migration`.
- Onglet **Codes d'accès** (admin) : « Copier » prépare le message à envoyer en privé, « Réinitialiser » en cas d'oubli ou de fuite.
- Onglet **Logs** (admins) : le journal de toutes les modifications (qui, quand, avant → après), et l'onglet **Admins** pour nommer ou retirer des admins.
  Chaque modification écrit sa ligne de journal en même temps (exigé par les règles) ; personne ne peut la modifier ni l'effacer.
  Le fondateur (l'admin d'origine) ne peut pas être modifié par un autre admin.

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
- Le mot de passe ne quitte jamais le navigateur : le site en calcule une clé secrète (PBKDF2).
- Chaque modification est envoyée avec une preuve à usage unique, illisible par tous : impossible de récupérer
  la clé d'un autre membre dans les données, ou de réutiliser une preuve.
- Changer ou réinitialiser un mot de passe désactive aussitôt l'ancien. Un membre viré ne peut plus rien modifier.
- Limite : quelqu'un qui récupère l'identifiant interne du crew (enregistré dans le navigateur d'un membre) peut lire les données, pas les modifier.

## Limites gratuites Firebase
50 000 lectures et 20 000 écritures par jour : largement suffisant pour un crew.
