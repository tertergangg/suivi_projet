# Architecture

Ce document décrit le site tel qu’il est (partie A) et tel qu’il doit devenir (partie B). Il s’en tient au flux des données, aux fichiers, aux formats et aux principes : le détail de chaque fonction est dans les commentaires du code. Il est mis à jour à la fin de chaque étape qui change ce qu’il décrit.

---

# A. État actuel : V1, statique, fabriquée par Astro (novembre 2026)

## Flux des données
```
donnees/frise_art_moderne_base.xlsx ─┐
                                     ├─> python construire.py ─> src/data/frise.json ─┐
fiches/<type>/<id>.md ───────────────┘                                                ├─> npm run build ─> dist/
src/pages/index.astro + src/scripts/app.js + src/lib/ + public/ (style.css, images) ──┘
```
Le site lui-même ne lit ni le tableur ni les fiches : il ne lit que `frise.json`, produit par `construire.py`. Astro transforme `index.astro` en `dist/index.html`. Il intègre au script de la frise `frise.json`, les fichiers de `src/lib/` et la librairie marked, puis publie ce script dans `dist/_astro/`, sous un nom qui change à chaque modification. Enfin, il copie le dossier `public/` tel quel dans `dist/`.

## Arborescence
C’est l’état de la branche `main`, celle qui est en ligne.
```
README.md                   mode d’emploi (installation, travail sur le contenu, publication)
construire.py               tableur + fiches -> src/data/frise.json (Python, librairie openpyxl)
package.json                projet Astro : dépendances astro et marked (version exacte, D30), wrangler en devDependencies (version exacte, D16), scripts dev, build, preview
package-lock.json           FABRIQUÉ par npm : versions exactes de toutes les dépendances, ne jamais le modifier à la main
astro.config.mjs            configuration d’Astro (par défaut, vide)
wrangler.jsonc              configuration Cloudflare : lance npm run build, publie ./dist
.gitignore                  ignore node_modules/, dist/, .astro/
suivi_projet/               documents de suivi du projet (D12), ignorés par Astro
donnees/
  frise_art_moderne_base.xlsx
fiches/
  _gabarits/                modèles : gabarit-mouvement.md, gabarit-artiste.md, gabarit-oeuvre.md
  _en-attente/              fiches gardées en attendant leur ligne dans le tableur (artistes/, oeuvres/)
  mouvements/  artistes/  oeuvres/     une fiche = un fichier <id>.md
src/
  pages/index.astro         structure de la page de la frise
  scripts/app.js            fonctionnement de la frise (≈ 740 lignes), module traité par Astro
  lib/                      partagé par la frise et les futures pages ; rien n’y touche à la page (D31)
    donnees.js              importe frise.json ; accès par identifiant (MOUV, ART, OEU, EVT), couleur, mouvementsTries
    outils.js               esc, sansAccent, nomCourt, compter, estImage…
    fiches.js               contenu des fiches (ficheOeuvre, ficheArtiste, ficheMouvement, ficheEvenement), avec marked
  data/frise.json           GÉNÉRÉ par construire.py, commité (D8) : ne jamais le modifier à la main
public/                     copié tel quel dans dist/
  style.css                 apparence (≈ 480 lignes)
  images/
  favicon.svg  favicon.ico  logos par défaut d’Astro (à remplacer en phase 3)
```
Les fiches de `fiches/_en-attente/` n’apparaissent pas sur le site. Quand leur ligne est ajoutée au tableur, il faut les ramener dans `fiches/artistes` ou `fiches/oeuvres`, sans quoi construire.py signale « Fiche manquante ».

## Le tableur
Une ligne compte comme un élément si sa colonne `ID` est remplie. Colonnes lues par `construire.py` :
- **Mouvements** : ID, Nom, Début, Fin, Lieu, Couleur, Résumé court (1 phrase).
- **Groupes** (D22) : ID, Nom, Mouvement, Début, Fin, Lieu, Résumé court (1 phrase). Un groupe est rattaché à un seul mouvement et n’a pas de fiche.
- **Artistes** : ID, Nom complet, Naissance, Décès, Nationalité, Rôle, Mouvement principal, Autres mouvements, Phrase-clé (1 phrase), Groupes.
  - « Autres mouvements » contient des **identifiants de mouvements séparés par des virgules** (`fauvisme, cubisme`).
  - « Groupes » contient des **identifiants de groupes séparés par des virgules**. Un artiste peut appartenir à plusieurs groupes, même d’un autre mouvement que son mouvement principal.
  - « Nationalité » est attendue au féminin (« française ») : l’étiquette de la vue mouvement affiche « Nationalité française ».
