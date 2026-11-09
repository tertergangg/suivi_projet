# Décisions

Chaque décision a un statut :
- **Décidé** : on ne revient pas dessus sans en rediscuter explicitement.
- **Proposé** : en attente de validation. Une fois validé, il passe à « Décidé ».

Quand une décision change, on ne l’efface pas : on la marque « Remplacé par Dx » et on en ajoute une nouvelle.

---

**D1 · Construire le site avec Astro** · Décidé (octobre 2026)
Astro génère un site statique, crée facilement une page par fiche (utile pour Google) et permet de garder le HTML, le CSS et le JavaScript existants.

**D2 · Héberger le site sur Cloudflare** · Décidé
Gratuit, avec un compte lié au dépôt GitHub. Remplace GitHub Pages.

**D3 · Garder le design actuel de la frise** · Décidé
La migration ne doit rien changer à l’apparence. Toute évolution visuelle fait l’objet d’une demande à part.

**D4 · Un nom de domaine en .com, sans « github »** · Décidé
Le nom du site reste à choisir.

**D5 · Le tableur et les fiches Markdown sont la source de vérité** · Décidé
Le tableur se modifie dans Excel, les fiches dans VS Code. Tout le reste (data.js, frise.json, pages) est généré à partir d’eux.

**D6 · Numéro non attribué**
Ce numéro a été sauté. On ne renumérote pas, pour que les mentions « Remplacé par Dx » restent justes.

**D7 · construire.py reste la porte d’entrée des données** · Décidé (novembre 2026)
Il produit les données que lisent la frise et les pages : `public/data.js` pendant la phase 1, puis `src/data/frise.json` à partir de la phase 2. On garde ainsi un seul chemin pour les données et ses vérifications existantes.
Alternative écartée : faire lire les fiches directement par Astro (les « content collections »). C’est plus conforme à Astro, mais cela crée un second chemin de données et oblige à réécrire les vérifications.

**D8 · Les données générées sont commitées** · Décidé (novembre 2026)
`public/data.js` (puis `src/data/frise.json` en phase 2) est versionné dans Git. Cloudflare n’a donc besoin ni de Python ni d’openpyxl pour construire le site. Sans ce fichier dans Git, Cloudflare publierait une frise vide. Il faut lancer `python construire.py` avant chaque commit qui touche le tableur ou les fiches.

**D9 · Des identifiants et des adresses stables** · Décidé (novembre 2026)
L’identifiant d’un élément (ex. `matisse`) est aussi le nom de sa fiche et la fin de son adresse (`/artistes/matisse/`). Tant que le site n’est pas public, un identifiant peut encore changer. Une fois le site public, on ne le change plus : cela casserait les liens et le référencement. La version anglaise vivra sous `/en/`. L’organisation des adresses à l’intérieur de `/en/` sera décidée au début de la phase 5.

**D10 · N’afficher que des images dont les droits le permettent** · Décidé (7 novembre 2026)
Une œuvre n’affiche son image que si elle est dans le domaine public en France : son artiste est mort depuis plus de 70 ans, comptés à partir du 1er janvier qui suit sa mort (en 2026 : mort en 1955 ou avant). construire.py le calcule depuis l’année de décès et l’année du build : le site suit le calendrier sans qu’on y touche. Une image saisie pour une œuvre protégée est retirée de frise.json et signalée. La colonne « Domaine public ? » ne peut que forcer la protection, pour une exception ; un désaccord entre elle et le calcul est signalé. La photo elle-même doit être libre (domaine public, CC0, ou licence qui demande seulement un crédit) : elle est rangée dans `public/images/`, et sa source va dans la colonne « Crédit image ». Une œuvre protégée affiche à la place un encart qui l’explique, avec sa date d’entrée dans le domaine public et un lien vers sa page sur le site du musée (colonne « Lien de l’œuvre »), s’il est rempli.
Alternative écartée : remplir « Domaine public ? » à la main et la faire lire. C’est une double saisie, qui ne suit pas le calendrier.

**D11 · Plus de GitHub Pages, sans redirection de l’ancienne adresse** · Décidé (novembre 2026)
GitHub Pages est désactivé et `docs/` supprimé : l’ancienne adresse en github.io renvoie une erreur 404. C’est le choix le plus simple : rien à maintenir jusqu’à la phase 3.
Alternative écartée : garder `docs/` jusqu’à la phase 3 pour y placer une page de redirection vers le .com.

**D12 · Les documents du projet sont dans le dépôt, dossier `suivi_projet/`** · Décidé (4 novembre 2026)
Les six documents (CONTEXTE, ARCHITECTURE, DECISIONS, ROADMAP, CONVENTIONS, JOURNAL) sont versionnés avec le code : on sait toujours quelle version des documents va avec quelle version du site, et l’historique Git garde le détail des anciennes entrées du JOURNAL. Astro ignore ce dossier, qui n’est ni dans `src/` ni dans `public/`. Le dépôt étant public, ces documents sont visibles par tous. Ce sont ces fichiers que l’on recopie dans le projet Claude.
Alternative écartée : les garder hors du dépôt, où ils finissent par se décaler du code.

