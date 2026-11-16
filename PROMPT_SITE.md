# Prompt de présentation : « Tablette du Crew »

> À coller tel quel à une autre IA pour qu'elle comprenne le site avant de t'aider à le modifier.

---

## 1. Contexte

Tu vas travailler sur **« Tablette du Crew »**, un site web privé pour une organisation criminelle **fictive** d'un serveur de jeu de rôle **GTA V / FiveM** (serveur « Révolution RP »). Tout se passe dans le jeu : braquages, laboratoires, renseignement sur les autres organisations… Le site sert d'outil de gestion à l'organisation (appelée « le crew »).

Il remplace ce que le crew faisait à la main sur Discord : les messages du salon **#bilan-activité**, la gestion du coffre de la villa, les formations, les sanctions, etc. Une grande partie des pages produit un **message prêt à copier-coller dans Discord**.

- Langue de l'interface : **français** (tutoiement, ton simple et direct).
- Utilisateurs : une quinzaine de joueurs, sur PC et sur téléphone.
- Hiérarchie du crew : **Patron → Co-Patron → Exécutant (élite) → Lieutenant → Membre**.
- L'admin du site est **« Le R »** (grade Exécutant + drapeau `admin`).

---

## 2. Technique

- **Un seul fichier `index.html`** : HTML, CSS et JavaScript « vanilla », sans framework ni étape de build. Les vues sont des fonctions qui renvoient des chaînes HTML (`VIEWS.missions = () => \`...\``). Les clics passent par un seul écouteur délégué, avec des attributs `data-a="action"` reliés à un objet `A = { action(t, e) { … } }`. Les champs utilisent `data-c="…"` pour les événements `input` et `change`.
- **Hébergement** : GitHub Pages (dépôt public `Black-at/tablette-crew`, branche `main`), à l'adresse `https://black-at.github.io/tablette-crew/`.
- **Base de données** : **Firebase Firestore**, SDK *compat* 10.12.2 chargé depuis `gstatic.com` (`firebase-app-compat.js` et `firebase-firestore-compat.js`). **Pas de Firebase Authentication** (refusé par le propriétaire) et **pas de Firebase Storage**.
- `config.js` contient `window.FIREBASE_CONFIG` (valeurs publiques). Avec `emulator: true`, le site se branche sur l'émulateur local (`127.0.0.1:8085`), ce qui sert aux tests.
- `firestore.rules` contient les règles de sécurité. **Après chaque modification, le propriétaire doit les recoller à la main** dans la console Firebase (Firestore Database → Règles → Publier). Il faut toujours le lui rappeler.
- Temps réel : tout passe par des `onSnapshot`. Chaque modification apparaît aussitôt chez tous les membres connectés.
- Mode local de secours : sans configuration Firebase, les données restent dans le `localStorage` de l'appareil.
- Tests : émulateur Firestore (`firebase emulators:exec`), `@firebase/rules-unit-testing` pour les règles, et Playwright pour les parcours dans le navigateur.

### Organisation du code (dans l'ordre du fichier)
`<style>` (jetons de design) → structure HTML (sidebar, en-tête, `#view`, `#modal`, `#toast`) → `<script>` qui contient : données statiques (icônes, missions, grades, postes, formations, menu) → helpers → `Store` (couche Firestore) → état `S` → droits → rendu → une section par page → actions `A` → écouteurs → démarrage (connexion, puis `connect()` qui ouvre tous les `onSnapshot`).

---

## 3. Style visuel

Le site reprend le style de la **tablette en jeu** du serveur : sombre, sobre, avec des accents bleus.

- **Thème sombre unique**, voulu (`color-scheme: dark`).
- **Fond de page** : rayures diagonales façon ruban de chantier (`repeating-linear-gradient(135deg, #141519 0 14px, #0a0b0d 14px 28px)`).
- **Cadre** : un grand panneau `#101113` aux coins arrondis de 8 px, avec une ombre portée profonde.
- **Barre latérale** (280 px) :
  - en haut, une pastille « ORGANISATION » (losange bleu) et le titre « CREW » (avec bouclier) en Poppins 800 majuscules ;
  - dessous, des tuiles de navigation rectangulaires (fond `#18191d`, bordure fine) ;
  - la tuile active a un dégradé bleu et une bordure gauche bleue de 2 px ;
  - les entrées « Bientôt » sont grisées ;
  - en bas, l'état de synchro (pastille verte) et le bouton rouge « Se déconnecter ».
