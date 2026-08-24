# IAS Trasee — hartă cu străzile interzise instruirii

Constanța și Năvodari. Arată, la ora curentă, pe ce străzi ai voie și pe ce străzi nu,
conform adresei IPJ Constanța nr. 585056/RDV/CT din 05.08.2026.

---

## Ce face

| | |
|---|---|
| **Bandă de stare** | Ceas live, câte străzi sunt interzise acum, și — cel mai util — **peste cât timp se schimbă**: „În 15 min se deschid 7 străzi”. |
| **Hartă colorată** | Roșu = interzis acum. Chihlimbar = se închide în mai puțin de 45 min. Verde = liber. Stilul liniei arată tipul regulii (continuu = permanent, întrerupt = ore de vârf, punctat = sezon), ca să se distingă și pe lumină puternică. |
| **Mod traseu** | Urmărește GPS-ul și dă alertă roșie + vibrație când te apropii la sub 120 m de o stradă interzisă **în acel moment**. |
| **Verifică altă oră** | Alegi o dată și o oră și vezi harta așa cum va arăta atunci — pentru planificarea ședințelor. |
| **Fișa străzii** | Numărul din adresa poliției, tronsonul exact, regula, articolul de lege și sancțiunea. |

Totul se calculează în telefon. Nu există server, nu există bază de date, nu se trimite nimic nicăieri.

---

## ⚠ Ce trebuie făcut înainte de a-l da altora

**1. Verifică geometria străzilor.**
Coordonatele din `index.html` sunt trasate aproximativ de mine, din cunoștințe generale
despre Constanța. Arată corect ca formă, dar **nu sunt precise la nivel de stradă** —
inacceptabil pentru un instrument care te apără de amendă.

Rulează o dată `unelte/genereaza-geometrie.py` (are instrucțiuni în el). Descarcă
traseele reale din OpenStreetMap. Apoi taie fiecare stradă la tronsonul cerut de adresă
(ex. Bd. Tomis doar între Aurel Vlaicu și Traian) și lipește liniile în lista `STRAZI`.

