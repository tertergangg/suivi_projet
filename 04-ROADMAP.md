# Roadmap

**Étape en cours : phase 2, étape 2h (finitions).** La structure du site est fixée (D17 à D28), le modèle de données est en place (étape 2d), les pages des mouvements, des artistes et des œuvres sont en ligne, avec le catalogue (étapes 2e et 2f), et le référencement de base est fait (étape 2g). Viennent ensuite la fin de la phase 2, le lot 4 (téléphone et en-tête), puis la phase 3 (ouverture au public).

---

## Terminé
Le détail est dans le JOURNAL et dans l’historique Git.
- **Phase 1 · Migration vers Astro, à l’identique** · 2 novembre 2026. Même site, fabriqué par Astro et publié par Cloudflare depuis `main`.
- **Lot 1 · Corrections de la V1** · 2 novembre 2026. Bulle d’aperçu, poignée du panneau, croix.
- **Lot 2 · Fiches** · 2 novembre 2026. Nationalité, Disciplines, « Aussi » cliquable, fiche mouvement en deux listes d’œuvres, image en grand.
- **Lot 3 · Design** · 4 novembre 2026. Couleurs, bandeau du haut, Index, bouton clair/sombre, étiquettes d’artistes, zoom « Moyen », barre du bas en vue mouvement. La croix actuelle est gardée.
- **Phase 0 · Remettre de l’ordre** · 4 novembre 2026. Fiches orphelines rangées et signalées, README, fin de GitHub Pages (D11), style.css rangé par élément de la page.
- **Préparation de la phase 2** · 6 novembre 2026. Documents dans `suivi_projet/` (D12), wrangler fixé en 4.157.0 (D16), structure du site décidée après le tri des idées de Lovable et un prototype de structure (D17 à D28).

## Phase 2 · Une page par fiche
Objectif : la structure de D18, avec une page par fiche rédigée (D13, D21), la frise restant à `/` (D17) et identique à aujourd’hui. Une étape = une branche, testée sur l’aperçu Cloudflare avant d’aller dans `main`.
- **2a · Vérifications.** Terminé le 5 novembre 2026. construire.py signale un lien `[[id]]` vers un identifiant inconnu ou mal formé, un chemin d’image qui ne commence ni par `/` ni par `http`, et une image absente de `public/`.
- **2b · Les données et la frise passent dans Astro.** Terminé le 6 novembre 2026. construire.py écrit `src/data/frise.json` et son `main()` est découpé. app.js est déplacé dans `src/scripts/` et devient un module traité par Astro, qui importe le JSON (D29).
- **2c · Découpage d’app.js.** Terminé le 6 novembre 2026, en version réduite (D31). marked passe en dépendance npm (D30). Le contenu des fiches et ses outils vont dans `src/lib/` (`donnees.js`, `outils.js`, `fiches.js`) ; le reste d’app.js n’est pas découpé.
- **2d · Le modèle de données.** Terminé le 6 novembre 2026, sur la branche `modele-donnees`, en cinq sous-étapes et sans rien changer à l’affichage : onglet « Groupes » et colonne « Groupes » des artistes (D22) ; relations typées entre artistes et mouvements (D23) ; onglet « Glossaire » (D24) ; niveau des œuvres déduit de leur fiche, avec le gabarit en deux parties (D21, D34) ; gabarits des parcours et des comparaisons (D24, D35, D36).

  À chaque sous-étape, les vérifications correspondantes, et l’unicité des identifiants étendue aux nouveaux types. La branche est testée sur la version de test, puis fusionnée dans `main`, une fois les cinq sous-étapes terminées.
