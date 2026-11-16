# Mitä kansioita teet ja mihin

Portfolio-repositoryn rakenne:

```
Portfolio/
├── index.html                      ← etusivu (ennallaan)
└── projektit/
    ├── muu-projekti/               ← muut portfoliosivut, oma kansio kullekin
    └── historialliset-kartat/      ← TÄMÄ PROJEKTI (uusi kansio)
        ├── index.html              ← sivu
        ├── data/
        │   └── kartat.json         ← kaikki karttojen tiedot ja tarinat
        └── kartat/                 ← omat georeferoidut kartat
            ├── kaava-1837/         ← yksi kansio per kartta
            └── ...
```

## Yksinkertaisesti

1. **Luo kansio** `projektit/historialliset-kartat/`. Kaikki tämän projektin tiedostot
   asuvat siellä, jotta muut portfoliosivut eivät sekoitu.
2. **Kopioi sinne** `index.html` ja kansio `data/` (jossa `kartat.json`).
3. **Luo tyhjä kansio** `kartat/` samaan paikkaan. Sinne menevät omat georeferoidut kartat.
4. **Jokaiselle omalle kartalle oma alikansio** `kartat/<kartan-nimi>/`. Nimi on sama
   kuin `id` kartat.json:ssa. Kansion sisällä on QGIS:n tekemät tiilikansiot (12, 13, 14…).
5. **Lisää kartta listaan**: kopioi jokin kohde kartat.json:sta, vaihda `id`,
   `otsikko`, `vuosi`, `rajat` ja `tarina`.

Helsingin pohjakartat (WMS) eivät tarvitse omaa kansiota, koska ne tulevat suoraan
Helsingin palvelimelta. Niille riittää rivi kartat.json:ssa.

Etusivun linkki projektiin: `projektit/historialliset-kartat/`.
Vanha `projektit/helsinki-historia.html` voi jäädä paikalleen tai poistaa, kun uusi toimii.

## Ryhmät ja tyypit kartat.json:ssa

- `ryhma`: `"pohja"` (yksi kerrallaan) tai `"kohde"` (useita päällekkäin).
- `tyyppi`: `"wms"` (ulkoinen palvelu: `wms.url` + `wms.layers`) tai `"tiili"` (omat tiilet).
- `tarina`: lista kappaleita, näkyy kortin infonapin (i) takana. HTML-linkit toimivat.

## Tarkista WMS-tasojen nimet

