# Architecture

Ce document décrit comment le site fonctionne (partie A) et ce qu’il doit devenir (partie B). Il s’en tient aux flux, aux fichiers, aux formats et aux principes : le détail des fonctions est dans les commentaires du code, les raisons des choix dans DECISIONS, le mode d’emploi dans le README. Il est mis à jour à la fin de chaque étape qui change ce qu’il décrit.

---

# A. État actuel (novembre 2026, étape 2f terminée)

## Flux des données
```
donnees/frise_art_moderne_base.xlsx ─┐
                                     ├─> python construire.py ─> src/data/frise.json ─┐
fiches/<dossier>/<id>.md ────────────┘                                                ├─> npm run build ─> dist/
src/pages/ + src/layouts/ + src/components/ + src/scripts/app.js + src/lib/ + public/ ┘
```
Le site ne lit ni le tableur ni les fiches, seulement `frise.json`. Au build, Astro transforme chaque fichier de `src/pages/` en une ou plusieurs pages de `dist/` : `index.astro` donne `dist/index.html`, `mouvements/[id].astro` une page par mouvement rédigé, `oeuvres/[id].astro` une page par œuvre documentée, et `oeuvres/index.astro` le catalogue. Il intègre au script de la frise `frise.json`, `src/lib/` et marked, et publie ce script dans `dist/_astro/`, sous un nom qui change à chaque modification. Les pages, elles, sont fabriquées entièrement au build : leur HTML contient déjà tout leur texte. Il copie `public/` tel quel.

## Arborescence
```
README.md                mode d’emploi
construire.py            tableur + fiches -> src/data/frise.json (Python, openpyxl), ≈ 570 lignes
package.json             astro, marked (18.0.14, D30) ; wrangler en devDependencies (4.157.0, D16)
package-lock.json        FABRIQUÉ par npm : ne jamais le modifier à la main
astro.config.mjs         réglages d’Astro (vide)
wrangler.jsonc           réglages de Cloudflare : lance npm run build, publie ./dist
.gitignore               node_modules/, dist/, .astro/
suivi_projet/            les six documents du projet (D12), ignorés par Astro
donnees/frise_art_moderne_base.xlsx
fiches/
  mouvements/ artistes/ oeuvres/     une fiche = un fichier <id>.md
  parcours/ comparaisons/            créés avec leur première fiche (Git ne garde pas un dossier vide)
  _gabarits/                         un gabarit par type de fiche
  _en-attente/                       fiches sans ligne dans le tableur, ignorées par le site
src/
  pages/
    index.astro          structure de la page de la frise
    mouvements/          index.astro (répertoire), [id].astro (une page par mouvement rédigé)
    artistes/            index.astro (répertoire et son filtre), [id].astro (une page par artiste rédigé)
    oeuvres/             index.astro (catalogue, sa recherche et ses filtres), [id].astro (une page par œuvre documentée)
  layouts/Base.astro     gabarit des pages : <head>, en-tête et menu du site, fil d’Ariane
  components/            morceaux communs aux pages : Ligne, Carte, EnteteFiche, Lecture
  scripts/app.js         fonctionnement de la frise, ≈ 755 lignes
  lib/                   partagé par la frise et les pages (D31)
    donnees.js           importe frise.json ; MOUV, ART, OEU, EVT, GRP, couleur, mouvementsTries
    outils.js            esc, sansAccent, nomCourt, compter, estImage…
    fiches.js            fiches du panneau (ficheOeuvre…, lirePage, encartDroits), et pour les pages : aUnePage, adresse, lien, markdown, textePage, encartDroits
  data/frise.json        FABRIQUÉ par construire.py, commité (D8)
public/                  copié tel quel : style.css (≈ 510 lignes), pages.css (≈ 280 lignes), images/, favicons d’Astro (à remplacer en phase 3)
```

## Le tableur
Une ligne compte si sa colonne `ID` est remplie. Colonnes lues :
- **Mouvements** : ID, Nom, Début, Fin, Lieu, Couleur, Résumé court (1 phrase).
- **Groupes** (D22) : ID, Nom, Mouvement, Début, Fin, Lieu, Résumé court (1 phrase). Un groupe est rattaché à un mouvement et n’a pas de fiche.
- **Artistes** : ID, Nom complet, Naissance, Décès, Nationalité (au féminin), Rôle, Mouvement principal, Autres mouvements, Groupes, Phrase-clé (1 phrase), Domaine public ?.
- **Œuvres** : ID, Titre, Artiste (ID), Année, Mouvement, Date affichée, Type, Musée, Ville, Majeure (Bible), Lien image (un fichier image, jamais une page web), Résumé court (1 phrase), Domaine public ? (une formule qui recopie celle de l’artiste), Crédit image, Lien de l’œuvre (la page de l’œuvre sur le site du musée).
- **Événements** : ID, Année, Date précise, Titre, Lieu, Mouvement, Description (1-2 phrases).
- **Influences**, qui réunit toutes les relations (D23) : ID, De (ID), Vers (ID), Type, Explication (1-2 phrases). De et Vers : un artiste ou un mouvement. Types : `influence`, `collaboration`, `rivalité`, `filiation`, `réaction`.
- **Glossaire** (D24) : ID, Terme, Définition, Voir aussi. Un terme n’a pas de fiche.

