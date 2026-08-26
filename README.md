# IAS Trasee — hartă cu străzile interzise instruirii

Constanța și Năvodari. Arată, la ora curentă, pe ce străzi ai voie și pe ce străzi nu,
conform adresei IPJ Constanța nr. 585056/RDV/CT din 05.08.2026.

Trei fișiere:

| Fișier | Ce e |
|---|---|
| `index.html` | Aplicația. Asta o deschizi când ești pe traseu. |
| `unealta.html` | Unealtă folosită rar: extrage traseele reale din OpenStreetMap. |
| `trasee.json` | Geometria verificată de tine. O produce unealta. |

---

## Ce ai de făcut acum, pas cu pas

### Pasul 1 — Urcă cele două fișiere

În repo-ul `ias-auto/trasee`: **Add file → Upload files** → alege `index.html` și
`unealta.html` → **Commit changes**.

`index.html` îl înlocuiește pe cel vechi. E în regulă, asta vrem.

Așteaptă un minut ca GitHub Pages să publice.

### Pasul 2 — Deschide unealta

**https://ias-auto.github.io/trasee/unealta.html**

Deschide-o de aici, nu din Descărcări. Are nevoie de o adresă https ca să poată vorbi
cu serverele OpenStreetMap.

Fii pe WiFi. Durează mai mult pe date mobile și e păcat de trafic.

### Pasul 3 — Apasă „Descarcă din OpenStreetMap”

Durează între 30 și 90 de secunde. Nu închide pagina.

Unealta cere o singură dată toate străzile din zonă, apoi lucrează local:
- găsește fiecare stradă din adresă („Bd. Tomis” → „Bulevardul Tomis” în OSM);
- lipește bucățile în care OSM o are împărțită, într-o linie continuă;
- caută intersecțiile cu străzile din coloana „Tronson” și **taie exact între ele**
  (Bd. Tomis doar între Aurel Vlaicu și Traian, nu tot bulevardul).

Dacă serverele sunt aglomerate, încearcă singură alte două servere. Dacă nici acelea
nu răspund, îți spune clar și încerci peste un sfert de oră. Nimic nu se strică.

### Pasul 4 — Verifică pe rând

**Nu se mai poate pierde nimic.** Fiecare apăsare se salvează în telefon. Poți închide
pagina, apăsa Back, primi un telefon — când te întorci, apeși *„Continuă de unde ai
rămas”* și ești exact unde erai.

Unealta pornește singură în modul **verifică pe rând**: o poziție pe ecran, harta
deasupra, trei butoane dedesubt. Nu trebuie să cauți nimic în listă.

Pentru fiecare stradă vezi:

- **linia verde** = tronsonul, cu numele scris pe ea;
- **liniile mov punctate** = străzile de la capete, din coloana „Tronson” a adresei,
  cu numele lor scris deasupra;
- **bulina albastră** = unde începe, **roșie** = unde se termină;
- bifele de sub hartă: *✓ Începe la Bulevardul Aurel Vlaicu · ✓ Se termină la Strada Traian*.

Asta e tot ce trebuie să compari cu adresa. Dacă cele două nume mov sunt cele din adresă
și linia verde e între ele, e corect.

Ce apeși:

- **E bine — acceptă** → trece la următoarea.
- **✎ Taie capetele** → o corectezi tu (mai jos).
- **Sari ›** → o lași pentru mai târziu.

Cele tăiate automat cu succes vin deja acceptate. Tu doar confirmi sau corectezi, deci
la majoritatea apeși o singură dată.

Verificarea cea mai rapidă rămâne lungimea, scrisă sub bife. Bd. Tomis între Aurel Vlaicu
și Traian trebuie să iasă câțiva kilometri. Dacă dă 200 m, a tăiat greșit.

### Cum corectezi un tronson

Apeși **✎ Taie capetele**:

1. Strada întreagă apare gri. Scrie *„Apasă pe hartă unde ÎNCEPE tronsonul”*. Apeși cu
   degetul, aproximativ. **Nu trebuie să nimerești exact** — unealta lipește apăsarea de
   cel mai apropiat punct de pe stradă, deci linia rămâne întotdeauna pe drum.
2. *„Acum apasă unde SE TERMINĂ”*. Apeși al doilea capăt. Vezi imediat câți metri are.
3. Nu e bine? Apeși din nou pe hartă — se mută capătul roșu. Sau **Ia de la început**.
4. **Salvează tronsonul**. Te întoarce unde erai.

