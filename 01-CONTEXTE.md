# Contexte du projet

## En bref
Un site gratuit d’histoire de l’art moderne, pour les étudiants et les curieux. Il est né de la « Bible de l’art », un document personnel de son auteur sur la peinture, surtout moderne.

Son cœur et sa porte d’entrée est une frise chronologique interactive de l’art moderne, d’environ 1874 à 1960. Autour d’elle se construit un ensemble de contenus :
- une encyclopédie, avec une page par mouvement, par artiste et par œuvre ;
- une rubrique « Comprendre », qui relie les fiches entre elles : parcours, comparaisons, glossaire ;
- plus tard, une version anglaise.

Le but du site : que le lecteur comprenne comment les mouvements, les artistes et les œuvres de l’art moderne s’articulent.

## L’auteur
Térence, étudiant, seul à développer et à maintenir le site. Il n’est pas développeur, mais il utilise Git, GitHub et VS Code, et il apprend Python et JavaScript. Il veut comprendre le code de son site pour pouvoir le retoucher lui-même.

## Objectifs, par ordre de priorité
1. Une frise claire et agréable, qui garde son design actuel et reste l’accueil du site.
2. Un site bien référencé sur Google, grâce à une page par mouvement, par artiste et par œuvre.
3. Un site gratuit à héberger et simple à mettre à jour.
4. Plus tard : la rubrique « Comprendre », puis une version anglaise.

## Contraintes
- **Hébergement** : Cloudflare, avec un compte lié à GitHub. Le code est dans le dépôt public `tertergangg/Frise-chronologique---Art-moderne`.
- **Adresse** : un nom de domaine en .com, sans « github » dans l’adresse. Le nom du site n’est pas encore choisi.
- **Coût** : nul, en dehors du nom de domaine.
- **Simplicité** : une seule personne, débutante, maintient le site. Une solution simple et compréhensible vaut mieux qu’une solution techniquement élégante.
- **Écriture** : tout le contenu est écrit par l’auteur seul. C’est le principal goulot d’étranglement du projet : une fonction qui demande beaucoup de texte attend que ce texte existe.
- **Droits** : une grande partie du corpus est encore protégée par le droit d’auteur en France (Picasso, Dalí, Magritte, Duchamp, Pollock, Rothko…). Le site doit donc être beau et complet sans image. construire.py calcule le domaine public d’après la date de décès de l’artiste : seule une œuvre libre affiche son image ; une œuvre protégée affiche un encart qui l’explique, avec un lien vers le musée (D10).

## Le contenu
- Le **tableur Excel** `donnees/frise_art_moderne_base.xlsx` est la base de données du site. Il contient les mouvements, les artistes, les œuvres, les événements et les relations (onglet « Influences »). Il est modifié dans Excel.
- Les **fiches** sont en Markdown, dans `fiches/`, et sont modifiées dans VS Code.
  - Les fiches mouvements et artistes ont été rédigées avec Claude à partir de la Bible, dans le style de l’auteur, sans plagiat et sans section « Sources ». Elles conservent ses citations.
  - L’auteur rédige lui-même les fiches œuvres. Une œuvre a trois niveaux : référencée (sa ligne dans le tableur), documentée (une présentation courte) et analysée (une analyse complète). Elle a sa page dès qu’elle est documentée (D13, D21).
- Ce projet Claude porte sur le **code** du site. On ne touche au texte des fiches que sur demande explicite.