**D13 · Seules les fiches rédigées ont une page** · Décidé (4 novembre 2026), précisé par D21 pour les œuvres
Une page sans texte n’apporte rien au lecteur et dessert le référencement. Un lien vers une fiche non rédigée mène à la frise, qui ouvre cette fiche (`/#id`). Une fiche qui devient rédigée gagne sa page au build suivant, et les liens qui y mènent changent d’eux-mêmes.
Alternative écartée : une page pour chaque élément du tableur, même sans texte.

**D14 · La frise reste la page d’accueil pendant la phase 2** · Remplacé par D17 (6 novembre 2026)
La frise reste à l’adresse `/`. L’idée d’une page d’accueil distincte (voir ROADMAP, « Idées à trier ») sera tranchée avant la phase 3, tant que le site n’est pas public et que l’adresse de la frise peut encore changer (D9).
Alternative écartée : construire la page d’accueil pendant la phase 2, qui est déjà la plus lourde de la roadmap.

**D15 · Le script de la frise devient un module traité par Astro** · Décidé (4 novembre 2026)
Aujourd’hui, `public/app.js` est chargé tel quel (`is:inline`) : il ne peut rien importer, et les pages ne peuvent pas réutiliser ses fonctions. En phase 2, il devient un module traité par Astro, qui lit `src/data/frise.json` et partage avec les pages les fonctions qui fabriquent les fiches (`src/lib/`). La logique de la frise n’est pas réécrite, et ce passage se fait seul, dans sa propre étape.
Alternative écartée : garder app.js tel quel et refaire le HTML des fiches dans les gabarits des pages. Chaque fiche existerait en deux versions, qui finiraient par diverger.

**D16 · Une version fixe de wrangler** · Décidé (4 novembre 2026)
wrangler est ajouté aux `devDependencies` de `package.json`, à une version exacte. Tout le déploiement repose sur lui (bloc `build`, `wrangler preview` en bêta) : une nouvelle version téléchargée d’office par `npx` pourrait casser la production sans qu’on ait rien changé. On le met à jour quand on le décide, dans une branche testée.
Alternative écartée : laisser `npx` télécharger la dernière version à chaque déploiement.

**D17 · La frise est l’accueil du site, à `/`, pour de bon** · Décidé (6 novembre 2026)
La frise reste à `/` et y restera : c’est l’originalité du site et sa porte d’entrée. Les lecteurs venus de Google arrivent surtout sur les pages des fiches, qui renvoient à la frise. Remplace D14, qui laissait la question ouverte jusqu’à la phase 3.
Alternative écartée : un accueil éditorial à `/` et la frise à `/frise/` (option B de Lovable, série 3). Elle reste possible plus tard à faible coût : sur le nouvel accueil, un script renverrait les anciens liens `/#id` vers `/frise/#id`. Rien ne la prévoit.

**D18 · Cinq rubriques ; une entrée du menu, un dossier d’adresse** · Décidé (6 novembre 2026), précisé par D55
Le menu commun à tout le site : Frise, Mouvements, Artistes, Œuvres, Comprendre. À propos et le plan du site sont dans le pied de page. Chaque entrée du menu est un dossier d’adresse (`/mouvements/`, `/artistes/`, `/oeuvres/`, `/comprendre/`), avec un répertoire, puis une page par élément. Les adresses sont rangées par type, jamais imbriquées (`/oeuvres/guernica/`, et non `/artistes/picasso/guernica/`), et reprennent les identifiants actuels (D9). Une rubrique n’apparaît dans le menu qu’avec son contenu.
Alternatives écartées : des adresses imbriquées, qui obligeraient à choisir un seul parent pour une œuvre ou un artiste ; une rubrique à la racine pour chaque contenu pédagogique (`/reviser/`, `/glossaire/`…), qui porterait le menu à neuf entrées.

**D19 · Un outil, une question ; un texte, une page** · Décidé (6 novembre 2026)
Chaque partie du site répond à une seule question. La frise : quand, et qu’est-ce qui se passe en même temps ? La vue mouvement de la frise : qui, et dans quel ordre, au sein d’un mouvement ? Les pages : quoi, et pourquoi ? Comprendre : comment tout cela s’articule ? Rechercher : où est telle chose ?
Un texte rédigé n’a qu’une page. Ailleurs, il apparaît en une phrase avec un lien. Les listes (œuvres d’un artiste, artistes d’un mouvement…) sont toujours calculées depuis le tableur, jamais écrites à la main. Pendant la phase 2, le panneau de la frise garde le texte complet de la fiche et reçoit un lien « Lire la page » : c’est de l’affichage en double, pas de l’écriture en double (D15). Son passage à une fiche courte sera décidé après la phase 2.

