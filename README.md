# Osman Residence UI

Een eigen, luxe dashboard-thema bovenop Home Assistant — donker, koperkleurig,
met een vaste verticale zijbalk in plaats van de standaard tabbladen.

Dit is **v1.0**: het thema + alle 9 pagina's volledig uitgewerkt (Overzicht,
Verlichting, Rolluiken, Klimaat, Energie, Media, Beveiliging, Camera's,
Instellingen), met functionele knoppen (lichten aan/uit, rolluiken op/neer,
scènes, alarm, sloten, enz.). De entity_id's staan nog op placeholders — zie
["Entiteiten aanpassen"](#entiteiten-aanpassen--zoek-op-todo) hieronder.

## Structuur

```
osman-residence-ui/
├── themes/
│   └── osman_residence.yaml   → het HA-thema
├── dashboards/
│   └── osman_residence.yaml   → de volledige dashboard-YAML (raw config)
├── docs/
└── README.md
```

## Vereisten (installeren via HACS → Frontend)

| Card              | Waarvoor gebruikt                                          |
|-------------------|-------------------------------------------------------------|
| Mushroom          | Lichten, covers, klimaat, media, sloten, alarm, chips, template-cards |
| Bubble Card       | De verticale zijbalk-menu + scène-knoppen                   |
| button-card       | Batterijkaart, "alles"-knoppen, snelkoppelingen, systeemknoppen |
| card-mod          | Kleuren/typografie afstemmen op het thema                   |
| layout-card       | De sidebar/content grid-indeling op elke pagina              |
| apexcharts-card   | Trendlijnen (binnenklimaat, energie-opwek, batterij, temperatuur) |
| browser_mod       | Nog niet actief gebruikt — gereserveerd voor een latere sprint (pop-ups) |

Camera's, het alarmpaneel en de deur-/raamlijst gebruiken bewust ingebouwde
HA-kaarten (`picture-entity`, `entities`) — daar is geen extra HACS-card voor
nodig.

## Installatie

1. **HACS-cards installeren** (zie tabel hierboven, via HACS → Frontend →
   zoek elke naam op en installeer).
2. **Thema plaatsen**: kopieer `themes/osman_residence.yaml` naar
   `/config/themes/` op je Home Assistant-installatie. Zorg dat je
   `configuration.yaml` dit bevat:
   ```yaml
   frontend:
     themes: !include_dir_merge_named themes
   ```
   Herstart daarna Home Assistant (of gebruik "YAML herladen → Thema's" als
   dat volstaat).
3. **Dashboard vullen**: open het dashboard "Osman-Residence" dat je al hebt
   aangemaakt → potlood (rechtsboven, bewerken) → drie puntjes → **"Raw
   configuratie bewerken"** → plak de volledige inhoud van
   `dashboards/osman_residence.yaml`.
4. **Thema koppelen aan dit dashboard**: dashboard-instellingen → Thema →
   `osman_residence` (of stel het in als jouw gebruikersthema onderaan je
   profielpagina).
5. **Live klok (optioneel maar aanbevolen)**: voeg de integratie **"Datum &
   tijd"** toe (Instellingen → Apparaten en diensten → Integratie
   toevoegen), zodat `sensor.time` en `sensor.date` bestaan voor de klok
   onderaan de zijbalk.

## Entiteiten aanpassen — zoek op "TODO"

De dashboard-YAML bevat overal `# TODO`-commentaar boven placeholder-
entiteiten zoals `light.woonkamer_spots`, `climate.woonkamer`,
`cover.slaapkamer`, `sensor.zonnepanelen_vermogen`, `person.eric`, enzovoort.
Vervang die door je eigen entity_id's. Je vindt de juiste namen via
**Ontwikkelaarstools → Staten** (filter bijvoorbeeld op `light.` of
`climate.`).

Checklist van wat je nodig hebt, per pagina:

**Overzicht**
- 1× `person.*` (voor de begroeting)
- 1× `weather.*` (buitenweer)
- 1× temperatuursensor binnen (of gebruik een `climate`-entiteit)
- 5× `light.*` (Woonkamer Spots, Eettafel, Keuken, Hal, Toilet)
- 4× `scene.*` (Filmstand, Avondstand, Eetstand, Nachtstand)
- 2× `climate.*` (Woonkamer, Slaapkamer)
- 3× `cover.*` (Woonkamer, Slaapkamer, Kantoor)
- Energiesensoren: zonnepanelen-vermogen, huisverbruik, batterijvermogen,
  batterijpercentage, batterij-resterend
- 1× `lock.*` (voordeur), 1× `cover.*` (garage), 1× `switch.*`
  (buitenkamer), 1× `light.*` (oprit)

**Verlichting** — dezelfde lampen als hierboven, plus `light.slaapkamer` en
`light.kantoor`. De knoppen "Alles aan/uit" sturen een vaste lijst
entity_id's aan (geen groep-helper nodig) — pas die lijst in de YAML aan
zodra je je eigen lampen hebt.

**Rolluiken** — dezelfde covers als hierboven; dit worden je Somfy-covers
zodra TaHoma Switch gekoppeld is. "Alles omhoog/omlaag/stop" werkt via
dezelfde aanpak (vaste entity-lijst, geen helper nodig).

**Klimaat** — dezelfde 2 `climate.*`-entiteiten, plus
`sensor.temperatuur_woonkamer` / `sensor.temperatuur_slaapkamer` voor de
trendgrafiek.

**Energie** — dezelfde energiesensoren als bij Overzicht.

**Media** — 3× `media_player.*` (Woonkamer, Slaapkamer, Keuken).

**Beveiliging** — 1× `alarm_control_panel.*`, plus lock/cover/switch/light
van hierboven, plus 3× `binary_sensor.*` (deur-/raamcontacten).

**Camera's** — 4× `camera.*` (Voordeur, Oprit, Achtertuin, Garage) — je
Hikvision-camera's zodra die integratie actief is.

**Instellingen** — dezelfde scènes en `person.eric`, plus optioneel een
`input_boolean.vakantiemodus`-hulpmiddel (Instellingen → Apparaten en
diensten → Hulpmiddelen → Aan/uit-hulpmiddel aanmaken).

Alles wat je nog niet hebt (bijv. Somfy-covers, warmtepomp, camera's) kun je
gewoon laten staan als placeholder — de kaart toont dan "unavailable" totdat
de entiteit bestaat. Niets breekt erdoor.

## Waarom geen losse YAML-integratie/tabbladen?

Je dashboard staat in **Storage Mode** (de UI-editor). Dat blijft zo — dit
bestand plak je één keer in de "Raw configuratie"-editor van dit specifieke
dashboard. De rest van je Home Assistant-configuratie blijft ongemoeid.

De zijbalk wordt op elke pagina herhaald (via een YAML-anchor `&sidebar` in
de bronfile) zodat hij overal identiek is. Bubble Card's menu-kaart herkent
zelf welke pagina actief is.

## Roadmap

- **v1.0 (nu)** — Alle 9 pagina's uitgewerkt met functionele knoppen, op
  basis van placeholder-entiteiten.
- **v1.1** — Jouw eigen entity_id's invullen (zie checklist hierboven) en
  live testen in je eigen Home Assistant.
- **Later** — Plattegrond, animaties, browser_mod pop-ups (bijv.
  camera-preview bij deurbel), eventueel groep-helpers voor "alles"-knoppen
  als de vaste entity-lijsten onhandig worden.