Une cellule qui contient une liste (Autres mouvements, Groupes, Voir aussi) contient des identifiants séparés par des virgules. Les autres colonnes (Fichier fiche, Statut fiche, Nb…, Notes / corrections, les noms recopiés De et Vers) et l’onglet Lisez-moi servent au suivi et ne sont pas lus. « Domaine public ? » ne sert plus qu’à forcer la protection d’un artiste ou d’une œuvre (D10). openpyxl ne sait pas lire les listes déroulantes du tableur, d’où l’avertissement `Data Validation extension is not supported` : il est sans danger, puisque construire.py n’enregistre jamais le fichier.

## Les fiches
- En-tête YAML (`id`, `type`), puis un titre `#`, puis les rubriques du gabarit.
- Une fiche est **vide** tant qu’elle a moins d’une vingtaine de caractères de vrai texte, hors titres et commentaires (`sans_texte()`). Le site affiche alors « fiche à venir ».
- **Œuvre** (D21, D34) : la présentation, puis l’analyse sous `## Analyse`. Le niveau se déduit de la fiche : `referencee` (vide), `documentee` (présentation seule), `analysee` (analyse rédigée). Une analyse vide est retirée du Markdown.
- **Parcours et comparaison** (D24, D35, D36) : sans ligne dans le tableur. L’identifiant est le nom du fichier, le titre est la ligne `#`. L’en-tête liste sur une ligne les identifiants reliés : `etapes:` (au moins deux, de tout type) ou `elements:` (exactement deux, du même genre : mouvements, artistes ou œuvres).
- Liens : `[[id]]` ou `[[id|texte]]`, vers n’importe quel identifiant.

## construire.py
Une fonction de lecture par onglet et par dossier de fiches, chacune avec ses vérifications. Viennent ensuite `verifier_ensemble()`, pour les vérifications qui demandent tous les identifiants, puis `ecrire()` et `afficher_bilan()`. Le script reste un seul fichier (D32). Il met en forme les textes affichés, jamais les identifiants : apostrophe typographique (`typo()`, sauf dans le Markdown), majuscule de la nationalité, du rôle et de la technique (colonne Type).

**Droits des images (D10).** `droits_artiste()` calcule si l’œuvre d’un artiste est dans le domaine public : l’année de décès + 71, comparée à l’année du build (`ANNEE`). `droits_oeuvre()` y ajoute l’exception éventuelle de l’œuvre. `lire_image()` ne garde l’image que d’une œuvre libre, et `lire_lien()` vérifie le lien vers le musée.

Il signale (`avertir()`) :
- un identifiant inconnu (mouvement, autre mouvement, groupe, artiste, bout d’une relation, « Voir aussi », étape, élément comparé), avec l’identifiant suggéré quand c’est possible ;
- un identifiant utilisé deux fois, tous types confondus ;
- une fiche manquante, ou une fiche sans ligne dans le tableur (`verifier_orphelines()`) ;
- une donnée manquante ou incohérente : année d’une œuvre, type de relation, terme ou définition, début d’un groupe après sa fin, année hors de la date affichée (`verifier_date()`) ;
- une relation dont le type ne relie pas ces genres d’éléments (`TYPES_RELATIONS`) ;
- un lien `[[id]]` inconnu ou mal formé, et un lien vers ce que la frise ne sait pas encore afficher (terme, parcours, comparaison) ;
- un chemin d’image relatif, ou une image absente de `public/` ;
- une image saisie pour une œuvre protégée (retirée), un « Lien image » qui n’est pas un fichier image, une image sans crédit, un « Lien de l’œuvre » qui ne commence pas par `https://` ;
- un désaccord entre la colonne « Domaine public ? » d’un artiste et le calcul ;
- une analyse sans présentation, ou un titre `## Analyse` en double ;
- dans un parcours ou une comparaison : un id ou un type d’en-tête faux, un titre manquant, un nombre ou un genre d’éléments incorrect.

