# Mesh3310 — productie-opmerkingen (rev. C, 18-10-2026: 4 lagen, 1,0 mm)

## Bestellen bij JLCPCB
- **4 lagen, 1,0 mm** (de behuizing van de 3310 laat niet meer toe), ENIG. JLC-opbouw met 3313-prepreg (JLC04101H-3313); in het bord staat 0,0994 mm prepreg / 0,68 mm core / 0,0994 mm prepreg = 0,9992 mm. Controleer bij het bestellen dat de gekozen JLC-opbouw 0,0994 mm (3313) onder de buitenlagen heeft; daar zijn de impedanties op gebaseerd.
  | Laag | Functie |
  |---|---|
  | F.Cu | onderdelen, RF (50 Ω CPWG: 0,14 mm spoor, 0,30 mm spleet) |
  | In1 | doorlopend GND (referentie RF, 0,0994 mm onder F.Cu) |
  | In2 | +3V3-vlak (zone P3V3_IN2) met signalen; rond de nRF52840 extra 3V3-sporen zodat elke 3V3-via zeker aan het vlak hangt |
  | B.Cu | toetsenbord, displayconnector, signalen, GND-vulling |
- Rev. A was 6 lagen / 1,6 mm (JLC06161H-3313) en paste niet in de behuizing. Bij de omzetting zijn de signalen van In3/In4 rond de nRF52840 opnieuw gelegd op F.Cu/In2/B.Cu; RF-, USB-, QSPI-, kristal- en voedingssporen zijn ongewijzigd.
- **Impedantiecontrole** aanvragen: 50 Ω enkelzijdig op F.Cu (ref. In1), 90 Ω differentieel USB (0,15/0,12 mm).
- **Via-in-pad gevuld en afgedekt (POFV/epoxy + cap)** is nodig: via's in de toets-middenpads (contactvlak onder de rubberen dome), in binnenste nRF52840-pads (aQFN-73) en in enkele kleine GND-pads.
- Kleinste maten: spoor/ruimte 0,10 mm (aQFN-uitlopen, enkele segmenten), via 0,30/0,15 mm (14 stuks), meestal 0,45/0,25 mm. Opnieuw gelegde signalen: 0,15 mm (aansluitstukjes op bestaande 0,127 mm-sporen even smal), via's 0,45/0,25 mm.

## Bestanden
- `gerber/` — 11 lagen + PTH/NPTH-boorbestanden + boorkaart + job-bestand (rev. C, 4 lagen)
- `Mesh3310-positie.csv` — positiebestand (zonder DNP)
- `Mesh3310-stuklijst.csv` — stuklijst in JLC-formaat (Comment, Designator, Footprint, LCSC), zonder DNP

Opnieuw maken: exporteer vanuit KiCad 9 (Plot + Drill Files, Position Files en Bill of Materials).

## Onderdelen (LCSC)
- Alle geplaatste onderdelen hebben een LCSC-nummer (veld `LCSC` in het schema). Zonder nummer: alleen pads/behuizingsdelen (SIM J3, J4, testpunten) en BT2 (niet geplaatst).
- Keuze: op voorraad (≥ 500), dan basic > preferred > extended, dan de laagste prijs. 12 basic en 72 extended soorten; onderdelen ~$46 per bord (vooral nRF9151 $17 en nRF52840 $6). Elk extended-onderdeel kost bij JLC ~$3 laadkosten per order; de meeste zijn 0201-passieven (die zijn bij JLC nooit basic).
- **Weinig voorraad** (vóór bestellen checken): SW18 C128539 (27 st.), U8 SKY65723 C2654196 (~330), U3 BQ24074 C54313 (~440), C77 C881877 (~875).
- Aangepast: C16/C17 van tantaal naar keramisch 0603 (C19702 10 µF, C59461 22 µF); X4 32,768 kHz met 9 pF laadcapaciteit (C97605), past bij C22/C29 van 15 pF.
- Datasheets van alle actieve en bijzondere onderdelen: `Kicad_Mesh3310/datasheets/` (van de nRF9151 alleen de productbrief; de volledige specificatie staat op docs.nordicsemi.com).

