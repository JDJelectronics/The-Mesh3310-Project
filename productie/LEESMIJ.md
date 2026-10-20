# Mesh3310 — productie-opmerkingen (rev. G, 19-10-2026: 4 lagen, 1,0 mm)

## Bestellen bij JLCPCB
- **4 lagen, 1,0 mm** (de behuizing van de 3310 laat niet meer toe), ENIG. JLC-opbouw met 3313-prepreg (JLC04101H-3313); in het bord staat 0,0994 mm prepreg / 0,68 mm core / 0,0994 mm prepreg = 0,9992 mm. Controleer bij het bestellen dat de gekozen JLC-opbouw 0,0994 mm (3313) onder de buitenlagen heeft; daar zijn de impedanties op gebaseerd.
  | Laag | Functie |
  |---|---|
  | F.Cu | onderdelen, RF (50 Ω CPWG: 0,14 mm spoor, 0,30 mm spleet) |
  | In1 | doorlopend GND (referentie RF, 0,0994 mm onder F.Cu) |
  | In2 | +3V3-vlak (zone P3V3_IN2) met de signalen die al op In2 lagen en de netten die van In4 kwamen. Onder de nRF52840 is het vlak door die signalen in een paar eilandjes verdeeld; die hangen via korte 3V3-spoortjes (0,15 mm) aan het hoofdvlak. |
  | B.Cu | toetsenbord, displayconnector, signalen, GND-vulling |
- Rev. A was 6 lagen / 1,6 mm (JLC06161H-3313) en paste niet in de behuizing. De 4-laagsversie is zo dicht mogelijk bij rev. A gehouden: 1574 van de 1741 sporen zijn ongewijzigd, F.Cu en B.Cu zijn vrijwel gelijk. Het 3V3-vlak ging van In3 naar In2. Alleen rond de nRF52840 is opnieuw gelegd: de 6 netten die op In4 lagen (LoRa SCK/MOSI, SWDIO, display SCK en D/C, ROW_3) en de In2-stukken van de netten die daarmee botsten (COL_2, COL_3, GSM_WAKE, PWR_OFF, CHG, BAT_RTC, LORA_MISO, P0.06, MCU_RESETn), recht of onder 45°. RF-, USB-, QSPI-, kristal- en voedingssporen zijn ongewijzigd.
- **Impedantiecontrole** aanvragen: 50 Ω enkelzijdig op F.Cu (ref. In1). USB D+/D− lopen niet als gekoppeld paar (0,118 mm, tot 9 mm uit elkaar, ~60 Ω enkelzijdig); voor USB Full Speed is dat geen probleem, maar een 90 Ω-eis bestellen heeft geen zin.
- **Via-in-pad gevuld en afgedekt (POFV/epoxy + cap)** is nodig en moet expliciet bij de bestelling: ~185 via's raken een SMD-pad (toets-middenpads onder de rubberen dome, binnenste nRF52840-pads van de aQFN-73, 0201-pads, QFN-pinnen, de thermal pad van U3). In het bord staan de via's op "tenting", dat is niet genoeg.
- Kleinste maten: spoor/ruimte 0,10 mm (aQFN-uitlopen, enkele segmenten), via 0,30/0,15 mm (18 stuks), meestal 0,45/0,25 mm. Opnieuw gelegde signalen rond de nRF52840: 0,15 mm over de hele lengte; nieuwe netten van rev. D/E: 0,2 mm (VSYS 0,4 mm); nieuwe via's 0,45/0,25 mm.

## Bestanden
- `gerber/` — 11 lagen + PTH/NPTH-boorbestanden + boorkaart + job-bestand (rev. G, 4 lagen)
- `Mesh3310-positie.csv` — positiebestand (zonder DNP)
- `Mesh3310-stuklijst.csv` — stuklijst in JLC-formaat (Comment, Designator, Footprint, LCSC), zonder DNP
- `Mesh3310-JLC-stuklijst.csv` + `Mesh3310-JLC-positie.csv` — deze uploaden bij JLC. Regels met hetzelfde LCSC-nummer zijn samengevoegd (anders vinkt JLC ze uit) en de positie staat in het JLC-formaat (Designator, Mid X, Mid Y, Layer, Rotation); het KiCad-positiebestand wordt geweigerd. Na het uploaden de LEDs (D5–D13) en de 0 Ω-groep zelf aanvinken.
- `variant-UFL/` — zelfde gerbers, LoRa naar U.FL in plaats van de chipantenne (RF4 erop, L19 en C87 eraf); eigen stuklijst en positiebestand

