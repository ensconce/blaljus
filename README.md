# Polisen (aka Blåljus)

Blåljus listar aktuella händelser runt om i Sverige via Polisen.se's öppna API (Som tyvärr lämnar mycket att önska)

[Länk till polisens öppna API](https://polisen.se/om-polisen/om-webbplatsen/oppna-data/api-over-polisens-handelser/)

Detta projektet skapades med inspiration från användaren Brottstatus på Flashback.

[Länk till Brottstatus tråd på Flashback](https://www.flashback.org/t3092919)
[Länk till Brottstatus projekt](https://brottstatus.se/)

Projektet skapades endast för att få ytterligare förståelse inom följande följande program/frameworks/metoder.

* npm
* NodeJS
* Quasar
* SPA (single-page-applications)
* OSM (Openstreetmap)

Egentligen vill spara händelserna i en egen databas för att lättare kunna se historiska data samt lättare kunna söka efter specifika händelser eller kring geografiska områden men detta använder sig endast av anrop mot polisen varje gång data läses in.



## Install the dependencies
```bash
npm install
```

### Start the app in development mode (hot-code reloading, error reporting, etc.)
```bash
quasar dev
```

### Lint the files
```bash
npm run lint
```

### Build the app for production
```bash
quasar build
```

### Customize the configuration
See [Configuring quasar.conf.js](https://quasar.dev/quasar-cli/quasar-conf-js).
