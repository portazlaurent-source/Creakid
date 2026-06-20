# Passation du projet CRÉA-KID

## 1. À lire en premier

Bienvenue ! Tu reprends le site web de CRÉA-KID, un centre d'activités pour enfants à Saint-Maximin-la-Sainte-Baume. Le site est terminé dans ses grandes lignes (toutes les pages, le design, les fonctionnalités) mais quelques informations restent à récupérer auprès de Marine, la gérante, et la mise en ligne définitive (hébergeur + nom de domaine) reste à faire. Ce document explique tout ce qu'il faut savoir pour prendre le relais sereinement.

Tout le développement de ce site a été fait avec **Claude Code** (l'outil en ligne de commande d'Anthropic). Il est fortement recommandé de continuer avec cet outil : il connaît bien ce type de projet, peut lire et modifier le code en suivant des instructions en français, et évite d'avoir à toucher au code à la main si tu n'es pas développeur. Voir la [section 10](#10-workflow-de-modification-au-quotidien) pour le workflow type.

**Important : les comptes Netlify, Formspree et Trustindex utilisés jusqu'ici ont été fermés.** Le site fonctionnait avec ces trois services, mais il faudra recréer un compte sur chacun avec ta propre adresse email. Les sections 6, 7 et 8 expliquent comment faire, étape par étape.

## 2. Contexte projet

- **Pour qui :** Marine, gérante de CRÉA-KID, un centre d'activités pour enfants de 0 à 6 ans à Saint-Maximin-la-Sainte-Baume (83).
- **Objectif du site :** une vitrine en ligne du lieu — présentation du concept, tarifs, planning des ateliers, informations pratiques, et un moyen de contacter Marine.
- **Décisions principales déjà prises :**
  - Nom de domaine cible : **creakid.fr** (pas encore acheté, voir section 9).
  - Le contact se fait via un **formulaire** (pas d'adresse email affichée en clair, pour limiter le spam).
  - Les avis clients sont affichés via un **widget tiers** (Trustindex), connecté à la fiche Google Business de CRÉA-KID.
  - Le planning est **hybride** : une image/embed Canva pour le planning hebdomadaire des ateliers (modifiable facilement par Marine elle-même chaque semaine), et un Google Calendar embed pour les événements ponctuels (anniversaires, fermetures, journées spéciales).

## 3. Stack technique

- **HTML / CSS / JavaScript pur**, site statique. Pas de framework (pas de React/Vue), pas d'étape de build, pas de serveur backend.
- **Polices Google Fonts :**
  - Fredoka — titres
  - Caveat — texte cursif / accents décoratifs
  - Nunito — corps de texte
- **Palette de couleurs :**
  - Pêche `#F5A56C`
  - Corail `#F5B5B0`
  - Menthe `#9FE0B7`
  - Or `#F8CC7A`
  - Crème `#FBF6EE`
- Les composants CSS réutilisables (boutons, cartes, carrousel, etc.) sont regroupés dans [style.css](style.css), avec des commentaires de section pour s'y retrouver.
- Un dossier `node_modules/` est présent localement (package `sharp`, utilisé une fois pour convertir des photos en WebP) mais il n'y a **aucun `package.json`** et **aucune étape de build** : ce dossier n'est pas nécessaire au fonctionnement du site et peut être ignoré ou supprimé sans risque. Il est de toute façon exclu du dépôt Git (`.gitignore`).

## 4. Structure du projet

### Pages HTML (à la racine)

| Fichier | Contenu |
|---|---|
| [index.html](index.html) | Page d'accueil : hero, présentation rapide du lieu, planning de la semaine en aperçu (généré depuis `data/activites.json`), carrousel de photos, section avis Google (widget Trustindex), appels à l'action. |
| [concept.html](concept.html) | Page "Le concept" : présentation détaillée du lieu et de sa philosophie, carrousel de photos. |
| [tarifs.html](tarifs.html) | Grille tarifaire des ateliers et formules d'abonnement. |
| [planning.html](planning.html) | Planning complet : embed Canva pour le planning hebdomadaire des ateliers + embed Google Calendar pour les événements mensuels/ponctuels. |
| [contact.html](contact.html) | Formulaire de contact (branché à Formspree), coordonnées, lien Google Maps. |
| [mentions-legales.html](mentions-legales.html) | Mentions légales et politique de confidentialité (RGPD). Plusieurs champs sont encore marqués `[À COMPLÉTER]` — voir section 12. |
| [404.html](404.html) | Page d'erreur personnalisée affichée quand une URL n'existe pas (gérée via `_redirects` sur Netlify). |

### Dossiers importants

- **`assets/`** — Images du site : logo, photos des espaces (au format WebP avec fallback JPG/JPEG via la balise `<picture>`), icônes.
- **`data/activites.json`** — Le planning affiché en aperçu sur la page d'accueil. C'est un fichier de données pur (pas de code) : on peut le modifier directement pour changer les activités de la semaine sans toucher au HTML. Le fichier contient lui-même un champ `"_aide"` qui explique comment l'éditer (quelle clé pour quel champ, quelles couleurs disponibles).
- **`js/carousel.js`** — Le script JavaScript du carrousel photos, réutilisé sur plusieurs pages (accueil, concept).
- **`partials/header.html` et `partials/footer.html`** — ⚠️ Ce sont des modèles de référence pour le header et le footer, mais ils **ne sont pas inclus dynamiquement** dans les pages : chaque fichier HTML a sa propre copie du header/footer collée directement dedans. Si tu modifies le menu de navigation ou le footer, il faut répercuter le changement **sur chaque page HTML individuellement** (ou demander à Claude Code de le faire partout en une fois).
- **`scraps/` et `uploads/`** — Dossiers de travail (captures d'écran de maquettes Canva, PDF et notes envoyées par Marine). Ils sont exclus du dépôt Git (`.gitignore`) : ils existent uniquement sur la machine actuelle et **ne seront pas transférés via GitHub**. Si ces fichiers sont utiles, pense à les copier manuellement à part avant de changer de machine.
- **`robots.txt`** — Indique aux moteurs de recherche d'indexer tout le site et pointe vers le sitemap.
- **`sitemap.xml`** — Liste les pages du site pour les moteurs de recherche, avec leurs URLs en `https://creakid.fr/...` (à garder cohérent avec le domaine final, voir section 9).
- **`PASSATION.md`** — Ce fichier-ci.

## 5. Mise en ligne : choix d'hébergeur

Pour un site HTML statique avec déploiement automatique depuis GitHub, il existe plusieurs options gratuites sérieuses :

**Netlify**
Déploiement continu depuis Git, gestion native des redirections via un fichier `_redirects` (déjà présent dans ce projet), formulaires natifs.
Inscription : https://www.netlify.com
Avantage : le plus simple à configurer, pensé pour des sites comme celui-ci.
Inconvénient : plan gratuit limité à 100 Go de bande passante/mois (largement suffisant ici).

**Vercel**
Déploiement continu depuis Git, à l'origine pensé pour des frameworks JS modernes mais fonctionne très bien aussi pour du HTML statique simple.
Inscription : https://vercel.com
Avantage : très rapide, interface claire, bon plan gratuit.
Inconvénient : les redirections se configurent via un fichier `vercel.json` (pas `_redirects`), donc une petite adaptation serait nécessaire pour la page 404.

**Cloudflare Pages**
Déploiement continu via le réseau mondial de Cloudflare.
Inscription : https://pages.cloudflare.com
Avantage : bande passante gratuite illimitée, très rapide, et utilise aussi un fichier `_redirects` (compatible avec celui déjà présent).
Inconvénient : interface un peu plus technique, et si le domaine est aussi géré chez Cloudflare cela ajoute une étape de configuration DNS.

**GitHub Pages**
Hébergement gratuit directement intégré à GitHub, activable depuis les paramètres du dépôt.
Inscription : aucune, c'est inclus dans ton compte GitHub (Settings → Pages du dépôt).
Avantage : aucun compte supplémentaire à créer, tout reste dans GitHub.
Inconvénient : pas de gestion native des redirections avancées ni de formulaires, configuration HTTPS sur domaine personnalisé un peu plus longue.

**Recommandation : Netlify.** Le site a été pensé et configuré pour cet hébergeur (le fichier `_redirects` à la racine est sa syntaxe spécifique pour la page 404), et c'est le plus simple à mettre en place pour quelqu'un qui découvre. Si tu choisis un autre hébergeur, vérifie juste comment adapter la redirection 404 (voir section 13).

## 6. Mise en ligne : procédure détaillée pour Netlify

1. Aller sur [netlify.com](https://www.netlify.com) et s'inscrire avec ton compte GitHub.
2. Autoriser Netlify à accéder à tes dépôts GitHub (il demandera la permission sur le dépôt "creakid" ou sur tous tes dépôts).
3. Cliquer sur **"Add new site"** → **"Import an existing project"** → choisir GitHub → sélectionner le dépôt **"creakid"**.
4. Configuration de build :
   - Branch à déployer : `main`
   - Base directory : laisser vide
   - Build command : laisser vide (pas de build, le site est déjà prêt tel quel)
   - Publish directory : laisser vide, ou mettre un simple point `.`
5. Cliquer sur **"Deploy creakid"**.
6. Une fois le déploiement terminé, renommer le site si tu veux une URL plus propre : **Site configuration → Site details → Change site name** (par exemple "creakid", ce qui donnera `creakid.netlify.app`).
7. Vérifier que le site est bien accessible à cette URL Netlify, en testant plusieurs pages (accueil, concept, contact, et une URL qui n'existe pas pour vérifier que la 404 personnalisée s'affiche).

## 7. Remplacement de Formspree (formulaire de contact)

Le formulaire de [contact.html](contact.html) envoie les messages via Formspree, un service tiers qui relaie le formulaire vers une adresse email. L'ancien compte a été fermé, il faut en recréer un.

### Étape A — Créer le nouveau compte

1. Aller sur [formspree.io](https://formspree.io).
2. S'inscrire (le plan **Free** suffit : 50 messages/mois).
3. Créer un nouveau formulaire, par exemple nommé "Créa-Kid Contact".
4. Renseigner l'adresse email qui recevra les messages — idéalement **marine.creakid@gmail.com**.
5. Récupérer l'URL d'endpoint du formulaire, au format `https://formspree.io/f/XXXXXXXX`.

### Étape B — Remplacer dans le code

Le fichier à modifier est [contact.html](contact.html), à la ligne où se trouve :
```html
<form id="form" class="form-card" action="https://formspree.io/f/mlgzewkw" method="POST" novalidate>
```
Il n'y a **qu'une seule occurrence** de cet endpoint dans tout le code (`mlgzewkw` est l'identifiant du formulaire précédent). Il suffit de le remplacer par le nouvel endpoint récupéré à l'étape A.

Le plus simple est de demander à Claude Code :
> "Remplace l'endpoint Formspree dans contact.html. L'ancien était https://formspree.io/f/mlgzewkw, le nouveau est https://formspree.io/f/XXXXXXXX"

### Étape C — Tester

Formspree exige une validation par email avant d'activer un formulaire : envoie un premier message de test depuis le site, ce qui déclenchera un email de confirmation envoyé par Formspree à l'adresse choisie à l'étape A — clique sur le lien de validation dans cet email. Envoie ensuite un vrai message de test pour vérifier que tout fonctionne de bout en bout.

## 8. Remplacement de Trustindex (widget avis Google)

La page d'accueil affiche une section avis clients via un widget Trustindex, qui récupère automatiquement les avis Google de la fiche CRÉA-KID. L'ancien compte a été fermé, il faut en recréer un.

### Étape A — Créer le nouveau compte

1. Aller sur [trustindex.io](https://www.trustindex.io).
2. S'inscrire (le plan **Free** suffit : 1500 vues/mois).
3. Connecter la fiche Google Business de CRÉA-KID (chercher "CREA-KID Saint Maximin" lors de la connexion).
4. Choisir un template de type **Carousel/Slider** (le widget précédent ressemblait à un encart avec un score global "EXCELLENT" suivi d'un carrousel d'avis individuels — reproduire ce style si possible pour rester cohérent).
5. Personnaliser l'apparence : couleur principale `#F5A56C`, fond blanc, mode aléatoire pour l'ordre des avis, filtrer pour n'afficher que les avis 4-5 étoiles, langue Français.
6. Récupérer le script d'intégration ("embed"), qui ressemble à :
   ```html
   <script defer async src='https://cdn.trustindex.io/loader.js?XXXXXXXXX'></script>
   ```

### Étape B — Remplacer dans le code

Le fichier à modifier est [index.html](index.html), dans la section avis (chercher "Trustindex" ou la balise `<script>` qui charge `cdn.trustindex.io`). L'ancien identifiant de widget était `7305f5f713f5398ac846662a984`.

Le plus simple est de demander à Claude Code :
> "Remplace le script Trustindex dans index.html. L'ancien ID était 7305f5f713f5398ac846662a984, le nouveau est XXXXXXXXX"

⚠️ Voir aussi la section 13 : ce widget ne s'affiche pas correctement testé en local, uniquement une fois le site déployé en ligne.

## 9. Branchement du domaine creakid.fr (à venir)

Ce nom de domaine n'est pas encore acheté. Quelques registrars populaires et fiables :

- **OVH** — Hébergeur français historique, interface en français, bon support si besoin d'aide par téléphone.
- **Gandi** — Registrar français reconnu pour sa transparence (pas de frais cachés, pas d'upsell agressif), bonne réputation.
- **Namecheap** — Registrar international, souvent parmi les moins chers, interface simple en anglais.

Une fois le domaine acheté, voici la procédure générale (les détails exacts dépendent de l'hébergeur choisi en section 5-6) :

1. Dans le dashboard de l'hébergeur (Netlify ou autre), aller dans **"Domain settings"** → **"Add custom domain"**.
2. Saisir `creakid.fr`.
3. Suivre les instructions affichées : il faudra généralement soit changer les **nameservers** du domaine chez le registrar pour qu'ils pointent vers l'hébergeur, soit ajouter des **enregistrements DNS** (de type A ou CNAME) directement chez le registrar.
4. Activer le HTTPS automatique (certificat Let's Encrypt, gratuit, inclus chez Netlify et la plupart des hébergeurs listés).
5. Une fois le domaine actif, vérifier que [sitemap.xml](sitemap.xml), [robots.txt](robots.txt) et les balises `og:url` / `twitter:image` dans le `<head>` de chaque page HTML pointent bien vers `https://creakid.fr/...` — c'est déjà le cas dans le code actuel, ces fichiers ont été préparés en avance pour ce domaine, donc en principe rien à changer si le domaine final reste bien `creakid.fr`.

## 10. Workflow de modification au quotidien

1. Cloner le dépôt sur ta machine (`git clone ...`).
2. Ouvrir le dossier dans VS Code.
3. Lancer Claude Code dans le terminal intégré avec la commande `claude`.
4. Lui donner les instructions de modification en français, en décrivant ce que tu veux changer.
5. Une fois satisfait du résultat, faire `git commit` puis `git push`.
6. L'hébergeur (Netlify ou autre) redéploie automatiquement le site en 30 à 60 secondes après chaque push sur `main`.

## 11. État du projet : lots déjà réalisés

- **Lot 1 — SEO complet** : meta description, Open Graph, Twitter Card, favicon, `robots.txt`, `sitemap.xml`.
- **Lot 2 — Optimisation images** : conversion en WebP avec fallback JPG/JPEG via `<picture>`, lazy loading.
- **Lot 3 — Formulaire de contact** : branchement à Formspree, validation des champs, protection anti-spam (honeypot), mention RGPD.
- **Lot 4 — Mise en ligne initiale** : dépôt GitHub + déploiement Netlify.
- **Lot 5 — Peaufinage** : placeholders soignés en attendant les vrais contenus, page 404 personnalisée, accessibilité, données structurées JSON-LD (LocalBusiness) pour le référencement.
- **Ajouts récents** : carrousel photos sur les pages concept et accueil, intégration des photos du coin café et de l'espace bébé, planning d'accueil rendu éditable via `data/activites.json`, bouton "Nous trouver" vers Google Maps, harmonisation de la marque en "CRÉA-KID" (majuscules), ajout de la section avis Google via le widget Trustindex.

## 12. Ce qui reste à faire

- Mettre le site en ligne sur un nouvel hébergeur (voir sections 5-6).
- Créer un nouveau compte Formspree et remplacer l'endpoint dans `contact.html` (voir section 7).
- Créer un nouveau compte Trustindex et remplacer l'identifiant de widget dans `index.html` (voir section 8).
- Récupérer auprès de Marine : SIRET, forme juridique, et l'URL Facebook exacte de CRÉA-KID — puis compléter [mentions-legales.html](mentions-legales.html) (champs marqués `[À COMPLÉTER]` : raison sociale, forme juridique, SIRET, RCS/RM, TVA intracommunautaire, code APE/NAF, directeur de publication, durée de conservation des données, DPO, politique cookies, crédits design/photos/illustrations) et corriger les liens Facebook (chercher les commentaires HTML `<!-- TODO: vérifier l'URL Facebook exacte avec Marine -->`, présents sur plusieurs pages).
- Une fois l'hébergeur choisi, compléter aussi le champ "Hébergeur" (société, adresse, téléphone) dans `mentions-legales.html`.
- Récupérer les vraies photos manquantes si Marine en a (le coin café et l'espace bébé sont déjà intégrés ; il reste par exemple une photo d'anniversaire à intégrer si disponible).
- Remplacer les embeds de test dans [planning.html](planning.html) par les embeds définitifs créés par Marine : le planning Canva et le calendrier Google Calendar actuels sont explicitement marqués dans le code comme `<!-- TEST EMBED CANVA -->` et `<!-- TEST EMBED GOOGLE CALENDAR -->` — ce sont des exemples de démonstration, pas les vrais comptes de Marine.
- Acheter et brancher le nom de domaine creakid.fr (voir section 9).
- Livrer le site à Marine avec un guide simplifié d'utilisation (en particulier : comment elle peut éditer elle-même `data/activites.json` pour changer le planning de la semaine, et comment mettre à jour le planning Canva).

## 13. Points d'attention

- Le widget Trustindex ne fonctionne pas en local (ouverture du fichier via `file://`) : il faut impérativement tester sur le site déployé en ligne.
- Les iframes Canva et Google Calendar dans `planning.html` doivent garder leurs paramètres de personnalisation actuels pour rester visuellement cohérents avec le reste du site (`bgcolor=%23FBF6EE`, `showCalendars=0`, `showTabs=0`, etc.) — si tu régénères un embed depuis Canva ou Google Calendar, pense à reporter ces réglages.
- Garder le pattern `<picture>` (source WebP + fallback JPG/JPEG) sur toutes les images du site, c'est ce qui permet le chargement rapide tout en restant compatible avec tous les navigateurs.
- Le projet est conçu **mobile-first** : toujours tester en responsive (réduire la largeur de la fenêtre ou utiliser les outils de développement du navigateur) avant de push une modification visuelle.
- Le fichier [_redirects](_redirects) à la racine est une syntaxe **spécifique à Netlify** (et compatible Cloudflare Pages) pour rediriger les URLs inexistantes vers `404.html`. Si tu choisis Vercel ou GitHub Pages, il faudra adapter cette redirection à leur système propre (`vercel.json` pour Vercel par exemple).
- Les dossiers `partials/header.html` et `partials/footer.html` ne sont **pas branchés dynamiquement** : chaque page HTML contient sa propre copie du header et du footer. Toute modification du menu ou du footer doit être répercutée sur chaque page (voir section 4).
- En explorant le dépôt au moment d'écrire ce document, certaines photos dans `assets/` étaient en cours de renommage (`.jpg` → `.jpeg`) alors que le code HTML référence encore l'extension `.jpg` pour plusieurs d'entre elles. Si des images de la galerie n'apparaissent plus après un `git pull`, vérifie que les extensions de fichiers dans `assets/` correspondent exactement à celles utilisées dans les balises `<img>` des pages HTML.

## 14. Conventions

- Les messages de commit sont rédigés en français.
- Indentation à 2 espaces dans tous les fichiers.
- Les composants CSS réutilisables sont centralisés dans `style.css`, organisés avec des commentaires de section.
- Pas de framework : garder le projet simple et léger, éviter d'introduire des dépendances ou un système de build sans raison forte.

## 15. Coordonnées Marine

- **Email :** marine.creakid@gmail.com
- **Téléphone :** 06.67.44.33.61
- **Adresse :** 55 Avenue d'Estienne d'Orves, 83470 Saint-Maximin-la-Sainte-Baume
- **Instagram :** creakid_stmaximin
- **Facebook :** à vérifier — l'URL exacte n'a pas encore été confirmée par Marine (voir section 12)