**D20 · Comprendre : Parcours, Comparer, Glossaire** · Décidé (6 novembre 2026), précisé par D47
La rubrique Comprendre (`/comprendre/`) a trois sous-rubriques : Parcours (`/comprendre/parcours/<id>/`), Comparer (`/comprendre/comparer/<id>/`) et Glossaire (`/comprendre/glossaire/`, une ancre par terme). Elle ne recopie aucune fiche : elle les relie. Les manifestes et écrits d’artistes restent des œuvres du tableur (type « texte ») : ils sont dans le catalogue des œuvres, avec un filtre « Écrits et manifestes », et dans les « Moments clés » de leur mouvement. Les comparaisons portent d’abord sur deux mouvements ; le format reste ouvert aux artistes et aux œuvres.
Alternative écartée : une sous-rubrique « Textes », qui aurait créé une seconde fiche pour des œuvres existantes et serait devenue un fourre-tout.

**D21 · Trois niveaux pour une œuvre ; une page dès qu’elle est documentée** · Décidé (6 novembre 2026)
Une œuvre est référencée (sa ligne dans le tableur, sans texte), documentée (une présentation courte) ou analysée (une analyse complète en plus). Elle a sa page dès qu’elle est documentée : c’est ainsi que D13 s’applique aux œuvres. Le niveau se déduit de la fiche, sans colonne dans le tableur, pour qu’il ne puisse pas contredire le texte. Le gabarit des fiches œuvres a deux parties : la présentation, puis l’analyse.
Alternative écartée : attendre l’analyse complète pour publier la page, ce qui laissait les 104 œuvres sans page.

**D22 · Les sous-courants sont des groupes, sans couloir dans la frise** · Décidé (6 novembre 2026)
Les sous-courants et les groupes (Die Brücke, Der Blaue Reiter, néo-impressionnisme…) entrent dans un nouvel onglet « Groupes » du tableur, rattachés à un mouvement. Ils sont des sections de la page de leur mouvement, avec une ancre (`/mouvements/expressionnisme/#die-brucke`). Ils n’ont pas de couloir dans la frise et pas de page propre ; un groupe gagnera sa page s’il reçoit un jour un texte développé. Le néo-impressionnisme devient un groupe du post-impressionnisme, et non un dixième mouvement.
Alternative écartée : traiter les sous-courants comme des mouvements, ce qui aurait ajouté des couloirs à la frise.

**D23 · Des relations typées, en nombre fermé** · Décidé (6 novembre 2026)
L’onglet « Influences » devient l’onglet de toutes les relations : ses colonnes De et Vers acceptent un artiste ou un mouvement. Les types forment une liste fermée : influence (d’un artiste vers un artiste ou un mouvement), collaboration et rivalité (entre deux artistes), filiation et réaction (entre deux mouvements). construire.py vérifie que chaque type relie les bons genres d’éléments. On garde « Mouvement principal » et « Autres mouvements » : la frise a besoin d’un couloir principal par artiste. On ne stocke pas ce qui se calcule : « contemporain » vient des dates, la période d’un artiste dans un mouvement vient des dates de ses œuvres.
Repoussé : les attributions discutées et les périodes d’activité écrites dans le tableur.

**D24 · Glossaire, parcours et comparaisons passent par les sources habituelles** · Décidé (6 novembre 2026)
Le glossaire est un onglet du tableur (ID, Terme, Définition, Voir aussi). Les fiches y renvoient avec la syntaxe habituelle, `[[ready-made]]`, vérifiée par construire.py ; aucun lien n’est ajouté automatiquement sur les mots. Les parcours et les comparaisons sont des fiches Markdown, dans `fiches/parcours/` et `fiches/comparaisons/`, dont l’en-tête YAML liste les étapes ou les deux éléments comparés. Tous ces identifiants partagent l’espace unique des autres (D9). construire.py reste la seule porte d’entrée (D7).

**D25 · La frise est l’affiche, les pages sont le livre** · Décidé (6 novembre 2026), précisé par D37
La frise et les outils de navigation gardent le thème « Affiche » (D3) : bordures épaisses, ombres décalées, couleurs des mouvements en aplats. Les pages de lecture sont pensées comme un ouvrage édité : la typographie porte la hiérarchie, les traits sont des filets plutôt que des cadres, on laisse respirer le texte, et les cartes sont réservées aux œuvres. Les couleurs des mouvements y servent de repères, pas de décor. Les pages mouvement, artiste et œuvre ont chacune leur composition, et partagent leurs composants. La page œuvre est pensée comme un cartel, complète sans image ; la reproduction s’y ajoute quand les droits le permettent (D10).
Alternative écartée : adoucir bordures et ombres sur tout le site, frise comprise, ce qui changeait l’identité visuelle.

**D26 · Ouverture au public sans la rubrique Comprendre** · Décidé (6 novembre 2026)
À l’ouverture (phase 3), le site comprend : la frise, les pages mouvements et artistes, le catalogue des 104 œuvres avec les pages des œuvres documentées, la recherche, À propos, les mentions légales et le référencement de base. Comprendre ouvre ensuite (phase 4), avec au moins un parcours, une comparaison et un glossaire de départ.
Alternative écartée : ouvrir avec cinq rubriques dont deux presque vides.