Opnieuw maken: exporteer vanuit KiCad 9 (Plot + Drill Files, Position Files en Bill of Materials).

## Onderdelen (LCSC)
- Alle geplaatste onderdelen hebben een LCSC-nummer (veld `LCSC` in het schema). Zonder nummer: alleen pads/behuizingsdelen (SIM J3, J4, testpunten) en BT2 (niet geplaatst).
- Keuze: op voorraad (≥ 500), dan basic > preferred > extended, dan de laagste prijs. 12 basic en 72 extended soorten; onderdelen ~$46 per bord (vooral nRF9151 $17 en nRF52840 $6). Elk extended-onderdeel kost bij JLC ~$3 laadkosten per order; de meeste zijn 0201-passieven (die zijn bij JLC nooit basic).
- **Weinig voorraad** (vóór bestellen checken): U15 TPS3420DDRYR C1848416 (56 st.), SW18 C128539 (27 st.), U8 SKY65723 C2654196 (~330), U3 BQ24074 C54313 (~440), C77 C881877 (~875).
- Aangepast: C16/C17 van tantaal naar keramisch 0603 (C19702 10 µF, C59461 22 µF); X4 32,768 kHz met 9 pF laadcapaciteit (C97605), past bij C22/C29 van 15 pF.
- Datasheets van alle actieve en bijzondere onderdelen: `Kicad_Mesh3310/datasheets/` (van de nRF9151 alleen de productbrief; de volledige specificatie staat op docs.nordicsemi.com).

## Rev. G (open punten, 19-10-2026)
- **Lange druk met een vaste tijd: U15 TPS3420** (TI, C1848416, USON-6 1,45 × 1 mm) in plaats van het RC-net uit rev. E (R63, C97, C98, Q9, D19 zijn weg). PB1 hangt aan de knop, PB2 en TS aan GND (= 7,5 s, 6–9 s over temperatuur en spreiding). Na 7,5 s trekt RST (open drain) **KNOP_IN** laag, de ingang van de MAX16054: die ziet een druk en zet de voeding uit. RST blijft laag tot je de knop loslaat, dus het toestel gaat daarna niet vanzelf weer aan (de oude schakeling kon dat wel). U15 draait op 3V3 en verbruikt alleen als het toestel aan staat (~0,35 µA); C101 100 nF ontkoppelt hem.
  - Vervalt: de spreiding van 0,5–6 s, de 70 µA door R63 en de regel dat P0.02 nooit laag mag. P0.02 mag nu gewoon een uitgang zijn: laag = niets, hoog = uit.
  - **R62 (1M) trekt de knop op naar VSYS** in plaats van 3V3. Als het toestel uit staat, voedt hij de 3V3 dus niet meer terug (was ~2,5 µA).
  - Houd je de knop bij het aanzetten langer dan 7,5 s vast, dan gaat het toestel weer uit.
- **R66 100k pull-up op FLASH_CS** (U2, flash van de nRF9151). Zo blijft de flash gedeselecteerd tot de nRF9151 zijn pinnen heeft ingesteld.
- **RF4 (U.FL LoRa) standaard niet plaatsen.** Hij hing parallel aan de chipantenne. Wil je een externe LoRa-antenne, plaats dan RF4 en laat L19 (serie naar de chipantenne) weg.
- **SX1262 (U10):** drie extra GND-via's (0,3/0,15) in de EP; er zat er maar één.
- **Netnaam:** `P0.26_BAT_RTC` heet nu `P0.26_PWR_KNOP` (knopingang, sinds rev. C geen knoopcel meer).
- Bord: R63/C97/C98/Q9/D19 weg, U15 en C101 op de plek van Q9, KNOP recht doorgetrokken waar Q9 zat, R62 via een 0,2 mm VSYS-spoor langs de onderkant van C6, KNOP_IN via B.Cu naar Q8. Bestaande sporen verder ongewijzigd.