- **Œuvres** : ID, Titre, Artiste (ID), Année, Mouvement, Date affichée, Type, Musée, Ville, Majeure (Bible), Lien image, Résumé court (1 phrase).
- **Événements** : ID, Année, Date précise, Titre, Lieu, Mouvement, Description (1-2 phrases).
- **Influences** : ID, De (ID), Vers (ID), Type, Explication (1-2 phrases). L’onglet réunit toutes les relations (D23). De et Vers acceptent un artiste ou un mouvement. Les types : `influence` (d’un artiste vers un artiste ou un mouvement), `collaboration` et `rivalité` (entre deux artistes), `filiation` et `réaction` (entre deux mouvements).

Les autres colonnes (Fichier fiche, Statut fiche, Nb…, Domaine public ?, De, Vers, Notes / corrections) servent au suivi et ne sont pas lues. L’onglet « Lisez-moi » non plus.
Le tableur contient des listes déroulantes Excel qu’openpyxl ne sait pas lire : d’où l’avertissement `Data Validation extension is not supported`, sans danger puisque construire.py n’enregistre jamais le fichier.

## construire.py
1. Lit les six onglets, et s’arrête avec un message clair si l’un d’eux manque.
2. Pour chaque mouvement, artiste et œuvre, lit `fiches/<dossier>/<id>.md`, sans l’en-tête YAML, le premier titre `#` et les commentaires `<!-- -->`. Une fiche de moins d’une vingtaine de caractères de vrai texte est considérée comme vide : le site affiche alors « fiche à venir ».
3. Met en forme les textes affichés, jamais les identifiants : apostrophe typographique (`typo`, pas sur le Markdown des fiches), majuscule initiale de la nationalité et du rôle (`majuscule`), découpage de « Autres mouvements » et de « Groupes » en listes (`decouper`, puis `lire_autres` et `lire_groupes_artiste`).
4. Vérifie la cohérence et affiche des avertissements (`avertir()`) :
   - mouvement, autre mouvement, groupe ou artiste inconnu, avec l’identifiant suggéré quand c’est possible ;
   - groupe dont le mouvement est inconnu ou dont le début suit la fin ;
   - relation dont un bout n’est ni un artiste ni un mouvement, dont le type manque ou sort de la liste de D23, ou qui relie des genres d’éléments que son type n’accepte pas (`TYPES_RELATIONS`) ;
   - fiche manquante, année manquante, identifiant en double (tous types confondus, groupes compris) ;
   - fiche sans ligne dans le tableur (`verifier_orphelines()`, qui ne parcourt pas les sous-dossiers) ;
   - lien `[[id]]` vers un identifiant inconnu, ou mal formé (`verifier_liens()`, même règle que `markdown()` dans `src/lib/fiches.js`) ; lien vers un groupe, signalé jusqu’à l’étape 2e parce que la frise ne sait pas l’afficher ;
   - chemin d’image relatif, ou image absente de `public/` (`verifier_image()`) ;
   - année d’une œuvre qui sort des années de sa date affichée (`verifier_date()`, avec `annees_ecrites()`, qui comprend « vers 1893 », « 1884-1886 » ou « années 1920 »).
5. Écrit `src/data/frise.json`, un objet JSON `{ mouvements, groupes, artistes, oeuvres, evenements, influences }`.

`main()` enchaîne ces étapes : une fonction de lecture par onglet (les groupes avant les artistes, qui y renvoient), puis `verifier_ensemble()`, `ecrire()` et `afficher_bilan()`. Le script reste en un seul fichier (D32).

