# TryHackMe — OhSINT

![Image du challenge](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*SekVhAaN0ZLISUlsuo4ogw.jpeg)

## 1. 📝 Introduction  
Ce challenge TryHackMe a pour objectif d’extraire, à partir d’une simple photo, un ensemble d’informations : avatar, localisation, email, wifi, blog, mot de passe… Ce write‑up détaille ma méthode étape par étape.

---

## 2. 🔍 Extraction des métadonnées  
Avec **ExifTool** , j’ai récupéré deux données clés :  
- **Copyright** : `OWoodflint`  
- **GPS** : 54° 17′ 41.27″ N, 2° 15′ 1.33″ W

> 💡 Conseil : Ne jamais négliger les métadonnées, elles sont comme des miettes laissées sur le clavier.

---

## 3. 🐱 Avatar utilisateur  
Recherche “OWoodflint” → profil **X (Twitter)** trouvé, avec un **avatar de chat**.

> 🐾 Un chat ? Voilà qui aiguise la curiosité. Flag validé !

---

## 4. 📍 Ville de résidence  
Sur Twitter, publication du **BSSID** `B4:5D:50:AA:86:41` → géolocalisation via **Wigle.net** → localisé à Londres → confirme la ville.

> 🌍 La géolocalisation Wi-Fi, c’est un peu comme du GPS, sans les satellites.

---

## 5. 📶 SSID du réseau Wi‑Fi  
Même interface Wigle.net → le nom du réseau est **UnileverWiFi**.

---

## 6. ✉️ Email personnel  
Recherche du pseudo → **GitHub** trouvé → dans le README : `owoodflint@gmail.com`.

> 🧠 GitHub est souvent plus bavard qu'on ne le croit.

---

## 7. 🌐 Site de découverte de l’email  
Le mail a été récupéré via **GitHub**.

---

## 8. 🗽 Destination des vacances  
Le blog WordPress `oliverwoodflint.wordpress.com` mentionne un voyage à **New York**.

---

## 9. 🔐 Mot de passe caché  
Inspection du code source du blog révèle :  
```html
<p style="color:#ffffff;">pennYDr0pper.!</p>
```  
C’est la **clé du challenge**.

> Comme quoi, inspecter le code source est parfois plus utile que lire la page.

---

## 10. 📊 Résumé des flags  

| Flag (question)                                   | Réponse                              |
|---------------------------------------------------|--------------------------------------|
| Avatar utilisateur                                | Chat                                 |
| Ville                                             | Londres                              |
| SSID du WAP                                       | UnileverWiFi                         |
| Email personnel                                   | owoodflint@gmail.com                 |
| Site où trouvé l’email                            | GitHub                               |
| Destination des vacances                          | New York                             |
| Mot de passe                                      | pennYDr0pper.!                       |

---

## 11. 🧠 Méthodologie retenue  
1. Analyse EXIF (ExifTool ou en ligne)  
2. Recherche du pseudo pour trouver profils sociaux  
3. Vérification de l'avatar (X)  
4. Géolocalisation du BSSID via Wigle  
5. Recherche d’email sur GitHub  
6. Lecture du blog (localisation + mot de passe caché)  
7. Inspection du code source pour le mot de passe dissimulé

---

## 12. ✅ Conseils & bonnes pratiques  
- Toujours **nettoyer les métadonnées** avant de publier une image (EXIF, GPS, auteur…)  
- La combinaison de profils sociaux, wifi, et blogs peut révéler des informations sensibles  
- L’inspection HTML peut dévoiler du contenu masqué (texte blanc, steganographie…)

> 🧼 Une photo propre, c’est une OPSEC propre.

---

***Auteur*** *: Saad Idrissi*