**D27 · « Voir dans la frise » mène au bon niveau** · Décidé (6 novembre 2026)
Depuis une page mouvement, le lien ouvre la vue mouvement de la frise. Depuis un artiste, une œuvre ou un événement, il ouvre la frise centrée sur l’élément, avec sa fiche, comme aujourd’hui avec `/#id`. La vue mouvement reçoit donc une adresse ; sa forme est à choisir à l’étape 2e.

**D28 · Un seul outil « Rechercher »** · Décidé (6 novembre 2026), précisé par D53
La recherche de l’en-tête et l’Index sont réunis en un seul tiroir : un champ, puis les onglets Mouvements, Artistes et Œuvres. Le menu mène aux rubriques, « Rechercher » trouve un élément. L’outil est présent sur tout le site. Comme il touche l’en-tête de la frise, il se fait au lot 4, avec l’ajout du menu du site dans cet en-tête.
Alternative écartée : garder trois façons de chercher la même chose (le menu, l’Index et la recherche).

**D29 · La frise reçoit ses données par un import du JSON** · Décidé (6 novembre 2026)
`src/scripts/app.js` commence par `import D from "../data/frise.json";`. Au build, Vite intègre les données au script publié dans `dist/_astro/`, sous un nom qui change à chaque modification : un navigateur ne peut plus garder en cache d’anciennes données. Les pages, elles, liront le même fichier au build, dans leur en-tête Astro.
Alternatives écartées : injecter les données dans la page (`<script type="application/json">`, puis `JSON.parse`), qui ajoute des pièces et oblige à échapper un éventuel `</script>` dans les fiches ; `define:vars`, qui rend le script `is:inline` et lui interdit tout import (contraire à D15).

**D30 · marked est une dépendance npm, en version exacte** · Décidé (6 novembre 2026)
marked, qui convertit le Markdown des fiches, est installé par npm en version exacte (18.0.14) et importé par `src/lib/fiches.js`. Vite l’intègre au script de la frise, et les pages pourront s’en servir au build : c’est la condition pour que `src/lib/` serve aux deux (D15). Le site ne charge plus rien depuis jsDelivr. On le met à jour quand on le décide, dans une branche testée, comme wrangler (D16).
Alternative écartée : garder le CDN, qui ne permettait pas de fabriquer le contenu des fiches au build.

**D31 · app.js n’est découpé qu’en ce qui sert aux pages** · Décidé (6 novembre 2026)
Seuls le contenu des fiches et ses outils sont sortis d’app.js, dans `src/lib/` (`donnees.js`, `outils.js`, `fiches.js`). La navigation, les vues, la recherche, l’Index et le thème restent dans `src/scripts/app.js`, rangés par sections. Rien dans la suite n’a besoin de ce découpage, et c’en était la partie la plus risquée : ordre d’exécution des modules, imports oubliés, imports circulaires. Un fichier déjà connu est plus simple à retoucher qu’une dizaine de modules qui s’importent les uns les autres. Le découpage pourra être repris plus tard, par exemple au lot 4, si app.js devient difficile à lire.
Alternative écartée : découper app.js en huit modules (état, vues, frise, panneau, survol, en-tête, recherche, thème).

**D32 · construire.py reste un seul fichier** · Décidé (6 novembre 2026)
Les nouvelles lectures et vérifications de l’étape 2d s’ajoutent à construire.py, qui garde une fonction de lecture par onglet. Ces fonctions font déjà leurs propres vérifications : les séparer obligerait à faire circuler `avertir()` et les identifiants entre deux fichiers. C’est le même raisonnement que D31. À reconsidérer si le fichier devient difficile à lire.
Alternative écartée : séparer la lecture et les vérifications en deux fichiers Python.

**D33 · Les cartes de la frise restent placées à l’année, sans la date affichée** · Décidé (6 novembre 2026)
Une carte est placée sur la frise à son année (colonne « Année ») et n’affiche pas la date affichée, qui reste dans sa fiche.
Alternative écartée : afficher la date affichée sur les cartes de la frise.

**D34 · Le titre « ## Analyse » marque l’analyse d’une fiche œuvre** · Décidé (6 novembre 2026)
Dans une fiche œuvre, ce qui précède `## Analyse` est la présentation ; ce qui suit est l’analyse, dont les rubriques sont des titres `###`. construire.py en déduit le niveau de l’œuvre (D21), écrit dans la clé `niveau` de frise.json : `referencee` (fiche vide), `documentee` ou `analysee`. Une analyse sans vrai texte est retirée du Markdown, pour que son titre ne s’affiche pas seul. Ce titre ne se renomme plus une fois des fiches écrites.
Alternative écartée : une colonne « Niveau » dans le tableur, qui pourrait contredire le texte (D21).

