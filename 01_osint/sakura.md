# TryHackMe — Sakura

---

## Vue d’ensemble

**But de la room.** Reconstituer l’identité et les traces d’un « attaquant » à partir d’indices laissés en ligne (image, dépôts publics, réseaux sociaux, traces crypto, etc.). La progression se fait par petites hypothèses vérifiables : métadonnées d’image, recherches par identifiant, historiques Git, et pivots vers des plateformes spécialisées (explorateurs blockchain, cartographie Wi‑Fi, etc.).

**Outils clés utilisés.**\
`exiftool` (métadonnées), inspection de SVG (code source), moteurs de recherche, dépôts GitHub, `gpg` (lecture de clé PGP), explorateur blockchain (Etherscan), Tor + DeepPaste, WiGLE (cartographie Wi‑Fi), recherche d’images (Google Lens/Yandex), Google Maps/Street View, Wayback Machine.

---

## 1) TIP‑OFF — partir d’une **image** (SVG)

**Idée.** Un SVG est du texte. On l’ouvre dans un éditeur / les DevTools (F12) et on lit le code ou on passe `exiftool` pour extraire les métadonnées.

**Résultat.** Le chemin d’export dans les métadonnées révèle un **nom d’utilisateur** dans le répertoire `/home/<username>/…`.

- **Username trouvé :** `SakuraSnowAngelAiko`\
  **Comment :**
  ```bash
  exiftool sakurapwnedletter.svg
  ```
  Le champ `Export-filename` contenait un chemin du type `/home/SakuraSnowAngelAiko/Desktop/...`. (Même résultat si on lit directement le SVG.)

---

## 2) RECON — recouper **identité** et **e‑mail**

Avec un identifiant unique en main, j’ai lancé une recherche et visité les profils pertinents (ici **GitHub**). On y trouve un dépôt **PGP** avec une **clé publique**.

- **E‑mail extrait de la clé PGP :** `SakuraSnowAngel83@protonmail.com`\
  **Comment :** récupération de la clé publique depuis le repo PGP, import/parse via `gpg`, lecture du bloc `uid`.

- **Nom réel :** *Aiko Abe*\
  **Comment :** la recherche de l’alias renvoie vers des profils (LinkedIn/Twitter) exposant le nom complet.

---

## 3) UNVEIL — **traces crypto** (wallet, pool, actifs)

En parcourant les repos GitHub (au‑delà des dépôts « épinglés »), un repo `` contient dans l’**historique des commits** une ligne de configuration de miner (stratum) avec une **adresse Ethereum**.

- **Adresse crypto :** `0xa102397***B53abB6ef`\
  **Comment :** commit « Update miningscript » dans le repo `ETH`.

- **Vérifications & détails de paiements :** sur **Etherscan**, l’adresse montre des paiements du **pool Ethermine** (notamment le **23 janvier 2021** UTC) ainsi que des mouvements **Tether** (USDT) en plus d’Ethereum.\
  **Pool :** *Ethermine* — **Actifs :** *Ethereum* (+ **Tether**).

---

## 4) TAUNT — **Twitter** & indices « cachés »

Un compte Twitter est rattaché à l’alias.

- **Handle actuel :** `@Sakura***Aiko`\
  **Comment :** repéré via recherche par identifiant.

Dans les tweets, deux mots en MAJUSCULES (« **DEEP** », « **PASTE** ») orientent vers un service **.onion** nommé **DeepPaste**, où se trouve un **lien** (avec un *md5 id*) vers une page listant des **SSIDs & mots de passe Wi‑Fi** (preuve de concept OSINT, *sans* connexion active).

- **URL .onion (DeepPaste) contenant les SSID/mots de passe :** page `show.php?md5=0a5c6e136a98a60b8a21643ce8c15a74` sur le domaine DeepPaste v3.\
  **Comment :** résolution de l’adresse v3 via des annuaires .onion, puis ouverture via Tor en injectant l’id MD5 repéré dans les tweets.

- **BSSID du Wi‑Fi « Home » :** `84:AF:EC:34:FC:F8`\
  **Comment :** à partir d’un **SSID** listé (ex. « DK1F‑G »), utilisation de **WiGLE** (recherche avancée) pour récupérer le **BSSID** (identifiant MAC du point d’accès).

---

## 5) HOMEBOUND — **géolocalisation** & itinéraires

Plusieurs publications (photo, carte en vol, etc.) permettent de recouper des lieux :

- **Aéroport le plus proche du spot photo « pré‑vol » :** **DCA** (Ronald Reagan Washington National Airport).\
  **Comment :** la photo montre au loin le **Washington Monument** et le **Capitol** → DCA est l’aéroport commercial le plus proche (code **DCA**).

- **Dernière escale (layover) :** **HND** (Tokyo–Haneda).\
  **Comment :** recherche d’images/résultats pour la photo de lounge → correspond à **Haneda**.

- **Lac visible sur la carte en vol :** **Lake Inawashiro**.\
  **Comment :** concordance de la carte partagée (îles, tracé) avec le **lac Inawashiro** sur Google Maps.

- **Ville « home » la plus probable :** **Hirosaki**.\
  **Comment :** cohérence entre SSID « City Free Wi‑Fi » listé (entrée `HIROSAKI_Free_Wi‑Fi` / recherche WiGLE) et trajectoires évoquées sur Twitter.

---

## 7) Notes de méthode

- **Images (SVG/JPG/PNG)** : toujours vérifier **EXIF** (`exiftool`) *et* le **contenu** des formats texte (SVG, PDF). De simples champs « chemin de fichier » ou « titre de doc » révèlent des **usernames**.
- **Identifiants uniques** : pivoter vers GitHub/Twitter/LinkedIn/Reddit et chercher des dépôts, clés PGP, historiques, *commits* qui « parlent trop ».
- **Crypto** : les **blockchains** sont publiques → une adresse trouvée (via un repo) s’**audite** (Etherscan) : **dates, pools, tokens**.
- **Dark web OSINT** : rester en **lecture seule** (Tor), identifier des **pastes** liés à des indices (hash/id).
- **Wi‑Fi** : **WiGLE** (compte gratuit) pour lier **SSID** ↔ **BSSID** ↔ **zone**.
- **IMINT/Géoloc** : motifs urbains (monuments), **reverse image search**, puis **Google Maps/Street View** pour confirmer.

---

***Auteur*** *: Saad Idrissi*
