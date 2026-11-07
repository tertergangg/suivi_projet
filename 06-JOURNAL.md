# Journal

Ce document dit où en est le projet. Il a deux parties : la liste des problèmes ouverts, tenue à jour (un problème réglé en sort), puis les entrées du journal, la plus récente en haut. Une entrée fait 5 à 10 lignes et ne recopie pas les problèmes ouverts (voir CONVENTIONS).

Le détail des entrées jusqu’au 4 novembre 2026 est dans l’historique Git de ce fichier : le premier commit du dossier `suivi_projet/` contient l’ancienne version complète.

---

# Problèmes ouverts

Les défauts propres au téléphone sont dans la ROADMAP, au lot 4.

## À régler pendant la phase 2
- Un lien `[[id]]` vers un groupe, un terme, un parcours ou une comparaison est signalé par construire.py (paramètre `ids_pas_encore_affiches` de `verifier_liens()`), car la frise afficherait l’identifiant brut. Sur les pages, `adresse()` sait déjà viser `/mouvements/<mouvement>/#<groupe>`, mais le panneau de la frise donne encore `#<groupe>`. À la sous-étape 4 de l’étape 2e, retirer les groupes de cet avertissement ; les termes, parcours et comparaisons, en phase 4.
- `src/lib/fiches.js` réunit maintenant la fabrique du panneau et des fonctions propres aux pages (`aUnePage`, `adresse`, `textePage`). À reconsidérer à l’étape 2f, quand la page œuvre ajoutera les siennes.
- Le sens d’une relation « filiation » ou « réaction » n’est pas fixé : que veut dire De → Vers ? La page mouvement affiche donc le type de la relation sans son sens. À décider avant de saisir les premières filiations.
- `NOMS_COURTS` (src/lib/outils.js) est une donnée rangée dans le code. Si deux artistes d’un même mouvement partagent un nom de famille, les listes de la fiche mouvement les confondront.
- L’onglet « Influences » et la clé `influences` de frise.json contiennent maintenant toutes les relations (D23) : leur nom est trompeur. Un renommage toucherait le tableur, construire.py et src/lib/fiches.js.

## Plus tard, sans urgence
**Frise**
- En vue mouvement, un mouvement court tient presque entier à l’écran : la vue ne défile pas, et un clic sur un repère de la barre du bas ne fait rien de visible.
- En vue détail, les dernières décennies (1950, 1960) ne passent sans doute jamais en noir dans la barre du bas.
- Le centrage d’une fiche ouverte est compté en années (`allerAnnee(annee - 3, …)`) : au zoom Moyen, la carte arrive plus près du bord gauche.
- `changerZoom()` repère les couloirs par leur position : il suppose que Détail et Moyen dessinent les mêmes couloirs dans le même ordre, ce qui est le cas aujourd’hui.
- Depuis la vue d’ensemble, un clic sur une décennie ramène au dernier zoom choisi, pas forcément à « Détail ».
- L’Index ne garde pas sa position de défilement au retour, et n’a pas d’adresse `#index`.
- En vue mouvement, des disciplines longues passent sur deux lignes et agrandissent la ligne de l’artiste.
- Thème : la clé `theme` et les valeurs `light`/`dark` sont écrites à trois endroits (le script du `<head>` d’index.astro, celui de Base.astro, et app.js). Après un clic, la bulle du bouton garde l’ancien libellé jusqu’au survol suivant.
- En-tête : la hauteur du bouton de thème (`2.25rem`) et le `margin-top` de la case « Œuvres majeures seulement » (`0.3rem`) sont calés à la main sur les boutons des mouvements.
- style.css : `--trait` n’est utilisée nulle part.
- `verifier_image()` ne voit pas une différence de majuscules entre le tableur et le nom du fichier : Windows les confond, Cloudflare non, et l’image serait cassée en ligne. Garder des noms d’images en minuscules.
- Astro est noté `^7.4.4` dans `package.json`, alors que marked et wrangler sont en version exacte. `package-lock.json` le protège déjà : à fixer en version exacte par cohérence, dans une branche testée.
- `ONGLETS_INDEX` (titres des onglets de l’Index) est dans src/lib/outils.js parce que `compter()` en a besoin. À séparer si les pages ont un jour d’autres mots à compter.

**Pages**
- Le `<head>` est écrit deux fois, dans index.astro et dans src/layouts/Base.astro, jusqu’au lot 4 : un changement des polices ou du script du thème se fait dans les deux.
- Le tri des œuvres par année est écrit à trois endroits, et pas de la même façon : `listeOeuvres()` (fiches.js) met une œuvre sans année en tête, les pages la mettent en fin de liste.
- `textePage()` suppose que marked écrit chaque titre `##` en `<h2>` sans attribut, dans l’ordre du Markdown. Une ligne `## ` dans un bloc de code décalerait les ids du sommaire.
- Répertoire des artistes : quand le filtre cache le premier mouvement, le suivant garde sa marge du haut.
- Le chapeau du répertoire des mouvements (« Du premier Salon impressionniste… ») est écrit dans le code : il ne suit pas un changement du tableur.

