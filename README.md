# Opsætning: Bestyrelsen + testparkeringssiden

Denne pakke indeholder de nye, selvstændige filer til bestyrelsesadministrationen og testparkeringssiden. Den er lavet til følgende GitHub-struktur:

- **Officiel hjemmeside:** [`Vigerslev-Skyttegard/Hjemmeside`](https://github.com/Vigerslev-Skyttegard/Hjemmeside)
- **Din personlige fork til udvikling og pull requests:** [`MalikQayum/Hjemmeside`](https://github.com/MalikQayum/Hjemmeside)
- **Separat datarepository:** [`Vigerslev-Skyttegard/bestyrelsen`](https://github.com/Vigerslev-Skyttegard/bestyrelsen)

De nye hjemmesidefiler skal først ind i din fork `MalikQayum/Hjemmeside` og derefter sendes som en pull request til det officielle repository `Vigerslev-Skyttegard/Hjemmeside`. Datarepositoryet `Vigerslev-Skyttegard/bestyrelsen` er selvstændigt og er ikke en fork af hjemmesiden.

**Vigtigt:** Pakken er bevidst delt i to. De to mapper skal placeres i hvert sit repository. Lad være med at blande indholdet fra `02-til-repository-bestyrelsen` ind i hjemmeside-repositoryet.

## Hvad pakken indeholder

- `01-til-hjemmesidens-repository/` – seks nye hjemmesidefiler til `/bestyrelsen/` og `/testparkeringside/`.
- `02-til-repository-bestyrelsen/` – `testparkeringside/data.json`, README og GitHub Actions-workflow til datarepositoryet.

Der er **ikke** en `testparkeringside/data.json` i hjemmesidens `testparkeringside`-mappe. Testdataene ligger kun i det separate repository `Vigerslev-Skyttegard/bestyrelsen`.

---

## Del 1 – Klargør repositoryet `Vigerslev-Skyttegard/bestyrelsen`

1. Åbn [`Vigerslev-Skyttegard/bestyrelsen`](https://github.com/Vigerslev-Skyttegard/bestyrelsen). Opret repositoryet under organisationen `Vigerslev-Skyttegard`, hvis det ikke allerede findes. Brug navnet **`bestyrelsen`**.
2. Kontrollér under **Settings → General**, at synligheden er **Public**. Den offentlige testside skal kunne hente `testparkeringside/data.json` uden en server eller privat backend.
3. Kontrollér, at standardbranchen hedder **`main`**.
4. Upload indholdet fra mappen `02-til-repository-bestyrelsen` til repositoryets **rod**. Den færdige struktur skal se sådan ud:

   ```text
   bestyrelsen/
   ├── .github/
   │   └── workflows/
   │       └── update-data.yml
   ├── README.md
   └── testparkeringside/
       └── data.json
   ```

   Hvis GitHubs web-upload ikke tager den skjulte `.github`-mappe med, så opret filen manuelt via **Add file → Create new file** og angiv stien `.github/workflows/update-data.yml`. Kopiér indholdet fra ZIP-pakken.

5. Du skal **ikke** aktivere GitHub Pages for `bestyrelsen`. Hjemmesiden læser data fra GitHubs offentlige REST Contents API med raw-medietypen. Det mindsker ventetid på ændringer sammenlignet med `raw.githubusercontent.com`, hvor GitHubs CDN kan vise en tidligere version kort tid efter et commit.
6. Åbn repositoryets **Settings → Actions → General**. Kontrollér, at GitHub Actions er tilladt. Under **Workflow permissions** vælges **Read and write permissions**, så workflowets egen midlertidige `GITHUB_TOKEN` må opdatere datafilen.
7. Kontrollér, at workflowet `Update test parking data` vises under fanen **Actions**.

### Repository-beskrivelse

Brug gerne denne tekst i feltet Description:

> Bestyrelsens testdata og GitHub Actions-workflow til Ejerforeningen Skyttegård/Vigerslevs hjemmeside.

---

## Del 2 – Tilføj de nye sider til din hjemmeside-fork

1. Åbn din personlige fork: [`MalikQayum/Hjemmeside`](https://github.com/MalikQayum/Hjemmeside).
2. Opret en ny branch, f.eks. `feature/bestyrelsen-testparkering`.
3. Upload **indholdet** fra mappen `01-til-hjemmesidens-repository` til repositoryets rod, så de to nye mapper bliver lagt ved siden af de eksisterende mapper.
4. Den nye struktur bliver:

   ```text
   Hjemmeside/
   ├── bestyrelsen/
   │   ├── index.html
   │   ├── bestyrelsen.css
   │   └── bestyrelsen.js
   └── testparkeringside/
       ├── index.html
       ├── testparkeringside.css
       └── testparkeringside.js
   ```

5. **Kopiér ikke** `testparkeringside/data.json` ind i hjemmesidens repository. Datafilen skal kun ligge i repositoryet `Vigerslev-Skyttegard/bestyrelsen`.
6. Opret en pull request med:
   - **Base repository:** `Vigerslev-Skyttegard/Hjemmeside`
   - **Base branch:** `main`
   - **Head repository:** `MalikQayum/Hjemmeside`
   - **Compare branch:** `feature/bestyrelsen-testparkering` (eller dit valgte branchnavn)
7. Når pull requesten er gennemgået og flettet ind i det officielle repository, publiceres siderne via den eksisterende GitHub Pages-opsætning:
   - [Bestyrelsens administration](https://www.skyttegaardvej.dk/bestyrelsen/)
   - [Testparkeringssiden](https://www.skyttegaardvej.dk/testparkeringside/)

Der skal ikke ændres i den eksisterende `/parkering/`-side, dens navigation, eksisterende workflows eller parkeringsfiler. Pakken tilføjer kun de seks nye filer.

---

## Del 3 – Opret adgangsnøglen til bestyrelsen

Adgangsnøglen er en **Fine-grained Personal Access Token**. Den oprettes fra GitHub-kontoen `MalikQayum`, fordi administrationssiden kontrollerer, at tokenens ejerkonto netop er `MalikQayum`. Tokenen skal have organisationen `Vigerslev-Skyttegard` som Resource owner. Den får ikke direkte skriverettighed til repositoryets filer.

1. Gå til [GitHub – Fine-grained personal access tokens](https://github.com/settings/personal-access-tokens/new).
2. Vælg et navn, f.eks. `Bestyrelsen – testparkering`.
3. Vælg en udløbsdato. En begrænset periode, f.eks. 90 dage, er en god start.
4. Under **Resource owner** vælges **`Vigerslev-Skyttegard`** – ikke `MalikQayum`.
5. Under **Repository access** vælges **Only select repositories** og derefter kun **`bestyrelsen`**.
6. Under **Repository permissions** sættes **Actions** til **Read and write**.
7. Giv **ikke** `Contents: Write`. Tokenen behøver ikke direkte skriveadgang til indholdet. Lad øvrige rettigheder stå på **No access**, hvor GitHub tillader det; `Metadata: Read-only` er en standardrettighed.
8. Opret tokenen, og kopiér den med det samme. GitHub viser normalt kun den fulde værdi ved oprettelsen.
9. Hvis GitHub viser, at tokenen afventer organisationsgodkendelse, skal en ejer af organisationen `Vigerslev-Skyttegard` godkende den under organisationens **Settings → Personal access tokens → Pending requests**. Indtil da kan tokenen ikke udføre de ønskede handlinger mod organisationens private ressourcer.
10. Opbevar tokenen sikkert og del den kun med de personer, der skal administrere testtabellen. Indsæt den aldrig i en fil, commit, issue eller besked på hjemmesiden.

### Hvad tokenen kan – og ikke kan

- Den er begrænset til organisationen `Vigerslev-Skyttegard` og kun repositoryet `bestyrelsen`.
- Den kan bruge GitHub Actions API i dette repository (workflow-dispatch kræver `Actions: write`; læsning af status kræver `Actions: read`).
- Den har **ikke** `Contents: Write`, så den kan ikke selv lave direkte filændringer via Contents API.
- Workflowets egen, midlertidige `GITHUB_TOKEN` får `contents: write` i det ene job. Workflowkoden validerer data og stage'r kun `testparkeringside/data.json`.
- GitHub har ikke filstisbaserede tokenrettigheder. `Actions: Read and write` gælder Actions-funktionerne i hele repositoryet – ikke kun én bestemt workflowfil. Hold derfor repositoryet `bestyrelsen` enkelt med kun dette ene workflow, og tilføj ikke andre workflows uden at genoverveje adgangsmodellen.

---

## Del 4 – Log ind og test

1. Åbn [Bestyrelsens administration](https://www.skyttegaardvej.dk/bestyrelsen/).
2. Brugernavn: **`bestyrelsen`**.
3. Indsæt Fine-grained tokenen i feltet GitHub-adgangsnøgle.
4. Tryk **Log ind**. Siden kontrollerer tokenen direkte hos GitHub via `GET /user` og bekræfter, at den tilhører `MalikQayum`. Der startes ikke et workflow ved login, så kontrollen bør være hurtig.
5. Når du er logget ind, kan du tilføje, redigere og slette rækker.
6. Tryk **Gem ændringer**. Først nu startes workflowet, som validerer indholdet og opdaterer `testparkeringside/data.json`.
7. GitHubs aktuelle API svarer normalt på workflow-dispatch med HTTP 200 og et `workflow_run_id`. Siden bruger dette ID til at følge den konkrete kørsel; en reservevej finder kørslen via et unikt `request_id`, hvis API-svaret ikke indeholder et ID.
8. Åbn [testparkeringssiden](https://www.skyttegaardvej.dk/testparkeringside/) for at se de opdaterede testdata.
9. Tryk **Log ud**, når du er færdig.

Bemærk: Login bekræfter, at tokenen er gyldig og tilhører den forventede GitHub-konto. Det kan ikke på forhånd bevise, at tokenen har `Actions: write`, uden at starte et workflow. Den konkrete Actions-adgang afprøves derfor først ved første gemning.

Tokenen gemmes ikke i `localStorage` eller `sessionStorage`. Den findes kun i administrationssidens hukommelse, mens den er åben. Luk siden eller log ud efter brug.

---

## Vigtige sikkerhedsforhold

- Datarepositoryet `Vigerslev-Skyttegard/bestyrelsen` skal være offentligt, så hjemmesiden kan læse `data.json`. Alle kan derfor læse testdataene.
- GitHub Pages kan ikke give serverbaseret loginbeskyttelse til selve HTML-siden. Login her beskytter redigeringsfunktionen, ikke sidens URL.
- `noindex` er ikke adgangskontrol.
- Brug udelukkende fiktive testdata. Brug aldrig rigtige navne, adresser, nummerplader, ventelisteoplysninger eller andre personoplysninger.
- Hvis tokenen bliver delt med uvedkommende, skal den tilbagekaldes i GitHub og erstattes med en ny.
- `/parkering/` og dens eksisterende Excel-fil, CSS og JavaScript indgår ikke i denne ændring.

## Fejlfinding

| Problem | Kontrollér |
|---|---|
| Login viser 401 | Tokenen er kopieret korrekt og stadig aktiv. |
| Login viser 403 | Kontrollér, at tokenen ikke er udløbet/tilbagekaldt, at organisationen har godkendt tokenen, og at GitHub ikke midlertidigt rate-limiter kontoen. |
| Gemning viser 404 | Kontrollér, at repositoryet hedder præcis `Vigerslev-Skyttegard/bestyrelsen`, og at workflowet ligger i `.github/workflows/update-data.yml` på `main`. |
| Gemning viser 403 | Kontrollér `Actions: Read and write`, Resource owner `Vigerslev-Skyttegard`, repositorybegrænsning til `bestyrelsen` og eventuel organisationsgodkendelse. |
| Workflow starter ikke | GitHub Actions er aktiveret, og workflowfilen ligger på standardbranchen `main`. |
| Workflow fejler ved push | `Settings → Actions → General → Workflow permissions` tillader `Read and write permissions`. Se fejl under Actions. |
| Testtabellen kan ikke hente data | `bestyrelsen` er offentligt, `testparkeringside/data.json` ligger i repositoryets rod, og branch hedder `main`. GitHub API kan midlertidigt rate-limit offentlige forespørgsler; prøv igen lidt senere. |

## Officiel GitHub-dokumentation

- [Create a workflow dispatch event – kræver Actions: write](https://docs.github.com/en/rest/actions/workflows#create-a-workflow-dispatch-event)
- [Get the authenticated user – Fine-grained PAT kræver ingen ekstra user-permissions](https://docs.github.com/en/rest/users/users#get-the-authenticated-user)
- [List workflow runs – kræver Actions: read](https://docs.github.com/en/rest/actions/workflow-runs#list-workflow-runs-for-a-workflow)
- [Fine-grained token permissions](https://docs.github.com/en/rest/authentication/permissions-required-for-fine-grained-personal-access-tokens)
- [Organization approval of fine-grained tokens](https://docs.github.com/en/organizations/managing-programmatic-access-to-your-organization/managing-requests-for-personal-access-tokens-in-your-organization)
- [GitHub Actions workflow permissions](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#permissions)

### Bemærkning om cache og opdatering af data

Testparkeringssiden bruger GitHubs REST Contents API (`api.github.com/repos/.../contents/...`) i stedet for `raw.githubusercontent.com`. Den rå filserver kan vise en cachet, ældre udgave kort tid efter et nyt commit. Contents API returnerer filens indhold via `application/vnd.github.raw+json` og kan læse offentlige filer uden token. Siden bruger også `cache: "no-store"` og et unikt query-parameter. Den opdaterer data ved indlæsning og når fanen bliver aktiv igen efter mindst 15 sekunder i baggrunden. Der er ikke indført konstant polling, så siden ikke bruger unødige API-kald.

GitHubs offentlige REST API har en begrænsning for anonyme forespørgsler (typisk 60 pr. time pr. offentlig IP). For en lille foreningsside, der henter den lille datafil ved indlæsning, bør det normalt være tilstrækkeligt.