## Format des données (src/data/frise.json)
- **mouvement** : id, nom, debut, fin, lieu, couleur, resume, fiche, ficheVide
- **groupe** : id, nom, mouvement, debut, fin, lieu, resume
- **artiste** : id, nom, naissance, deces, nationalite, role, mouvement, autres, groupes, phrase, fiche, ficheVide
  - `autres` est une **liste** : des identifiants de mouvements, ou du texte pour une valeur inconnue.
  - `groupes` est une **liste** d’identifiants de groupes connus : une valeur inconnue est signalée et écartée.
  - `role` est affiché sous le libellé « Disciplines ».
- **oeuvre** : id, titre, artiste, annee, mouvement, dateAffichee, type, musee, ville, majeure (booléen), image, resume, fiche, ficheVide
- **evenement** : id, annee, date, titre, lieu, mouvement, description
- **influence** : de, vers (un artiste ou un mouvement), type, explication

Le champ `fiche` contient le Markdown de la fiche, ou une chaîne vide si elle n’est pas rédigée. La frise ne lit pas encore `groupes`, et n’affiche que les relations entre deux artistes.

## index.astro
La structure fixe de la page : le script du `<head>` qui pose `data-theme` avant l’affichage, l’en-tête, le bandeau de la vue mouvement, la zone qui défile, la barre du bas, la bulle, le panneau et le `<dialog>` de l’image en grand. Un seul script termine la page : `src/scripts/app.js`, sans `is:inline`. Astro le traite comme un module, résout ses imports (`frise.json`, `src/lib/`, marked) et le publie dans `dist/_astro/`.

## app.js (src/scripts/) et src/lib/
- **`src/lib/`** contient ce qui servira aussi aux pages : les données (`donnees.js`), les petits outils (`outils.js`) et le contenu des fiches (`fiches.js`). Rien n’y touche à la page (ni `document` ni `window`), pour que ces fichiers fonctionnent au build, dans Node. Un fichier n’exporte que ce dont un autre a besoin. La frise passe à `ficheMouvement(m, avecExplorer)` l’information qu’elle seule connaît.
- **app.js** importe ses données et le contenu des fiches depuis `src/lib/` (D29, D31). C’est un module : il s’exécute en mode strict, et ses fonctions ne sont pas globales. L’objet `REGLAGES`, en tête du fichier, réunit les réglages modifiables : période affichée, année de départ, échelle et largeur des cartes aux zooms « Détail » et « Moyen », hauteur des lignes, marges de la vue mouvement.
- **Un état, un dessin.** L’objet `etat` dit ce qui est affiché (vue, mouvement, zoom Moyen, case « Œuvres majeures seulement »). `dessiner()` refait tout le HTML de la frise à partir de cet état, et `fenetre()` calcule la période et l’échelle.
- **Trois vues** : « détail » (la frise complète, un couloir par mouvement, une carte par œuvre, vue par défaut), « ensemble » (toute la période sans défilement, une bande par mouvement), « mouvement » (un mouvement en grand, une ligne par artiste). Le zoom « Moyen » n’est pas une quatrième vue : c’est la vue détail resserrée (`etat.moyen`), dont seule `fenetre()` tient compte.
- **Cartes d’œuvres** : elles affichent l’année qui les place sur la frise, et non la date affichée (D33).
- **Navigation** : glisser à la souris, flèches du clavier, barre du bas (trois boutons de zoom, puis des repères : les décennies, ou des années en vue mouvement), sommaire des mouvements, recherche insensible aux accents, bouton « Une œuvre au hasard ».
- **Panneau** : il affiche une fiche, dont le HTML vient de `src/lib/fiches.js`, ou l’Index. S’y ajoutent la bulle d’aperçu au survol d’une œuvre et l’image en grand (`<dialog>`).
- **Liens internes** : `[[id]]` ou `[[id|texte]]` devient un lien `#id`. Une fiche ouverte met `#id` dans l’adresse ; une adresse en `#id` ouvre la fiche.
- **Markdown** converti par marked, dépendance npm intégrée au script (D30).
- **Thème** : le bouton inverse `data-theme` sur `<html>` et mémorise le choix (`localStorage`, clé `theme`).
- **Accessibilité**, à préserver : attributs ARIA, focus visible, navigation au clavier, respect de `prefers-reduced-motion`, `aria-live` du panneau coupé pendant l’Index.