**Fiches et construire.py**
- construire.py fait 488 lignes ; l’étape 2e n’y a rien ajouté. Il reste en un seul fichier (D32), à reconsidérer s’il grossit encore.
- construire.py : `lire_groupes_artiste()` et `lire_autres()` se ressemblent (découpage, avertissement, suggestion d’identifiant), mais traitent différemment une valeur inconnue : la première l’écarte, la seconde la garde en texte.
- Une rubrique laissée vide dans une fiche rédigée affiche son titre seul (seule l’analyse vide est retirée, D34). Piste : retirer toute rubrique vide, après avoir vérifié l’effet sur les fiches mouvements et artistes déjà rédigées.
- « Voir aussi » garde dans frise.json un identifiant inconnu (signalé), alors qu’un groupe inconnu est écarté.
- `lire_entete()` ne lit que des lignes `clé: valeur` : une liste YAML écrite sur plusieurs lignes (`- monet`) serait lue comme vide. Pour un parcours ou une comparaison, l’avertissement sur le nombre d’éléments le signale.
- Le bilan « fiche(s) rédigée(s) sur 147 » ne compte pas les parcours et les comparaisons.

**À tester**
- La coupure d’`aria-live` pendant l’Index n’a pas été testée avec un vrai lecteur d’écran. À faire avant l’ouverture au public.
- La flèche ↗ s’affiche bien dans Chrome, avec Archivo. Reste à vérifier sur Safari et Firefox.

**Contenu**
- « Nationalité … » suppose que la colonne « Nationalité » est au féminin. Rien ne signale une forme masculine.
- Les fiches œuvres créées avec l’ancien gabarit gardent une section « Sources » vide. Une fiche rédigée qui l’aurait gardée affichera ce titre et une puce vide.
- Les titres du gabarit œuvre (« Ce qu'on voit », « l'audace ») ont une apostrophe droite, et `typo()` ne s’applique pas au Markdown des fiches.

**Outils et documents**
- Cloudflare, onglet « Previews Base » : la Build command et la Preview command sont saisies mais ignorées. Si Cloudflare se met à les appliquer, le site sera fabriqué plusieurs fois : sans danger, mais à nettoyer.
- Avertissements Git « LF will be replaced by CRLF » (réglage `core.autocrlf` de Windows) : sans danger. Piste : un fichier `.gitattributes`.
- Le README et ARCHITECTURE se recoupent (arborescence, déploiement). Principe proposé : le README reste un mode d’emploi, et le détail technique va dans ARCHITECTURE.
- `npm install` signale que les scripts postinstall d’esbuild et de workerd (arrivés avec wrangler) ne sont pas couverts par `allowScripts` : ils ne sont pas lancés. Sans effet constaté, en local comme sur Cloudflare. À surveiller si une version de npm en fait une erreur.
- Tableur : les listes déroulantes « De (ID) » et « Vers (ID) » de l’onglet Influences ne proposent que les artistes. Un identifiant de mouvement est accepté après un simple message, car Excel ne sait pas réunir deux onglets dans une même liste.
- Tableur : le tableau « Avancement » de l’onglet Lisez-moi n’a de ligne ni pour les groupes ni pour le glossaire, et l’onglet ne décrit pas le Glossaire.

---

# Entrées

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

## 4 novembre 2026 · Version de wrangler fixée
**Fait** : wrangler ajouté aux `devDependencies` en version exacte 4.157.0, celle du dernier déploiement de production réussi (`npm install --save-dev --save-exact`). `package-lock.json` mis à jour et commité. Vérifié par une simulation locale (`npx wrangler deploy --dry-run`), puis sur la version de test et en production : le log affiche `wrangler 4.157.0`. Le nom du dossier des documents est corrigé partout : `suivi_projet/`, et non `projet/`.
**Reste à faire** : trier les « Idées à trier » dans une conversation dédiée, puis la phase 2 (étape 2a).
**Décisions prises** : aucune (application de D16).

