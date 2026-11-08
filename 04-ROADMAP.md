# Roadmap

**Étape en cours : lot 4 (version téléphone et en-tête du site).** La phase 2 est terminée : chaque fiche rédigée a sa page, avec le catalogue, le référencement de base, À propos et le plan du site. Viennent ensuite la phase 3 (ouverture au public), puis la rubrique Comprendre (phase 4).

---

## Terminé
Le détail est dans le JOURNAL et dans l’historique Git.
- **Phase 1 · Migration vers Astro, à l’identique** · 2 novembre 2026. Même site, fabriqué par Astro et publié par Cloudflare depuis `main`.
- **Lot 1 · Corrections de la V1** · 2 novembre 2026. Bulle d’aperçu, poignée du panneau, croix.
- **Lot 2 · Fiches** · 2 novembre 2026. Nationalité, Disciplines, « Aussi » cliquable, fiche mouvement en deux listes d’œuvres, image en grand.
- **Lot 3 · Design** · 4 novembre 2026. Couleurs, bandeau du haut, Index, bouton clair/sombre, étiquettes d’artistes, zoom « Moyen », barre du bas en vue mouvement. La croix actuelle est gardée.
- **Phase 0 · Remettre de l’ordre** · 4 novembre 2026. Fiches orphelines rangées et signalées, README, fin de GitHub Pages (D11), style.css rangé par élément de la page.
- **Préparation de la phase 2** · 6 novembre 2026. Documents dans `suivi_projet/` (D12), wrangler fixé en 4.157.0 (D16), structure du site décidée après le tri des idées de Lovable et un prototype de structure (D17 à D28).
- **Phase 2 · Une page par fiche** · 8 novembre 2026. Huit étapes, chacune sur sa branche, testée sur la version de test puis fusionnée dans `main` :
  - 2a, vérifications des liens et des images dans construire.py ;
  - 2b, `frise.json` importé par app.js, devenu un module traité par Astro (D29) ;
  - 2c, `src/lib/` partagé par la frise et les pages, et marked installé par npm (D30, D31) ;
  - 2d, modèle de données : groupes, relations typées, glossaire, niveau des œuvres, parcours et comparaisons (D32 à D36) ;
  - 2e, pages et répertoires des mouvements et des artistes, et ponts avec la frise (D37 à D43) ;
  - 2f, droits des images, pages des œuvres et catalogue (D10, D44 à D47) ;
  - 2g, référencement de base : descriptions, canoniques, sitemap, robots.txt, contrôle de `dist/` (D48 à D50) ;
  - 2h, liens de l’Index vers les pages, pied de page, plan du site et À propos (D51, D52).

## Lot 4 · Version téléphone et en-tête du site
Phase dédiée, dans sa propre conversation, avant l’ouverture au public. Une branche par étape, testée sur la version de test.
- **Pour commencer** : Térence apporte la liste des changements qu’il veut faire dans l’en-tête du site. La trier avec les points ci-dessous, puis découper le lot en étapes.
- L’en-tête de la frise reçoit le menu du site (D18) et l’outil unique « Rechercher », qui réunit la recherche et l’Index (D28), dans un `<dialog>` pour la gestion du focus.
- Revoir toute l’expérience sur petit écran, pages de la phase 2 comprises.
- La frise passe au gabarit commun (`Base.astro`) : un seul `<head>`, le bouton de thème et le pied de page sur tout le site. D’ici là, les pages suivent le thème choisi sur la frise, sans bouton. La ligne « Pages du site » de l’Index (D51) devient alors inutile : la retirer.
- À voir : réunir en une seule liste les pages fixes, écrites à cinq endroits (voir le JOURNAL).
- Décider si le filtre du répertoire des artistes (D42) et la recherche du catalogue (D47) restent à côté de « Rechercher ».
- Pages : sur téléphone, le sommaire « Dans cette page » repousse le texte loin vers le bas ; un sommaire repliable réglerait ça. À propos et le plan du site ont été vus sur téléphone ; les pages des mouvements, des artistes et des œuvres, pas encore en détail.
- À trancher : la frise doit-elle s’ouvrir sur le zoom Ensemble lors d’une première visite ?
- Les règles téléphone ajoutées aux étapes 2a et 2b du lot 3 (libellé court « Au hasard », recherche sur toute la largeur, liste des résultats accrochée à gauche, bouton « Index » dans la même ligne) sont provisoires. La ligne des outils déborde sans doute.
- Étiquettes d’artistes de la vue mouvement : sur téléphone, les compléments (nombre d’œuvres, nationalité, disciplines, « Surtout ») sont cachés (classe `.complement`). À revoir si l’étiquette s’élargit.
- Barre du bas : avec les trois boutons de zoom, elle est serrée sur téléphone (libellés à 0,75 rem).
- À voir : découper la suite d’app.js (D31) si l’ajout du menu et de « Rechercher » le rend difficile à lire.