## style.css
- Rangé par élément de la page, dans l’ordre où on les rencontre. Chaque section réunit la disposition et l’apparence de son élément, puis son bloc `@media (max-width: 720px)` pour le téléphone. Une nouvelle règle va dans la section de son élément, jamais en fin de fichier.
- Le bloc `prefers-reduced-motion` est en toute fin : à poids égal, c’est la règle écrite en dernier qui gagne.
- Couleurs et polices en variables sur `:root`, mode sombre dans `:root[data-theme="dark"]`. Un seul bloc sombre suffit, puisque le script du `<head>` pose toujours l’attribut. Police Archivo (Google Fonts). La couleur de chaque mouvement vient du tableur.

## Déploiement
À chaque push, Cloudflare installe les dépendances, puis lance la Deploy command `npx wrangler deploy`. Wrangler lit le bloc `build` de `wrangler.jsonc`, lance `npm run build`, puis publie `./dist`. Dans le tableau de bord, la Build command de l’onglet Production reste vide, sans quoi le site serait fabriqué deux fois.
- Branche de production : `main`. Adresse : https://frise-chronologique---art-moderne.tjimblet.workers.dev
- Les autres branches ont une version de test (encadré « Previews »), publiée par `wrangler preview` (commande en bêta) grâce au bloc `"previews": {}`.
- wrangler est fixé à une version exacte dans les `devDependencies` (4.157.0, D16) : `npx wrangler deploy` utilise cette version, installée avec le reste. On la change seulement par choix, dans une branche testée. marked l’est aussi, dans les `dependencies` (18.0.14, D30).
- GitHub Pages est désactivé : l’ancienne adresse en github.io renvoie une erreur 404 (D11).

---

# B. Architecture cible : le site complet (phase 2 et suivantes)

Les choix de cette partie sont fixés par D17 à D33. Un prototype de structure, publié sur claude.ai le 6 novembre 2026 et rangé hors du dépôt, en donne une image cliquable ; il est antérieur aux décisions D20 à D22 et montre encore une sous-rubrique « Textes ».

## Principes
1. **Les sources ne bougent pas.** Le tableur et les fiches restent la source de vérité et se modifient comme aujourd’hui (D5).
2. **construire.py reste la porte d’entrée des données** et garde toutes ses vérifications (D7). Il écrit `src/data/frise.json`, commité (D8).
3. **Astro génère des pages HTML statiques** : la frise, plus une page par fiche rédigée (D13, D21). Pas de serveur ni de base de données.
4. **La frise garde sa logique et son apparence**, et reste l’accueil (D17). Son script est un module traité par Astro, sans réécriture (D15).
5. **Un outil, une question ; un texte, une page** (D19). Les listes sont calculées, jamais écrites.
6. **Une seule fabrique pour les fiches.** Les fonctions qui produisent le contenu d’une fiche sont dans `src/lib/` et servent au panneau comme aux pages.
7. **Une rubrique n’apparaît qu’avec son contenu** (D18).
8. **La frise est l’affiche, les pages sont le livre** (D25).
9. **Aucun framework** d’interface (React, Vue…) ni de CSS (Tailwind…).

## Plan du site
```
/                          la frise : zooms Ensemble, Moyen, Détail ; vue mouvement ; panneau des fiches
/mouvements/               répertoire des mouvements
  /mouvements/<id>/        page mouvement ; ses groupes en sections (#<id-du-groupe>)
/artistes/                 répertoire des artistes, rangés par mouvement principal
  /artistes/<id>/          page artiste
/oeuvres/                  catalogue : filtres mouvement, œuvres majeures, « Écrits et manifestes »
  /oeuvres/<id>/           page œuvre, dès que l’œuvre est documentée
/comprendre/               accueil de la rubrique (phase 4)
  /comprendre/parcours/<id>/
  /comprendre/comparer/<id>/
  /comprendre/glossaire/   une page, une ancre par terme
/a-propos/  /plan-du-site/ dans le pied de page
/en/…                      le même arbre (phase 5)
```
- Les adresses sont rangées par type, jamais imbriquées, et reprennent les identifiants du tableur (D9, D18).
- Le menu, commun à tout le site : Frise, Mouvements, Artistes, Œuvres, Comprendre (dès la phase 4), puis « Rechercher » et le thème (D28).
- Les événements n’ont pas de page : ils vivent dans la frise et dans les « Moments clés » de leur mouvement. Les groupes n’ont pas de page au départ (D22).
- Un lien vers un élément sans page mène à la frise, qui ouvre sa fiche (`/#id`).

