# Journal

Ce document dit où en est le projet. Il a deux parties : la liste des problèmes ouverts, tenue à jour (un problème réglé en sort), puis les entrées du journal, la plus récente en haut. Une entrée fait 5 à 10 lignes et ne recopie pas les problèmes ouverts (voir CONVENTIONS).

Les entrées antérieures au 5 novembre 2026 ont été retirées le 8 novembre 2026 : elles sont dans l’historique Git de ce fichier, et leur résumé dans la ROADMAP (« Terminé »).

---

# Problèmes ouverts

Les défauts propres au téléphone sont dans la ROADMAP, au lot 4 ; les tâches à faire avant l’ouverture au public, à la phase 3 ; le sens des filiations, dans « En parallèle · Écriture ». Aucun problème ci-dessous ne bloque le lot 4.

**Données**
- `NOMS_COURTS` (src/lib/outils.js) est une donnée rangée dans le code. Si deux artistes d’un même mouvement partagent un nom de famille, les listes de la fiche mouvement les confondront.
- L’onglet « Influences » et la clé `influences` de frise.json contiennent toutes les relations (D23) : leur nom est trompeur. Un renommage toucherait le tableur, construire.py et src/lib/fiches.js.
- `ONGLETS_INDEX` (titres des onglets de l’Index) est dans src/lib/outils.js parce que `compter()` en a besoin. À séparer si les pages ont un jour d’autres mots à compter.

**Frise**
- En vue mouvement, un mouvement court tient presque entier à l’écran : la vue ne défile pas, et un clic sur un repère de la barre du bas ne fait rien de visible.
- En vue détail, les dernières décennies (1950, 1960) ne passent sans doute jamais en noir dans la barre du bas.
- `changerZoom()` repère les couloirs par leur position : il suppose que Détail et Intermédiaire dessinent les mêmes couloirs dans le même ordre, ce qui est le cas aujourd’hui.
- Depuis la vue d’ensemble, un clic sur une décennie ramène au dernier zoom choisi, pas forcément à « Détail ».
- L’Index ne garde pas sa position de défilement au retour, et n’a pas d’adresse `#index` (prévue à l’étape 4c, D53).
- En vue mouvement, des disciplines longues passent sur deux lignes et agrandissent la ligne de l’artiste.
- Une adresse `#id` qui reprend une propriété que tout objet JavaScript hérite (`#constructor`, `#toString`) fait planter `ouvrir()` : `OEU[id]` y trouve une fonction. Le démarrage vérifie `?mouvement=` avec `Object.hasOwn`, mais pas le `#id`. Correction d’une ligne, jugée inutile pour l’instant.
- Le zoom « Intermédiaire » garde dans le code le nom de « Moyen » (`etat.moyen`, `data-niveau="moyen"`, `zoomMoyen()`, `pxParAnMoyen`, `largeurCarteMoyenne`) : voulu (D56), mais à garder en tête en lisant app.js.
- Le décalage d’une fiche ouverte (3 ans au zoom Détail, dans `ouvrir()`) est un nombre écrit dans le code, alors que CONVENTIONS demande de nommer une valeur qu’on touche : à passer dans `REGLAGES`.
- Thème : la clé `theme` et les valeurs `light`/`dark` sont écrites à trois endroits (le script du `<head>` d’index.astro, celui de Base.astro, et app.js). Après un clic, la bulle du bouton garde l’ancien libellé jusqu’au survol suivant.
- En-tête : la hauteur du bouton de thème (`2.25rem`) et le `margin-top` de la case « Œuvres majeures seulement » (`0.3rem`) sont calés à la main sur les boutons des mouvements.
- style.css : `--trait` n’est utilisée nulle part.

