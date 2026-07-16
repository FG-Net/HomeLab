# Shelly Pro 3EM in Home Assistant

- ### Shelly Pro 3EM Energiesaldierung

  Der Shelly Pro 3EM stellt einen korrekten Leistungswert bereit, der in Home Assistant direkt verwendet werden kann.

  Die Energiesaldierung berechnet der Shelly Pro 3EM jedoch _phasenweise_ und nicht _phasenübergreifend_ wie ein typischer Haushaltsstromzähler. Dadurch entstehen systembedingt abweichende Import‑ und Exportenergiewerte, die nicht der offiziellen Messlogik des Netzbetreibers entsprechen.

  Um Energiewerte zu erhalten, die dem Haushaltsstromzähler entsprechen, müssen diese in Home Assistant neu berechnet werden. Der Leistungswert bleibt unverändert und dient als korrekte Grundlage für die phasenübergreifende Energieintegration.

- ### Abstraktionssensoren

  Um ein langfristig stabiles Messsystem in Home Assistant zu erhalten, werden Abstraktionssensoren eingesetzt. Sie abstrahieren die Daten eines Smart Meters - unabhängig vom Hersteller - zu einheitlichen Referenzsensoren.

  Dadurch kann ein Smart Meter später problemlos ersetzt werden, ohne dass historische Daten verloren gehen oder das Energie‑Dashboard neu konfiguriert werden muss.

## Übersicht

```mermaid
flowchart TD

    subgraph SHELLY["<b>Shelly Pro 3EM Integrastionssensoren</b>"]
        SHELLY_LEISTUNG["<b>&quot;Leistung&quot;</b><br><code style="background-color:transparent;">sensor.shellypro3em_xyz_leistung</code>"]
        SHELLY_ENERGIE_IMPORT["<b>&quot;Energie&quot;</b><br><code style="background-color:transparent;">sensor.shellypro3em_xyz_energie</code>"]
        SHELLY_ENERGIE_EXPORT["<b>&quot;Energieeinspeisung&quot;</b><br><code style="background-color:transparent;">sensor.shellypro3em_xyz_energieeinspeisung</code>"]
        X["🚫<br>Werte entsprechen nicht dem Haushaltsstromzähler!"]
    end

    subgraph SENSORS["<b>Sensoren</b>"]
        LEISTUNG["Abstraktion<br>Template-Sensor<br><b>&quot;Haus Stromzähler Leistung&quot;</b>"]
        ENERGIE_IMPORT["Abstraktion<br>Template-Sensor<br><b>&quot;Haus Stromzähler Energie Import&quot;</b>"]
        ENERGIE_EXPORT["Abstraktion<br>Template-Sensor<br><b>&quot;Haus Stromzähler Energie Export&quot;</b>"]

        subgraph SHELLY_SENSORS["<b>Shelly Pro 3EM Berechnungssensoren</b>"]
            LEISTUNG_IMPORT["Template-Sensor<br><b>&quot;ShellyPro3EM Leistung Import&quot;</b>"]
            LEISTUNG_EXPORT["Template-Sensor<br><b>&quot;ShellyPro3EM Leistung Export&quot;</b>"]
            ENERGIE_IMPORT_SALDIERT["Integral-Sensor<br><b>&quot;ShellyPro3EM Energie Import (korrekt saldiert)&quot;</b>"]
            ENERGIE_EXPORT_SALDIERT["Integral-Sensor<br><b>&quot;ShellyPro3EM Energie Export (korrekt saldiert)&quot;</b>"]
        end
    end

    subgraph UTILITY_METER["<b>Verbrauchszähler-Sensoren</b>"]
        ENERGIE_IMPORT_PRO_TAG["&quot;Haus Stromzähler Energie Import pro Tag&quot;"]
        ENERGIE_EXPORT_PRO_TAG["&quot;Haus Stromzähler Energie Export pro Tag&quot;"]
    end

    subgraph DASHBOARD["<b>Energie-Dashboard</b>"]
        DASHBOARD_LEISTUNG["Leistungsmessung"]
        DASHBOARD_ENERGIE_IMPORT["Aus dem Netz<br>bezogene Energie"]
        DASHBOARD_ENERGIE_EXPORT["In das Netz<br>eingespeiste Energie"]
    end

    SHELLY_LEISTUNG ---> LEISTUNG
    SHELLY_ENERGIE_IMPORT -.- X
    SHELLY_ENERGIE_EXPORT -.- X
    LEISTUNG --> LEISTUNG_IMPORT
    LEISTUNG --> LEISTUNG_EXPORT
    LEISTUNG_IMPORT --> ENERGIE_IMPORT_SALDIERT
    LEISTUNG_EXPORT --> ENERGIE_EXPORT_SALDIERT
    ENERGIE_IMPORT_SALDIERT --> ENERGIE_IMPORT
    ENERGIE_EXPORT_SALDIERT --> ENERGIE_EXPORT
    LEISTUNG --> DASHBOARD_LEISTUNG
    ENERGIE_IMPORT --> DASHBOARD_ENERGIE_IMPORT
    ENERGIE_EXPORT --> DASHBOARD_ENERGIE_EXPORT
    ENERGIE_IMPORT --> ENERGIE_IMPORT_PRO_TAG
    ENERGIE_EXPORT --> ENERGIE_EXPORT_PRO_TAG

    linkStyle 12 stroke-width:2px,stroke-dasharray: 5 5;
    linkStyle 13 stroke-width:2px,stroke-dasharray: 5 5;

    classDef optional stroke-width:2px,stroke-dasharray: 5 5;
    class UTILITY_METER,ENERGIE_IMPORT_PRO_TAG,ENERGIE_EXPORT_PRO_TAG optional;

    classDef hw_sensors fill:#E1F5FE;
    class SHELLY_LEISTUNG,SHELLY_ENERGIE_IMPORT,SHELLY_ENERGIE_EXPORT hw_sensors;

    classDef helper_sensors fill:#FFF3CD;
    class LEISTUNG_IMPORT,LEISTUNG_EXPORT,ENERGIE_IMPORT_SALDIERT,ENERGIE_EXPORT_SALDIERT helper_sensors;

    classDef dashboard fill:transparent;
    class DASHBOARD_LEISTUNG,DASHBOARD_ENERGIE_IMPORT,DASHBOARD_ENERGIE_EXPORT,ENERGIE_IMPORT_PRO_TAG,ENERGIE_EXPORT_PRO_TAG dashboard;

    style X fill:none,stroke:#f00,color:#f00;
```

