# Changelog — IVM Planner

## Sinds v1.8.4 (origineel)

### Bug fixes
- **Taak-balken weer verplaatsbaar (move-drag)**
  Toevoeging `onMouseDown` op de balk-body zelf (was alleen op resize-handles). Cursor `grab`/`grabbing`. Click-suppression na drag via `justDraggedRef` (300ms cooldown) zodat selectie niet verandert direct na een verplaatsing.

- **Rechter resize-handle gefixt**
  Twee aparte bugs gevonden en gefixt:
  1. **Boundary check omgedraaid**: `diffD(ne, vs+vd) <= 0` was logisch het tegenovergestelde van wat bedoeld was. Vergelijk met linker handle (`diffD(vs, ns) >= 0`). Gecorrigeerd naar `diffD(ne, addD(vs,vd-1)) >= 0`.
  2. **z-index probleem**: het taak-label `<span>` had `zIndex:1` waardoor het de rechter resize-handle (geen zIndex) volledig afdekte. De linker handle was nog gedeeltelijk klikbaar door de stateIcon ervoor. Handles kregen `zIndex:2` zodat ze boven het label liggen.

- **Scroll-bug gefixt**
  Origineel: outer container `overflow:auto` + binnen `tlRef` óók `overflow:auto` → sidebar en timeline-header gingen uit lijn bij scrollen omdat sticky sidebar/header in verschillende scroll-contexten zaten.
  Fix: complete herstructurering naar **één** scroll-container (`tlRef`) met sticky header-rij (`position:sticky;top:0`) en sticky sidebar-kolom (`position:sticky;left:0`). Body-rij scrollt categorieën en timeline-rijen synchroon. Vangnet `onScroll` op tlRef forceert `scrollLeft=0` voor het geval browser-quirks horizontaal scrollen toestaan ondanks `overflowX:hidden`.

### Features
- **Taak-popover bij single click**
  Nieuwe `TP` component die boven (of onder, smart positioned) de geselecteerde taak verschijnt. Toont: status-icoon + volledige naam (met **marquee-animatie** als naam te lang is — via `useRef` + `scrollWidth > clientWidth` detectie), datums (nl-NL formaat), voortgang vs doel-% met kleur, beschrijving (indien aanwezig). Klikken op popover → opent bewerk-modal.

- **Dubbelklik op taak opent edit-modal**
  `onDoubleClick` toegevoegd, sluit popover en opent `sET({...t, isNew:false})` direct.

- **Werkdagen invoerbaar, kalenderdagen alleen-lezen**
  In de taak-edit modal: "Dagen" input is nu `readOnly` met grijs kleurschema (`cursor:not-allowed`). "Werkdgn" blijft editable in cyan. Logica: startdatum + werkdagen → einddatum wordt berekend via `addWD`, "Dagen" volgt automatisch.

- **Doel-% (target) berekening en weergave**
  Nieuwe helper `targetPct(startDate, endDate)` = `(verstreken werkdagen / totale werkdagen) * 100`. Berekend met `countWD()`. Vandaag voor start → 0%, vandaag na eind → 100%.
  - **In popover**: "voortgang / doel-%" met groene/rode kleur en ▲▼ icoon afhankelijk van voor of achter schema
  - **Op de balk zelf**: kleine ▲ (groen, voor op schema) of ▼ (rood, achter op schema) naast de % — alleen zichtbaar als taak vandaag loopt (tussen start en eind)

- **PDF export met dependency-pijlen**
  Tabel kreeg `position:relative` en vaste header-hoogte (28px). Per taak wordt pixel-positie `{x1, x2, y}` bewaard. SVG-overlay na de tabel met `position:absolute;left:sw;top:hh`, bezier-curves met oranje arrow-marker tussen `from` en `to` taken. Pijl-y-coördinaat = `ci*rh + rh/2` (robuust voor verschillende rij-hoogtes).

### Layout iteraties (veel heen-en-weer geweest)
Begonnen met origineel `ROW_H=80, SIDEBAR_W=260`. Na meerdere iteraties:
- ROW_H verlaagd (80 → 40 → 24 → 16) — gebruiker wilde "dunner", betekende verticale rij-hoogte
- SIDEBAR_W misverstand: 260 → 130 → 260 → 520 → 200 (gebruiker wilde "smaller dan origineel" na overshoot naar 520)
- Uiteindelijke werkende waarden: `ROW_H=42, SIDEBAR_W=200`
- Lesson learned: bij visuele wijzigingen vooraf vragen wat exact bedoeld wordt — "breder/dunner" kan op meerdere dimensies slaan

### CSS animations toegevoegd
- `marq` — marquee-scroll voor lange titels in popover
- `popIn` — fade+scale entry voor popover

## Versie-tracking
- v1.8.4 = originele staat zoals aangeleverd
- Geen formele versie-bump in HTML zelf, alleen documentair. Bij Teams app update zou manifest versie wel verhoogd moeten worden.
