```json
{
  "board_name": "AmpelKreuzungLernplatine",
  "one_liner": "Eine didaktische Straßenkreuzungs-Lernplatine mit 20 THT-LEDs und integrierter Straßen-Grafik, wahlweise steuerbar per STM32 Bluepill oder Raspberry Pi.",
  "market_gap": "Es gibt kaum günstige, ansprechende Hardware-Simulatoren für Ampelsteuerungen, die ohne Kabelsalat direkt STM32-C++ und Raspberry-Pi-Python-Lehrinhalte auf einer optisch ansprechenden Straßenkarte vereinen.",
  "confidence": "high",
  "price_eur": 19,
  "target_enclosure": "100x80mm Eurocard-Halterung / Tischaufsteller / Acryl-Sandwich",
  "injection_notes": "keine"
}
```

## BUILD-PROMPT

### 1. Hard DFM-Vorgaben & Stackup
- **Lagen:** 4-Layer-Stackup (`F.Cu` = Signale/Bauteile, `In1.Cu` = GND-Plane, `In2.Cu` = VCC-Plane/Signale, `B.Cu` = Signale/SMD-Widerstände).
- **Leiterbahnen/Abstände:** Signalleitungen ≥ 0.4 mm, Abstände (Clearance) ≥ 0.3 mm.
- **Dichte:** 20 THT-LEDs auf der Oberseite, 20 SMD-Widerstände auf der Unterseite (0805, leicht handlötbar), 1× Raspberry Pi GPIO-Header, 1× Bluepill-Sockel. Extrem entflochtenes, einfaches Routing.

### 2. Mechanik & Geometrie
- **Board-Größe:** 100.0 mm × 80.0 mm (Rechteckig).
- **Koordinaten-Ursprung (0,0):** Unten links.
- **Befestigung:** 4× M3-Bohrungen (Durchmesser 3.2 mm), platziert an den Ecken:
  - Loch 1: (5.0, 5.0) mm
  - Loch 2: (95.0, 5.0) mm
  - Loch 3: (95.0, 75.0) mm
  - Loch 4: (5.0, 75.0) mm
- **Keepout:** 6.0 mm kreisrunde Sperrzone (Sperre für Leiterbahnen und Bauteile) um jedes der vier Befestigungslöcher.

### 3. Bestückung & Platzierung
- **Oberseite (F.Cu) Komponenten:**
  - **U1 (STM32 Bluepill Sockel):** Zwei einreihige Buchsenleisten `PinSocket_1x20_P2.54mm_Vertical` im Abstand von 0.9″ (22.86 mm). Vertikal platziert auf der linken Boardseite:
    - Reihe 1 (Pins 1–20): x = 12.0 mm, y = 15.0 mm bis 63.26 mm.
    - Reihe 2 (Pins 21–40): x = 34.86 mm, y = 15.0 mm bis 63.26 mm.
  - **J1 (Raspberry Pi GPIO Header):** 2x20-poliger Stiftleisten-Stecker `PinHeader_2x20_P2.54mm_Vertical`. Vertikal platziert auf der rechten Boardseite:
    - x = 85.0 mm, y = 15.0 mm bis 63.26 mm.
  - **LEDs (20× THT 5mm `LED_THT:LED_D5.0mm`):** Platziert im Zentrum (x = 38.0 mm bis 72.0 mm) an den Ecken einer gedachten Straßenkreuzung:
    - **Ampel Nord (NS-Achse, parallel geschaltet):** `D1` (Rot), `D2` (Gelb), `D3` (Grün) platziert bei (62.0, 26.0) mm.
    - **Ampel Süd (NS-Achse, parallel geschaltet):** `D4` (Rot), `D5` (Gelb), `D6` (Grün) platziert bei (38.0, 54.0) mm.
    - **Ampel Ost (EW-Achse, parallel geschaltet):** `D7` (Rot), `D8` (Gelb), `D9` (Grün) platziert bei (38.0, 26.0) mm.
    - **Ampel West (EW-Achse, parallel geschaltet):** `D10` (Rot), `D11` (Gelb), `D12` (Grün) platziert bei (62.0, 54.0) mm.
    - **Fußgänger Nord (NS-Achse):** `D13` (Rot), `D14` (Grün) bei (38.0, 58.0) mm.
    - **Fußgänger Süd (NS-Achse):** `D15` (Rot), `D16` (Grün) bei (62.0, 22.0) mm.
    - **Fußgänger Ost (EW-Achse):** `D17` (Rot), `D18` (Grün) bei (34.0, 26.0) mm.
    - **Fußgänger West (EW-Achse):** `D19` (Rot), `D20` (Grün) bei (66.0, 54.0) mm.
- **Unterseite (B.Cu) Komponenten:**
  - **R1 bis R20 (20× Vorwiderstände 330 Ohm, `Resistor_SMD:R_0805_2012Metric`):** Jeweils direkt unter/neben den Lötpads der zugehörigen LED auf der Unterseite platziert.