**Pages**
- Le `<head>` est écrit deux fois, dans index.astro et dans src/layouts/Base.astro, jusqu’au lot 4 : un changement des polices, du script du thème, de la description ou de la canonique se fait dans les deux.
- Le libellé « Lire la page ↗ » est écrit à trois endroits : `lirePage()` (src/lib/fiches.js), Ligne.astro et Carte.astro.
- Le tri des œuvres par année est écrit à six endroits (pages mouvement, artiste et œuvre, catalogue, plan du site, `listeOeuvres()`), et pas de la même façon : `listeOeuvres()` met une œuvre sans année en tête, les pages la mettent en fin de liste. Piste : une fonction `parAnnee` dans src/lib/outils.js.
- Les pages fixes sont listées à cinq endroits : `MENU` et `PIED` (Base.astro), `PAGES_SITE` (app.js, Index de la frise, D51), `PAGES_FIXES` du plan du site (avec leurs libellés) et du sitemap (leurs adresses). `npm run verifier` rattrape un oubli dans le sitemap, pas ailleurs. Piste, au lot 4 : une seule liste dans src/lib/.
- La règle qui décide d’afficher une image (domaine public et fichier image) est écrite trois fois : `visuelOeuvre()` (src/lib/fiches.js), et `imageVisible` dans la page œuvre et dans le catalogue. Sans risque, puisque construire.py retire déjà les images des œuvres protégées. Piste : une fonction `imageAffichee()` dans fiches.js.
- Les répertoires des artistes et des œuvres ont chacun leur script de filtre, proches mais séparés.
- L’encart d’une œuvre protégée a ses règles CSS en deux exemplaires, dans style.css et pages.css (D44) : voulu, l’affiche et le livre n’ont pas le même dessin, mais un changement de structure se fait dans les deux.
- Les vignettes du catalogue et de « Du même artiste » sont presque toutes des aplats : une seule œuvre a une image. Le recadrage des images (`object-fit: cover`) reste à juger avec d’autres œuvres.
- `textePage()` suppose que marked écrit chaque titre `##` en `<h2>` sans attribut, dans l’ordre du Markdown. Une ligne `## ` dans un bloc de code décalerait les ids du sommaire.
- Les chapeaux des répertoires, les descriptions de la frise, des répertoires et des pages fixes, et le texte d’À propos sont écrits dans le code (D52) : ils ne suivent pas un changement du tableur (seuls les nombres et les dates des descriptions sont calculés). Piste : un onglet « Pages » dans le tableur, si ces textes deviennent nombreux.

**Fiches et construire.py**
- Un lien `[[id]]` vers un terme, un parcours ou une comparaison est signalé par construire.py (paramètre `ids_pas_encore_affiches` de `verifier_liens()`), car la frise afficherait l’identifiant brut. À retirer de cet avertissement en phase 4, quand ces éléments auront leur page.
- construire.py fait environ 570 lignes. Il reste en un seul fichier (D32), à reconsidérer s’il grossit encore.
- `verifier_image()` ne voit pas une différence de majuscules entre le tableur et le nom du fichier : Windows les confond, Cloudflare non, et l’image serait cassée en ligne. Garder des noms d’images en minuscules.
- `verifier_image()` accepte encore une image hébergée sur un autre site (`http…`), alors que D10 range les images dans `public/images/`. Piste : un avertissement.
- construire.py : `lire_groupes_artiste()` et `lire_autres()` se ressemblent (découpage, avertissement, suggestion d’identifiant), mais traitent différemment une valeur inconnue : la première l’écarte, la seconde la garde en texte.
- Une rubrique laissée vide dans une fiche rédigée affiche son titre seul (seule l’analyse vide est retirée, D34). Piste : retirer toute rubrique vide, après avoir vérifié l’effet sur les fiches mouvements et artistes déjà rédigées.
- « Voir aussi » garde dans frise.json un identifiant inconnu (signalé), alors qu’un groupe inconnu est écarté.
- `lire_entete()` ne lit que des lignes `clé: valeur` : une liste YAML écrite sur plusieurs lignes (`- monet`) serait lue comme vide. Pour un parcours ou une comparaison, l’avertissement sur le nombre d’éléments le signale.
- Le bilan « fiche(s) rédigée(s) sur 147 » ne compte pas les parcours et les comparaisons.

