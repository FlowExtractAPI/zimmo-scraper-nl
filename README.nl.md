# 🏠 Zimmo Scraper

**Extraheer Belgische vastgoedadvertenties van zimmo.be.**

Plak gewoon een zimmo.be zoek-URL rechtstreeks uit je browser. De scraper haalt de volledige detailpagina op voor elke advertentie en slaat de ruwe API-respons op — zo krijg je elk veld dat zimmo.be beschikbaar stelt: prijs, EPC-certificaten, overstromingsstatus, ruimtelijke ordening, kadastraal inkomen, agentschapsgegevens, afbeeldingen, documenten en meer.

---

## 🌍 Ondersteund

| Platform | Domein | Talen |
|----------|--------|-------|
| Zimmo België | zimmo.be | Nederlands (`/nl/zoeken/`), Frans (`/fr/rechercher/`) |

> ⚠️ **Internationale URL's** (`/international/...`) worden nog niet ondersteund. Ze worden overgeslagen en genoteerd in de dataset met een verklarende melding.

---

## 🚀 Snel starten

### Basis: 10 resultaten van één zoekopdracht

```json
{
  "startUrls": [
    { "url": "https://www.zimmo.be/nl/zoeken/?search=eyJmaWx0ZXIiOnsic3RhdHVzIjp7ImluIjpbIkZPUl9TQUxFIl19LCJwbGFjZUlkIjp7ImluIjpbMTExOH19fX0=" }
  ],
  "maxResults": 10
}
```

### Meerdere URL's — verschillende regio's of filtersets

```json
{
  "startUrls": [
    { "url": "https://www.zimmo.be/nl/zoeken/?search=eyJmaWhhx0...." },
    { "url": "https://www.zimmo.be/fr/rechercher/?search=eyJmaW2g4..." }
  ],
  "maxResults": 50,
  "proxyConfiguration": {
    "useApifyProxy": true
  }
}
```

### Onbeperkte resultaten (begrensd door budget indien ingesteld)

```json
{
  "startUrls": [
    { "url": "https://www.zimmo.be/nl/zoeken/?search=eyJmaWx0ZXIiOnsic3Rh....." }
  ],
  "maxResults": 0
}
```

---

## 📋 Invoerparameters

| Parameter | Type | Standaard | Beschrijving |
|-----------|------|-----------|--------------|
| `startUrls` | array | | Een of meer zimmo.be zoek-URL's. Kopieer rechtstreeks uit je browser na het toepassen van filters. |
| `maxResults` | integer | `10` | Maximum aantal te scrapen advertenties **per URL**. Stel in op `0` voor onbeperkt. |
| `proxyConfiguration` | object | Geen proxy | Apify proxy-instellingen. Aanbevolen voor grote runs om rate limiting te vermijden. |

### Hoe krijg je een start-URL?

