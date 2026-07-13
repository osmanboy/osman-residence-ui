# Osman Residence UI

Een eigen, luxe dashboard-thema bovenop Home Assistant — donker, koperkleurig,
met een vaste verticale zijbalk in plaats van de standaard tabbladen.

Dit is **v0.1 (Sprint 1: Foundation)**: het thema + het volledig uitgewerkte
"Overzicht"-scherm, plus 8 lege pagina's (Verlichting, Rolluiken, Klimaat,
Energie, Media, Beveiliging, Camera's, Instellingen) die in latere sprints
gevuld worden.

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

| Card              | Waarvoor gebruikt in v0.1                              |
|-------------------|----------------------------------------------------------|
| Mushroom          | Lichten, klimaat, covers, chips, template-cards          |
| Bubble Card       | De verticale zijbalk-menu + scène-knoppen                |
| button-card       | Batterijkaart, "alles"-knoppen, snelkoppelingen           |
| card-mod          | Kleuren/typografie afstemmen op het thema                |
| layout-card       | De sidebar/content grid-indeling                          |
| apexcharts-card   | De kleine trendlijntjes (binnenklimaat, energie-opwek)   |
| browser_mod       | Nog niet actief gebruikt — gereserveerd voor Sprint 3 (pop-ups) |

Je had dit rijtje zelf ook al genoemd — dit is exact die stack, ingezet op de
plekken waar elke card het sterkst is.

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

Korte checklist van wat je nodig hebt voor het Overzicht-scherm:

- 1× `person.*` (voor de begroeting)
- 1× `weather.*` (buitenweer)
- 1× temperatuursensor binnen (of gebruik een `climate`-entiteit)
- 5× `light.*` (Woonkamer Spots, Eettafel, Keuken, Hal, Toilet)
- 4× `scene.*` (Filmstand, Avondstand, Eetstand, Nachtstand)
- 2× `climate.*` (Woonkamer, Slaapkamer)
- 3× `cover.*` (Woonkamer, Slaapkamer, Kantoor) — dit worden je Somfy-covers
  zodra TaHoma Switch gekoppeld is
- Energiesensoren: zonnepanelen-vermogen, huisverbruik, batterijvermogen,
  batterijpercentage
- 1× `lock.*` (voordeur), 1× `cover.*` (garage), 1× `switch.*`
  (buitenkamer), 1× `light.*` (oprit)

Alles wat je nog niet hebt (bijv. Somfy-covers, warmtepomp) kun je gewoon
laten staan als placeholder — de kaart toont dan "unavailable" totdat de
entiteit bestaat. Niets breekt erdoor.

## Waarom geen losse YAML-integratie/tabbladen?

Je dashboard staat in **Storage Mode** (de UI-editor). Dat blijft zo — dit
bestand plak je één keer in de "Raw configuratie"-editor van dit specifieke
dashboard. De rest van je Home Assistant-configuratie blijft ongemoeid.

De zijbalk wordt op elke pagina herhaald (via een YAML-anchor `&sidebar` in
de bronfile) zodat hij overal identiek is. Bubble Card's menu-kaart herkent
zelf welke pagina actief is.

## Roadmap

- **v0.2** — Verlichting-pagina (per-kamer overzicht, groepen)
- **v0.3** — Rolluiken-pagina (Somfy TaHoma Switch-integratie)
- **v0.4** — Klimaat-pagina (LG airco + Hisense warmtepomp)
- **v0.5** — Camera's (Hikvision)
- **v0.6** — Energie-dashboard (uitgebreide grafieken)
- **v1.0** — Eerste stabiele versie: plattegrond, scènebeheer, animaties

## Naar GitHub pushen

Ik heb dit nu klaargezet als bestanden die je zo kunt committen. Ik heb zelf
geen schrijftoegang tot een GitHub-repository — dat moet via een expliciet
gekoppelde GitHub-integratie in deze chat, en anders doe je het zelf met:

```bash
git init osman-residence-ui
cd osman-residence-ui
# kopieer de gedownloade bestanden hierin
git add .
git commit -m "v0.1 — Foundation: thema + Overzicht-dashboard"
git branch -M main
git remote add origin git@github.com:<jouw-gebruikersnaam>/osman-residence-ui.git
git push -u origin main
```