**À tester**
- La coupure d’`aria-live` pendant l’Index n’a pas été testée avec un vrai lecteur d’écran. À faire avant l’ouverture au public.
- La flèche ↗ s’affiche bien dans Chrome, avec Archivo. Reste à vérifier sur Safari et Firefox.
- Le lien vers un groupe dont le mouvement n’a pas de page (il ouvre alors la fiche du mouvement dans le panneau) n’a pas pu être testé : les 9 mouvements sont rédigés.
- Panneau : les intertitres `###` sont maintenant plus petits que les `##`. Vérifier l’effet sur les fiches mouvements et artistes qui en utilisent.
- La page œuvre n’a été testée qu’avec une fiche provisoire : à revoir avec la première vraie œuvre documentée, et une œuvre protégée avec un « Lien de l’œuvre ».
- Une page œuvre n’a pas encore été vue dans le sitemap ni dans le plan du site, dont la rubrique Œuvres n’apparaît qu’avec une première œuvre documentée.

**Contenu**
- « Nationalité … » suppose que la colonne « Nationalité » est au féminin. Rien ne signale une forme masculine.
- L’âge écrit dans la page À propos est en dur : à changer chaque année.
- Le domaine public est calculé d’après l’année du build : au premier build de 2027, construire.py signalera Pollock et Nolde, dont la cellule « Domaine public ? » dira encore « Non ».
- Les fiches œuvres créées avec l’ancien gabarit gardent une section « Sources » vide. Une fiche rédigée qui l’aurait gardée affichera ce titre et une puce vide.
- Les titres du gabarit œuvre (« Ce qu'on voit », « l'audace ») ont une apostrophe droite, et `typo()` ne s’applique pas au Markdown des fiches.

**Outils et documents**
- Cloudflare, onglet « Previews Base » : la Build command et la Preview command sont saisies mais ignorées. Si Cloudflare se met à les appliquer, le site sera fabriqué plusieurs fois : sans danger, mais à nettoyer.
- Avertissements Git « LF will be replaced by CRLF » (réglage `core.autocrlf` de Windows) : sans danger. Piste : un fichier `.gitattributes`.
- Astro est noté `^7.4.4` dans `package.json`, alors que marked et wrangler sont en version exacte. `package-lock.json` le protège déjà : à fixer en version exacte par cohérence, dans une branche testée.
- `verifier_dist.mjs` ne tourne qu’à la main : rien ne rappelle de le lancer avant une fusion. Il lit le HTML par des expressions régulières, qui supposent des attributs entre guillemets doubles (D50).
- `npm install` signale que les scripts postinstall d’esbuild et de workerd (arrivés avec wrangler) ne sont pas couverts par `allowScripts` : ils ne sont pas lancés. Sans effet constaté, en local comme sur Cloudflare. À surveiller si une version de npm en fait une erreur.
- Tableur : les listes déroulantes « De (ID) » et « Vers (ID) » de l’onglet Influences ne proposent que les artistes. Un identifiant de mouvement est accepté après un simple message, car Excel ne sait pas réunir deux onglets dans une même liste.
- Tableur : le tableau « Avancement » de l’onglet Lisez-moi n’a de ligne ni pour les groupes ni pour le glossaire, et l’onglet ne décrit pas le Glossaire.

---

# Entrées

## 9 novembre 2026 · Lot 4 : en-tête décidé, découpage, étape 4a terminée
**Fait** : tri des changements d’en-tête voulus par Térence, avec une maquette cliquable (ordinateur et téléphone, vraies couleurs), publiée sur claude.ai et gardée hors du dépôt. En-tête décidé : deux lignes, l’Index garde son nom et son panneau, À propos entre dans le menu, les mouvements restent des boutons encadrés mais tiennent sur une ligne, l’étoile « Œuvres majeures » passe dans la barre du bas. Les largeurs ont été mesurées avec Archivo. Le lot est découpé en étapes 4a à 4f (ROADMAP). Étape 4a, sur la branche `zoom-intermediaire` : la frise s’ouvre au zoom « Intermédiaire », nouveau nom de « Moyen » (libellé seul), et une fiche ouverte est placée en pixels, sans changement en Détail. Testé en local et sur la version de test, téléphone compris (la barre du bas tient) ; fusionné dans `main`.
**Écarté** : l’Index dans un `<dialog>`, un onglet Événements, une barre continue de mouvements soulignés de leur couleur, une phrase d’aide dans le bouton Index.
**Reste à faire** : l’étape 4b (en-tête commun), dans une nouvelle conversation.
**Décisions prises** : D53 à D56 ; D18 et D28 précisées.