**D35 · L’en-tête des parcours et des comparaisons se lit sans librairie YAML** · Décidé (6 novembre 2026)
Les identifiants reliés s’écrivent sur une seule ligne, séparés par des virgules, comme dans le tableur : `etapes: impressionnisme, monet` ou `elements: fauvisme, expressionnisme`. construire.py lit lui-même ces lignes `clé: valeur`. Le format reste du YAML valide. L’identifiant est le nom du fichier et doit être celui de l’en-tête ; le titre est la ligne `#`.
Alternative écartée : PyYAML, une dépendance de plus pour quatre champs.

**D36 · Une comparaison relie deux éléments du même genre** · Décidé (6 novembre 2026)
Deux mouvements, deux artistes ou deux œuvres, jamais un mélange : cela précise D20. Un parcours a au moins deux étapes, de n’importe quel type. Le « Voir aussi » d’un terme du glossaire peut viser n’importe quel identifiant.

**D37 · Des cartes aussi pour le répertoire des mouvements** · Décidé (7 novembre 2026)
Sur les pages, les cartes sont réservées aux œuvres et au répertoire `/mouvements/`. Ce répertoire sert à choisir où aller, pas à lire : il tient de l’affiche, et les cartes y montrent les couleurs des mouvements. Les artistes restent en listes, partout. Précise D25.
Alternative écartée : une liste à liseré coloré, comme pour les artistes.

**D38 · La vue mouvement a pour adresse `/?mouvement=<id>`** · Décidé (7 novembre 2026)
C’est l’adresse que vise « Voir dans la frise » depuis une page mouvement (D27). Le `#` reste réservé à la fiche ouverte, et les deux se combinent : `/?mouvement=fauvisme#matisse`. Elle est en place depuis la sous-étape 4 de l’étape 2e (7 novembre 2026), et l’adresse suit la vue (D43).
Alternative écartée : `/#vue-<id>`, qui donnerait deux sens au même `#` et empêcherait d’y ajouter une fiche.

**D39 · Les pages ont leur feuille de style, `public/pages.css`** · Décidé (7 novembre 2026), précisé par D44
Chargée après style.css, elle en reprend les variables, donc le mode sombre ; la frise ne change pas d’une ligne. `body.page` annule la mise en page de la frise (hauteur de l’écran, défilement bloqué). Les classes de style.css servent à la frise : une classe des pages ne reprend jamais leurs noms (`.carte-page`, et non `.carte`).
Alternatives écartées : tout mettre dans style.css, qui mélangerait l’affiche et le livre (D25) ; sortir les variables dans un fichier commun, ce qui déplacerait du code de la frise. À reconsidérer si pages.css doit annuler d’autres règles de style.css.

**D40 · Une seule fonction décide de l’adresse d’un élément** · Décidé (7 novembre 2026)
`aUnePage()` et `adresse()`, dans `src/lib/fiches.js`, disent si un élément a une page et où mène un lien vers lui :
- `/mouvements/<id>/` ou `/artistes/<id>/` si sa fiche est rédigée, `/oeuvres/<id>/` si l’œuvre est documentée (depuis l’étape 2f) ;
- `/mouvements/<mouvement>/#<groupe>` pour un groupe, ou `/#<mouvement>` si ce mouvement n’a pas de page ;
- `/#id` dans tous les autres cas.

Les pages, les composants `Ligne` et `Carte`, et `markdown(md, true)` s’appuient sur elles. Le panneau de la frise garde ses liens `#id`, sauf pour un groupe : il mène à la page de son mouvement, ou, si ce mouvement n’a pas de page, à sa fiche dans le panneau (`destinationPanneau()`).
Alternative écartée : un fichier `src/lib/adresses.js`, un module de plus pour deux fonctions.

**D41 · « En une phrase » reste sur les pages ; la page artiste n’a pas de chapeau** · Décidé (7 novembre 2026)
Sur une page mouvement, le chapeau (le résumé du tableur) est une accroche, et la section « En une phrase » une définition : les deux restent. Sur une page artiste, la phrase-clé du tableur répétait presque la section : le chapeau est retiré, et la phrase-clé reste dans les bulles de la frise.
Alternatives écartées : retirer la section des pages d’artistes ; tout garder, au prix d’une répétition.

**D42 · Un filtre par nom dans le répertoire des artistes** · Décidé (7 novembre 2026), à revoir au lot 4
Le répertoire `/artistes/` a un champ qui filtre la liste par nom, sans tenir compte des accents. C’est le premier script des pages, écrit en amélioration progressive : sans JavaScript, le champ reste caché et la page montre tous les artistes. Il ajoute une façon de chercher à côté de celles que D28 veut réunir : au lot 4, décider s’il reste à côté de « Rechercher ».
Alternative écartée : attendre « Rechercher » (lot 4).