- **En-tête de page** : icône, titre et sous-titre à gauche ; à droite, une carte avec le nom du membre et son grade. Dessous, un trait horizontal avec **un petit carré blanc à chaque extrémité**, signature de la tablette. Le séparateur vertical de la sidebar a les mêmes carrés.
- **Police** : **Poppins** (400 à 800) partout, chiffres en `tabular-nums`.
- **Couleurs (jetons CSS)** :
  - fonds : `--panel #16171a`, `--card #1b1c20` ;
  - texte : `--fg #eceef1`, `--muted #8e939c` ;
  - accent : `--accent #1e90e8` / `--accent-hi #5bb3f5` ;
  - états : `--ok #3fb950`, `--warn #e3b341`, `--danger #e5484d` ;
  - postes : Négociateur violet `#a78bfa`, Surveillance otages ambre `#e3b341`, Braquage rouge `#f0625f`, Polyvalent turquoise `#3fc1a6`, Conducteur rose `#f472b6` ;
  - grades : Patron or, Co-Patron orange, Exécutant bleu, Lieutenant turquoise, Membre gris.
- **Boutons** : rectangles aux coins arrondis de 4 px. Le bouton principal est un dégradé bleu vertical (comme « Commencer » dans le jeu). Il existe aussi des variantes grise, rouge (danger) et verte (validation).
- **Cartes de mission** : une bannière illustrée (dégradé teinté selon la mission, motif de hachures, grande icône au trait, nom de la mission en filigrane), une pastille de temps de recharge en haut à droite (« 24h »), puis une ligne avec le nom en majuscules, 4 statistiques en grille 2×2 (Joueurs, Gain XP, Durée, Niveau) et le bouton. Une mission verrouillée passe en noir et blanc avec « Niveau X requis ».
- **Inventaire** : il imite l'inventaire du jeu. Tuiles carrées avec poids en haut à gauche, « x1 000 » en haut à droite, icône SVG colorée au centre, nom en bas et barre bleue (rouge sous le minimum). Au-dessus : onglets de catégories (le bleu = actif), barre de recherche et poids total « 97.8 / 1 500 kg ».
- **Fenêtres (assistants)** : superposition floutée, en-tête illustré, étapes numérotées en onglets, pied de page avec Précédent / Suivant.
- **Composants récurrents** : pastilles d'état (`st-pill` ok / wait / fail / info), puces de personnes (`pc`, pastille de couleur selon le poste), compteurs (`kpi`), classements à barres (`lb`), bandeaux d'avertissement jaunes, notifications (`toast`) en bas.
- **Responsive** : sous 900 px, la sidebar passe au-dessus et le menu défile horizontalement. Les colonnes s'empilent. Aucun défilement horizontal de la page.
- **Confirmations dans la page** (pas de `confirm()`) : le bouton se transforme en « Confirmer / Annuler ».

---

## 4. Connexion et sécurité (important)

Il n'y a **pas de Firebase Auth**. Le système est maison :

1. **Identifiant = nom dans le crew** (ex. « Le R »), plus un **mot de passe**.
2. Le navigateur calcule une **clé secrète** : `PBKDF2-SHA256(motDePasse, sel = "tablette-crew:" + slug(nom), 120 000 itérations)`, préfixée par `k_`. Le mot de passe ne quitte jamais l'appareil.
3. `keys/<clé>` (collection racine, **lisible seulement si on connaît la clé**, impossible à lister) contient `{crew, memberId, kv, vault?}`. `crew` est l'identifiant secret du crew. Toutes les données sont sous `crews/<crew>/…`.
4. **Chaque écriture** est un batch : une **preuve à usage unique** `crews/<crew>/proofs/<id> = {k: clé}` (illisible par tous), plus la donnée avec le champ `_p: <id>`. Les règles vérifient que la preuve est créée dans le même batch (`!exists` avant, `existsAfter` après), retrouvent la clé, puis le membre et son grade. La donnée ne contient donc jamais la clé d'un autre membre, et une preuve vue dans les données ne peut pas être réutilisée.
5. **Versions de clé (`kv`)** : la fiche membre porte `kv`. Une clé n'est valable que si `key.kv == member.kv`. Changer ou réinitialiser un mot de passe incrémente `kv`, ce qui désactive l'ancienne clé immédiatement.
6. **Codes d'accès (admin seulement)** : les mots de passe de départ générés au hasard (`XXXX-XXXX-XXXX`) sont rangés sous `crews/<crew>/v/<secretVault>/codes/<memberId>`. Le chemin secret est stocké dans la clé de l'admin.
7. Anciens systèmes encore pris en charge :
   - **anciens codes** `XXXX-XXXX-XXXX` saisis dans le champ mot de passe ;
   - **mise à jour de sécurité** : quand l'admin se connecte et que `crew/v3` n'existe pas, le site régénère les mots de passe de tout le monde ;
   - lien `#migration` pour un crew jamais sécurisé.
