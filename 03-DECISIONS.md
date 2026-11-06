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

**D10 · N’afficher que des images dont les droits le permettent** · Proposé, reporté (novembre 2026)
On s’appuie sur la colonne « Domaine public ? » du tableur. Une œuvre encore protégée s’affiche sans image.
À trancher avant d’ajouter d’autres images.

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

**D18 · Cinq rubriques ; une entrée du menu, un dossier d’adresse** · Décidé (6 novembre 2026)
Le menu commun à tout le site : Frise, Mouvements, Artistes, Œuvres, Comprendre. À propos et le plan du site sont dans le pied de page. Chaque entrée du menu est un dossier d’adresse (`/mouvements/`, `/artistes/`, `/oeuvres/`, `/comprendre/`), avec un répertoire, puis une page par élément. Les adresses sont rangées par type, jamais imbriquées (`/oeuvres/guernica/`, et non `/artistes/picasso/guernica/`), et reprennent les identifiants actuels (D9). Une rubrique n’apparaît dans le menu qu’avec son contenu.
Alternatives écartées : des adresses imbriquées, qui obligeraient à choisir un seul parent pour une œuvre ou un artiste ; une rubrique à la racine pour chaque contenu pédagogique (`/reviser/`, `/glossaire/`…), qui porterait le menu à neuf entrées.

**D19 · Un outil, une question ; un texte, une page** · Décidé (6 novembre 2026)
Chaque partie du site répond à une seule question. La frise : quand, et qu’est-ce qui se passe en même temps ? La vue mouvement de la frise : qui, et dans quel ordre, au sein d’un mouvement ? Les pages : quoi, et pourquoi ? Comprendre : comment tout cela s’articule ? Rechercher : où est telle chose ?
Un texte rédigé n’a qu’une page. Ailleurs, il apparaît en une phrase avec un lien. Les listes (œuvres d’un artiste, artistes d’un mouvement…) sont toujours calculées depuis le tableur, jamais écrites à la main. Pendant la phase 2, le panneau de la frise garde le texte complet de la fiche et reçoit un lien « Lire la page » : c’est de l’affichage en double, pas de l’écriture en double (D15). Son passage à une fiche courte sera décidé après la phase 2.

**D20 · Comprendre : Parcours, Comparer, Glossaire** · Décidé (6 novembre 2026)
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

**D25 · La frise est l’affiche, les pages sont le livre** · Décidé (6 novembre 2026)
La frise et les outils de navigation gardent le thème « Affiche » (D3) : bordures épaisses, ombres décalées, couleurs des mouvements en aplats. Les pages de lecture sont pensées comme un ouvrage édité : la typographie porte la hiérarchie, les traits sont des filets plutôt que des cadres, on laisse respirer le texte, et les cartes sont réservées aux œuvres. Les couleurs des mouvements y servent de repères, pas de décor. Les pages mouvement, artiste et œuvre ont chacune leur composition, et partagent leurs composants. La page œuvre est pensée comme un cartel, complète sans image ; la reproduction s’y ajoute quand les droits le permettent (D10).
Alternative écartée : adoucir bordures et ombres sur tout le site, frise comprise, ce qui changeait l’identité visuelle.

**D26 · Ouverture au public sans la rubrique Comprendre** · Décidé (6 novembre 2026)
À l’ouverture (phase 3), le site comprend : la frise, les pages mouvements et artistes, le catalogue des 104 œuvres avec les pages des œuvres documentées, la recherche, À propos, les mentions légales et le référencement de base. Comprendre ouvre ensuite (phase 4), avec au moins un parcours, une comparaison et un glossaire de départ.
Alternative écartée : ouvrir avec cinq rubriques dont deux presque vides.

**D27 · « Voir dans la frise » mène au bon niveau** · Décidé (6 novembre 2026)
Depuis une page mouvement, le lien ouvre la vue mouvement de la frise. Depuis un artiste, une œuvre ou un événement, il ouvre la frise centrée sur l’élément, avec sa fiche, comme aujourd’hui avec `/#id`. La vue mouvement reçoit donc une adresse ; sa forme est à choisir à l’étape 2e.

**D28 · Un seul outil « Rechercher »** · Décidé (6 novembre 2026)
La recherche de l’en-tête et l’Index sont réunis en un seul tiroir : un champ, puis les onglets Mouvements, Artistes et Œuvres. Le menu mène aux rubriques, « Rechercher » trouve un élément. L’outil est présent sur tout le site. Comme il touche l’en-tête de la frise, il se fait au lot 4, avec l’ajout du menu du site dans cet en-tête.
Alternative écartée : garder trois façons de chercher la même chose (le menu, l’Index et la recherche).