## Rev. F (controleronde 18/19-10-2026)
Volledige controle van schema en bord (voeding, beide Nordic-chips, LoRa/RF, interfaces, layout). Doorgevoerd:
- **D20 1N4148WS** (was BAT60A), **R64 100k** (was 1M) en **C100 1 µF 0402** (was 100 nF 0201): de BAT60A lekt in sper ~0,3 mA (25 °C) tot 18 mA (80 °C), waardoor de 3V3-vertraging in de praktijk maar 0,3–1,5 ms was. Nu weer ~10–50 ms (τ = 0,1 s).
- **D19 BAT54WS** (C22629, was BAT60A): zelfde lekprobleem op CLR; BAT54 heeft ook een lage drempelspanning, nodig voor de CLR-marge.
- **C12 niet plaatsen** (100 nF op COEX0): Nordic staat op COEX0 maximaal 50 pF toe. **R14 100k** (was 1M): Nordic schrijft ~100 kΩ pull-down voor op COEX.
- **C47 4,7 µF** (was 2,2 µF) op DECUSB: de nRF52840 eist 2,35–5,5 µF.
- **L16 47 nH 0402 Murata LQW15AN47NJ00D** (C192855, 210 mA; was 0201, 100 mA): de PA van de SX1262 trekt ~118 mA door deze choke bij +22 dBm. De courtyard overlapt die van de 0201-buren (pads ≥ 0,25 mm vrij); uitzondering in `Mesh3310.kicad_dru`.
- **C81 3,3 pF** (C161425, ±0,1 pF; was 3 pF): het harmonische filter L17/C81 lag op 2×915 MHz, nu op 2×868 MHz (de antenne is 868 MHz). Nog meten.
- **C2 (100 nF), C26 (15 pF) en C28 (47 µF) van +3V3 naar de VDD van de nRF9151** (net nu `/GSM/VDD_9151`): restanten van toen VDD aan 3V3 hing. C2/C26 zitten direct onder pin 14, C28 3 mm verder.
- **C91 10 µF** (was 4,7 µF) op BAT van de lader (na DC-bias was er ~2,5 µF over; TI vraagt ≥ 4,7 µF).
- **R65 100k pull-down op display-RES**: de PCD8544 moet bij opkomen van VDD binnen 30 ms gereset worden; zonder pull-down zweeft RES tijdens de boot.
- **Q4/Q7 AO3400A** (C20917, was BSS138): de BSS138 heeft bij 3,3 V gate ~8–10 Ω; motor en buzzer kregen te weinig spanning.
- **D4/D8 1N4148WS** (was BAT60A): de flyback-diodes lekten mA's zolang motor/buzzer aan staan.
- **R36 1M** (was 220k): de microfoonvoorversterker Q1 stond op Vc ≈ 0,85 V (clipping), nu ~1,3–1,5 V.
- Bord: drie via's in de thermal pad van U3 (lader) naar GND op In1, C90/C99/C100/U3 kregen hun GND-verbinding terug; de GND-via bij R5 ligt weer naast de pad; schuine stukjes rechtgezet.

## Rev. C (schemacontrole tegen de datasheets)
- **C95 10 µF (0402, C15525)** toegevoegd op de VDD van de nRF9151, na FB1. Daar zat geen condensator, terwijl Nordic op VDD 47 µF + 10 µF + 2× 1 µF voorschrijft en VDD onder last ≥ 3,0 V moet blijven (LTE-zendpieken). C28 (47 µF) zit sinds rev. F ook op VDD.
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
  - **Lang ingedrukt (typisch ~2–3 s, spreiding ~0,5–6 s):** C97+C98 (2× 47 µF) laden via R63 (47k) uit 3V3, en D19 (BAT54WS sinds rev. F) trekt CLR boven de drempel. De voeding gaat dan hard uit, ook als de firmware hangt. Q9 (BSS138) maakt het tijdnet direct leeg zodra je loslaat, dus korte drukken tellen niet op. R62 (1M) houdt het knopknooppunt hoog voordat de firmware P0.26 heeft ingesteld; D18 voorkomt dat P0.26 terugvoeding krijgt als het toestel uit staat.
  - R51 (pull-down op CLR) is 330k geworden (was 100k), anders haalt het tijdnet de CLR-drempel niet.
  - (Sinds rev. G vervangen door U15, zie hierboven.) **Firmware (rev. E/F):** P0.02 nooit laag sturen. Laat hem ingang/los en maak hem alleen hoog om uit te zetten; een laag gestuurde P0.02 blokkeert de lange druk. Houd je de knop na het aanzetten langer dan ~3 s vast, dan gaat het toestel weer uit.
