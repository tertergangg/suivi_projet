# Conventions

Ces règles décrivent le style déjà en place dans le code. Le nouveau code doit leur ressembler.

## Langue
- Les noms de variables, de fonctions et de classes CSS sont en français, sans accents : `dessiner`, `oeuvres`, `REGLAGES`, `.panneau`.
- Les commentaires sont en français.
- Les mots imposés par un outil restent en anglais : Astro, npm, les noms de propriétés HTML et CSS.

## JavaScript
- JavaScript moderne et sans framework : `const` par défaut, `let` si la valeur change, jamais `var`.
- Guillemets doubles, points-virgules, indentation de deux espaces.
- Des fonctions courtes qui font une seule chose, avec un nom qui dit ce qu’elles font.
- Pas de nouveau nombre magique : un réglage qu’on peut vouloir modifier va dans `REGLAGES`, une valeur fixe dans une constante nommée. Le code existant en contient encore (largeurs sur téléphone, pas des flèches du clavier…) : on nomme une valeur quand on la touche.
- Un commentaire au-dessus de chaque bloc dont l’intention n’est pas évidente. On explique pourquoi, pas ce que dit déjà le code.
- Tout texte venant des données et inséré dans du HTML passe par `esc()`.

## CSS
- Les couleurs, polices et tailles passent par les variables de `:root`. Chaque nouvelle couleur a sa valeur pour le mode sombre.
- Exceptions voulues, identiques dans les deux modes :
  - le blanc (`#FFFFFF` et ses transparences) et les fonds noirs translucides posés sur les couleurs des mouvements ;
  - les lignes `--grille-bande`, tracées par-dessus ces couleurs ;
  - le voile de l’image en grand, écrit en dur car `::backdrop` ne voit pas toujours les variables.
- Pas de style écrit en dur dans index.astro (ni, plus tard, dans les autres gabarits). Dans app.js, l’attribut `style` ne sert qu’aux valeurs calculées : positions et largeurs (`left`, `width`) et couleur du mouvement (`--c`). Tout le reste va dans style.css.
- Une nouvelle règle va dans la section de son élément, et sa version téléphone dans le bloc `@media (max-width: 720px)` de cette section.
- On garde le focus visible et le respect de `prefers-reduced-motion`.
- La frise est l’affiche, les pages sont le livre (D25). La frise et les outils de navigation gardent bordures épaisses et ombres décalées. Sur les pages de lecture, les traits sont des filets, la typographie porte la hiérarchie, et les cartes sont réservées aux œuvres. Une page doit rester complète sans image.

## Python (construire.py)
- Indentation de quatre espaces, noms en français.
- Toute nouvelle règle de cohérence passe par `avertir()`, pour être affichée avec les autres.

## Identifiants
- En minuscules, sans accents, avec des tirets : `van-gogh`, `les-demoiselles-d-avignon`.
- L’identifiant est le même dans le tableur (colonne ID), dans le nom de la fiche (`<id>.md`) et dans l’adresse de la page.
- Il est unique sur tout le site, tous types confondus : mouvements, artistes, œuvres, événements, groupes, termes du glossaire, parcours et comparaisons.
- Une œuvre reprend son titre complet, jamais coupé (`un-dimanche-apres-midi-a-l-ile-de-la-grande-jatte`) ; « œ » s’écrit `oe` (`l-oeil-cacodylate`). Un artiste et un événement ont un identifiant court (`monet`, `cabaret-voltaire`).
- On ne le change plus une fois le site public (D9).

## Fiches
- Un en-tête YAML avec `id` et `type`, puis un titre `#`, puis les rubriques du gabarit correspondant dans `fiches/_gabarits/`.
- Pour lier une autre fiche, un groupe ou un terme du glossaire : `[[id]]` ou `[[id|texte du lien]]`. On ne met pas de lien automatique sur les mots.
- Les commentaires `<!-- -->` des gabarits ne s’affichent pas.
- Les fiches mouvements et artistes n’ont pas de section « Sources ».
- Une fiche œuvre a deux parties : la présentation, qui suffit à publier la page, puis l’analyse (D21).
- Les fiches de parcours (`fiches/parcours/`) et de comparaison (`fiches/comparaisons/`) listent dans leur en-tête YAML les identifiants qu’elles relient (D24). Elles ne recopient aucun texte d’une autre fiche.

## Tableur
- Les relations (onglet « Influences ») n’utilisent que les types de D23 : `influence`, `collaboration`, `rivalité`, `filiation`, `réaction`. Une relation sans explication est permise, mais on n’invente jamais d’explication.
- On ne saisit pas ce qui se calcule : les contemporains, la période d’un artiste dans un mouvement, les listes d’œuvres ou d’artistes.
- Une liste dans une cellule (« Autres mouvements », « Groupes ») : des identifiants séparés par des virgules.
- Le tableur ne s’enregistre jamais avec openpyxl, qui supprimerait ses listes déroulantes. Une modification faite par un script passe directement par le XML du fichier, et on compare ensuite toutes les cellules avec l’original.

## Typographie des textes affichés
- Apostrophe typographique (’), guillemets français (« ») et tiret demi-cadratin entre deux dates (1874–1960).
- Dans le code, on garde les guillemets et apostrophes droits (' et ").

## Documents du projet
- Les six documents sont dans `suivi_projet/` (D12). Ils sont mis à jour à la clôture de chaque conversation, dans un commit à part.
- **JOURNAL** : 5 à 10 lignes par entrée (fait, reste à faire, décisions). Une entrée ne recopie pas les problèmes ouverts : la liste en tête du JOURNAL est tenue à jour, et un problème réglé en sort.
- Un défaut propre au téléphone va dans la ROADMAP, au lot 4, et pas dans la liste des problèmes ouverts.
- **ARCHITECTURE** décrit le flux, les fichiers, les formats et les principes, pas chaque fonction : le détail est dans les commentaires du code.

## Git
- Une branche par tâche, avec un nom court : `astro`, `pages-fiches`, `correction-recherche`.
- On fait un commit avant de demander une modification à Claude, puis un commit par étape qui fonctionne.
- Messages courts en français, qui décrivent le changement : « Recherche insensible aux accents ».
- On relit le diff dans VS Code avant chaque commit.
- On lance `python construire.py` avant tout commit qui touche le tableur ou les fiches.
- `package-lock.json` est fabriqué par npm : on ne le modifie jamais à la main. En cas de conflit, on garde la version de `main`, puis on relance `npm install`.
- Au début de chaque phase, on regarde si une nouvelle version de wrangler existe. Si oui, on la teste dans une branche avant de l’installer dans `main` (D16).