**D43 · L’adresse de la frise suit la vue, sans ajouter d’étape à l’historique** · Décidé (7 novembre 2026)
`changerVue()` tient l’adresse à jour avec `history.replaceState`, comme `ouvrir()` le fait pour la fiche : `?mouvement=<id>` en vue mouvement, rien dans les autres vues, et le `#id` de la fiche ouverte est gardé. Un rechargement retrouve ce qui était affiché, et un `?mouvement=` ne survit pas au retour à la frise. Un `?mouvement=` inconnu est retiré de l’adresse au démarrage. Le bouton « Précédent » quitte donc la frise au lieu de revenir à la vue précédente.
Alternative écartée : `pushState`, qui demandait d’écouter `popstate` et de redessiner la bonne vue, pour un gain modeste.

**D44 · Un élément commun à la frise et aux pages garde ses classes** · Décidé (7 novembre 2026)
Un morceau de HTML fabriqué par `src/lib/` pour le panneau et pour les pages (l’encart d’une œuvre protégée, `.encart-droits`), ou un élément de la frise réutilisé tel quel sur une page (la fenêtre d’image `.visionneuse`), garde le même nom de classe des deux côtés. style.css range ses règles sous `.fiche`, pages.css sous `.texte` ; une classe déjà complète dans style.css (`.visionneuse`) n’est pas redéfinie dans pages.css. Le texte n’existe ainsi qu’en un exemplaire (D15). Précise D39, dont la règle reste vraie pour tout le reste.
Alternative écartée : un composant Astro avec ses propres classes, qui aurait recopié la phrase de l’encart.

**D45 · La page œuvre est un cartel, sans sommaire** · Décidé (7 novembre 2026)
La page œuvre n’utilise pas le composant `Lecture` : le texte d’une œuvre est court et se lit d’un trait. Sous l’en-tête (technique, date, « Œuvre majeure », titre, « Voir dans la frise »), une seule colonne : l’image et sa légende de cartel, ou l’encart des droits (D10), les rubriques Artiste, Mouvement et Où la voir entre deux filets, la présentation, l’analyse, puis « Du même artiste », en cartes avec une vignette. Un clic sur l’image l’ouvre en grand dans un `<dialog>`, en amélioration progressive. La composition s’inspire d’une maquette Lovable.
Alternative écartée : les sections repliables de cette maquette, qui cachaient le texte derrière des clics.

**D46 · src/lib/fiches.js reste un seul fichier** · Décidé (8 novembre 2026)
Il réunit les adresses et le texte des fiches, partagés avec les pages (`aUnePage`, `adresse`, `lien`, `markdown`, `textePage`, `encartDroits`), et les fiches du panneau de la frise. Les séparer obligerait les secondes à importer les premiers, avec les risques écartés par D31 et D32, pour un fichier d’environ 210 lignes qui reste lisible. À reconsidérer s’il dépasse 300 lignes.
Alternative écartée : trois modules, `adresses.js`, `texte.js` et `panneau.js` (D40 écartait déjà `adresses.js`).

**D47 · Le catalogue a une recherche, un choix du mouvement et la case « Œuvres majeures »** · Décidé (8 novembre 2026), à revoir au lot 4
Le catalogue `/oeuvres/` range les œuvres par mouvement, en cartes avec vignette. Son champ cherche dans le titre et le nom de l’artiste, sans tenir compte des accents ; une liste choisit le mouvement, une case garde les œuvres majeures. Le script est en amélioration progressive : sans JavaScript, la zone reste cachée et la page montre toutes les œuvres. Le filtre « Écrits et manifestes » prévu par D20 est écarté : il servait peu, et les écrits restent dans le catalogue, trouvables par la recherche, et dans les « Moments clés ». Comme le filtre des artistes (D42), cette recherche s’ajoute aux façons de chercher que D28 veut réunir : au lot 4, décider si elle reste à côté de « Rechercher ».
Alternative écartée : les seuls filtres de la roadmap, sans recherche.

**D48 · L’adresse du site est écrite une seule fois, dans `site`** · Décidé (8 novembre 2026)
Le champ `site` d’`astro.config.mjs` donne l’adresse de production : les adresses canoniques, le sitemap et robots.txt en dérivent. Jusqu’à la phase 3, c’est l’adresse provisoire en workers.dev, et les moteurs de recherche peuvent déjà explorer le site. La canonique d’une page est toujours sur la production et avec la barre finale, comme les liens d’`adresse()` : une version de test ou une adresse tapée sans barre renvoie à la même page. Toutes les vues de la frise (`/?mouvement=<id>`) déclarent `/`. En phase 3, le .com remplace cette seule ligne.
Alternative écartée : interdire l’exploration dans robots.txt jusqu’à la phase 3, ce qui faisait une chose de plus à ne pas oublier au lancement.

