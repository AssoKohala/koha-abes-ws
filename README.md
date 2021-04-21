# Groupe Koha/ABES

Le groupe **Koha-ABES de l'Hackathon Koha 2021** a évalué les **services web de
l'ABES** dans la perspective de leur utilisation depuis le SIGB Koha. 

![Abes](img/logo-abes.svg)
![KohaLa](img/logo-kohala.png)

CONTENU :

- [Groupe Koha/ABES](#groupe-kohaabes)
  - [Participants](#participants)
  - [Bilan](#bilan)
    - [Fonctionnalités unifiées](#fonctionnalités-unifiées)
    - [Notices biblio](#notices-biblio)
    - [Notices d'autorité](#notices-dautorité)
  
## Participants

- ABES :
  - Mickaël Seror, seror@abes.fr
  - Thomas Michaux, michaux@abes.fr
  - François Mistral, mistral@abes.fr
- KohaLa :
  - Aurore Sorieux, aurore.sorieux@univ-rennes2.fr
  - Asyeh Ghafourian, asyeh.ghafourian@bulac.fr
  - Olivier Delangle, olivier.delangle@univ-amu.fr
  - Julien Sicot, julien.sicot@univ-rennes2.fr
  - Sonia Bouis, sonia.bouis@univ-lyon3.fr
  - Frédéric Demians, f.demians@tamil.fr
  - Matthias Meusburger, matthias.meusburger@biblibre.com

Services Web considérés :

- [multiwhere](http://documentation.abes.fr/sudoc/manuels/administration/aidewebservices/index.html#multiwhere)
- [merged](http://documentation.abes.fr/sudoc/manuels/administration/aidewebservices/index.html#merged)
- [bibliocontrol](http://documentation.abes.fr/sudoc/manuels/administration/aidewebservices/index.html#BiblioControl)
- [AlgoLiens](http://documentation.abes.fr/sudoc/manuels/controle_bibliographique/algoliens/index.html)
- [API SRU BNF](https://api.bnf.fr/fr/api-sru-catalogue-general)
- Vue XML notice SUDOC : https://www.sudoc.fr/{PPN}.xml

Autres outils étudiés : [Checksudoc](http://domybiblio.net/check_sudoc/), développé
par Yves Tomic et [e-PPNator](http://akareup.alwaysdata.net/controlequalite.html)

## Bilan

Le groupe a identifié les besoins suivants d'améliorations des WS de l'ABES :

### Fonctionnalités unifiées

Les fonctionnalités des trois services web (multiwhere, bibliocontrol,
AlgoLiens) gagneraient à être unifiées :

- **multiwhere** est le modèle à suivre.

- **JSON** — Réponses de bibliocontrol et AlgoLiens en JSON. Cela facilite la 
  consommation des services web par les applications, comme un plugin Koha.

- **Filtrage** par ILN/RCR/PPN et Unica. Le filtrage par PPN manque à bibliocontrol et
  AlgoLiens. Le filtrage par PPN est nécessaire pour proposer des services
  d'identification des anomalies de catalogage directement depuis Koha, en
  affichage d'une notice ou dans un rapport de chargement quotidien de notices.

- **Autorités** — AlgoLiens détecte des anomalies de catalogage des autorités.
  Sur ce modèle, _bibliocontrol_ pourrait détecter des anomalies dans les
  notices bibliographiques.
  
### Notices bibliographiques

Les anomalies qui doivent être repérées dans les notices bibliographiques :

**Zones obligatoires** — L'absence de zones (ou sous-zones) obligatoires est
détectée :

- 106 $a
- 181
- 182
- 183
  
**Zones ous sous-zones interdites** — La présence de certaines zones ou sous-zones est détectée comme étant
une anomalie :

- 200 $b
- 309

**Sous-zones de liens**
- Vers notices d'autorités :
  - 500 $3 
  - 6xx $3 (si $2 = 'rameau' ou 'Fmesh')
  - 7xx $3
- Vers notices bibliographiques :
  - 410 $0

**Conditionnels** — Les conditions suivantes (on pourrait leur trouver un nom)
sont détectées comme des erreurs :

| Zone  | Condition                                     |
| ----- | --------------------------------------------- |
| 100   | $a contient 'X' ET ind1 = 0                  |
| 104   | $d <> 'ba' ET $c = 'y'                        |
| 104   | $f <> 'fr'                                    |                                      |
| 200   | $f <> '' ET pas_de_zone(7xx)                 |
| 214   | absence(214) ET notice < 'janvier 2020'       |
| 210/4 | type_doc ∈ (Ab, Ad, Ob, Od) ET absence(214) (ou 210 avant 1/1/2020) |
| 225   | présence(225) ET absence(410) ET absence(461) |
| 410   | présence(410) ET absence(225)                 |
| 7xx   | $4 = '000'                                    |

**Divers**

- **7xx selon $4** — Possibilité, en fonction de certains codes de fonction, de
  repérer l’utilisation d’une mauvaise étiquette en 7XX ? Exemples : Un
  préfacier (code fonction 080) doit être en 702 et pas en 700 ou 701 Un
  commissaire priseur (code fonction 065) doit être en 700 ou 701 et pas en 702

### Notices d'autorité

**Zones obligatoires** — L'absence de zones (ou sous-zones) obligatoires est
détectée :

- 200 $9
- 200 $c (à confirmer avec le pôle Autorités de l’Abes)
- 400 $9
- 810 $a

**Conditionnels** — Les conditions suivantes (on pourrait leur trouver un nom)
sont détectées comme des erreurs :

| Zone  | Condition                                     |
| ----- | --------------------------------------------- |
| 103   | $a et $b ne contiennent pas [0-9X] (chiffres arabes et la lettre X) |
| 103   | $a='XXX' OU $b='XXX                           |
| 106   | ($a, $b, $c) contient '#'                     |
| 200   | présence($f) ET absence(103)                  |
| 300   | type_doc(Td) ET présence(300)                 |
| 340   | type_doc(Td) ET absence(340)                  |
| 810   | contient @                                    |
