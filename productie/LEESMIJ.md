# Mesh3310 — productie-opmerkingen (rev. E, 18-10-2026: 4 lagen, 1,0 mm)

## Bestellen bij JLCPCB
- **4 lagen, 1,0 mm** (de behuizing van de 3310 laat niet meer toe), ENIG. JLC-opbouw met 3313-prepreg (JLC04101H-3313); in het bord staat 0,0994 mm prepreg / 0,68 mm core / 0,0994 mm prepreg = 0,9992 mm. Controleer bij het bestellen dat de gekozen JLC-opbouw 0,0994 mm (3313) onder de buitenlagen heeft; daar zijn de impedanties op gebaseerd.
  | Laag | Functie |
  |---|---|
  | F.Cu | onderdelen, RF (50 Ω CPWG: 0,14 mm spoor, 0,30 mm spleet) |
  | In1 | doorlopend GND (referentie RF, 0,0994 mm onder F.Cu) |
  | In2 | +3V3-vlak (zone P3V3_IN2) met de signalen die al op In2 lagen en de netten die van In4 kwamen; het vlak is één geheel |
  | B.Cu | toetsenbord, displayconnector, signalen, GND-vulling |
- Rev. A was 6 lagen / 1,6 mm (JLC06161H-3313) en paste niet in de behuizing. De 4-laagsversie is zo dicht mogelijk bij rev. A gehouden: 1574 van de 1741 sporen zijn ongewijzigd, F.Cu en B.Cu zijn vrijwel gelijk. Het 3V3-vlak ging van In3 naar In2. Alleen rond de nRF52840 is opnieuw gelegd: de 6 netten die op In4 lagen (LoRa SCK/MOSI, SWDIO, display SCK en D/C, ROW_3) en de In2-stukken van de netten die daarmee botsten (COL_2, COL_3, GSM_WAKE, PWR_OFF, CHG, BAT_RTC, LORA_MISO, P0.06, MCU_RESETn), recht of onder 45°. RF-, USB-, QSPI-, kristal- en voedingssporen zijn ongewijzigd.
- **Impedantiecontrole** aanvragen: 50 Ω enkelzijdig op F.Cu (ref. In1), 90 Ω differentieel USB (0,15/0,12 mm).
- **Via-in-pad gevuld en afgedekt (POFV/epoxy + cap)** is nodig: via's in de toets-middenpads (contactvlak onder de rubberen dome), in binnenste nRF52840-pads (aQFN-73) en in enkele kleine GND-pads.
- Kleinste maten: spoor/ruimte 0,10 mm (aQFN-uitlopen, enkele segmenten), via 0,30/0,15 mm (14 stuks), meestal 0,45/0,25 mm. Opnieuw gelegde signalen rond de nRF52840: 0,15 mm over de hele lengte; nieuwe netten van rev. D/E: 0,2 mm (VSYS 0,4 mm); nieuwe via's 0,45/0,25 mm.

## Bestanden
- `gerber/` — 11 lagen + PTH/NPTH-boorbestanden + boorkaart + job-bestand (rev. E, 4 lagen)
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