## 8 novembre 2026 · Étape 2h terminée, fin de la phase 2
**Fait** : sur la branche `finitions`, en quatre sous-étapes. Index de la frise : une ligne « Pages du site » sous « Comment lire la frise », et non en bas, où elle serait passée après les 104 œuvres (D51). Pied de page des pages dans `Base.astro` (À propos, Plan du site), le lien de la page affichée marqué `aria-current`. Plan du site `/plan-du-site/`, calculé comme le sitemap (D52). Page À propos `/a-propos/`, au texte écrit dans son gabarit, en une colonne centrée (`.page-simple`, `:has()`) ; l’adresse de contact est factice. Les deux pages sont dans `PAGES_FIXES`, avec leur description. `npm run verifier` : aucun lien cassé. Testé en local et sur la version de test, où une adresse sans barre finale mène bien à l’adresse avec barre ; branche fusionnée dans `main`. La phase 2 est terminée.
**Ménage des documents** : entrées du JOURNAL antérieures au 5 novembre retirées (elles sont dans Git) ; problèmes ouverts reclassés (une rubrique « Données », et chaque problème sous la partie qu’il concerne) ; détail de la phase 2 condensé dans la ROADMAP ; dans ARCHITECTURE, la partie B ne garde que ce qui reste à construire, et la section « À trancher », recopiée de la ROADMAP, est retirée.
**Reste à faire** : le lot 4, dans une nouvelle conversation, en commençant par la liste des changements d’en-tête de Térence.
**Décisions prises** : D51, D52.

## 8 novembre 2026 · Étape 2g terminée : référencement de base
**Fait** : sur la branche `referencement`, en quatre sous-étapes. Description : `Base.astro` reçoit `description`, le résumé court d’un mouvement ou d’une œuvre, la phrase-clé d’un artiste ; la frise et les trois répertoires ont leur phrase, dont les nombres et les dates sont calculés (les chapeaux, peu clairs hors contexte, ne sont pas repris). Adresse canonique : le champ `site` d’astro.config.mjs reçoit l’adresse provisoire en workers.dev, et chaque page déclare sa canonique, sur la production et avec la barre finale ; la frise déclare `/` dans toutes ses vues. sitemap.xml et robots.txt : deux endpoints statiques, sans dépendance, le sitemap suivant `aUnePage()`. Contrôle de `dist/` : `verifier_dist.mjs`, lancé par `npm run verifier`, testé avec un lien cassé exprès. Premier `npm run build` en local : l’ancien `dist/`, qui datait de l’étape 2c, est remplacé. Testé en local et sur la version de test.
**Écarté** : `@astrojs/sitemap`, un robots.txt dans `public/`, un outil de liens tout fait, le contrôle à chaque déploiement, la date de modification dans le sitemap.
**Reste à faire** : vérifier la barre finale sur la version de test et fusionner `referencement` dans `main`, si ce n’est fait ; puis l’étape 2h (À propos, plan du site, liens de l’Index), dans une nouvelle conversation.
**Décisions prises** : D48, D49, D50.