Alternativ, dacă vrei să eviți scriptul: deschide [geojson.io](https://geojson.io),
trasează cu mâna fiecare tronson peste hartă, exportă și copiază coordonatele.
Durează o seară, dar iese exact.

**2. Schimbă furnizorul de plăci de hartă** (vezi mai jos). Cel din fișier acum e bun
pentru testare, nu pentru 500 de oameni.

**3. Pune un mesaj scurt de responsabilitate**: harta e un ajutor, adresa poliției e sursa.

---

## Unde îl pui ca să nu crape la 500 de utilizatori

### Aplicația în sine: nicio problemă

Cifrele, pe scurt: `index.html` are ~28 KB, comprimat ~8 KB. La 500 de instructori care
deschid aplicația de 10 ori pe zi înseamnă **~1,5 GB pe lună**. Limita GitHub Pages e
100 GB/lună. Ești la 1,5% din ea.

„Simultan” nu înseamnă nimic aici, pentru că nu există server care să calculeze ceva.
Un fișier static livrat de un CDN la 500 de oameni deodată este un non-eveniment —
aceleași servere livrează fișiere la milioane de oameni. **Ce te-ar putea da jos e o
bază de date sau un backend. Tu nu ai niciunul, intenționat.**

| Găzduire | Preț | Limită de trafic | Observație |
|---|---|---|---|
| **GitHub Pages** | gratuit | ~100 GB/lună (limită de curtoazie) | Îl ai deja configurat pentru IAS. Cea mai simplă variantă. |
| **Cloudflare Pages** | gratuit | **fără limită de trafic** | Recomandarea mea. CDN mai rapid în România, poți adăuga R2 pentru hărți, control pe antete. |
| Netlify | gratuit | 100 GB/lună | Bun, dar fără avantaj față de primele două. |

**Recomandare: pune-l pe GitHub (ca sursă) și conectează Cloudflare Pages la repo.**
Cloudflare publică automat la fiecare commit, tu lucrezi în continuare la fel ca acum.
Dacă vrei simplitate maximă, GitHub Pages singur e suficient.

### Plăcile de hartă: aici e adevărata problemă

Asta e singura resursă partajată și singurul lucru care chiar cedează la scară.
O sesiune de condus cu mișcat pe hartă înseamnă 150–400 de plăci. La 500 de instructori,
2 sesiuni pe zi: **~7 milioane de plăci pe lună**.

Serverul public OpenStreetMap **interzice explicit** acest tip de folosire, și te
blochează. Nivelurile gratuite obișnuite (MapTiler 100 mii/lună, Stadia 200 mii/lună)
se termină în câteva ore la volumul ăsta.

Trei variante care rezistă:

**A. Protomaps + PMTiles pe Cloudflare R2** — cea mai bună la scară
Un singur fișier `.pmtiles` cu județul Constanța (30–80 MB), pus pe Cloudflare R2.
Browserul cere doar bucata de octeți de care are nevoie, prin range requests.
R2 nu percepe **nimic** pentru trafic de ieșire, iar nivelul gratuit acoperă 10 GB
stocare și 10 milioane de citiri pe lună. Practic: gratuit, la orice număr de utilizatori.
Necesită înlocuirea Leaflet cu MapLibre GL, sau adăugarea pluginului `protomaps-leaflet`.

**B. Plăci proprii, pre-generate, lângă aplicație** — cea mai simplă
Generezi o singură dată plăcile PNG doar pentru dreptunghiul Constanța–Năvodari și doar
pentru zoom 12–16. Ies câteva zeci de mii de fișiere, 100–300 MB. Le pui în repo,
lângă `index.html`. De acolo încolo sunt fișiere statice pe CDN: gratuite, pentru
totdeauna, imposibil de suprasolicitat. Dezavantaj: repo mare, actualizare manuală.

**C. Fără plăci deloc** — varianta la care merită să te gândești serios
Tu nu ai nevoie de clădiri, magazine și parcuri. Ai nevoie de **liniile străzilor**.
Descarci o dată rețeaua de străzi a Constanței din OpenStreetMap ca GeoJSON
(2–5 MB comprimat), o pui în aplicație și o desenezi cu Leaflet pe canvas.
Zero cereri de rețea după prima încărcare, **funcționează complet offline**, arată
distinct — și scapi de orice furnizor extern și de orice factură viitoare.
Pentru o aplicație de instructor auto, e probabil răspunsul corect.

**În plus, indiferent de variantă: service worker.**
Îl ai deja în IAS. Dacă pui plăcile în cache, traficul scade cu 80–90% după prima zi,
pentru că fiecare instructor conduce mereu prin aceleași zone. Și aplicația merge
și când semnalul e prost — ceea ce contează în mașină.

---

## Când poliția trimite o adresă nouă

Editezi un singur loc: lista `STRAZI` de la începutul lui `index.html`. Fiecare intrare are
`crt` (numărul din adresă), `nume`, `tronson`, `regula` și `linie` (coordonatele).
Regulile disponibile sunt `sezon`, `orar` și `mereu`. Dacă apar alte intervale orare,
schimbi constanta `INTERVALE`. Commit, și gata — toți utilizatorii văd noua listă
la următoarea deschidere.

Adresa cere și ca școlile să trimită **propuneri de includere/excludere** de străzi.
Dacă strângi din trafic real ce străzi chiar sunt problematice, ai un argument bun de trimis
înapoi la Serviciul Siguranță Rutieră — și ai un motiv în plus ca ceilalți instructori să
folosească aplicația.

---

## Cum se leagă de IAS

Trei variante, în ordinea efortului:

1. **Link separat.** Buton în IAS → deschide harta. Zero riscuri pentru aplicația existentă.
2. **Al doilea ecran în IAS.** Muți conținutul într-o filă nouă. Leaflet se încarcă doar când intri pe ea.
3. **Legat de calendar.** Când programezi o ședință la o anumită oră, IAS îți arată ce e
   interzis în intervalul acela și te avertizează dacă locul de întâlnire e pe o stradă închisă.
   Asta ar fi funcția pe care n-o are nimeni altcineva.

---

*Sursa datelor: IPJ Constanța, Serviciul Siguranță Rutieră, adresa nr. 585056/RDV/CT din 05.08.2026.
Hartă și date geografice: © colaboratorii OpenStreetMap, licență ODbL.*
