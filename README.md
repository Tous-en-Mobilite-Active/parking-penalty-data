# Pénalité de stationnement — France métropolitaine et DROM

Temps supplémentaire lié à la voiture, en minutes, pour chaque carreau d'environ
100 m × 100 m : **départ** (rejoindre sa voiture à pied, se préparer, sortir de
sa place) et **arrivée** (trouver une place, se garer, sortir de la voiture,
marcher jusqu'à l'adresse).

Produit par le générateur `tools/AppMobiliteActive-parking-builder` du projet
[Tous en Mobilité Active](https://tous-en-mobilite-active.fr), à partir de la BD TOPO® de l'IGN et
d'OpenStreetMap. Utilisé par la carte des zones plus rapidement accessibles en
mobilité active (marche, vélo, vélo électrique) qu'en voiture.

- Publication n° **1** du 02/11/2026, modèle v2
- Adresse des données : https://biz-lab.github.io/parking-penalty-data/v1/
- Volume : 16.9 Mo de tuiles compressées

## Format

`v1/manifest.json` décrit les grilles et la liste des tuiles publiées.
Chaque territoire a sa grille en longitude/latitude (WGS 84) :

    col = floor((lon − lon0) ÷ dLon)      row = floor((lat − lat0) ÷ dLat)
    tuile tx = floor(col ÷ 200), ty = floor(row ÷ 200)

| Territoire | Nom | Tuiles | lon0 | lat0 | dLon | dLat |
|---|---|---|---|---|---|---|
| `myt` | Mayotte | 0 | 44.9 | -13.1 | 0.000921 | 0.0009 |
| `mtq` | Martinique | 0 | -61.4 | 14.3 | 0.000928 | 0.0009 |
| `reu` | La Réunion | 0 | 55.1 | -21.5 | 0.000963 | 0.0009 |
| `glp` | Guadeloupe | 0 | -62 | 15.7 | 0.000935 | 0.0009 |
| `guf` | Guyane | 0 | -54.8 | 2 | 0.0009 | 0.0009 |
| `metro` | France métropolitaine | 1540 | -6 | 41 | 0.0013 | 0.0009 |

`v1/<territoire>/<tx>_<ty>.bin.gz` : tuile compressée (gzip) de
200 × 200 carreaux, 2 bandes de 1 octet à la suite
(`depart`, puis `arrivee`). Dans une bande, carreaux ligne par
ligne du **sud au nord**, et dans une ligne d'**ouest en est** :
`octet = bande × 200² + row_local × 200 + col_local`.
Valeur = minutes × 10 (0 à 25,4 min) ; 255 = pas de donnée (mer…).
Une tuile absente du manifeste, ou un carreau sans donnée, prend la valeur
`defaults` du manifeste (cas rural : place devant la destination).

`v1/overview/<territoire>.json|.bin.gz` : aperçu (moyenne par blocs
de 10 × 10 carreaux), même disposition, pour une carte d'ensemble.

Lecteur JavaScript prêt à l'emploi (sans dépendance) : `js/parking-db.js` du
moteur de la carte (dépôt du site), interpolation bilinéaire entre carreaux.

## Méthode

Offre de stationnement (places sur rue d'après les tronçons BD TOPO, parkings
OpenStreetMap) et demande (voitures des résidents, visiteurs selon l'usage et la
surface des bâtiments) lissées sur un rayon de marche ; pression = demande ÷
offre, convertie en temps de recherche et de marche ; parkings propres aux sites
(zones d'activité, commerces, santé, gares) ; manœuvres selon la densité ;
continuité entre carreaux voisins. Détail sur [le site](https://tous-en-mobilite-active.fr).

## Licence et attribution

Base de données sous licence **ODbL 1.0** (voir `LICENSE.md`) : elle dérive
d'OpenStreetMap. Mentionner : « Pénalité de stationnement — Tous en Mobilité
Active ; sources © IGN BD TOPO® (Licence Ouverte Etalab 2.0), © contributeurs
OpenStreetMap (ODbL) ».