8. **Limite connue** : quelqu'un qui récupère l'identifiant du crew (dans le navigateur d'un membre) peut **lire** les données, mais pas les modifier.
9. **Journal obligatoire** : chaque batch écrit aussi `crews/<crew>/logs/<id de la preuve>` = `{at, byId, by, items[{s, t, p, d[{f, a, b}], minor}], more, minor}`. La règle `lvl()` exige `existsAfter` de cette ligne : aucune écriture authentifiée ne peut échapper au journal, et les lignes ne sont jamais modifiables ni effaçables. Les mots de passe, clés, chemin du coffre à codes et photos n'y sont jamais recopiés. Si les anciennes règles refusent la ligne, le site réessaie une fois sans journal (transition).
10. **Fondateur** : `crew/meta.admin` est l'admin d'origine. Aucun autre admin ne peut modifier sa fiche ni lui créer une clé.
11. **Rien n'est jamais supprimé** : on marque `_del: true`, `removed: true` ou `status: 'none'`. Les règles interdisent les suppressions définitives.
12. Champs hérités à tolérer : `_k` (ancien code de l'auteur, retiré à chaque réécriture).

### Droits par grade (appliqués dans les règles Firestore et dans l'interface)
Les règles calculent un niveau : 0 = aucun, 1 = Membre/Lieutenant, 2 = Exécutant, 3 = Patron/Co-Patron, 4 = admin.

| Niveau | Peut faire |
|---|---|
| Membre / Lieutenant | Consulter ; s'inscrire à une formation ; cocher la roue du coffre quand c'est son tour ; déposer et retirer dans les coffres ; enregistrer récoltes et ventes ; compléter les fiches de renseignement ; changer son mot de passe |
| Exécutant (élite) | En plus : lancer les OP, tirer la roue, gérer les formations, **sanctionner, changer le grade (Lieutenant ↔ Membre) et virer les Lieutenants et Membres**, gérer les objets du coffre, corriger ou supprimer récoltes et ventes, supprimer une fiche de renseignement |
| Patron / Co-Patron | Tout sauf les codes d'accès : ajouter des membres, tous les grades, paramètres, annuler une sanction, supprimer un bilan, désigner les formateurs |
| Admin | Tout, y compris les codes d'accès, la réinitialisation des mots de passe, les **Logs** et la nomination d'autres admins (le fondateur reste protégé) |

Un **formateur désigné** (même Membre) peut accepter, refuser et valider les demandes de **sa** formation, et seulement celle-là.

---

## 5. Les pages

### Missions
- **15 missions** : 3 opérations du crew (**Attaque de territoire**, **Attaque de laboratoire**, **PO sur Orga** : pas de niveau ni de temps de recharge ; pas d'argent pour les attaques, argent **facultatif** pour la PO sur Orga) et 12 missions de la tablette, de **Gruppe6** (niveau 1, 2-7 joueurs, +2 500 XP, recharge 24 h) à **Petit bunker** (niveau 45, 7-23 joueurs, +27 500 XP, recharge 168 h). Les autres sont Bateau de trafiquant, Flecca, Pine, Bijouterie, Roxwood, Paleto, Braquage de train, Yacht, Pacific Standard, Humane Labs et Plateforme pétrolière.
- Les missions au-dessus du **niveau du crew** (réglable, 22 par défaut) sont verrouillées. Une mission faite récemment affiche « En recharge · encore X h ».
- **« Commencer »** (Exécutant et plus) ouvre un assistant en 4 étapes :
  1. **Équipe** : on coche les membres présents (regroupés par grade, avec leurs badges de formation) et on peut ajouter des renforts extérieurs. Le site prévient si le nombre est hors de la fourchette de la mission.
  2. **Postes** : Négociateur, Surveillance otages, Braquage ou Polyvalent. Le bouton **Auto** répartit au hasard selon des proportions (≥ 2 joueurs : 1 négociateur, 2 à partir de 10 ; ≥ 3 : 25 % surveillance ; ≥ 6 : 15 % polyvalent ; le reste au braquage). Une option donne la priorité aux membres formés.
  3. **Véhicules** : on choisit le nombre de véhicules, et 4 ou 5 places pour chacun. Un schéma de voiture vue de dessus montre les sièges (Conducteur, Passager, Arrière G, M, D). On place les gens en touchant un nom puis une place, ou par glisser-déposer. **Auto** place tout le monde au hasard (formés « Conducteur » au volant en priorité) et ajoute des véhicules s'il manque des places.
  4. **Bilan** : Réussie ou Échouée et total gagné (obligatoire pour les braquages, facultatif pour la PO sur Orga, absent pour les attaques ; `payMode(mission)` = req / opt / none), part par personne, organisation visée (pour une PO), puis le **message Discord** avec un bouton Copier.
- Pendant l'OP, elle apparaît **« OP en cours »** chez tout le monde, avec le poste et la place de chacun. Elle est sauvegardée en continu, un Exécutant peut la reprendre, et on peut l'annuler.
- Format du message Discord :
```
# 💵 Braquage de train

Détail : Brandon, Mathias, Le R
Total gagné = 1.733.650 $

Postes :
🗣️ Négociateur : Le R
```
  (🏴 territoire, 🧪 labo, 🎯 PO, ❌ si échouée ; montants avec des points tous les 3 chiffres ; « Organisation visée : X » pour une PO.)

### Bilan d'activité
- Historique par semaine, mois ou depuis le début : missions, **récoltes et ventes** de labo.
- Compteurs : total gagné (missions + ventes), OP réussies, membres actifs, meilleure OP.
- Classement de participation : nombre d'activités et gains estimés.
- **Admins** : bouton « Modifier » sur chaque bilan d'OP (mission, résultat, date et heure, total, organisation visée, participants et postes, ajout d'invités). Le bilan affiche « modifié par X » et la modification apparaît dans les Logs. Les récoltes et ventes se corrigent avec « Corriger » (Exécutants et plus).
- Sur chaque entrée : « Copier pour Discord » ; la suppression est réservée au Patron et au Co-Patron.

### Coffre (villa), 2 onglets
- **Inventaire** (façon jeu) :
  - catégories (Armes, Munitions, Nourriture, Vêtements, Labo, Divers), recherche, poids total sur 2 500 kg ;
  - on touche un objet pour **Déposer** ou **Retirer** une quantité (raccourcis 1 / 10 / 100), avec un incrément atomique Firestore ;
  - **journal** des mouvements (qui, quoi, quand) ;
  - les Exécutants modifient le nom, la catégorie, l'icône, le poids, la quantité exacte et le **minimum** (rouge en dessous) ;
  - bouton d'import du contenu initial ;
  - 14 icônes SVG maison choisies automatiquement d'après le nom (munitions, billets, pistolet, fusil, nourriture, gilet, tenue, masque, jerrycan, outil, produit chimique, pochon…).
- **Roue de la semaine** :
  - une roue (canvas, animation de 5 s) tire au sort le membre qui doit remplir le coffre cette semaine (semaine ISO) ;
  - il doit déposer 100 menus et 100 balles de chaque type ; la liste est réglable dans Paramètres ;
  - il coche au fur et à mesure ;
  - la personne tirée la semaine précédente est exclue automatiquement, et l'on peut exclure d'autres membres ;
  - historique « Rempli / Incomplet ».

### Laboratoires, 2 onglets
- **Récoltes et ventes**, en assistants comme les missions :
  - **Récolte** (2 étapes) : présents, puis produit (Fentanyl) et nombre de pochons créés. Message :
    `# 🧪 Récolte de fentanyl / Détail : … / Pochons créés = 44`. Les pochons vont dans le **stock à vendre**.
  - **Vente** (3 étapes) :
    1. on choisit dans le stock les récoltes vendues, ce qui identifie leurs **créateurs** ;
    2. on coche les vendeurs ;
    3. les pochons sont répartis automatiquement entre vendeurs (modifiable) et **chaque vendeur saisit son argent**.
    Message : `# 💵 Vente de fentanyl / Détail : Brandon / Créés par : Le R / Pochons vendus = 44 / (une ligne par vendeur s'ils sont plusieurs) / Total gagné = 120.000 $`.
  - Compteurs (stock, créés, vendus, argent), classements Création et Ventes.
  - Les Exécutants peuvent **corriger** (l'assistant se rouvre prérempli) ou supprimer. Une récolte déjà en partie vendue ne peut pas être supprimée.
- **Coffre du labo** : le même inventaire, séparé (champ `place: 'lab:<id>'`, ex. coffre « Roxwood » de 1 500 kg avec acide, catalyseur, base opioïde, solvant, pochons, méthylamine…).

### Renseignements
- Une **fiche par organisation adverse**, triée par menace :
  - nom, couleur et signes distinctifs (plus une teinte), revendication, **fréquence radio**, menace (faible, moyenne, haute) ;
  - **villa** : capture de la carte avec le point, plus l'adresse ;
  - **patron** : nom, infos, photo de sa carte d'identité ;
  - **membres connus** : nom, rôle, radio, infos, photo de la carte d'identité ;
  - **journal des infos** avec la source ;
  - historique des modifications.
- **Photos** : compressées en JPEG (< 700 Ko) dans un canvas, puis stockées en base64 dans la collection `intelimg` (chargées seulement à l'ouverture de la fiche). Envoi par fichier, glisser-déposer ou **Ctrl+V** après « Coller une capture ». Une visionneuse agrandit les photos.
- Une **PO sur Orga** enregistrée avec une organisation visée crée ou complète automatiquement la fiche.

### Formations
- **4 formations** : Négociateur, Surveillance des otages, Comment braquer, Conducteur. Chacune a une description et un programme.
- **Parcours** : le membre **demande** (il peut choisir son formateur), puis un **formateur désigné accepte** (en indiquant le moment de la session) ou **refuse** (avec une raison visible), puis **« Formé »**, **« À refaire »** ou **« Me désister »**.
- Panneaux : « Mes formations » (barre Demande → Session → Formé), « À former » pour les formateurs, « Meilleurs formateurs », « Désigner les formateurs » (Patron, Co-Patron, admin), et un tableau membres × formations que les Exécutants peuvent forcer.
- Les badges de formation servent aux boutons Auto des missions.

### Membres
- Tableau par grade : grade (modifiable selon les droits), formations, nombre d'OP, nombre de fois tiré à la roue, avertissements (x/2), bouton pour virer avec confirmation.
- Ajout de membres (Patron, Co-Patron, admin).

### Sanctions
- **Rappel à l'ordre** (ne compte pas) ou **Avertissement**, toujours avec une raison. **Au 2e avertissement, le membre est viré automatiquement** (après une confirmation).
- Les Exécutants sanctionnent les Lieutenants et les Membres ; le Patron et le Co-Patron sanctionnent aussi les Exécutants ; le Patron et le Co-Patron peuvent annuler une sanction.
- Historique et « Avertissements en cours » (deux pastilles rouges).

### Paramètres
- Nom et niveau du crew, contenu de la roue du coffre.
- **Mon profil** : identifiant, **changement de mot de passe** (8 caractères minimum), déconnexion.
- Rappel de qui peut faire quoi.

### Codes d'accès (admin seulement)
- Pour chaque membre : son mot de passe de départ (masqué, avec « Afficher »), le bouton **Copier** (message prêt à envoyer en privé : lien `#id=Nom`, identifiant, mot de passe), **Réinitialiser**, et « Créer les mots de passe manquants ».
- La mention « Changé par le membre » s'affiche quand il l'a modifié.

### Logs (admins seulement), 2 onglets
- **Journal** : toutes les modifications, groupées par jour (heure, auteur, partie du site, phrase lisible comme « Le J : grade Membre → Lieutenant » ou « Coffre de la villa : -3 Menus »). On déplie une ligne pour voir le détail avant → après. Filtres par auteur et par partie, recherche, case « Sauvegardes auto » (sauvegardes de l'OP en cours et historique du coffre, masquées par défaut), bouton « Voir les plus anciennes » (par 150).
- **Admins** : liste des admins (badge fondateur), « Nommer admin » (nouveau mot de passe généré avec l'accès aux codes, affiché pour être envoyé), « Retirer admin ». On ne peut pas changer son propre statut.

### Bientôt
Paies.

---

## 6. Modèle de données (sous `crews/<crew>/`)

| Chemin | Contenu |
|---|---|
| `crew/config` | `crewName, crewLevel, coffreItems[], coffreExclude[], coffreExcludeLast, trainers{formation: [memberId]}, labSites[], members[]` (ancien) |
| `crew/meta`, `crew/v3` | marqueurs de sécurisation |
| `members/<id>` | `name, grade, admin, removed, kv, pwChanged` |
| `ops/<id>` | bilans de mission (`missionId, missionName, result, total, participants[{name, poste}], vehicles, targetOrg, startedAt, endedAt, by`) |
| `live/<id>` | OP en cours (état de l'assistant) |
| `coffre/<AAAA-Wss>` | tirage de la roue (`memberId, member, items[{label, qty, done}]`) |
| `inv/<id>` | objets des coffres (`name, cat, qty, w, min, icon, place, at, by, byId`) |
| `invlog/<id>` | journal des mouvements (`delta, item, place, by`) |
| `labs/<id>` | `kind: 'recolte'` (`people, pochons, product`) ou `'vente'` (`sources[{id, qty, people}], creators, sellers[{name, pochons, money}], pochons, total`) ; champs `recipe` et `lastById` obligatoires |
| `formations/<memberId>__<formation>` | `status (inscrit, accepte, forme, none), wantId, trainerId, trainer, session, refusals[], validatedBy, history[]` |
| `sanctions/<id>` | `memberId, member, type (rappel, avert), reason, by, byId, cancelled` |
| `intel/<id>` | fiches de renseignement (`name, color, colorHex, claims, radio, threat, villa{desc, imgId}, boss{name, notes, imgId}, people[], notes[], log[], lastById`) |
| `intelimg/<id>` | `{data: dataURL JPEG, orgId, byId}` |
| `logs/<id de preuve>` | journal des modifications (lu par la page Logs des admins) |
| `proofs/<id>` | preuves d'écriture (illisibles) |
| `v/<secret>/codes/<memberId>` | mots de passe de départ (admin) |

Hors crew : `keys/<clé>`.

---

## 7. Règles à respecter pour toute modification

1. Garder **un seul `index.html`** en JavaScript vanilla, et le style existant (jetons CSS, composants, ton français).
2. **Toute écriture passe par `Store.write / Store.batch / Store.incr`**, qui ajoutent la preuve `_p` et la ligne de journal (décrite par `logItem` : ajouter un cas pour toute nouvelle collection). Ne jamais écrire directement dans Firestore.
3. **Toute nouvelle collection ou tout nouveau champ contrôlé** demande une mise à jour de `firestore.rules`, puis le rappel au propriétaire de **recoller les règles**. Garder les fonctions de règles économes (une limite de 1 000 expressions par règle oblige à calculer `lvl(c)` une seule fois par fonction).
4. Pas de suppressions définitives : marquer `_del`, `removed` ou `status: 'none'`.
5. Vérifier les droits **dans l'interface ET dans les règles**.
6. Tout doit marcher sur **téléphone** (largeur 390 px, sans défilement horizontal).
7. Tester avec l'émulateur Firestore (règles et parcours Playwright) avant de pousser sur `main`. GitHub Pages met environ 1 minute à se mettre à jour, et les utilisateurs doivent parfois faire **Ctrl + F5**.
8. Les messages Discord suivent le format du crew : titre `# <emoji> Nom`, ligne vide, `Détail : noms`, puis les totaux. Les montants s'écrivent avec des points (`1.733.650 $`).