1. Ga naar [zimmo.be](https://www.zimmo.be) en pas je filters toe (type, locatie, prijs, EPC, enz.)
2. Kopieer de volledige URL uit je browser
3. Plak deze in `startUrls`

---

## 📤 Uitvoer

Elk dataset-item is de **ruwe detail API-respons** voor één advertentie, met twee extra velden:

| Veld | Beschrijving |
|------|--------------|
| `sourceUrl` | De zoek-URL waar deze advertentie in gevonden werd |
| `scrapedAt` | ISO-tijdstempel van wanneer het gescraped werd |

### Voorbeeld uitvoerrecord (ingekort)

```json
{
  "id": "e36e00c9-907d-4f3c-b666-6d9e21793a96",
  "estate": {
    "type": "HOUSE",
    "subType": "ONE_FAMILY_HOUSE",
    "status": "FOR_SALE",
    "price": { "unit": "EUR", "value": 375000 },
    "certificate": {
      "epcCertificate": {
        "energyLabel": "B",
        "epcValue": { "unit": "kWh/m²", "value": 147 }
      }
    },
    "flooding": { "floodingValues": ["..."] },
    "location": {
      "street": "Duiventorenstraat",
      "streetNumber": "10",
      "postalCode": "8310",
      "locality": { "nl": "Assebroek" },
      "coordinates": { "latitude": 51.187863, "longitude": 3.24166 }
    }
  },
  "dealer": {
    "name": "Waeyler",
    "email": "contact@voorbeeld.be",
    "phoneNumber": "050373137"
  },
  "sourceUrl": "https://www.zimmo.be/nl/zoeken/?search=...",
  "scrapedAt": "2026-04-13T23:37:57.746Z"
}
```

---

## 📊 Dataset-weergaven

Vijf vooraf geconfigureerde weergaven zijn beschikbaar in de Apify dataset-UI:

| Weergave | Doel |
|----------|------|
| **Overzicht** | Snelle samenvatting: ID, type, prijs, staat, slaapkamers/badkamers, oppervlakte, locatie, afbeelding |
| **Gedetailleerd** | Volledige vastgoeddata: certificaten, ruimtelijke ordening, overstroming, indeling, agentschapsgegevens |
| **Contacten** | Agentschapsnaam, e-mail, telefoon, mobiel, website en locatie per advertentie |
| **Prijzen** | Prijs, kadastraal inkomen, oppervlakten, verkoper, beschikbaarheidsdatum voor marktanalyse |
| **Energie & Juridisch** | EPC-certificaat, elektriciteitscertificaat, overstromingswaarden, planningsstatus, P/G-scores |

---

## 💰 Prijzen

Aangerekend per vastgoedresultaat dat succesvol opgeslagen werd in de dataset.

| Tier | Prijs per resultaat |
|------|---------------------|
| FREE | $0,008 |
| BRONZE | $0,004 |
| SILVER | $0,0035 |
| GOLD | $0,003 |

Als je een **budget** instelt, berekent de scraper automatisch hoeveel resultaten hij zich kan veroorloven vóór de start en begrenst `maxResults` dienovereenkomstig — geen verspilde API-aanroepen.

---

## ⚡ Prestaties

- **~200ms vertraging** tussen detailaanvragen per advertentie (vriendelijk crawlen)
- **~500ms vertraging** tussen zoekpagina's
- **100 advertenties** ≈ 1–2 minuten afhankelijk van netwerk en proxy
- Resultaten worden gepushed en aangerekend in batches van 10

### Grote runs

Voor runs met meer dan een paar honderd resultaten wordt het inschakelen van Apify proxy sterk aanbevolen:

```json
"proxyConfiguration": {
  "useApifyProxy": true
}
```

---

## ⚠️ Belangrijke opmerkingen

- Alleen Belgische advertenties worden ondersteund. Internationale URL's worden automatisch gedetecteerd en overgeslagen.
- Gebruikers moeten voldoen aan de Gebruiksvoorwaarden van zimmo.be en de toepasselijke wetgeving inzake gegevensbescherming (AVG/GDPR).

**De maker van deze actor is niet verantwoordelijk voor het gebruik van de geëxtraheerde gegevens.**

---

## 💬 Ondersteuning & Contact

- 🌐 **Website**: [flowextractapi.com](https://flowextractapi.com)
- 📧 **E-mail**: [flowextractapi@outlook.com](mailto:flowextractapi@outlook.com)
- 🙋 **Apify-profiel**: [FlowExtract API](https://apify.com/dz_omar?fpr=smcx63)
- 💬 **GitHub**: [FlowExtractAPI](https://github.com/FlowExtractAPI)
- 💼 **LinkedIn**: [flowextract-api](https://www.linkedin.com/in/flowextract-api/)
- 🐦 **Twitter**: [@FlowExtractAPI](https://x.com/@FlowExtractAPI)
- 📱 **Facebook**: [flowextractapi](https://www.facebook.com/flowextractapi)

---

## 🌟 Gerelateerde actors van FlowExtract API

**[PropertyFinder Scraper](https://apify.com/dz_omar/propertyfinder-scraper?fpr=smcx63)** — VAE, Saudi-Arabië, Bahrein, Egypte, Qatar

**[Idealista Scraper](https://apify.com/dz_omar/idealista-scraper?fpr=smcx63)** — Spaans vastgoed

**[YouTube Transcript & Metadata Extractor](https://apify.com/dz_omar/youtube-transcript-metadata?fpr=smcx63)**

**[AI Contact Intelligence Extractor](https://apify.com/dz_omar/ai-contact-intelligence?fpr=smcx63)**

---

**Klaar om zimmo.be-data te extraheren?** [Begin nu met Zimmo Scraper!](https://apify.com/dz_omar/zimmo-scraper?fpr=smcx63)