- **3V3 pas na ENABLE van de nRF9151:** R64 + C100 vertragen de EN van U14 (sinds rev. F 100k / 1 µF, ~10–50 ms; de EN-drempel ligt tussen 0,4 en 1,2 V). Nordic vraagt VDD_GPIO > 6 ms na ENABLE en VDD. D20 (sinds rev. F een 1N4148WS) maakt C100 bij uitzetten meteen leeg.
- **C99 47 µF op VSYS** bij FB1/de lader: buffer voor de LTE-zendpieken van de nRF9151. Naast de nRF9151 zelf was geen plek; achter FB1 zit C95 (10 µF).
- Alle nieuwe onderdelen staan al in de stuklijst (BSS138, BAT60A, 47 µF 0805, 1M, 47k, 100 nF), behalve D18 1N4148WS (C2128, basic) en R51 330k (C142017).
- Ze staan op F.Cu onder U9/U14 (x 121–139, y 89–95), waar eerst alleen GND-stitching-vias zaten. Tien daarvan zijn weggehaald. **Bouwhoogte** (SOT-23 ~1,1 mm, 0805 ~1,3 mm) daar nog in de behuizing controleren.

## Controle
- Rev. G (19-10-2026): DRC met schemapariteit 0 onverbonden en geen koper-, afstands- of boorfouten, ook zonder de zones opnieuw te vullen. De overgebleven meldingen zijn dezelfde soort als in rev. F (veldverschillen LCSC, bibliotheekafwijkingen, zeefdruk). ERC gelijk aan rev. F, plus de waarschuwing dat het nieuwe symbool U15 niet in een bibliotheek staat (net als de andere eigen symbolen).
- Rev. F (19-10-2026): DRC met schemapariteit 0 onverbonden en geen koper-, afstands- of boorfouten, ook zonder de zones opnieuw te vullen. Meldingen t.o.v. rev. A: veldverschillen (LCSC-veld), bibliotheekafwijking SOT-23 (Q4–Q9) en L16 (0402), één zeefdrukoverlap D20/Q8. ERC: 1 fout (los label `MCU_RESETn`, al sinds rev. A) en waarschuwingen over bibliotheeksymbolen. GND op In1 één geheel.
- Rev. E (opnieuw opgebouwd vanaf rev. A, 18-10-2026): DRC met schemapariteit 0 onverbonden en geen koper-, afstands- of boorfouten, ook zonder de zones opnieuw te vullen (de vulling staat in het bestand). Bijgekomen t.o.v. rev. A: veldverschillen (LCSC-veld in de nieuwe footprints), SOT-23 wijkt af van de bibliotheek (zoals Q4) en één zeefdrukoverlap D20/Q8. ERC gelijk aan rev. D. GND op In1 en +3V3 op In2 elk één geheel.
- Rev. D: DRC (met schemapariteit en opnieuw gevulde zones) precies gelijk aan rev. C, 0 onverbonden; ERC gelijk aan rev. C. GND op In1 en +3V3 op In2 nog steeds elk één geheel.
- Rev. C: DRC (met schemapariteit en opnieuw gevulde zones) precies gelijk aan rev. B, 0 onverbonden; ERC gelijk aan rev. B (1 fout: los label `MCU_RESETn` op het hoofdblad, plus waarschuwingen over bibliotheeksymbolen).
- Rev. A: DRC 0 fouten, 0 onverbonden, pariteit schema ↔ bord 0.
- Nagelopen: voedings- en signaalverbindingen in de netlijst, behuizingsonderdelen en bordrand ongewijzigd t.o.v. main, GND-vlak doorlopend, RF-sporen zonder via's, hoofdvoedingssporen 0,4 mm (aftakkingen naar losse condensatoren 0,2–0,3 mm).
- Bewuste uitzonderingen (in `Mesh3310.kicad_dru` / projectbestand, met toelichting):
  ANT1-antennepatroon (netloos koper), BLE-antennepin nRF52840 (0,15 mm bij de pin),
  testmal-testpunten (courtyards), courtyard van L16 (0402 tussen 0201-buren), backlight-LED's in eigen uitsparing, aangepaste footprints (waarschuwing).

