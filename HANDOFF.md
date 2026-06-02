# IVM Planner — Handoff (v1.8.6+)

## Wat zit er in deze ZIP?
- `index.html` — de volledige werkende app (laatste versie)
- `ivm-planner-teams.zip` — Teams app package (manifest + icons, niet gewijzigd sinds v1.8.4)
- `HANDOFF.md` — dit bestand
- `CHANGELOG.md` — wat er is veranderd sinds de originele v1.8.4

## Hosting & infra
- **Hosting:** GitHub Pages → https://turkushan490.github.io/ivm-planner/
- **Repo:** https://github.com/turkushan490/ivm-planner
- **Firebase project:** ivm-planner (Spark/gratis plan)
- **Firebase DB:** https://ivm-planner-default-rtdb.europe-west1.firebasedatabase.app
- **Teams App ID:** 44d396d9-07cb-4bd7-b47b-0e75bdb41d0c

## Firebase config (in de HTML)
```
apiKey: "AIzaSyDFWd1lUphQ-6QGjfyz3kfqxFgD3ohwoSY"
databaseURL: "https://ivm-planner-default-rtdb.europe-west1.firebasedatabase.app"
projectId: "ivm-planner"
```

## Firebase database structuur
```
/teams/{teamId}/         → team info (name, color, created)
/planners/{teamId}/      → planner data per team
  categories: []         → hiërarchische categorieën
  tasks: []              → taken met datums, kleuren, voortgang, description
  dependencies: []       → pijlen tussen taken
  versions: []           → versie-snapshots
  templates: []          → herbruikbare taak-templates
```

## Belangrijke technische details
- **Geen build-stap** — alles in één HTML bestand, React via CDN
- **Firebase CDN:** cdn.jsdelivr.net (gstatic.com is geblokkeerd op het netwerk)
- **Teams SDK:** voor tab configuratie + Teams-only detectie
- **Data sync:** Firebase `.once()` bij laden, `.set()` bij wijzigen (geen realtime listener)
- **Fallback:** localStorage als backup per team
- **DAY_W:** dynamisch berekend op basis van vensterbreedte

## Layout constanten (regel 43 in index.html)
```js
const ROW_H=42,HEAD_H=52,SIDEBAR_W=200,INDENT=16,TPAD=4,TH=ROW_H-TPAD*2;
```
- `ROW_H` = hoogte van één categorie-rij in pixels
- `HEAD_H` = hoogte van de header-rij (maand + dagen)
- `SIDEBAR_W` = breedte van de linker categorieën-kolom
- `INDENT` = inspring per nesting-niveau in de boom
- `TPAD` = top/bottom padding binnen rij
- `TH` = taak-balk hoogte (volgt automatisch uit ROW_H - 2*TPAD)

Deze constanten zijn vaak aangepast tijdens iteratie — controleer huidige waarden voor je nieuwe waardes voorstelt.

## Hoe te updaten
1. Wijzig `index.html`
2. Push naar GitHub repo (vervang de oude)
3. Wacht 1-2 min op GitHub Pages deploy
4. Hard refresh in browser, of Teams afsluiten via systray en heropenen
5. Manifest hoeft alleen vernieuwd als URL/scopes/permissions wijzigen

## Test-workflow
- **In browser direct:** `https://turkushan490.github.io/ivm-planner/?v=N` (verhoog N bij elke push om cache te omzeilen)
- **Teams cached zwaar.** Hard refresh werkt vaak niet — Teams via systray afsluiten en heropenen is betrouwbaar
- Voor snelle iteratie: test eerst in een normale browser, dan pas in Teams

## Bekende issues / TODO
- **Geen realtime sync** tussen gebruikers (data laadt bij openen, niet live via `.on('value')`)
- **Geen authenticatie** op Firebase (DB is open voor iedereen met de URL)
- **Touch/mobile drag werkt niet** (alleen muis-events)
- **PDF export kleuren** alleen zichtbaar als "achtergrondafbeeldingen" aan staat bij printen (browser instelling)
- **Categorie-naam afkapping**: lange namen krijgen ellipsis ("..."); hover toont volledige naam via `title` attribuut

## Belangrijke gedrag-conventies
- **Single click op taak** → selecteert + opent popover boven balk
- **Dubbelklik op taak** → opent bewerk-modal direct
- **Sleep midden van balk** → verschuift hele taak
- **Sleep linker rand** → past startdatum aan
- **Sleep rechter rand** → past einddatum aan
- **Rechtermuis op taak/categorie** → context menu
- **Werkdagen invoeren** → kalenderdagen volgen automatisch (NIET andersom)

## Communicatie-stijl met de gebruiker
- Antwoord in **casual Nederlands** met typo's; gebruiker is Turks (NL native)
- Direct, geen overdreven beleefdheid
- Bij elke code-wijziging: **lever een file** via `present_files` (niet alleen tonen in chat)
- Verwijs bij voorkeur naar versie-nummers in handoff (v1.8.4, v1.8.5, ...) ipv "de laatste" wat verwarrend wordt na meerdere iteraties

## Stappenplan voor nieuwe Claude-sessie
1. Lees deze HANDOFF.md
2. Lees CHANGELOG.md voor recente wijzigingen
3. Pak `index.html` uit en gebruik als startpunt
4. Vraag bij twijfel **eerst** met `ask_user_input_v0` voor je iets aanpast — de gebruiker werkt visueel, dus rare layout-wijzigingen lopen vaak fout
5. Lever na elke wijziging een nieuwe `index.html` via `present_files`