## Rev. D (voeding nRF9151 en lader)
- **nRF9151 rechtstreeks op VSYS** (FB1 pin 1 van +3V3 naar VSYS). De nRF9151 werkt op 3,0–5,5 V en VSYS is maximaal 4,4 V. Zo levert de TPS63031 (800 mA buck / 500 mA boost) de LTE-zendpieken niet meer; op 3V3 blijven nRF52840, SX1262, versterker, flash en backlight over.
- **ENABLE van de nRF9151 op PWR_EN** (R12 van VDD naar de uitgang van U9, die ook de TPS63031 aanzet). Nordic: ENABLE moet boven 0,89 × VDD komen; U9 is CMOS en voedt uit VSYS, dus PWR_EN haalt dat. Staat het toestel uit, dan is de nRF9151 uitgeschakeld (ENABLE en VDD_GPIO laag) en verbruikt hij bijna niets.
- **EN1 van de lader (U3 pin 6) van VBUS naar VSYS** (OUT, max. 4,4 V). Daarmee kan EN1 niet meer boven de 7 V komen als er een lader met te hoge spanning aan hangt. EN1 hoog / EN2 laag blijft de 500 mA-stand (USB500).
- Koper: EN1 met een kort spoor rechts langs U3 naar U9 pin 6. Voor dat spoor is de VBUS-doorvoervia rechts van U3 weg (VBUS loopt daar alleen op B.Cu door), en zijn twee GND-vias 0,15 mm opgeschoven. FB1 krijgt VSYS via een 0,3 mm-spoor op B.Cu. PWR_EN loopt met een paar via's over F.Cu/In2 en bewust niet onder L2 door, vanwege de schakelruis op ENABLE. C95 wordt nu rechtstreeks vanaf de VDD-via gevoed.
- De opstartvolgorde (VDD_GPIO > 6 ms na ENABLE) is in rev. E opgelost, zie hieronder.

## Rev. E (aan/uit-knop, opstartvolgorde, buffer)
- **Aan/uit-knop SW18:**
  - **Uit:** een korte druk zet het toestel aan (MAX16054, zoals voorheen).
  - **Aan:** Q8 (BSS138, gate op /OUT van U9) koppelt de knop los van U9, zodat een druk de voeding niet meer omzet. De firmware leest de knop via D18 (1N4148WS) op **P0.26**: laag = ingedrukt; zet de interne pull-up aan. Korte druk = standby (of wat de firmware wil); netjes uitzetten = **P0.02 hoog** (CLR).
  - **Lang ingedrukt (~3 s, met tolerantie 2–5 s):** C97+C98 (2× 47 µF) laden via R63 (47k) uit 3V3, en D19 (BAT60A) trekt CLR boven de drempel. De voeding gaat dan hard uit, ook als de firmware hangt. Q9 (BSS138) maakt het tijdnet direct leeg zodra je loslaat, dus korte drukken tellen niet op. R62 (1M) houdt het knopknooppunt hoog voordat de firmware P0.26 heeft ingesteld; D18 voorkomt dat P0.26 terugvoeding krijgt als het toestel uit staat.
  - R51 (pull-down op CLR) is 330k geworden (was 100k), anders haalt het tijdnet de CLR-drempel niet.
  - **Firmware:** P0.02 nooit laag sturen. Laat hem ingang/los en maak hem alleen hoog om uit te zetten; een laag gestuurde P0.02 blokkeert de lange druk. Houd je de knop na het aanzetten langer dan ~3 s vast, dan gaat het toestel weer uit.
- **3V3 pas na ENABLE van de nRF9151:** R64 (1M) + C100 (100 nF) vertragen de EN van U14 met 12–51 ms (de EN-drempel ligt tussen 0,4 en 1,2 V). Nordic vraagt VDD_GPIO > 6 ms na ENABLE en VDD. D20 (BAT60A) maakt C100 bij uitzetten meteen leeg, zodat 3V3 samen met ENABLE wegvalt.
- **C99 47 µF op VSYS** bij FB1/de lader: buffer voor de LTE-zendpieken van de nRF9151. Naast de nRF9151 zelf was geen plek; achter FB1 zit C95 (10 µF).
- Alle nieuwe onderdelen staan al in de stuklijst (BSS138, BAT60A, 47 µF 0805, 1M, 47k, 100 nF), behalve D18 1N4148WS (C2128, basic) en R51 330k (C270349).
- Ze staan op F.Cu onder U9/U14 (x 121–139, y 89–95), waar eerst alleen GND-stitching-vias zaten. Tien daarvan zijn weggehaald. **Bouwhoogte** (SOT-23 ~1,1 mm, 0805 ~1,3 mm) daar nog in de behuizing controleren.
- Er zijn geen nieuwe via's in pads bijgekomen.

