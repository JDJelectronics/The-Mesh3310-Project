# Mesh3310 rev. G – variant U.FL voor LoRa

Hetzelfde bord als rev. G (dezelfde gerbers uit `productie/gerber/`), alleen anders bestückt:

| Ref | Standaard | Deze variant |
|---|---|---|
| RF4 (U.FL, C22467595) | niet plaatsen | **plaatsen** |
| L19 (8,2 nH, serie naar chipantenne) | plaatsen | **niet plaatsen** |
| C87 (3,6 pF, shunt bij U.FL) | plaatsen | **niet plaatsen** |

Zonder L19 is de chipantenne ANT1 los en gaat LoRa alleen naar RF4. C87 gaat mee
weg omdat 3,6 pF aan de U.FL-kant een fout van ongeveer 1 dB geeft op 50 Ω.
C88 en ANT1 mogen blijven zitten; ze hangen dan los en doen niets.

Gebruik: een externe 868 MHz-antenne met U.FL-kabel. Zo werkt LoRa zeker, ook als
de chipantenne nog niet is afgestemd. Terug naar de chipantenne: L19 en C87
erop solderen (0201) en de U.FL-kabel eraf.

Bestanden voor JLC:
- `Mesh3310-UFL-stuklijst.csv`
- `Mesh3310-UFL-positie.csv`

Controleer in de JLC-preview of RF4 dezelfde kant op staat als RF3 (hetzelfde onderdeel).