## Le rôle de chaque partie
| Partie | Question | Ce qu’elle ne fait pas |
|---|---|---|
| Frise | Quand ? Qu’est-ce qui se passe en même temps ? | Elle ne remplace pas les pages : son panneau mène à « Lire la page ». |
| Vue mouvement | Qui, et dans quel ordre, au sein d’un mouvement ? | Elle n’a pas de texte : elle mène à la page du mouvement. |
| Pages | Quoi, et pourquoi ? | Elles n’ont pas de chronologie complète : un repère simple et « Voir dans la frise » (D27). |
| Comprendre | Comment tout cela s’articule ? | Elle ne recopie aucune fiche : elle les relie. |
| Rechercher | Où est telle chose ? | C’est un outil, pas une rubrique. |

## Les gabarits
Peu de gabarits, qui partagent leurs composants : en-tête de fiche (bande de la couleur du mouvement), fil d’Ariane, sommaire « Dans cette page », listes, carte d’œuvre, repère chronologique.
- **Accueil** : la frise.
- **Répertoire** : `/mouvements/`, `/artistes/`, `/oeuvres/`, et plus tard l’accueil de Comprendre.
- **Page mouvement**, une page de contexte : repère chronologique, texte, groupes, artistes, œuvres majeures puis autres œuvres, moments clés, filiations (d’où il vient, ce qu’il annonce).
- **Page artiste**, une page de trajectoire : rubriques (Nationalité, Disciplines, Mouvement, Aussi, Groupes), texte, œuvres dans l’ordre chronologique, liens avec d’autres artistes et mouvements.
- **Page œuvre**, une page cartel : le cartel en grand (titre, artiste, date, technique, lieu de conservation), la reproduction si les droits le permettent (D10), la présentation, puis l’analyse si elle existe, enfin les œuvres du même artiste.
- **Article** : un parcours ou une comparaison (phase 4).
- **Page simple** : À propos, plan du site, 404.

Sur les pages, les cartes sont réservées aux œuvres. Les mouvements et les artistes s’affichent en listes ou dans le texte (D25).

## Modèle de données cible
Ce qui change dans le tableur et dans `frise.json` (étape 2d) :
- **Onglet « Groupes »** et colonne « Groupes » de l’onglet Artistes : faits (sous-étape 2d.1, voir partie A). Exemple d’un groupe d’un autre mouvement : Kandinsky, mouvement principal `abstraction`, groupe Der Blaue Reiter, rattaché à l’expressionnisme.
- **Onglet « Influences »** étendu aux mouvements, avec la liste fermée des types de D23 : fait (sous-étape 2d.2, voir partie A).
- **Onglet « Glossaire »** (nouveau) : ID, Terme, Définition, Voir aussi (identifiants séparés par des virgules). Une fiche y renvoie par `[[id]]` (D24).
- **Niveau d’une œuvre**, déduit de sa fiche, sans colonne (D21) : fiche vide, l’œuvre est référencée ; présentation sans analyse, elle est documentée ; analyse présente, elle est analysée. Le titre qui marque l’analyse sera fixé avec le nouveau gabarit.
- **Fiches** : deux nouveaux dossiers, `fiches/parcours/` et `fiches/comparaisons/`. Leur en-tête YAML liste les étapes (identifiants dans l’ordre) ou les deux éléments comparés.
- **Identifiants** : groupes, termes, parcours et comparaisons partagent l’espace unique des autres éléments (D9). Le contrôle des doublons de construire.py couvre déjà les groupes, et s’étendra aux autres.