## Programmeren
- nRF52840: SWD via de testpunten van de testmal (SWDIO/SWDCLK/RESET/SWO).
- nRF9151: R10/R28 (0R naar de gedeelde SWD-lijnen) zijn standaard **niet geplaatst**. Twee SWD-chips op dezelfde lijnen werkt niet; de nRF9151 programmeren via de pads van R10/R28 (kant U12) of de 0R's tijdelijk plaatsen.

## Nog te doen / aandachtspunten
Bewust niet op het bord veranderd (firmware of meten):
- **nRF9151:** P1.15 (reset) in de firmware als open-drain sturen (Nordic: geen externe pull-up op nRESET). Bij netjes uitzetten eerst `AT+CFUN=0`, daarna P0.02 hoog: ENABLE valt dan pas als de modem klaar is. Bij een harde uit (lange druk) is dat niet te voorkomen.
- **GSM_RX zit op P1.07, een low-frequency-pin** van de nRF52840 (≤ 10 kHz volgens Nordic, vanwege storing op de radio). Aan die kant van de chip zijn alle vrije pinnen low-frequency; omwisselen met P0.27 vraagt twee lange sporen dwars onder de aQFN door. Een UART-ingang op 115200 baud werkt; houd de baudrate laag en meet de BLE-gevoeligheid.
- **Geen flow control/IRQ tussen de chips:** de Serial LTE Modem-firmware van Nordic werkt zonder RTS/CTS; GSM_WAKE is de wekker.
- **QSPI-flash U4** zit niet op de aanbevolen QSPI-pinnen: QSPI op ≤ 16 MHz houden. **CS van U4 heeft geen pull-up** (geen plek naast de pinnen); zet in de bootloader/firmware CS vroeg hoog of de interne pull-up aan.
- **nRF52840:** VDD-pinnen W1/Y2/AD14 hangen via 3V3-eilandjes op In2 en dunne spoortjes aan hun condensatoren (6–8 mm). Het werkt (alles is verbonden), maar beter wordt het alleen door In2-signalen onder de chip te verleggen.
- **LoRa:** geen ESD op het antennepad (met RF4 niet geplaatst zit er geen externe aansluiting meer aan). L3 (15 µH) zit precies op de minimum-SRF van Semtech.
- **Motor/buzzer** blijven op 3V3: VSYS (tot 4,4 V) is te veel voor een 3 V-motor. Q4/Q7 zijn sinds rev. F AO3400A.
- **U14 (TPS63031):** C5 (100 nF) zit 1,3 mm van VIN, de 10 µF (C92) 5,6 mm verder. Bij onrust op de 3V3 eerst hier meten.
- **BLE-antenne kiezen:** RF3 (U.FL) en ANT2 hangen op hetzelfde knooppunt achter het matchnetwerk. Chipantenne: geen kabel op RF3. Externe antenne via RF3: L4 (1,5 nH, in serie naar ANT2) niet plaatsen, anders deelt het signaal zich over beide.
- **VDD van de nRF9151:** 10 µF (C95) direct achter FB1 en 47 µF (C99) op VSYS ervoor. Bij spanningsdips onder LTE-zenden eerst hier meten.
- **USB:** J4 zijn de pads van de originele onderconnector; er zitten geen CC-weerstanden (5k1) op het bord. Komt er een USB-C-verloopstuk aan, dan moeten die daarin, anders levert een USB-C-lader geen 5 V.
- **RF afstemmen** met de echte antennes en kabels: LTE-pi-netwerk R61/C93/C94 (shunts nu DNP), LoRa en BLE volgens het oorspronkelijke ontwerp.
- **LTE-antenne**: plak-flexantenne via U.FL J8 (bv. Taoglas FXUB63); GNSS-antenne via U.FL J11.
- **Montagegat H6** ligt in de LoRa-antennezone en is aan GND gehangen (kort spoor langs de zonerand); eventueel los laten na meting.
- **Bouwhoogte** van U.FL-connectoren J8/J11 (+ stekker) controleren in de behuizing.
- Referenties van kleine passieven staan niet op de zeefdruk (wel op de Fab-laag / in het positiebestand).