---

## Sensoren erstellen

- ### "Haus Stromzähler Leistung"

  Abstraktion des Sensors für Leistung als eine zentrale Referenz für alle weiteren Verwendungen.

  ➡️ **Einstellungen → Geräte & Dienste → Helfer → Helfer erstellen → Template → Sensor**

  <sub>❗ Verwende statt `sensor.shellypro3em_xyz_leistung` den korrekten Sensornamen.</sub>

  |Attribut|Wert|
  |---|---|
  |Name|`Haus Stromzähler Leistung`|
  |Zustand|`{{ states('sensor.shellypro3em_xyz_leistung') \| float(0) }}`|
  |Maßeinheit|`W`|
  |Geräteklasse|`Leistung`|
  |Zustandsklasse|`Messwert`|

  ✔️ **Ergebnis**: `sensor.haus_stromzaehler_leistung`

- ### Shelly Pro 3EM Berechnungssensoren

  - ### "ShellyPro3EM Leistung Import"
  
    Berechnung der bezogenen Leistung.
  
    ➡️ **Einstellungen → Geräte & Dienste → Helfer → Helfer erstellen → Template → Sensor**
  
    |Attribut|Wert|
    |---|---|
    |Name|`ShellyPro3EM Leistung Import`|
    |Zustand|`{% set p = states('sensor.haus_stromzaehler_leistung') \| float(0) %}`<br>`{{ [p, 0] \| max }}`|
    |Maßeinheit|`W`|
    |Geräteklasse|`Leistung`|
    |Zustandsklasse|`Messwert`|
  
    ✔️ **Ergebnis**: `sensor.shellypro3em_leistung_import`
  
  - ### "ShellyPro3EM Leistung Export"
  
    Berechnung der eingespeisten Leistung.
  
     ➡️ **Einstellungen → Geräte & Dienste → Helfer → Helfer erstellen → Template → Sensor**
  
    |Attribut|Wert|
    |---|---|
    |Name|`ShellyPro3EM Leistung Export`|
    |Zustand|`{% set p = states('sensor.haus_stromzaehler_leistung') \| float(0) %}`<br>`{{ [-p, 0] \| max }}`|
    |Maßeinheit|`W`|
    |Geräteklasse|`Leistung`|
    |Zustandsklasse|`Messwert`|
  
    ✔️ **Ergebnis**: `sensor.shellypro3em_leistung_export`

  - ### "ShellyPro3EM Energie Import (korrekt saldiert)"

    Berechnung der bezogenen Energie.

    ➡️ **Einstellungen → Geräte & Dienste → Helfer → Helfer erstellen → Integralsensor**

    |Attribut|Wert|
    |---|---|
    |Name|`ShellyPro3EM Leistung Import (korrekt saldiert)`|
    |Metrisches Präfix|`k (kilo)`|
    |Zeiteinheit |`Stunden`|
    |Eingangssensor|`sensor.shellypro3em_leistung_import`|
    |Integrationsmethode|`Linke Riemannsche Summe`|
    |Genauigkeit|`3`|

    ✔️ **Ergebnis**: `sensor.shellypro3em_energie_import_korrekt_saldiert`

  - ### "ShellyPro3EM Energie Export (korrekt saldiert)"

    Berechnung der eingespeisten Energie.

    ➡️ **Einstellungen → Geräte & Dienste → Helfer → Helfer erstellen → Integralsensor**

    |Attribut|Wert|
    |---|---|
    |Name|`ShellyPro3EM Leistung Export (korrekt saldiert)`|
    |Metrisches Präfix|`k (kilo)`|
    |Zeiteinheit |`Stunden`|
    |Eingangssensor|`sensor.shellypro3em_leistung_export`|
    |Integrationsmethode|`Linke Riemannsche Summe`|
    |Genauigkeit|`3`|
  
    ✔️ **Ergebnis**: `sensor.shellypro3em_energie_export_korrekt_saldiert`