Ce qui ne change pas : « Mouvement principal » (la frise a besoin d’un couloir par artiste) et « Autres mouvements » ; la paire Année / Date affichée pour les dates incertaines ; le format des fiches existantes.
Ce qu’on ne stocke pas : « contemporain » (calculé à partir des dates) ; la période d’un artiste dans un mouvement (déduite des dates de ses œuvres).
Repoussé : les attributions discutées.

## Arborescence visée
```
astro.config.mjs  package.json  wrangler.jsonc  .gitignore  README.md
construire.py                 écrit src/data/frise.json
suivi_projet/  donnees/       inchangés
fiches/
  mouvements/  artistes/  oeuvres/  _gabarits/  _en-attente/
  parcours/  comparaisons/    nouveaux (D24)
public/                       copié tel quel : images, favicon
src/
  data/frise.json             GÉNÉRÉ, commité (D8)
  layouts/Base.astro          <head>, polices, script du thème, en-tête et pied de page du site
  pages/
    index.astro               la frise (D17)
    mouvements/index.astro    répertoire ; mouvements/[id].astro : une page par mouvement
    artistes/index.astro      répertoire ; artistes/[id].astro : une page par artiste
    oeuvres/index.astro       catalogue ; oeuvres/[id].astro : une page par œuvre documentée
    a-propos.astro  plan-du-site.astro
    comprendre/…              phase 4
  scripts/app.js              la frise : navigation, vues, recherche, Index (D31)
  lib/                        partagé par la frise et les pages : données, outils (esc, nomCourt, compter…), contenu des fiches, liens [[id]], Markdown
```

## Ce qui est déjà décidé
- **Accueil** : la frise, à `/`, pour de bon (D17).
- **Rubriques et adresses** : D18. **Comprendre** : D20, ouverte après le lancement (D26).
- **Pages** : seulement pour les fiches rédigées (D13) ; pour une œuvre, dès qu’elle est documentée (D21).
- **Script de la frise** : un module traité par Astro, qui partage `src/lib/` avec les pages (D15).
- **Données de la frise** : le module importe `src/data/frise.json`, par l’intermédiaire de `src/lib/donnees.js` (D29).
- **Liens** : une seule fonction de `src/lib/` transforme `[[id]]`. Dans le panneau de la frise, elle donne `#id`. Sur les pages, elle donnera `/<type>/<id>/` si la fiche est rédigée, `/mouvements/<mouvement>/#<id>` pour un groupe, `/comprendre/glossaire/#<id>` pour un terme, `/#id` sinon.
- **Images** : chemins absolus (`/images/…`), vérifiés par construire.py, pour fonctionner à toutes les profondeurs d’adresse.
- **Découpage d’app.js** : seuls le contenu des fiches et ses outils sont dans `src/lib/` (D31), sans renommage. `majDecennies()`, `.decennie` et `#decennies` gardent leur nom.
- **Markdown** : marked, dépendance npm, utilisé par la frise et au build (D30).
- **Recherche** : un seul outil, au lot 4 (D28).

## Points à trancher, à l’étape indiquée (voir ROADMAP)
- **2d · Analyse d’une œuvre** : le titre de section qui marque le niveau « analysée ».
- **2e · Vue mouvement** : la forme de son adresse (D27), sachant que `/#id` ouvre aujourd’hui la fiche dans la vue détail.
- **2e · Styles** : style.css reste-t-il dans `public/`, lié tel quel, avec les règles des pages ajoutées par section ? Ou les pages ont-elles leur propre feuille ? Le plus simple est de garder un seul fichier.
- **2f · Images** : D10, avant le catalogue.
- **Lot 4** : le zoom affiché lors d’une première visite ; le découpage de la suite d’app.js, si besoin (D31).
- **Après la phase 2** : le passage du panneau de la frise à une fiche courte (D19).

## Phase 5
L’organisation des fichiers pour la version anglaise (`/en/…`) est à décider avant de commencer, pas pendant.

## Déploiement visé
C’est déjà le cas depuis la phase 1 : Cloudflare lance `npm run build` (via `wrangler.jsonc`) et publie le dossier `./dist`. Comme `frise.json` est commité, Cloudflare n’a besoin ni de Python ni du tableur.