Două scurtături: **Până la capăt** (apeși doar începutul — cazul „până la Năvodari”) și
**Vreau strada întreagă** (anulează orice tăiere).

Dacă apeși Back din greșeală în timp ce tai, ieși doar din tăiere, nu din pagină.

**Când o stradă apare în mai multe bucăți**, în listă apar butoane *„bucata 1 · 820 m”*.
De obicei sunt cele două sensuri ale unui bulevard și le lași pe amândouă. Dacă una e din
altă parte a orașului, o stingi cu o apăsare.

### Pasul 5 — Descarcă `trasee.json`

Butonul verde de jos. Poți apăsa oricând, chiar dacă n-ai acceptat toate — pozițiile
neacceptate rămân pe geometria aproximativă și vor fi marcate în aplicație.

### Pasul 6 — Urcă `trasee.json` în repo

Aceeași rutină: **Add file → Upload files → Commit changes**.

### Pasul 7 — Deschide aplicația

**https://ias-auto.github.io/trasee/**

Bara galbenă de sus îți spune câte trasee sunt încă aproximative. Când ai acceptat toate
cele 23, bara dispare și niciun ⚠ nu mai apare în listă.

---

## De ce e construită așa

**Aplicația nu vorbește niciodată cu OpenStreetMap.** Citește `trasee.json` din repo și
atât. Documentația Overpass spune apăsat că a folosi serverele lor publice ca backend
pentru o aplicație este exact felul de folosire care duce la blocare — dar că o cerere
pusă manual de un om este extrem de puțin probabil să deranjeze pe cineva.

Deci: tu, o dată, ceri. Cei 500 de utilizatori nu cer niciodată nimic.

**Nimic nu se pierde în tăcere.** Dacă `trasee.json` lipsește sau e stricat, aplicația
nu se blochează și nu se preface că e în regulă — merge pe coordonatele aproximative
și îți spune pe față, în bara galbenă, câte trasee nu sunt verificate. Am testat exact
scenariul ăsta.

**Tu ai ultimul cuvânt.** Unealta propune, tu accepți. O stradă neacceptată nu ajunge
niciodată în fișier.

---

## Când poliția trimite o adresă nouă

1. În `unealta.html`, lista `TINTE` de la început: adaugi, scoți sau modifici poziții.
   Fiecare are `crt`, `nume`, `tronson`, `regula` (`sezon` / `orar` / `mereu`),
   `osm` (numele complet așa cum e în OpenStreetMap) și `taie`.
2. Aceeași listă se modifică și în `index.html`, în `STRAZI`.
3. Rulezi unealta din nou, urci noul `trasee.json`.

Dacă apar alte intervale orare, schimbi `INTERVALE` în `index.html`.

Adresa cere școlilor și **propuneri de includere/excludere** de străzi. Dacă strângi din
traseu real ce străzi chiar sunt problematice, ai un argument bun de trimis înapoi la
Serviciul Siguranță Rutieră.

---

## Când o dai la mulți oameni

Aplicația în sine nu e o problemă: e un fișier static de ~11 KB comprimat. 500 de
instructori × 10 deschideri pe zi ≈ 1,5 GB/lună, adică 1,5% din limita GitHub Pages.
Nu există server care să pice, pentru că nu există server.

Ce trebuie schimbat înainte sunt **plăcile de hartă** — singura resursă partajată. La
volumul ăla se termină orice nivel gratuit obișnuit. Variante, de la cea mai bună la scară:

1. **Protomaps + PMTiles pe Cloudflare R2.** Un fișier cu județul Constanța (30–80 MB).
   R2 nu percepe nimic pentru trafic de ieșire. Practic gratuit la orice număr de utilizatori.
2. **Plăci proprii, pre-generate**, doar Constanța–Năvodari, zoom 12–16, puse lângă
   `index.html`. Fișiere statice pe CDN: gratuite pentru totdeauna.
3. **Fără plăci deloc.** Tu ai nevoie de linii de străzi, nu de clădiri și magazine.
   Descarci o dată rețeaua ca GeoJSON și o desenezi singur. Zero cereri de rețea,
   merge complet offline în mașină, nicio factură viitoare. Pentru cazul tău,
   probabil răspunsul corect.

Plus un service worker, ca la IAS: traficul scade cu 80–90% după prima zi, pentru că
fiecare instructor conduce prin aceleași zone.

---

*Sursa restricțiilor: IPJ Constanța, Serviciul Siguranță Rutieră, adresa nr. 585056/RDV/CT
din 05.08.2026. Date geografice: © colaboratorii OpenStreetMap, licență ODbL.*