- **2e · Pages mouvements et artistes.** Terminé le 7 novembre 2026, sur la branche `pages`, en quatre sous-étapes :
  1. **Liens.** `aUnePage()` et `adresse()` décident de l’adresse de chaque élément (D40).
  2. **Mouvements.** `Base.astro` (en-tête et menu du site, fil d’Ariane), `public/pages.css` (D39), les composants de `src/components/`, le répertoire `/mouvements/` (D37) et une page par mouvement : repère chronologique, sommaire, groupes en sections, artistes, œuvres, moments clés, filiations.
  3. **Artistes.** Le répertoire `/artistes/`, avec son filtre (D42), et une page par artiste : rubriques, texte (D41), œuvres, liens.
  4. **Ponts.** « Lire la page » dans le panneau de la frise (D19). « Voir dans la frise » mène à la vue mouvement, à l’adresse `/?mouvement=<id>` (D27, D38, D43). Dans le panneau, un lien vers un groupe mène à la page de son mouvement, et construire.py cesse de le signaler.

  Testée sur la version de test, puis fusionnée dans `main`.
- **2f · Catalogue et pages œuvres.** Terminé le 8 novembre 2026, sur la branche `oeuvres`, en trois sous-étapes :
  1. **Droits des images.** Terminé le 7 novembre 2026. D10 est tranchée : construire.py calcule le domaine public, retire les images des œuvres protégées et lit les nouvelles colonnes « Crédit image » et « Lien de l’œuvre ». Dans le panneau, une œuvre protégée affiche un encart à la place de l’image, et l’image d’une œuvre libre a son crédit. La technique prend une majuscule, le sous-titre de l’œuvre grandit, et les intertitres `###` du panneau passent sous les `##`.
  2. **Page œuvre.** Terminé le 7 novembre 2026. `aUnePage()` et `adresse()` apprennent les œuvres documentées. Chaque œuvre documentée a sa page, en cartel et sans sommaire (D45) : image en grand dans un `<dialog>`, encart des droits, « Du même artiste » en cartes avec vignette (`Carte.astro` reçoit l’option `vignette`). Testé avec une fiche provisoire, non commitée : aucune œuvre n’est encore documentée.
  3. **Catalogue.** Terminé le 8 novembre 2026. Le répertoire `/oeuvres/`, rangé par mouvement, en cartes avec vignette, avec une recherche par titre ou par artiste, un choix du mouvement et la case « Œuvres majeures seulement » (D47). L’entrée « Œuvres » dans le menu. Le filtre « Écrits et manifestes » est écarté.

  `src/lib/fiches.js` reste un seul fichier (D46). Testée sur la version de test, puis fusionnée dans `main`.
- **2g · Référencement de base.** Terminé le 8 novembre 2026, sur la branche `referencement`, en quatre sous-étapes :
  1. **Description.** `Base.astro` reçoit une `description` : le résumé court d’un mouvement ou d’une œuvre, la phrase-clé d’un artiste. La frise et les trois répertoires ont leur phrase, aux nombres et aux dates calculés. Les titres, déjà uniques, ne changent pas.
  2. **Adresse canonique.** L’adresse du site est écrite une seule fois, dans `site` (D48) ; chaque page en déduit sa canonique, avec la barre finale, et la frise déclare `/` dans toutes ses vues.
  3. **sitemap.xml et robots.txt**, fabriqués par Astro, sans dépendance (D49).
  4. **Contrôle de `dist/`.** `verifier_dist.mjs`, lancé par `npm run verifier` : liens internes, ancres, concordance avec le sitemap (D50).
- **2h · Finitions.** La page « À propos » et le plan du site (`/plan-du-site/`). L’Index reçoit les liens vers les nouvelles pages. Ajouter les deux pages à `PAGES_FIXES` (`src/pages/sitemap.xml.js`), et leur donner une description ; `npm run verifier` signale une page absente du sitemap.

**Terminé quand** : chaque fiche rédigée a sa page ; aucun lien interne n’est cassé (construire.py, puis `npm run verifier`) ; le sitemap est généré ; la frise fonctionne comme avant, sur ordinateur et sur téléphone.

