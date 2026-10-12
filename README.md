# Café Comptoir

Le tribunal du café de l'équipe : qui boit, qui achète, qui nettoie Gérard la machine.
XP, niveaux, badges, roue de la corvée, mur de la honte et mini-jeux.

Une seule page (`index.html`), hébergée sur GitHub Pages. Les données sont partagées en direct entre tous les collègues grâce à Firebase, dans sa formule gratuite. Personne n'a besoin de compte : il suffit d'ouvrir le lien.

Tant que Firebase n'est pas configuré, la page tourne en **mode démo**, avec des collègues d'exemple.

---

## 1. Créer la base Firebase (10 minutes, gratuit)

1. Allez sur <https://console.firebase.google.com> et connectez-vous avec un compte Google.
2. **Créer un projet**, par exemple `cafe-comptoir`. Google Analytics n'est pas nécessaire.
3. Menu **Créer** → **Firestore Database** → **Créer une base de données**.
   - Emplacement : `eur3 (Europe)` ou `europe-west9 (Paris)`.
   - Mode : **production**.
4. Onglet **Règles** : remplacez tout le contenu par celui du fichier `firestore.rules`, puis cliquez sur **Publier**.
5. Menu **Créer** → **Authentication** → **Commencer** → onglet **Méthode de connexion** → **Anonyme** → **Activer** → **Enregistrer**.
6. Roue dentée → **Paramètres du projet** → section **Vos applications** → icône **Web `</>`**. Donnez un nom, cliquez sur **Enregistrer l'application**, puis copiez les valeurs affichées dans `firebaseConfig`.

## 2. Régler la page

Ouvrez `index.html` et cherchez le bloc **RÉGLAGES** en haut du script :

```js
const FIREBASE_CONFIG = {
  apiKey: "AIza…",
  authDomain: "cafe-comptoir.firebaseapp.com",
  projectId: "cafe-comptoir",
  appId: "1:123…:web:abc…"
};
const TEAM = "comptoir-a-changer";
```

- Collez les quatre valeurs copiées à l'étape 1.6. Les autres champs de Firebase sont inutiles.
- Remplacez `TEAM` par un nom difficile à deviner, par exemple `kawa-3e-etage-7f2k`. Plusieurs équipes peuvent partager le même projet Firebase avec des noms différents.

La clé `apiKey` n'est pas un secret : chez Firebase, elle est faite pour être publiée dans la page. La protection vient des règles de l'étape 1.4.

## 3. Publier sur GitHub Pages

1. Sur <https://github.com>, créez un dépôt, par exemple `cafe-comptoir`.
2. **Add file** → **Upload files** → déposez `index.html` (et ce README si vous voulez) → **Commit changes**.
3. **Settings** → **Pages** → Source : **Deploy from a branch**, branche `main`, dossier `/ (root)` → **Save**.
4. Après une minute, la page est en ligne à l'adresse `https://<votre-compte>.github.io/cafe-comptoir/`.
5. Facultatif : dans Firebase, **Authentication** → **Paramètres** → **Domaines autorisés** → ajoutez `<votre-compte>.github.io`.

Envoyez le lien à l'équipe. Chacun clique sur **+ Nouveau** pour s'inscrire, puis choisit son prénom dans « Qui es-tu ? ».

---

## Comment c'est stocké

Toutes les données sont rangées sous `equipes/<TEAM>` dans Firestore :

| Collection | Contenu |
|---|---|
| `buveurs` | Une fiche par collègue : prénom, couleur, photo réduite (environ 15 Ko), totaux (tasses, grammes achetés, nettoyages, détartrages, XP). |
| `evenements` | Une ligne par action (café, achat, nettoyage, détartrage). Elle alimente le journal et les badges. |
| `jeux` | Les grains jetés sur le mur de la honte et les records des mini-jeux. |
| document `equipes/<TEAM>` | La date du dernier nettoyage et du dernier détartrage, pour la santé de Gérard. |

- **Temps réel** : chaque action apparaît en une à deux secondes sur tous les écrans ouverts.
- **Hors ligne** : les actions sont gardées puis envoyées au retour du réseau.
- **Historique** : la page charge les 60 derniers jours d'événements (réglage `HISTORY_DAYS`). Les totaux de chacun, eux, sont permanents.
- **Quotas gratuits** : 50 000 lectures et 20 000 écritures par jour, très largement suffisant pour une équipe.

## À savoir

- La page est publique : toute personne qui a le lien **et** connaît le nom d'équipe peut voir et modifier le comptoir. C'est adapté à un usage café entre collègues, pas à des données sensibles.
- Chacun peut déclarer pour un autre en choisissant son prénom. C'est pratique pour les oublis, et ça repose sur la confiance… et sur Gérard.
- Pour remettre le comptoir à zéro : dans la console Firestore, supprimez le document `equipes/<TEAM>` et ses collections, ou changez simplement `TEAM`.