- ### "Haus Stromzähler Energie Import"

  Abstraktion des Sensors für bezogene Energie als eine zentrale Referenz für alle weiteren Verwendungen.

  ➡️ **Einstellungen → Geräte & Dienste → Helfer → Helfer erstellen → Template → Sensor**

  |Attribut|Wert|
  |---|---|
  |Name|`Haus Stromzähler Energie Import`|
  |Zustand|`{{ states('sensor.shellypro3em_energie_import_korrekt_saldiert') \| float(0) }}`|
  |Maßeinheit|`kWh`|
  |Geräteklasse|`Energie`|
  |Zustandsklasse|`Steigender Summenwert`|

  ✔️ **Ergebnis**: `sensor.haus_stromzaehler_energie_import`

- ### "Haus Stromzähler Energie Export"

  Abstraktion des Sensors für eingespeiste Energie als eine zentrale Referenz für alle weiteren Verwendungen.

  ➡️ **Einstellungen → Geräte & Dienste → Helfer → Helfer erstellen → Template → Sensor**

  |Attribut|Wert|
  |---|---|
  |Name|`Haus Stromzähler Energie Export`|
  |Zustand|`{{ states('sensor.shellypro3em_energie_export_korrekt_saldiert') \| float(0) }}`|
  |Maßeinheit|`kWh`|
  |Geräteklasse|`Energie`|
  |Zustandsklasse|`Steigender Summenwert`|

  ✔️ **Ergebnis**: `sensor.haus_stromzaehler_energie_export`