## Format de src/data/frise.json
Un objet `{ mouvements, groupes, artistes, oeuvres, evenements, influences, glossaire, parcours, comparaisons }` :
- **mouvement** : id, nom, debut, fin, lieu, couleur, resume, fiche, ficheVide
- **groupe** : id, nom, mouvement, debut, fin, lieu, resume
- **artiste** : id, nom, naissance, deces, nationalite, role (affiché « Disciplines »), mouvement, autres, groupes, phrase, fiche, ficheVide
- **oeuvre** : id, titre, artiste, annee, mouvement, dateAffichee, type, musee, ville, majeure, image, credit, domainePublic, entreeDomainePublic, lienOeuvre, resume, fiche, ficheVide, niveau

`image` est vide pour une œuvre protégée. `entreeDomainePublic` est l’année où elle sera libre, ou `null` si elle l’est déjà ou si sa protection est forcée.
- **evenement** : id, annee, date, titre, lieu, mouvement, description
- **influence** : de, vers, type, explication
- **terme** : id, terme, definition, voirAussi
- **parcours** : id, titre, etapes, fiche, ficheVide
- **comparaison** : id, titre, elements, fiche, ficheVide

`fiche` contient le Markdown, ou une chaîne vide si la fiche est vide. Les listes (`autres`, `groupes`, `voirAussi`, `etapes`, `elements`) contiennent des identifiants. Une valeur inconnue est signalée ; `autres` et `voirAussi` la gardent, `groupes` l’écarte. La frise ne lit les groupes que pour les liens `[[groupe]]` ; elle n’utilise ni la clé `groupes` des artistes, ni `glossaire`, ni `parcours`, ni `comparaisons`, et n’affiche que les relations entre deux artistes. `niveau` décide, par `aUnePage()`, si une œuvre a sa page. Les pages utilisent `groupes` et toutes les relations ; `glossaire`, `parcours` et `comparaisons` attendent la phase 4.

