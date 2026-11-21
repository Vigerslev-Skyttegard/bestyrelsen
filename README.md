# Bestyrelsen – ejerforeningens offentlige data

Dette repository indeholder offentlige data til ejerforeningens hjemmeside. Data redigeres via administrationssiden `/bestyrelsen/`.

## Parkeringsoversigt

`parkering/data.json` indeholder de 69 faste boliger og to værdifelter pr. adresse:

- `car`: ventelistedato eller bilens nummerplade
- `motorcycle`: ventelistedato eller MC/scooterens nummerplade

Datoer gemmes i dansk format `DD/MM/ÅÅÅÅ`. Når en beboer tilmeldes ventelisten, skrives dags dato. Når en plads tildeles, erstatter bestyrelsen datoen med nummerpladen.

Workflow: `.github/workflows/update-data.yml`.
Det validerer parkeringsdataene og skriver **kun** `parkering/data.json`.

## Prisblad til generalforsamlingen

`generalforsamling/prisblad.json` indeholder de ydelser, beskrivelser og priser, der vises på hjemmesidens side `/generalforsamling/`.

Bestyrelsen kan tilføje, redigere og slette prisrækker via administrationssiden `/bestyrelsen/`. Hver række har:

- `title`: ydelse eller emne
- `description`: kort beskrivelse (kan være tom)
- `amount`: pris i hele danske kroner
- `unit`: eventuelt tillæg, fx `/ md.`

Workflow: `.github/workflows/update-price-list.yml`.
Det validerer prislisten og skriver **kun** `generalforsamling/prisblad.json`.

## Sikkerhed og adgang

Administrationssiden bruger en GitHub Fine-grained Personal Access Token med adgang til dette repository og `Actions: Read and write`, men uden `Contents: Write`. De to workflows har hver deres særskilte validering og skriver kun til deres egen datafil med deres egen begrænsede `GITHUB_TOKEN`. De deler en fælles concurrency-gruppe, så samtidige gemninger ikke forsøger at pushe til `main` på samme tid.

Repositoryet er offentligt, fordi hjemmesiden henter data direkte fra filerne. Alle oplysninger i begge JSON-filer kan derfor læses af alle. Gem ikke adgangskoder, personfølsomme oplysninger eller interne bestyrelsesoplysninger her.

## Historisk PDF

`prisblad-2026.pdf` i hjemmeside-repositoryets dokumentarkiv kan bevares som et historisk/underskrevet dokument. Den aktuelle tabel på hjemmesiden hentes fremover fra `generalforsamling/prisblad.json` og vedligeholdes via bestyrelsessiden.