## 8 novembre 2026 · Étape 2f terminée (sous-étape 3 : catalogue)
**Fait** : sur la branche `oeuvres`, le catalogue `/oeuvres/`. Les œuvres sont rangées par mouvement, en cartes avec vignette : l’image d’une œuvre libre, un aplat hachuré « Œuvre protégée » ou un aplat uni « Image à venir ». Une recherche par titre ou par artiste, un choix du mouvement et la case « Œuvres majeures seulement », en amélioration progressive (D47). `Carte.astro` recopie sur son lien les attributs qu’on ne lui déclare pas (`data-…`) ; une carte avec vignette a un cadre égal, et la couleur du mouvement en trait sous l’image. « Œuvres » entre dans le menu, et le fil d’Ariane des pages œuvres ne mène plus à une erreur 404. Dans un répertoire filtré, le premier bloc visible n’a plus de marge en haut. Testé en local et sur la version de test ; branche `oeuvres` fusionnée dans `main`.
**Écarté** : le filtre « Écrits et manifestes » (D47) ; le découpage de `src/lib/fiches.js` (D46) ; le rangement des doublons du tri par année et de la règle d’affichage des images, jugé non nécessaire.
**Reste à faire** : l’étape 2g (référencement de base), dans une nouvelle conversation.
**Décisions prises** : D46, D47 ; D20 précisée par D47.

## 7 novembre 2026 · Étape 2f, sous-étapes 1 et 2 : droits des images et page œuvre
**Fait** : sur la branche `oeuvres`. Sous-étape 1 : D10 tranchée. construire.py calcule le domaine public (année de décès + 71, comparée à l’année du build), retire l’image d’une œuvre protégée et signale les écarts avec la colonne « Domaine public ? ». Le tableur reçoit, par son XML, les colonnes « Crédit image » et « Lien de l’œuvre » ; le Lisez-moi est mis à jour. Dans le panneau : encart des œuvres protégées, crédit sous l’image, sous-titre de l’œuvre agrandi, technique en majuscule, intertitres `###` sous les `##`. Sous-étape 2 : `aUnePage()` et `adresse()` apprennent les œuvres documentées ; une page par œuvre, en cartel (D45), avec l’image en grand dans un `<dialog>` et « Du même artiste » en cartes avec vignette. Testé en local avec une fiche provisoire, restaurée avant le commit. Branche non fusionnée.
**Reste à faire** : la sous-étape 3 (catalogue `/oeuvres/`, ses filtres, l’entrée « Œuvres » du menu), dans une nouvelle conversation, puis la version de test et la fusion dans `main`.
**Décisions prises** : D10 passe à « Décidé » ; D44, D45 ; D39 et D40 précisées.

## 7 novembre 2026 · Étape 2e terminée (sous-étape 4 : ponts)
**Fait** : sous-étape 4, en trois commits sur la branche `pages`. Liens de groupe : dans le panneau, `destinationPanneau()` mène `[[groupe]]` à la section du groupe sur la page de son mouvement (ou, si ce mouvement n’a pas de page, à sa fiche dans le panneau) ; le lien affiche le nom du groupe et non son identifiant, sur les pages comme dans le panneau ; construire.py cesse de signaler ces liens. « Lire la page » : `lirePage()` ajoute un lien bordé sous le sous-titre des fiches qui ont une page (mouvements et artistes, les œuvres suivront à l’étape 2f), aligné sur « Explorer… » par le bloc flex `.actions-fiche`. Vue mouvement : son adresse `/?mouvement=<id>` (D38) est lue au démarrage et tenue à jour par `changerVue()` (D43) ; « Voir dans la frise » d’une page mouvement y mène. Testé en local et sur la version de test ; branche `pages` fusionnée dans `main`.
**Reste à faire** : l’étape 2f (catalogue et pages œuvres), dans une nouvelle conversation, en commençant par trancher D10.
**Décisions prises** : D43 ; D38 et D40 précisées.