**D49 · sitemap.xml et robots.txt sont fabriqués par Astro, sans dépendance** · Décidé (8 novembre 2026)
Ce sont deux « endpoints » statiques de `src/pages/` (`sitemap.xml.js`, `robots.txt.js`), appelés au build. Le sitemap liste les pages fixes (`PAGES_FIXES`, tenue à la main), puis les mouvements, artistes et œuvres dont `aUnePage()` est vrai, à l’adresse que donne `adresse()` (D40) : il ne peut pas contredire les liens du site. Les groupes, sections d’une page, n’y sont pas. Pas de date de modification (`<lastmod>`), qu’on ne connaît pas.
Alternatives écartées : `@astrojs/sitemap`, une dépendance pour une liste qu’on a déjà ; un robots.txt dans `public/`, qui recopierait l’adresse du site à la main.

**D50 · Un contrôle des liens de `dist/`, en Node, sans dépendance, lancé à la main** · Décidé (8 novembre 2026)
`verifier_dist.mjs`, à la racine, lancé par `npm run verifier` après `npm run build`, avant chaque fusion dans `main`. Dans chaque page de `dist/`, il vérifie que chaque lien interne mène à un fichier, que chaque ancre correspond à un `id` de la page visée (sauf vers la frise, qui fabrique ses fiches en JavaScript), et que les pages et le sitemap concordent. Il lit le HTML par des expressions régulières, ce qui suffit pour le HTML régulier qu’écrit Astro. Il complète construire.py, qui vérifie les liens des fiches à la source.
Alternatives écartées : un outil tout fait (linkinator, lychee), qui est une dépendance ; un script Python, alors que `dist/` est le monde de Node ; le lancer à chaque déploiement Cloudflare, où un lien cassé bloquerait la mise en production (à reconsidérer).

**D51 · Jusqu’au lot 4, la frise mène aux pages par l’Index** · Décidé (8 novembre 2026)
La frise n’a ni menu ni pied de page avant le lot 4 (D18, D28). En attendant, l’Index reçoit une ligne « Pages du site » : Mouvements, Artistes, Œuvres, À propos et Plan du site, de vrais liens que le panneau n’intercepte pas. Elle est placée sous « Comment lire la frise », et non en bas, où elle serait passée après les 104 œuvres de l’onglet Œuvres. Elle est provisoire : on la retire quand la frise reçoit le menu et le pied de page.
Alternatives écartées : un pied de page dans la frise, qui touchait sa mise en page avant le lot 4 ; attendre le lot 4, en laissant la frise sans chemin vers les répertoires.

**D52 · Les pages fixes sont écrites dans leur gabarit ; le plan du site est calculé** · Décidé (8 novembre 2026)
À propos et le plan du site sont deux fichiers de `src/pages/`. Le texte d’À propos est écrit dans `a-propos.astro`, en une colonne de lecture centrée, et l’adresse de contact dans une seule constante (`COURRIEL`). Le plan du site liste les pages fixes, puis les mouvements, artistes et œuvres dont `aUnePage()` est vrai, à l’adresse que donne `adresse()`, comme le sitemap (D40, D49) : il ne peut pas contredire les liens du site. Une rubrique vide (les œuvres, tant qu’aucune n’est documentée) n’y est pas affichée. Le pied de page qui mène à ces deux pages n’est que sur les pages jusqu’au lot 4 (D51).
Alternatives écartées : une fiche Markdown pour À propos, qui ferait passer une page unique par construire.py ; un onglet « Pages » dans le tableur, prématuré pour deux textes ; un plan du site écrit à la main, qui se décalerait des pages.

**D53 · L’outil unique s’appelle « Index » et s’ouvre dans le panneau** · Décidé (9 novembre 2026)
L’outil de D28 garde le nom de l’Index actuel et reste dans le panneau latéral des fiches : la frise reste visible à côté, et il garde la croix, la poignée, la touche Échap et « Retour à l’index ». Le champ de recherche de l’en-tête disparaît, et celui de l’Index devient la seule recherche de la frise. Le bouton porte une loupe et le seul mot « Index », sans phrase d’aide ; pour qu’on comprenne qu’on peut y taper un nom, le curseur se place dans le champ à l’ouverture, sur ordinateur. Les onglets restent Mouvements, Artistes et Œuvres : les événements, que l’ancien champ trouvait, ne sont plus cherchables et restent visibles dans la frise. À la fermeture, le focus revient au bouton. Sur les pages, le bouton mène à la frise avec l’Index ouvert, à l’adresse `/#index`. L’affichage des résultats pendant la frappe se fixe à l’étape 4c. Précise D28.
Alternatives écartées : le nom « Rechercher » ; l’Index dans un `<dialog>` au milieu de l’écran, qui cachait la frise ; un onglet « Événements » ; un vrai panneau sur chaque page, qui demandait les données et un script partout (à reconsidérer).

