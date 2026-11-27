# Vuosaaren satama – kuusi kuukautta satelliittikuvina

```
vuosaaren-satama/
├── index.html        ← sivu (Leaflet)
├── data/kuvat.json   ← kuvalista: kuukausi, tiedosto, päivämäärä, kellonaika
└── kuvat/            ← yksi kuva per kuukausi, esim. 2026-05.jpg
```

## Näin sivu toimii

- Jokainen kuva on oma pieni Leaflet-karttansa (paneeli). Kuva asetetaan kartalle
  `L.imageOverlay`-tasona koordinaattirajojen `rajat` mukaan.
- *+ Add image* lisää paneelin, ruutu jakautuu: 1–3 rinnakkain, 4 = 2×2, 5–6 = 3×2.
- Kartat on kytketty toisiinsa: kun yhtä liikuttaa tai zoomaa, muut seuraavat.
- Mittaus piirtyy kaikkiin paneeleihin. Kirjanmerkit ovat vain muistissa ja
  katoavat, kun sivu ladataan uudelleen.

## Kuvien haku Copernicus Browserista (ilmainen, vaatii tunnuksen)

1. Avaa https://browser.dataspace.copernicus.eu/ ja kirjaudu (ilmainen rekisteröinti).
2. Zoomaa Vuosaaren satamaan. Valitse *Sentinel-2 L2A*, väri *True color*.
3. Aseta pilvisyys (*Max. cloud coverage*) pieneksi, esim. 10 %, ja valitse
   kalenterista kuukauden selkein päivä. Tarkista, ettei satamassa ole pilviä.
4. Rajaa alue: piirrä suorakulmio (*Create an area of interest*) täsmälleen
   rajoille **60.200–60.240 N, 25.130–25.230 E**. Sama rajaus kaikille kuville,
   niin ne osuvat päällekkäin.
5. *Download image* → *Analytical* → JPG, resoluutio *High*. Tallenna nimellä
   `kuvat/2026-05.jpg` jne.
6. Kirjaa `data/kuvat.json`:iin kuvan `pvm` (muoto `2026-05-14`) ja `aika`
   (UTC, esim. `09:50`). Ne löytyvät Copernicus Browserin kuvan tiedoista.

Jos rajaus ei osu täsmälleen, avaa ladattu GeoTIFF QGIS:ssä, katso todelliset rajat
(Layer Properties → Information → Extent) ja lisää kuvalle oma kenttä:
`"rajat": [[etelä, länsi], [pohjoinen, itä]]`. Se ohittaa yhteiset rajat.

Marraskuu jätettiin pois, koska Helsingissä aurinko on silloin niin matalalla ja
pilviä niin paljon, että käyttökelpoista kuvaa on harvoin. Kuukaudet voi vaihtaa
kuvat.json:ssa.

Lähdemerkintä (pakollinen): *Contains modified Copernicus Sentinel data 2026*.
Se näkyy kartan oikeassa alakulmassa.

Paikallisesti sivu avataan palvelimen kautta (`python -m http.server` tässä kansiossa).