## 7 novembre 2026 · Étape 2e, sous-étapes 1 à 3 : pages mouvements et artistes
**Fait** : l’étape 2e est découpée en quatre sous-étapes, sur la branche `pages`. Sous-étape 1 : `aUnePage()` et `adresse()` (src/lib/fiches.js) décident de l’adresse de chaque élément ; `lien()` et `markdown()` reçoivent `surPage` ; `GRP` rejoint donnees.js. Le panneau est inchangé, et une page de test non commitée a vérifié les adresses. Sous-étape 2 : `Base.astro` (head, menu Frise / Mouvements / Artistes, fil d’Ariane), `public/pages.css`, quatre composants dans `src/components/` (Ligne, Carte, EnteteFiche, Lecture), le répertoire `/mouvements/` en cartes et une page par mouvement : repère chronologique en bande sous l’en-tête, sommaire fait de vrais liens (`textePage()`), groupes avec leur ancre, œuvres majeures en cartes avec l’étoile, autres œuvres en liste. Sous-étape 3 : le répertoire `/artistes/`, rangé par mouvement, avec un filtre par nom (premier script des pages), et une page par artiste. Le design part du prototype de structure, retouché en cours de route. Testé en local et sur la version de test, adresses avec et sans barre finale comprises. Branche non fusionnée.
**Reste à faire** : la sous-étape 4 (« Lire la page », « Voir dans la frise » vers la vue mouvement, `?mouvement=`, avertissement des groupes), dans une nouvelle conversation, puis la fusion dans `main`.
**Décisions prises** : D37 à D42 ; D25 est précisé par D37.

## 6 novembre 2026 · Étape 2d terminée (sous-étapes 3 à 5)
**Fait** : sous-étape 3, onglet « Glossaire » (ID, Terme, Définition, Voir aussi, Notes / corrections), ajouté directement dans le XML du tableur ; construire.py signale un terme sans nom ou sans définition, et un « Voir aussi » inconnu ou qui renvoie au terme lui-même. Sous-étape 4 : `## Analyse` sépare la présentation de l’analyse (D34) ; `niveau_oeuvre()` en déduit le niveau de chaque œuvre, retire une analyse vide du Markdown et signale une analyse sans présentation ou un titre en double ; la règle des 20 caractères devient `sans_texte()`. Nouveau gabarit œuvre ; les 104 fiches œuvres vides y sont passées par un script ponctuel, non commité. Sous-étape 5 : gabarits des parcours et des comparaisons, lus par `lire_articles()` et vérifiés par `verifier_articles()` (D35, D36). À chaque sous-étape, frise.json n’a changé que par les ajouts prévus. Testé sur la version de test ; fusionné dans `main`.
**Reste à faire** : l’étape 2e (pages mouvements et artistes), dans une nouvelle conversation.
**Décisions prises** : D34, D35, D36.

## 6 novembre 2026 · Étape 2d, sous-étapes 1 et 2, et revue des données
**Fait** : l’étape 2d est découpée en cinq sous-étapes, sur la branche `modele-donnees`, et construire.py reste un seul fichier (D32). Sous-étape 1 : onglet « Groupes » (le néo-impressionnisme, rattaché au post-impressionnisme) et colonne « Groupes » des artistes (Seurat), lus et vérifiés par construire.py, qui s’arrête aussi avec un message clair si un onglet manque. Sous-étape 2 : De et Vers acceptent un mouvement, la liste des types du tableur est celle de D23 (`maître-élève` retiré), et construire.py vérifie le type et les genres d’éléments qu’il relie (`TYPES_RELATIONS`). Revue des données : construire.py signale un lien vers un groupe et une année qui sort de la date affichée, six identifiants d’œuvres coupés sont complétés (fiches renommées avec `git mv`), et l’onglet Groupes reçoit sa colonne « Notes / corrections ». Le tableur a été modifié directement dans son XML, pour garder listes déroulantes et formules. À chaque étape, frise.json n’a changé que par les ajouts prévus et les six identifiants.
**Écarté** : afficher la date affichée sur les cartes de la frise (D33).
**Reste à faire** : les sous-étapes 3 (glossaire), 4 (niveau des œuvres et nouveau gabarit) et 5 (parcours et comparaisons), dans une nouvelle conversation, puis le test sur la version de test et la fusion dans `main`.
**Décisions prises** : D32, D33.