**D54 · L’en-tête de la frise tient en deux lignes** · Décidé (9 novembre 2026)
Ligne 1, commune à tout le site : le titre en version compacte (celle des pages), le menu, puis à droite « Au hasard », le bouton Index, avec l’ombre décalée de l’outil principal, et le bouton de thème. Ligne 2, sur la frise seulement : les neuf mouvements en boutons encadrés avec leur pastille de couleur, sur une seule ligne, alignés à gauche ; en vue mouvement, le bouton du mouvement ouvert reste rempli de sa couleur. Pour tenir, les boutons sont resserrés (pastille de 12 px, police à la largeur 76 au lieu de 85, marges réduites) : mesurée avec Archivo, la ligne passe de 1 288 à 1 144 px et tient dès une fenêtre d’environ 1 220 px ; en dessous, le texte passe à 0,84 rem, puis la ligne défile horizontalement. La case « Œuvres majeures seulement » devient un bouton étoile dans la barre du bas, à côté des zooms : le haut sert à choisir où aller, le bas à choisir comment regarder. Sur téléphone, le titre puis trois boutons (au hasard, Index, ☰), le ☰ réunissant les rubriques, À propos, le plan du site et le thème. L’en-tête passe d’environ 175 à 105 px sur ordinateur, et le thème « Affiche » est gardé (D3, D25). Une maquette cliquable, publiée sur claude.ai et gardée hors du dépôt, sert de référence.
Alternatives écartées : une barre continue où chaque mouvement est souligné de sa couleur ; des cases entièrement colorées, trop chargées au-dessus des bandes et peu lisibles en blanc sur certains tons ; un champ de recherche toujours ouvert ; un bouton Index avec une phrase d’aide (« un artiste, une œuvre… ») ; la case « Œuvres majeures » au bout de la ligne des mouvements, où elle ne tient pas sous 1 440 px environ.

**D55 · À propos entre dans l’en-tête, à l’écart des rubriques** · Décidé (9 novembre 2026)
Le lien « À propos » suit les rubriques dans le menu de l’en-tête, décalé et un peu moins gras, pour qu’on voie qu’il n’en est pas une. La frise, page d’accueil, n’a pas de pied de page, puisqu’elle occupe tout l’écran : sans ce lien, À propos n’y serait trouvable qu’au bas de l’Index, alors que savoir qui écrit et d’où viennent les informations compte pour un site pédagogique. Le plan du site reste au pied des pages et, sur la frise, au bas de l’Index. Sur téléphone, les deux sont dans le ☰. Précise D18, dont le menu reste celui des rubriques.
Alternative écartée : À propos seulement au pied des pages et au bas de l’Index.

**D56 · La frise s’ouvre au zoom « Intermédiaire », nouveau nom de « Moyen »** · Décidé (9 novembre 2026)
Au chargement, la vue détail s’affiche au zoom intermédiaire : sur un écran de 1 440 px, environ 43 ans au lieu de 18 en Détail, de l’impressionnisme au cubisme d’un seul regard, ce qui est la question de la frise (D19). Le bouton « Moyen » devient « Intermédiaire ». Seul le libellé change : les noms du code (`etat.moyen`, `data-niveau="moyen"`, `zoomMoyen()`, `pxParAnMoyen`, `largeurCarteMoyenne`) restent, pour ne pas toucher une quinzaine de lignes sans effet visible. Une fiche ouverte depuis un lien, l’Index ou « Au hasard » est placée à une distance du bord gauche comptée en pixels, celle de 3 ans au zoom Détail, et non plus en années : le comportement en Détail est inchangé. Tranche la question de la ROADMAP sur l’ouverture au zoom Ensemble.
Alternatives écartées : ouvrir sur Ensemble ou sur Détail ; renommer aussi les noms du code ; centrer la carte au milieu de l’espace visible, qui dépend de la largeur du panneau et changeait le comportement en Détail.

**D57 · L’en-tête des pages est un composant, avec une seule liste des pages fixes et un thème partagé** · Décidé (9 novembre 2026)
Les pages fixes (la frise, les trois répertoires, À propos, le plan du site) sont écrites une seule fois, dans `src/lib/site.js` (`PAGES_FIXES`) : le menu, le pied de page, la ligne « Pages du site » de l’Index, le plan du site et le sitemap en prennent chacun une partie avec `filter()`. L’en-tête des pages est le composant `EnteteSite.astro` : le titre, le menu (les rubriques, puis À propos, marqué `aPart`, à l’écart et un peu moins gras, D55) et le bouton de thème, au bout de la ligne. Le thème passe par deux composants communs à la frise et aux pages : `ScriptTheme.astro`, le script `is:inline` du `<head>`, qui doit s’exécuter avant l’affichage, et `BoutonTheme.astro`, le bouton, dont le script `src/scripts/theme.js` est un module traité par Astro. app.js ne gère plus le thème. « Au hasard » et le bouton Index n’entrent dans l’en-tête des pages qu’à l’étape 4c, avec l’adresse `/#index` ; la frise prend `EnteteSite` à l’étape 4d. Précise D18 et D55.
Alternatives écartées : un onglet « Pages » dans le tableur, trop lourd pour six lignes qui changent rarement ; `define:vars`, pour écrire la clé `theme` une seule fois, dont le fonctionnement avec un script inline n’était pas sûr (la clé reste écrite à deux endroits) ; À propos dans une seconde `<nav>`, deux menus à annoncer pour un seul lien.