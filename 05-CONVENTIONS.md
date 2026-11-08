# Conventions

Les règles du code, du contenu et de Git. Le nouveau code doit ressembler à l’existant.

## Langue
- Noms de variables, de fonctions et de classes CSS en français, sans accents : `dessiner`, `oeuvres`, `.panneau`.
- Commentaires en français. Les mots imposés par un outil restent en anglais.

## JavaScript
- Moderne et sans framework : `const` par défaut, `let` si la valeur change, jamais `var`.
- Guillemets doubles, points-virgules, deux espaces d’indentation.
- Des fonctions courtes, qui font une seule chose, avec un nom qui dit ce qu’elles font.
- Pas de nouveau nombre magique : un réglage modifiable va dans `REGLAGES`, une valeur fixe dans une constante nommée. On nomme une valeur existante quand on la touche.
- Un commentaire au-dessus de chaque bloc dont l’intention n’est pas évidente : il dit pourquoi, pas ce que dit déjà le code.
- Tout texte venant des données et inséré dans du HTML passe par `esc()`.
- Rien dans `src/lib/` ne touche à `document` ni à `window`.
- Dans un gabarit Astro, un texte venant des données s’insère avec `{…}`, qu’Astro échappe lui-même ; `set:html` est réservé au HTML fabriqué par `src/lib/` (le texte des fiches).
- Chaque page passe à `Base.astro` une `description` d’une phrase : tirée du tableur pour une fiche, sans balise si la cellule est vide ; écrite dans l’en-tête Astro pour une page fixe, avec ses nombres calculés.
- Un script de page fonctionne en amélioration progressive : sans JavaScript, la page reste complète, et le script n’affiche que ce qu’il sait faire marcher.

## CSS
- Couleurs, polices et tailles passent par les variables de `:root`, et chaque nouvelle couleur a sa valeur pour le mode sombre. Exceptions voulues, identiques dans les deux modes :
  - le blanc et les noirs translucides posés sur les couleurs des mouvements ;
  - `--grille-bande` ;
  - le voile de l’image en grand (`::backdrop` ne voit pas toujours les variables).
- Pas de style écrit en dur dans les gabarits Astro. Dans app.js comme dans les gabarits, l’attribut `style` ne sert qu’aux valeurs calculées : positions, largeurs, couleur du mouvement (`--c`).
- Les règles de la frise vont dans `public/style.css`, celles des pages dans `public/pages.css` (D39). pages.css est chargé après style.css : une classe des pages ne reprend jamais un nom de style.css (`.carte`, `.entete`, `.bande`, `.resume`…). Seule exception, un élément commun à la frise et aux pages (D44) : il garde ses classes, rangées sous `.fiche` dans style.css et sous `.texte` dans pages.css.
- Une nouvelle règle va dans la section de son élément, et sa version téléphone dans le bloc `@media (max-width: 720px)` de cette section.
- On garde le focus visible et le respect de `prefers-reduced-motion`.
- La frise est l’affiche, les pages sont le livre (D25) :
  - la frise et la navigation gardent leurs bordures épaisses et leurs ombres décalées ;
  - les pages utilisent des filets et laissent la typographie porter la hiérarchie, et les cartes y sont réservées aux œuvres et au répertoire des mouvements (D37) ;
  - une page reste complète sans image.

## Python (construire.py)
- Quatre espaces d’indentation, noms en français.
- Une fonction de lecture par onglet ou par dossier de fiches, qui fait ses propres vérifications.
- Toute règle de cohérence passe par `avertir()`. On ne supprime jamais une vérification pour faire taire un avertissement.

## Identifiants
- En minuscules, sans accents, avec des tirets : `van-gogh`, `les-demoiselles-d-avignon`.
- Le même dans le tableur (colonne ID), dans le nom de la fiche (`<id>.md`) et dans l’adresse de la page.
- Unique sur tout le site, tous types confondus.
- Une œuvre reprend son titre complet (`un-dimanche-apres-midi-a-l-ile-de-la-grande-jatte`), et « œ » s’écrit `oe`. Un artiste ou un événement a un identifiant court (`monet`, `cabaret-voltaire`).
- On ne le change plus une fois le site public (D9).

## Fiches
- En-tête YAML (`id`, `type`), titre `#`, puis les rubriques du gabarit de `fiches/_gabarits/`. Les commentaires `<!-- -->` ne s’affichent pas.
- Liens : `[[id]]` ou `[[id|texte]]`, jamais de lien automatique sur les mots.
- Pas de section « Sources » dans les fiches mouvements et artistes.
- Fiche œuvre : la présentation, qui suffit à publier la page, puis l’analyse, facultative, sous `## Analyse`. Ce titre ne se renomme pas (D34).
- Parcours et comparaison :
  - l’id est le nom du fichier ;
  - l’en-tête liste sur une ligne les identifiants reliés, séparés par des virgules (`etapes:`, `elements:`) ;
  - une comparaison relie deux éléments du même genre (D35, D36) ;
  - ces fiches relient les autres, sans recopier leur texte.

## Tableur
- Les relations n’utilisent que les types de D23 : `influence`, `collaboration`, `rivalité`, `filiation`, `réaction`. Une explication peut manquer, mais on n’en invente jamais.
- Une liste dans une cellule : des identifiants séparés par des virgules.
- On ne saisit pas ce qui se calcule : contemporains, période d’un artiste dans un mouvement, listes d’œuvres ou d’artistes, domaine public (D10).
- Une image d’œuvre : un fichier de `public/images/`, au nom en minuscules, seulement pour une œuvre du domaine public, avec sa source dans « Crédit image » (D10). La page d’une œuvre protégée sur le site du musée va dans « Lien de l’œuvre », jamais dans « Lien image ».
- Le tableur ne s’enregistre jamais avec openpyxl, qui effacerait ses listes déroulantes. Une modification par script passe par le XML du fichier, puis on compare toutes les cellules avec l’original.

## Typographie des textes affichés
- Apostrophe typographique (’), guillemets français (« »), tiret demi-cadratin entre deux dates (1874–1960).
- Dans le code, guillemets et apostrophes droits.

## Documents du projet
- Les six documents sont dans `suivi_projet/` (D12). Ils sont mis à jour à la clôture de chaque conversation, dans un commit à part.
- Chaque document a son rôle, sans répéter les autres :
  - le README est le mode d’emploi ;
  - ARCHITECTURE décrit le fonctionnement (flux, fichiers, formats, principes), pas chaque fonction ;
  - DECISIONS garde les raisons des choix ;
  - la ROADMAP donne l’étape en cours et la suite ;
  - le JOURNAL tient les problèmes ouverts et l’historique.
- JOURNAL : une entrée de 5 à 10 lignes (fait, reste à faire, décisions), qui ne recopie pas les problèmes ouverts. Un problème réglé sort de la liste. Un défaut propre au téléphone va dans la ROADMAP, au lot 4.

## Git
- Une branche par tâche, au nom court. Elle passe par sa version de test avant d’aller dans `main`.
- Avant de fusionner une branche dans `main` : `npm run build`, puis `npm run verifier` (D50).
- Un commit avant de demander une modification à Claude, puis un commit par étape qui fonctionne.
- Des messages courts en français, qui décrivent le changement.
- On relit le diff dans VS Code avant chaque commit.
- On lance `python construire.py` avant tout commit qui touche le tableur ou les fiches.
- `package-lock.json` est fabriqué par npm. En cas de conflit, on garde la version de `main` et on relance `npm install`.
- Au début de chaque phase, on regarde si une nouvelle version de wrangler existe, et on la teste dans une branche (D16).