### 4. Schaltplan & Netze (Verdrahtungs-Schema)
- Alle LED-Kathoden (-) führen über ihren jeweiligen Vorwiderstand (`R1` bis `R20`) direkt an die durchgehende Masse-Ebene (`GND`).
- Die LED-Anoden (+) sind parallel geschaltet und werden von beiden Host-Schnittstellen (STM32 Bluepill und Raspberry Pi GPIO) angesteuert:
  1. **Netz `NS_RED`** (Verbindet Anoden von `D1` & `D4`): Bluepill Pin `PA0` und Raspberry Pi GPIO Pin 3 (`GPIO2`).
  2. **Netz `NS_YEL`** (Verbindet Anoden von `D2` & `D5`): Bluepill Pin `PA1` und Raspberry Pi GPIO Pin 5 (`GPIO3`).
  3. **Netz `NS_GRN`** (Verbindet Anoden von `D3` & `D6`): Bluepill Pin `PA2` und Raspberry Pi GPIO Pin 7 (`GPIO4`).
  4. **Netz `EW_RED`** (Verbindet Anoden von `D7` & `D10`): Bluepill Pin `PA3` und Raspberry Pi GPIO Pin 11 (`GPIO17`).
  5. **Netz `EW_YEL`** (Verbindet Anoden von `D8` & `D11`): Bluepill Pin `PA4` und Raspberry Pi GPIO Pin 13 (`GPIO27`).
  6. **Netz `EW_GRN`** (Verbindet Anoden von `D9` & `D12`): Bluepill Pin `PA5` und Raspberry Pi GPIO Pin 15 (`GPIO22`).
  7. **Netz `NS_PED_RED`** (Verbindet Anoden von `D13` & `D15`): Bluepill Pin `PA6` und Raspberry Pi GPIO Pin 19 (`GPIO10`).
  8. **Netz `NS_PED_GRN`** (Verbindet Anoden von `D14` & `D16`): Bluepill Pin `PA7` und Raspberry Pi GPIO Pin 21 (`GPIO9`).
  9. **Netz `EW_PED_RED`** (Verbindet Anoden von `D17` & `D19`): Bluepill Pin `PB0` und Raspberry Pi GPIO Pin 23 (`GPIO11`).
  10. **Netz `EW_PED_GRN`** (Verbindet Anoden von `D18` & `D20`): Bluepill Pin `PB1` und Raspberry Pi GPIO Pin 29 (`GPIO5`).
- **GND-Verbindung:** Verbinde die GND-Pins der Bluepill (z. B. Pin 19 / 20) und den GND-Pin des Raspberry Pi (Pin 39) mit der internen Ground-Plane (`In1.Cu`).

### 5. Grafischer Bestückungsdruck (Silkscreen F.SilkS)
- **Straßen-Layout:** Zeichne im Bestückungsdruck zwei sich kreuzende Straßen (Nord-Süd von x = 40 mm bis 60 mm; Ost-West von y = 30 mm bis 50 mm) mit gestrichelten Mittellinien.
- Zeichne 4 Zebrastreifen (Zebra-Markierungen) an den vier Fußgängerübergängen.
- Markiere jede Ampelgruppe mit einer weißen Box und klarer Beschriftung (z.B. „NS-AUTO“, „EW-AUTO“, „NS-PED“, „EW-PED“).

### 6. Workflow-Aufgaben für den Bau-Agenten
1. **Projekt initialisieren:** KiCad-Projekt in einem neuen Arbeitsverzeichnis anlegen.
2. **Schaltplan erstellen:** Schaltplan-Symbole platzieren, Netze wie oben definiert verbinden, ERC (Electrical Rules Check) fehlerfrei ausführen.
3. **Platinen-Layout definieren:** Edge-Cuts auf 100x80 mm setzen, M3-Löcher platzieren.
4. **Platzierung:** Bluepill-Sockel links, Pi-Header rechts und die THT-LEDs exakt an den Straßenkreuzungs-Koordinaten platzieren. Widerstände auf die Rückseite spiegeln.
5. **Kupferflächen:** Durchgehende GND-Plane auf `In1.Cu` erzeugen.
6. **Autorouting:** Exportiere das Board als `.dsn`, starte Freerouting mit den eingestellten DFM-Regeln (Min 0.3 mm Clearance), importiere das fertige Session-File `.ses` zurück in KiCad.
7. **Verifikation & Export:** Führe den DRC (Design Rules Check) fehlerfrei aus. Exportiere sämtliche Gerber-Dateien und Bohrdaten (drill files) in den Ordner `./gerbers`.
8. **Zusammenfassung:** Erstelle am Ende einen ehrlichen Bericht, was geklappt hat und wo Optimierungen vorgenommen wurden.