## 4 novembre 2026 · Revue d’ensemble avant la phase 2
**Fait** : relecture complète des documents et du code. Les bases sont saines. Risques relevés : une documentation qui grossissait plus vite que le site ; une phase 2 trop large, dont la décision principale était cachée (app.js, chargé en `is:inline`, ne peut pas partager de code avec les pages) ; un chemin d’image relatif qui casserait sur les pages ; wrangler non fixé ; un contenu qui avance moins vite que l’outillage (aucune fiche œuvre rédigée, une seule image). Documents mis à jour : ils vont dans le dépôt, le JOURNAL est raccourci autour d’une seule liste de problèmes ouverts, ARCHITECTURE ne décrit plus app.js fonction par fonction, la phase 2 est découpée en six étapes, CONVENTIONS est alignée sur le code, la section « Variables » de style.css est corrigée dans le README. Les renommages de `majDecennies()`, `.decennie` et `#decennies` sont retirés de la roadmap.
**Reste à faire** : documents dans `projet/`, wrangler fixé, conversation de tri des « Idées à trier », puis la phase 2 (étape 2a).
**Décisions prises** : D12 à D16.

## 4 novembre 2026 · Phase 0 terminée
**Fait** : style.css rangé par élément de la page, chaque section avec son bloc téléphone, sans changement visuel (vérifié par un script hors du dépôt) ; au passage, le mouvement réduit s’applique enfin aux cartes. ARCHITECTURE à jour. construire.py : `verifier_orphelines()` et docstring. Les 12 fiches orphelines (ball, eluard, manet, miro et leurs œuvres) sont rangées dans `fiches/_en-attente/`. Gabarit œuvre sans section « Sources ». GitHub Pages désactivé et `docs/` supprimé. `README.md` remplace LISEZMOI.md. Tout est fusionné dans `main`.
**Décisions prises** : D11.

## 4 novembre 2026 · Lot 3 terminé (étapes 4 à 6)
**Fait** : zoom « Moyen », qui est la vue détail resserrée et non une quatrième vue, avec la même année à gauche et le même couloir en haut d’un zoom à l’autre. Barre du bas adaptée à la vue mouvement (repères tous les 1, 2, 5 ou 10 ans) ; Détail, Moyen et « ‹ Toute la frise » ramènent à l’année du bord gauche. Ligne de vie des artistes retirée. Croix de fermeture gardée (étape 5 abandonnée).
**Écarté** : zoomer avec Ctrl + molette ; mémoriser le zoom ; mettre en noir toutes les décennies visibles.

## 3 novembre 2026 · Lot 3, étapes 1 à 3
**Fait** : couleurs papier ambré (clair) et brun (sombre). Bandeau compact, avec « Une œuvre au hasard ». Index dans le panneau des fiches (onglets, recherche, compteur, « ‹ Retour à l’index »). Bouton clair/sombre, avec un script dans le `<head>` qui pose toujours `data-theme` : plus de flash, un seul bloc sombre dans style.css. Étiquettes d’artistes plus complètes en vue mouvement.
**Écarté** : des chiffres-clés dans le bandeau.

## 3 novembre 2026 · Exploration de design avec Lovable
**Fait** : reconstruire le site avec Lovable est écarté (React, TypeScript et Tailwind, contraires à D1, D3 et D7). Lovable a servi de carnet de croquis : rien de son code n’est repris, et ses idées sont dans la ROADMAP, « Idées à trier ».

## 2 novembre 2026 · Lots 1 et 2 terminés
**Fait** : lot 1, la bulle d’aperçu disparaît enfin (`.bulle.riche { display: flex }` écrasait `[hidden]` : à poids égal, la règle écrite en dernier gagne), la poignée reste en place (c’est `#interieur-panneau` qui défile), la croix est dessinée en CSS. Lot 2 : rubriques Nationalité et Disciplines, « Aussi » cliquable (la colonne « Autres mouvements » contient des identifiants, lus par `lire_autres()`), fiche mouvement avec Œuvres majeures et Autres œuvres, image en grand dans un `<dialog>`.

## 2 novembre 2026 · Phase 1 terminée
**Fait** : D7, D8 et D9 validées, D10 reportée. `wrangler.jsonc` reçoit un bloc `build` (Cloudflare ignorait la commande du tableau de bord) et un bloc `"previews": {}`. La branche `astro` est fusionnée dans `main`, et le premier déploiement de production est validé (wrangler 4.156.2). Les captures avant/après prévues sont abandonnées : la liste de contrôle a suffi.

## 30 octobre 2026 · Début de la migration
**Fait** : la V1 est en ligne, avec 9 mouvements, 34 artistes, 104 œuvres, 14 événements et 12 influences ; 43 fiches sont rédigées sur 147 (aucune œuvre). La branche `astro` est créée : projet Astro minimal, frise copiée à l’identique dans `public/`, construire.py écrit `public/data.js`.

---

<!-- Modèle d’entrée (5 à 10 lignes) :
## JJ mois AAAA · Titre de la tâche
**Fait** :
**Reste à faire** :
**Décisions prises** : (à reporter dans DECISIONS)
Les nouveaux problèmes vont dans la liste en tête du fichier, pas dans l’entrée.
-->