Tasojen nimet ovat [HRI:n sivulta](https://hri.fi/data/en/dataset/helsingin-historialliset-kartat),
ja etuliite `avoindata:` on päätelty palvelun muista tasoista. Jos jokin kartta
ei näy, katso oikea nimi osoitteesta
`https://kartta.hel.fi/ws/geoserver/avoindata/wms?request=getCapabilities`
(etsi vuosiluvulla). `rajat` on kaikille Helsingin kartoille sama karkea arvio:
säädä se, jos haluat zoomin osuvan paremmin.

## Esihistoriallinen taso: GTK:n Litorinameri

Pohjakartoissa on myös `litorinameri`, joka tulee Geologian tutkimuskeskuksen
(GTK) Muinaisrannat-aineistosta. Aineiston taso 20 (`muinaisrannat`) sisältää
neljä luokkaa: Vedenkoskematon, Subakvaattinen vanhempi, Subakvaattinen Litorina
ja Itämeri. WMS ei osaa näyttää vain yhtä luokkaa, joten sivu käyttää samaa
palvelua ArcGIS-rajapinnan kautta (`tyyppi: "arcgis"`) ja suodattaa luokan
`STAGE='Subaquatic, Litorina'`. Kenttä `taso` on tason numero (20) ja `suodatin`
on suodatusehto. Muita luokkia saat vaihtamalla ehdon arvon:
`Supra-aquatic`, `Subaquatic, older` tai `Baltic Sea`.

Jos kartta ei ilmesty, palvelun `/export`-rajapinta ei ehkä ole käytössä.
Varavaihtoehto on tavallinen WMS-tyyppi (`tyyppi: "wms"`, `wms.url`
`https://gtkdata.gtk.fi/arcgis/rest/services/Rajapinnat/GTK_Maapera_WMS/MapServer/WMSServer`,
`wms.layers` `muinaisrannat`), mutta silloin näkyvät kaikki neljä luokkaa.
Tarkista myös GTK:n aineiston lisenssi ja merkitse se lähdetietoon.

## Tarinatekstit, lähteet ja paikkalinkit

Jokaisella kartalla voi olla kentät:

- `tarinaOtsikko`: otsikko tarinan alussa.
- `tarina`: lista kappaleita (yksi kappale = yksi rivi listassa).
- `lisalahteet`: lista tekstin lähteitä. Ne näkyvät samassa paikassa kuin karttatason
  oma lähde (`lahde`), listana otsikon "Lähteet" alla.

Paikkalinkki, joka zoomaa kartan tietylle paikalle, tehdään tekstiin näin:

```html
<a href="#" class="paikka" data-lat="60.173323" data-lng="24.955274" data-zoom="15">Vironniemi</a>
```

`data-lat` ja `data-lng` ovat desimaaliasteina (leveys, pituus) ja `data-zoom` on
zoomaustaso (10 = kaupunkiseutu, 15 = kaupunginosa, 17 = kortteli). Koordinaatit
saat esimerkiksi Google Mapsista klikkaamalla paikkaa hiiren oikealla.

Linkki toimii minkä tahansa kartan tarinassa. Paikan tasoa ei tarvitse
kytkeä päälle, linkki vain siirtää näkymän.

## Näkymä, vuositeksti ja oma kohdekartta

- Kun karttataso avataan, kartta zoomaa Helsinkiin. Oletusnäkymä on `index.html`:n
  alussa rivillä `const HELSINKI = { keski: [60.2, 24.95], zoom: 11 };`.
  Yksittäiselle kartalle voi antaa oman näkymän kartat.json:iin:
  `"nakyma": { "keski": [60.17, 24.94], "zoom": 14 }`.
- `vuosi` on järjestysnumero (pienin ensin). Kortissa näkyvän tekstin voi ylikirjoittaa
  kentällä `vuosiTeksti`, esim. `"vuosiTeksti": "1870–1907"`.
- Kohdekartat ja kaavat -ryhmä näkyy sivulla vasta, kun kartat.json:ssa on vähintään
  yksi kartta, jolla on `"ryhma": "kohde"`.

Pohja omalle georeferoidulle kartalle (lisää listan loppuun, muista pilkku edellisen perään):

```json
{
  "id": "kaava-1837",
  "ryhma": "kohde",
  "tyyppi": "tiili",
  "otsikko": "Helsingin asemakaava 1837",
  "vuosi": 1837,
  "lyhyt": "Lyhyt kuvaus listakorttiin.",
  "rajat": [[60.158, 24.925], [60.175, 24.965]],
  "minZoom": 12,
  "maxZoom": 17,
  "formaatti": "jpg",
  "nakyma": { "keski": [60.167, 24.945], "zoom": 15 },
  "tarinaOtsikko": "Otsikko",
  "tarina": ["Ensimmäinen kappale.", "Toinen kappale."],
  "lisalahteet": ["Lähde 1.", "Lähde 2."],
  "lahde": { "nimi": "Kansallisarkisto", "url": "https://astia.narc.fi/" }
}
```

Tarinassa jokainen kappale on oma rivinsä lainausmerkkien sisällä ja rivien välissä on
pilkku (viimeisen perässä ei). Jos tekstissä on lainausmerkkejä, kirjoita ne `\"` tai
käytä yksittäisiä `'`. Tarkista tiedosto aina osoitteessa jsonlint.com ennen kuin
viet sen GitHubiin.

## Sivupalkin leveys

Leveys on `index.html`:n alussa kohdassa `grid-template-columns: clamp(440px, 34vw, 620px) 1fr;`.
Ensimmäinen luku on vähimmäisleveys, toinen osuus ruudusta ja kolmas enimmäisleveys.

## Työnkulku omalle kartalle

1. Lataa skannaukset Kansallisarkistosta samaan kansioon.
2. Kokoa osat (jos tarvitaan): ImageMagick
   `magick montage osa*.jpg -tile 3x2 -geometry +0+0 koottu.jpg`
   tai georeferoi osat erikseen ja tee tiilet kaikista yhdessä.
3. Georeferoi QGIS:ssä (Layer → Georeferencer), 6–10 tunnistepistettä, kohde EPSG:3857.
4. Tee tiilet: Processing Toolbox → *Generate XYZ tiles (Directory)*, zoom esim. 12–17,
   JPG laatu 80, tulostekansio `kartat/<id>/`.
5. Täytä kartat.json, commit ja push.

Paikallisesti sivu avataan palvelimen kautta (`python -m http.server` kansiossa
`historialliset-kartat`), ei tuplaklikkaamalla.