## En parallèle · Écriture
L’écriture n’attend pas le code, et c’est elle qui fixe le rythme du site.
- Documenter les œuvres majeures (35 aujourd’hui). Les 104 fiches œuvres sont au nouveau gabarit : la présentation suffit, l’analyse sous `## Analyse` est facultative (D34). Aucune n’est encore documentée : la page œuvre n’a été testée qu’avec une fiche provisoire.
- Ajouter des images pour les œuvres du domaine public (62 sur 104 en 2026), dans `public/images/`, en minuscules, avec leur crédit (D10). Remplir « Lien de l’œuvre » pour les œuvres protégées.
- Au premier build de 2027, Pollock et Nolde entrent dans le domaine public : construire.py le signalera, et il faudra passer leur cellule « Domaine public ? » à « Oui ».
- Arrêter la liste des groupes et écrire leur résumé d’une phrase, dans l’onglet « Groupes » du tableur (seul le néo-impressionnisme y est), puis remplir la colonne « Groupes » des artistes concernés. Pistes tirées des fiches : Die Brücke et Der Blaue Reiter (expressionnisme), néo-impressionnisme et synthétisme (post-impressionnisme), suprématisme et De Stijl (abstraction), action painting et color field (expressionnisme abstrait).
- Ajouter les relations entre mouvements (filiation, réaction), avec leur explication, et compléter les explications des 12 relations entre artistes.
- Avant de saisir les filiations et les réactions, fixer leur sens : que veut dire De → Vers ? La page mouvement l’affichera ensuite.
- Ajouter au tableur Ball, Eluard, Manet et Miró, avec leurs œuvres, puis ramener leurs fiches de `fiches/_en-attente/` dans `fiches/artistes` et `fiches/oeuvres`. Retirer au passage la section « Sources » des 4 fiches artistes.

## Lot 4 · Version téléphone et en-tête du site
Phase dédiée, dans sa propre conversation, après la phase 2 et avant l’ouverture au public.
- L’en-tête de la frise reçoit le menu du site (D18) et l’outil unique « Rechercher », qui réunit la recherche et l’Index (D28), dans un `<dialog>` pour la gestion du focus.
- Revoir toute l’expérience sur petit écran, pages de la phase 2 comprises.
- La frise passe au gabarit commun (`Base.astro`) : un seul `<head>`, et le bouton de thème sur tout le site. D’ici là, les pages suivent le thème choisi sur la frise, sans bouton.
- Décider si le filtre du répertoire des artistes (D42) et la recherche du catalogue (D47) restent à côté de « Rechercher ».
- Pages : sur téléphone, le sommaire « Dans cette page » repousse le texte loin vers le bas ; un sommaire repliable réglerait ça. Les pages n’ont pas encore été vues sur téléphone.
- À trancher : la frise doit-elle s’ouvrir sur le zoom Ensemble lors d’une première visite ?
- Les règles téléphone ajoutées aux étapes 2a et 2b du lot 3 (libellé court « Au hasard », recherche sur toute la largeur, liste des résultats accrochée à gauche, bouton « Index » dans la même ligne) sont provisoires. La ligne des outils déborde sans doute.
- Étiquettes d’artistes de la vue mouvement : sur téléphone, les compléments (nombre d’œuvres, nationalité, disciplines, « Surtout ») sont cachés (classe `.complement`). À revoir si l’étiquette s’élargit.
- Barre du bas : avec les trois boutons de zoom, elle est serrée sur téléphone (libellés à 0,75 rem).
- À voir : découper la suite d’app.js (D31) si l’ajout du menu et de « Rechercher » le rend difficile à lire.

## Phase 3 · Nom de domaine et ouverture au public
Le contenu minimum à l’ouverture est fixé par D26 : la frise, les mouvements, les artistes, le catalogue et les œuvres documentées, la recherche, sans la rubrique Comprendre.
- Choisir le nom, acheter le .com et le brancher sur Cloudflare. Remplacer l’adresse provisoire dans le champ `site` d’`astro.config.mjs` (D48), et dans le README.
- Rédiger les mentions légales.
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