## La frise
- **index.astro** : la structure fixe de la page (script du thème dans le `<head>`, en-tête, bandeau, zone qui défile, barre du bas, bulle, panneau, `<dialog>` de l’image) et un seul script, `src/scripts/app.js`, qu’Astro traite comme un module.
- **src/lib/** fabrique ce qui sert aussi aux pages : données, outils, contenu des fiches. Rien n’y touche à `document` ni à `window`, pour que ces fichiers fonctionnent au build.
- **app.js** fait tout le reste :
  - `REGLAGES`, en tête du fichier, réunit les réglages modifiables ;
  - l’objet `etat` dit ce qui est affiché, `dessiner()` refait la frise à partir de lui, et `fenetre()` calcule la période et l’échelle.
- **Vues** :
  - « détail », par défaut : un couloir par mouvement, une carte par œuvre, placée à son année et non à sa date affichée (D33) ;
  - « ensemble » : toute la période, sans défilement ;
  - « mouvement » : une ligne par artiste, à l’adresse `/?mouvement=<id>` (D38).

  Le zoom « Moyen » est la vue détail resserrée.
- **Adresse** : elle suit l’affichage, par `history.replaceState`, sans ajouter d’étape à l’historique (D43). `changerVue()` tient à jour `?mouvement=<id>`, et `ouvrir()` le `#id` de la fiche ouverte ; les deux se combinent (`/?mouvement=fauvisme#matisse`). Au démarrage, la vue est affichée d’abord, la fiche ensuite ; un `?mouvement=` inconnu est retiré de l’adresse.
- **Navigation** : glisser, flèches du clavier, barre du bas (zooms et repères), sommaire des mouvements, recherche insensible aux accents, « Une œuvre au hasard ».
- **Panneau** : il affiche une fiche ou l’Index. Les liens `[[id]]` deviennent des liens `#id`, qui ouvrent la fiche sur place ; un lien vers un groupe mène à la page de son mouvement (`destinationPanneau()`). Une fiche qui a sa page affiche « Lire la page » (D19), un vrai lien que le panneau n’intercepte pas. Une fiche d’œuvre commence par son image et son crédit, ou par l’encart d’une œuvre protégée (`encartDroits()`, D10).
- **Thème** : `data-theme` sur `<html>`, mémorisé dans `localStorage` (clé `theme`).
- **Accessibilité**, à préserver : ARIA, focus visible, clavier, `prefers-reduced-motion`, `aria-live` du panneau coupé pendant l’Index.

## Les pages
- **Gabarit** : chaque page passe par `src/layouts/Base.astro`. Il pose le `<head>` (une copie de celui d’index.astro jusqu’au lot 4), l’en-tête du site avec son menu et le fil d’Ariane. Le menu montre Frise, Mouvements, Artistes et Œuvres : Comprendre n’y entrera qu’avec son contenu (D18). Pas de bouton de thème avant le lot 4 : les pages suivent le choix fait sur la frise, ou le réglage de l’appareil.
- **Fabrication** : tout est calculé au build, depuis `frise.json`. Une page dynamique (`[id].astro`) exporte `getStaticPaths()`, qui renvoie un objet par page à fabriquer ; seuls les éléments dont `aUnePage()` est vrai en reçoivent une (D13).
- **Adresses** : `aUnePage()` et `adresse()` (src/lib/fiches.js) sont les seules à décider où mène un lien (D40). Les composants `Ligne` et `Carte` en déduisent leur adresse et leur flèche (« Lire la page » ou « Voir dans la frise »). Le texte des fiches passe par `textePage()`, qui appelle `markdown(md, true)` et donne un id à chaque titre `##`, pour le sommaire.
- **Composants** (`src/components/`) :
  - `Ligne` : une ligne de liste ;
  - `Carte` : une œuvre, ou un mouvement dans son répertoire (D37), avec l’étoile des œuvres majeures. Avec une vignette (catalogue, « Du même artiste ») : un cadre égal, l’image bord à bord ou un aplat (hachuré « Œuvre protégée », uni « Image à venir »), et la couleur du mouvement en trait sous l’image. Elle recopie sur son lien les attributs qu’on ne lui déclare pas (`data-…`) ;
  - `EnteteFiche` : la bande de couleur, le titre, « Voir dans la frise », et le repère chronologique d’un mouvement ;
  - `Lecture` : le sommaire « Dans cette page » et la colonne de texte.
- **Page mouvement** : l’en-tête avec ses dates et son lieu, le repère, puis le texte, les groupes (une section et une ancre chacun), les artistes, les œuvres majeures en cartes, les autres œuvres en liste, les moments clés, les filiations et réactions.
- **Page artiste** : l’en-tête sans chapeau (D41), les rubriques (Nationalité, Disciplines, Mouvement, Aussi, Groupes), le texte, les œuvres en cartes, les liens avec d’autres artistes et avec des mouvements.
- **Page œuvre** (D45) : un cartel, sans le composant `Lecture` ni sommaire. L’en-tête (technique, date, « Œuvre majeure », titre), l’image et sa légende ou l’encart des droits (D10, D44), les rubriques Artiste, Mouvement et Où la voir, la présentation et l’analyse, puis « Du même artiste » en cartes avec vignette.
- **Catalogue** (D47) : les œuvres rangées par mouvement, en cartes avec vignette, avec une recherche par titre ou par artiste, sans tenir compte des accents, un choix du mouvement et la case « Œuvres majeures seulement ».
- **Scripts**, en amélioration progressive : le filtre du répertoire des artistes (D42), la recherche et les filtres du catalogue (D47), et l’image en grand de la page œuvre, qui reprend le `<dialog>` `.visionneuse` de la frise.
- « Voir dans la frise » mène à la vue mouvement (`/?mouvement=<id>`) depuis une page mouvement, et à `/#id` depuis une page artiste ou œuvre (D27).

## style.css et pages.css
style.css est rangé par élément de la page, dans l’ordre où on les rencontre ; chaque section a son bloc `@media (max-width: 720px)`. Le bloc `prefers-reduced-motion` est en toute fin. Les couleurs et polices sont des variables sur `:root`, et le mode sombre est dans `:root[data-theme="dark"]`. Police Archivo (Google Fonts). La couleur des mouvements vient du tableur.

pages.css, la feuille des pages (D39), est chargée après style.css et rangée de la même façon. Elle en reprend les variables, donc le mode sombre, et annule par `body.page` la mise en page de la frise. Ses classes ne reprennent jamais un nom de style.css, sauf pour un élément commun à la frise et aux pages (D44) : l’encart des droits, rangé sous `.texte`, et la fenêtre d’image, utilisée telle quelle.

## Déploiement
À chaque push, Cloudflare installe les dépendances et lance `npx wrangler deploy`. Wrangler lit le bloc `build` de `wrangler.jsonc`, lance `npm run build` et publie `./dist`. La Build command de l’onglet Production du tableau de bord reste vide, sans quoi le site serait fabriqué deux fois.
- `main` est la production. Les autres branches ont une version de test, publiée par `wrangler preview` (en bêta) grâce au bloc `"previews": {}`.
- wrangler et marked sont en version exacte : on ne les met à jour que par choix, dans une branche testée.
- GitHub Pages est désactivé (D11).

---

# B. Architecture cible

## Principes
1. Le tableur et les fiches restent la source de vérité (D5) ; construire.py reste la seule porte d’entrée des données (D7).
2. Astro génère des pages statiques : la frise, plus une page par fiche rédigée ou par œuvre documentée (D13, D21). Ni serveur, ni base de données, ni framework.
3. La frise garde sa logique et son apparence, et reste l’accueil, à `/` (D3, D17).
4. Un outil, une question ; un texte, une page ; les listes sont calculées, jamais écrites (D19).
5. Une seule fabrique pour les fiches : `src/lib/` sert au panneau comme aux pages (D15).
6. Une rubrique n’apparaît dans le menu qu’avec son contenu (D18).
7. La frise est l’affiche, les pages sont le livre (D25).
8. À chaque choix, la solution la plus simple qui marche.

## Plan du site
```
/                                 la frise ; /?mouvement=<id> pour la vue mouvement (D38)
/mouvements/  /mouvements/<id>/   répertoire, puis une page par mouvement ; ses groupes en sections (#<id-du-groupe>)
/artistes/    /artistes/<id>/     répertoire (par mouvement principal), puis une page par artiste
/oeuvres/     /oeuvres/<id>/      catalogue filtrable, puis une page par œuvre documentée
/comprendre/  parcours/<id>/  comparer/<id>/  glossaire/     phase 4
/a-propos/  /plan-du-site/        dans le pied de page
/en/…                             phase 5
```
Le menu : Frise, Mouvements, Artistes, Œuvres, Comprendre (dès la phase 4), puis « Rechercher » et le thème (D28). Les adresses sont rangées par type, jamais imbriquées (D18). Les événements n’ont pas de page.

**Liens** : une seule fonction de `src/lib/` transforme `[[id]]`. Dans le panneau de la frise, elle donne `#id`, sauf pour un groupe, qui mène à la page de son mouvement. Sur les pages, elle donnera :
- `/<type>/<id>/` si l’élément a une page ;
- `/mouvements/<mouvement>/#<id>` pour un groupe ;
- `/comprendre/glossaire/#<id>` pour un terme ;
- `/#id` dans tous les autres cas.

## Le rôle de chaque partie
| Partie | Question | Ce qu’elle ne fait pas |
|---|---|---|
| Frise | Quand ? Qu’est-ce qui se passe en même temps ? | Son panneau mène à « Lire la page ». |
| Vue mouvement | Qui, et dans quel ordre ? | Pas de texte : elle mène à la page du mouvement. |
| Pages | Quoi, et pourquoi ? | Un repère simple et « Voir dans la frise » (D27). |
| Comprendre | Comment tout cela s’articule ? | Elle relie les fiches, sans les recopier. |
| Rechercher | Où est telle chose ? | Un outil, pas une rubrique. |

## Les gabarits
Ils partagent leurs composants : en-tête de fiche, fil d’Ariane, listes, carte d’œuvre, repère chronologique.
- **Mouvement** : repère chronologique, texte, groupes, artistes, œuvres majeures puis autres œuvres, moments clés, filiations.
- **Artiste** : rubriques (Nationalité, Disciplines, Mouvement, Aussi, Groupes), texte, œuvres dans l’ordre chronologique, liens.
- **Œuvre**, pensée comme un cartel : titre, artiste, date, technique, lieu, reproduction si les droits le permettent (D10), présentation, puis analyse si elle existe.
- **Répertoire**, **Article** (parcours, comparaison) et **Page simple** (À propos, plan du site, 404).

## Arborescence visée
```
src/
  layouts/Base.astro           <head>, polices, script du thème, en-tête et pied de page du site
  components/                  les morceaux communs des gabarits
  pages/
    index.astro                la frise
    mouvements/index.astro  mouvements/[id].astro
    artistes/index.astro    artistes/[id].astro
    oeuvres/index.astro     oeuvres/[id].astro
    a-propos.astro  plan-du-site.astro
    comprendre/…               phase 4
  scripts/app.js  lib/  data/frise.json     comme aujourd’hui
```

## À trancher, à l’étape indiquée
- **Avant les premières filiations** : le sens de De → Vers dans une relation « filiation » ou « réaction ».
- **Lot 4** : le zoom affiché lors d’une première visite ; le découpage de la suite d’app.js, si besoin (D31) ; le filtre du répertoire des artistes (D42) et la recherche du catalogue (D47) face à « Rechercher ».
- **Après la phase 2** : le panneau de la frise réduit à une fiche courte (D19).
- **Phase 5** : l’organisation de `/en/…`, avant de commencer.