## En parallèle · Écriture
L’écriture n’attend pas le code, et c’est elle qui fixe le rythme du site.
- Documenter les œuvres majeures (35 aujourd’hui). Les 104 fiches œuvres sont au nouveau gabarit : la présentation suffit, l’analyse sous `## Analyse` est facultative (D34). Aucune n’est encore documentée : la page œuvre n’a été testée qu’avec une fiche provisoire.
- Ajouter des images pour les œuvres du domaine public (62 sur 104 en 2026), dans `public/images/`, en minuscules, avec leur crédit (D10). Remplir « Lien de l’œuvre » pour les œuvres protégées.
- Au premier build de 2027, Pollock et Nolde entrent dans le domaine public : construire.py le signalera, et il faudra passer leur cellule « Domaine public ? » à « Oui ».
- Arrêter la liste des groupes et écrire leur résumé d’une phrase, dans l’onglet « Groupes » du tableur (seul le néo-impressionnisme y est), puis remplir la colonne « Groupes » des artistes concernés. Pistes tirées des fiches : Die Brücke et Der Blaue Reiter (expressionnisme), néo-impressionnisme et synthétisme (post-impressionnisme), suprématisme et De Stijl (abstraction), action painting et color field (expressionnisme abstrait).
- Ajouter les relations entre mouvements (filiation, réaction), avec leur explication, et compléter les explications des 12 relations entre artistes.
- Avant de saisir les filiations et les réactions, fixer leur sens : que veut dire De → Vers ? La page mouvement l’affichera ensuite.
- Ajouter au tableur Ball, Eluard, Manet et Miró, avec leurs œuvres, puis ramener leurs fiches de `fiches/_en-attente/` dans `fiches/artistes` et `fiches/oeuvres`. Retirer au passage la section « Sources » des 4 fiches artistes.

## Phase 3 · Nom de domaine et ouverture au public
Le contenu minimum à l’ouverture est fixé par D26 : la frise, les mouvements, les artistes, le catalogue et les œuvres documentées, la recherche, sans la rubrique Comprendre.
- Choisir le nom, acheter le .com et le brancher sur Cloudflare. Remplacer l’adresse provisoire dans le champ `site` d’`astro.config.mjs` (D48), et dans le README.
- Rédiger les mentions légales.
- Page À propos : remplacer l’adresse factice (`contact@example.com`, constante `COURRIEL` d’`a-propos.astro`) par une adresse dédiée au site, plutôt que l’adresse personnelle ; publier les sources auxquelles renvoie le texte, ou corriger la phrase qui les annonce ; vérifier l’âge.
- Ajouter une page 404, un favicon (à la place des logos d’Astro) et un aperçu pour le partage sur les réseaux (Open Graph). La page 404 n’a pas sa place dans le sitemap : l’exclure du contrôle de `verifier_dist.mjs`, qui la signalerait comme « page absente du sitemap ».
- Déclarer le site et son sitemap dans Google Search Console.
- À voir : un outil de mesure d’audience gratuit et sans cookies, pour suivre le nombre de fiches ouvertes chaque semaine.

## Phase 4 · Comprendre
La rubrique de D20, qui n’apparaît dans le menu qu’avec son contenu minimum (D26).
- L’accueil `/comprendre/`, les gabarits Parcours (article en étapes, qui renvoie aux fiches), Comparer (un tableau rempli depuis le tableur, plus quelques lignes rédigées) et Glossaire (une page, une ancre par terme).
- Contenu de départ : un parcours pour débuter, par exemple « L’art moderne en neuf étapes », une comparaison et une vingtaine de termes.
- Idées de Lovable à reprendre ici : « Trois questions pour se vérifier » en fin de page mouvement, « Ne pas confondre », le vrai ou faux justifié, la question transversale, une version imprimable des pages de révision.

## Phase 5 · Version anglaise
L’organisation des fichiers et des adresses (`/en/…`) est à décider avant de commencer.

## Repoussé, sans date
- Une page propre pour un groupe qui reçoit un texte développé (D22).
- Les comparaisons d’artistes et d’œuvres (D20).
- Depuis une comparaison, ouvrir la frise avec les deux couloirs mis en avant.
- Le passage du panneau de la frise à une fiche courte (D19).
- Un graphe visuel des influences.
- Les repères d’histoire générale (Première Guerre mondiale, photographie, tube de peinture…), affichés dans la frise et non comme une rubrique.
- Les attributions discutées et les périodes d’activité dans le tableur (D23).