## Rev. C (schemacontrole tegen de datasheets)
- **C95 10 µF (0402, C15525)** toegevoegd op de VDD van de nRF9151, na FB1. Daar zat geen condensator, terwijl Nordic op VDD 47 µF + 10 µF + 2× 1 µF voorschrijft en VDD onder last ≥ 3,0 V moet blijven (LTE-zendpieken). C28 (47 µF) zit vóór de ferriet.
- **C96 56 pF (0201, C2183491)** in plaats van R6 (0R) tussen RFC van de LoRa-schakelaar U1 en de antenne. De TPE4259-63 eist DC-blokcondensatoren op alle RF-poorten (56 pF); RF1/RF2 hadden die al, RFC niet. Dezelfde padmaat, geen ander koper.
- **C70/C71 niet plaatsen.** De SX1262 heeft interne trimcondensatoren op XTA/XTB (11,3–33,4 pF, Semtech: geen externe nodig); met de extra 10 pF komt de laadcapaciteit boven de 10 pF van X1 uit en kan de firmware niet meer terugtrimmen.
- **BT2, R52 en D17 niet plaatsen.** De knoopcel hing direct aan P0.26 en werd via D17 + R52 uit de 3V3 geladen; de nRF52840 heeft geen apart backup-domein, dus de cel had geen functie en liep via de ESD-diode van P0.26 leeg in de 3V3-rail als het toestel uit staat. De footprints blijven.
- **Batterijdeler R59/R60 1 MΩ (C473482)** in plaats van 47k: lekstroom van ~45 µA naar ~2 µA, ook als het toestel uit staat. C27 (100 nF) aan de ADC-ingang levert de lading voor de meting, dus de hogere bronweerstand (500k) geeft geen meetfout van betekenis.
- **TMR (U3 pin 14) los** in plaats van aan GND: de veiligheidstimers van de lader staan nu aan met de standaardwaarden (voorladen 30 min, laden 5 uur). Aan GND stonden ze uit.

## Controle
- Rev. C: DRC (met schemapariteit en opnieuw gevulde zones) precies gelijk aan rev. B, 0 onverbonden; ERC gelijk aan rev. B (1 fout: los label `MCU_RESETn` op het hoofdblad, plus waarschuwingen over bibliotheeksymbolen).
- Rev. B (4 lagen): DRC 0 onverbonden en precies dezelfde meldingen als rev. A (alleen de bewuste uitzonderingen hieronder, zeefdruk en veldverschillen schema ↔ bord); geen koper-, afstands- of boorfouten. GND op In1 één geheel, +3V3 op In2 één geheel.
- Rev. A: DRC 0 fouten, 0 onverbonden, pariteit schema ↔ bord 0.
- Nagelopen: voedings- en signaalverbindingen in de netlijst, behuizingsonderdelen en bordrand ongewijzigd t.o.v. main, GND- en +3V3-vlak doorlopend, RF-sporen zonder via's, voedingssporen ≥ 0,4 mm.
- Bewuste uitzonderingen (in `Mesh3310.kicad_dru` / projectbestand, met toelichting):
  ANT1-antennepatroon (netloos koper), BLE-antennepin nRF52840 (0,15 mm bij de pin),
  testmal-testpunten (courtyards), backlight-LED's in eigen uitsparing, aangepaste footprints (waarschuwing).

## Programmeren
- nRF52840: SWD via de testpunten van de testmal (SWDIO/SWDCLK/RESET/SWO).
- nRF9151: R10/R28 (0R naar de gedeelde SWD-lijnen) zijn standaard **niet geplaatst**. Twee SWD-chips op dezelfde lijnen werkt niet; de nRF9151 programmeren via de pads van R10/R28 (kant U12) of de 0R's tijdelijk plaatsen.

## Nog te doen / aandachtspunten
- **EN1 van de lader (U3) hangt aan VBUS.** EN1 mag maximaal 7 V hebben, de lader werkt tot 10,5 V. Met een gewone 5 V-lader is dat geen probleem, wel met een lader die meer geeft. Beter: EN1 aan OUT (VSYS, max. 4,4 V); dat vraagt een nieuw spoortje naast U3.
- **Voeding:** de TPS63031 levert 800 mA (buck) / 500 mA (boost, lege accu). LTE-zendpieken, LoRa +22 dBm, de versterker en de backlight tegelijk kunnen de 3V3 laten zakken; in de firmware niet tegelijk laten pieken.
- **Aan/uit-knop SW18** schakelt via de MAX16054 direct de voeding; de firmware ziet de knop niet en kan niet netjes afsluiten (P0.02 kan wel zelf uitzetten).
- **BLE-antenne:** RF3 (U.FL) en ANT2 zitten zonder keuzeweerstand tegelijk op de lijn; bij afstemmen kiezen.
- **RF afstemmen** met de echte antennes en kabels: LTE-pi-netwerk R61/C93/C94 (shunts nu DNP), LoRa en BLE volgens het oorspronkelijke ontwerp.
- **LTE-antenne**: plak-flexantenne via U.FL J8 (bv. Taoglas FXUB63); GNSS-antenne via U.FL J11.
- **Montagegat H6** ligt in de LoRa-antennezone en is aan GND gehangen (kort spoor langs de zonerand); eventueel los laten na meting.
- **Bouwhoogte** van U.FL-connectoren J8/J11 (+ stekker) controleren in de behuizing.
- Referenties van kleine passieven staan niet op de zeefdruk (wel op de Fab-laag / in het positiebestand).