## 6 novembre 2026 · Étape 2c terminée
**Fait** : marked passe du CDN à une dépendance npm, dans la même version, fixée en version exacte (18.0.14) : le site ne charge plus rien depuis jsDelivr. Création de `src/lib/` : `donnees.js` (données, accès par identifiant, `couleur`, `mouvementsTries`), `outils.js` (`esc`, `sansAccent`, `nomCourt`, `compter`, `estImage`…) et `fiches.js` (les quatre fonctions `fiche…` et ce qu’elles utilisent). `ficheMouvement()` reçoit un paramètre `avecExplorer` et ne lit plus `etat`. Le reste d’app.js n’est pas découpé (D31). `hauteurAxe()` remplace le calcul fait deux fois, et `markdown()` renvoie maintenant à `verifier_liens()`. Une page de test temporaire, non commitée, a prouvé que `src/lib/` fonctionne au build, dans Node. Testé en local, puis sur la version de test ; fusionné dans `main`.
**Reste à faire** : l’étape 2d (modèle de données), dans une nouvelle conversation.
**Décisions prises** : D30, D31.

## 6 novembre 2026 · Étape 2b terminée
**Fait** : `main()` de construire.py est découpé en une fonction par onglet (`lire_mouvements()`…), puis `verifier_ensemble()`, `ecrire()` et `afficher_bilan()`. data.js est resté identique avant et après le découpage, ce qui a aussi été vérifié sur un faux tableur qui déclenche les 18 avertissements. construire.py écrit maintenant `src/data/frise.json`, et `public/data.js` est supprimé. app.js est déplacé dans `src/scripts/`, sous le même nom, et devient un module traité par Astro qui importe le JSON (D29). `marked` reste chargé depuis le CDN, avant lui. Une analyse ESLint, hors du dépôt, n’a trouvé aucune variable non déclarée qui casserait le mode strict des modules. Testé en local, puis sur la version de test ; fusionné dans `main`.
**Reste à faire** : l’étape 2c (découpage d’app.js), dans une nouvelle conversation.
**Décisions prises** : D29.

## 6 novembre 2026 · Structure du site
**Fait** : tri des « Idées à trier » de Lovable, puis deux nouvelles séries de maquettes Lovable (en-tête, étiquettes, zoom ; plans de site et gabarits). Un prototype de structure cliquable, fait avec les vraies données, publié sur claude.ai et gardé hors du dépôt. Une analyse de ce prototype par une autre IA, confrontée point par point. La structure est fixée : la frise reste l’accueil, cinq rubriques, des adresses par type, un texte par page, trois niveaux d’œuvre, des groupes pour les sous-courants (le néo-impressionnisme en devient un, sans couloir dans la frise), des relations typées, Comprendre réduite à trois sous-rubriques et ouverte après le lancement. Les six documents sont mis à jour.
**Reste à faire** : l’étape 2b, dans une nouvelle conversation. La phase 2 compte désormais huit étapes (2a à 2h), dont une étape « Modèle de données » (2d). En parallèle, le gabarit des fiches œuvres à deux parties et les premières œuvres documentées.
**Décisions prises** : D17 à D28 ; D14 est remplacé par D17, D13 est précisé par D21.

## 5 novembre 2026 · Étape 2a terminée
**Fait** : le tri des « Idées à trier » est reporté, car il ne concerne que l’étape 2d et la suite. construire.py vérifie maintenant les liens des fiches (`verifier_liens()`) : identifiant inconnu, ou lien mal formé que la fonction `markdown()` d’app.js ne reconnaîtrait pas. Il vérifie aussi les chemins d’images (`verifier_image()`) : chemin qui ne commence ni par `/` ni par `http`, ou fichier absent de `public/`. Ce second contrôle va un peu au-delà de la roadmap. Dans le tableur, l’image d’*Impression, soleil levant* passe à `/images/…`. Testé avec de faux liens, puis sur la version de test ; fusionné dans `main`.
**Reste à faire** : l’étape 2b, dans une nouvelle conversation. Le tri des idées, avant l’étape 2d.
**Décisions prises** : aucune.

---

<!-- Modèle d’entrée (5 à 10 lignes) :
## JJ mois AAAA · Titre de la tâche
**Fait** :
**Reste à faire** :
**Décisions prises** : (à reporter dans DECISIONS)
Les nouveaux problèmes vont dans la liste en tête du fichier, pas dans l’entrée.
-->