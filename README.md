# Bestyrelsen – testdata til ejerforeningens hjemmeside

Dette repository indeholder testdata og den GitHub Actions-workflow, som bestyrelsens administrationsside bruger til at gemme ændringer.

Repositoryet er adskilt fra hjemmesidens repository `MalikQayum/Hjemmeside`.

## Indhold

- `testparkeringside/data.json` – den offentligt læsbare testtabel.
- `.github/workflows/update-data.yml` – kontrolleret workflow, der validerer data og opdaterer **kun** `testparkeringside/data.json`.

## Sådan fungerer gemning

1. Bestyrelsesmedlemmet logger ind på `https://www.skyttegaardvej.dk/bestyrelsen/` med brugernavnet `bestyrelsen` og en GitHub Fine-grained Personal Access Token.
2. Tokenen starter workflowet `update-data.yml` via GitHubs workflow-dispatch API.
3. Tokenen har **Actions: Read and write** i dette repository, men ingen `Contents: Write`.
4. Workflowet kører med sin egen midlertidige `GITHUB_TOKEN`, som i denne workflow kun får `contents: write`.
5. Workflowets kode validerer indholdet og stage'r eksplicit kun `testparkeringside/data.json`.
6. Den offentlige testside henter `testparkeringside/data.json` direkte fra dette repository.

Tokenen har altså ikke direkte skriveadgang til filer. Den kan bruge Actions-funktionerne i dette repository, ikke kun starte denne ene workflowfil. Hold derfor dette repository enkelt, og læg ikke andre workflows her, medmindre tokenens rettigheder og formålet revurderes.

## Vigtigt om data

Repositoryet er offentligt, og indholdet i `testparkeringside/data.json` kan læses af alle. Brug kun fiktive testdata. Gem aldrig navne, adresser, registreringsnumre, kontaktoplysninger eller andre personoplysninger her.

## Forventet dataformat

```json
{
  "schemaVersion": 1,
  "updatedAt": null,
  "rows": [
    {
      "id": "test-row-1",
      "address": "TESTADRESSE 1, 1. th",
      "car": "TEST-BIL-01",
      "motorcycle": ""
    }
  ]
}
```

Workflowet accepterer højst 100 rækker og kontrollerer felterne, før filen bliver gemt.