## Controle
- Rev. E (opnieuw opgebouwd vanaf rev. A, 18-10-2026): DRC met schemapariteit 0 onverbonden en geen koper-, afstands- of boorfouten, ook zonder de zones opnieuw te vullen (de vulling staat in het bestand). Bijgekomen t.o.v. rev. A: veldverschillen (LCSC-veld in de nieuwe footprints), SOT-23 wijkt af van de bibliotheek (zoals Q4) en één zeefdrukoverlap D20/Q8. ERC gelijk aan rev. D. GND op In1 en +3V3 op In2 elk één geheel.
- Rev. D: DRC (met schemapariteit en opnieuw gevulde zones) precies gelijk aan rev. C, 0 onverbonden; ERC gelijk aan rev. C. GND op In1 en +3V3 op In2 nog steeds elk één geheel.
- Rev. C: DRC (met schemapariteit en opnieuw gevulde zones) precies gelijk aan rev. B, 0 onverbonden; ERC gelijk aan rev. B (1 fout: los label `MCU_RESETn` op het hoofdblad, plus waarschuwingen over bibliotheeksymbolen).
- Rev. A: DRC 0 fouten, 0 onverbonden, pariteit schema ↔ bord 0.
- Nagelopen: voedings- en signaalverbindingen in de netlijst, behuizingsonderdelen en bordrand ongewijzigd t.o.v. main, GND- en +3V3-vlak doorlopend, RF-sporen zonder via's, voedingssporen ≥ 0,4 mm.
- Bewuste uitzonderingen (in `Mesh3310.kicad_dru` / projectbestand, met toelichting):
  ANT1-antennepatroon (netloos koper), BLE-antennepin nRF52840 (0,15 mm bij de pin),
  testmal-testpunten (courtyards), backlight-LED's in eigen uitsparing, aangepaste footprints (waarschuwing).

## Programmeren
- nRF52840: SWD via de testpunten van de testmal (SWDIO/SWDCLK/RESET/SWO).
- nRF9151: R10/R28 (0R naar de gedeelde SWD-lijnen) zijn standaard **niet geplaatst**. Twee SWD-chips op dezelfde lijnen werkt niet; de nRF9151 programmeren via de pads van R10/R28 (kant U12) of de 0R's tijdelijk plaatsen.

## Nog te doen / aandachtspunten
- **BLE-antenne kiezen:** RF3 (U.FL) en ANT2 hangen op hetzelfde knooppunt achter het matchnetwerk. Chipantenne: geen kabel op RF3. Externe antenne via RF3: L4 (1,5 nH, in serie naar ANT2) niet plaatsen, anders deelt het signaal zich over beide.
- **VDD van de nRF9151:** 10 µF (C95) direct achter FB1 en 47 µF (C99) op VSYS ervoor. Bij spanningsdips onder LTE-zenden eerst hier meten.
- **Netnaam P0.26** heet nog `P0.26_BAT_RTC` (vroeger knoopcel); het is nu de knopingang.
- **USB:** J4 zijn de pads van de originele onderconnector; er zitten geen CC-weerstanden (5k1) op het bord. Komt er een USB-C-verloopstuk aan, dan moeten die daarin, anders levert een USB-C-lader geen 5 V.
- **RF afstemmen** met de echte antennes en kabels: LTE-pi-netwerk R61/C93/C94 (shunts nu DNP), LoRa en BLE volgens het oorspronkelijke ontwerp.
- **LTE-antenne**: plak-flexantenne via U.FL J8 (bv. Taoglas FXUB63); GNSS-antenne via U.FL J11.
- **Montagegat H6** ligt in de LoRa-antennezone en is aan GND gehangen (kort spoor langs de zonerand); eventueel los laten na meting.
- **Bouwhoogte** van U.FL-connectoren J8/J11 (+ stekker) controleren in de behuizing.
- Referenties van kleine passieven staan niet op de zeefdruk (wel op de Fab-laag / in het positiebestand).
