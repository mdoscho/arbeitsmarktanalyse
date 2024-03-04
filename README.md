# Analyse des Arbeitsmarkts nach verschiedenen Kriterien

![Static Badge](https://img.shields.io/badge/Power_BI_|_Python-green)
![Static Badge](https://img.shields.io/badge/release-0.1.0-important)

## Inhaltsverzeichnis

1. [Wichtige Informationen](#wichtige-informationen)
2. [Daten](#daten)
    1. [Karten](#karten)
    2. [DEKRA Akademie Standorte](#dekra-akademie-standorte)
    3. [Agentur für Arbeit](#agentur-für-arbeit)
3. [Power BI](#power-bi)
4. [FAQ](#faq)

## Beschreibung

In kürze...

## Wichtige Informationen

1. Die Standard *Filled map* von Power BI scheint Probleme bei der Räpresentation von Landkreisen
und kreisfreien Städten mit identischen Namen, wie z. B. Kassel, zu haben.
2. Es ist zurzeit **nicht** möglich eine
[Filled map](https://learn.microsoft.com/de-de/power-bi/visuals/power-bi-visualization-filled-maps-choropleths?tabs=powerbi-desktop)
in Power BI mit Punkten (Breiten- und Längengrad) zu kombinieren.

> 💡 Die Verwendung der kostenlose Visualisierung [Icon Map](https://icon-map.com/) für Microsoft
Power BI Desktop schafft in beiden Fällen abhilfe. Siehe Kapitel [Power BI](#power-bi).

## Daten

### Karten

Flächen werden in Icon Map mithilfe von [GeoJSON](https://en.wikipedia.org/wiki/GeoJSON)-Dateien
abgebildet.

> 📖 Weiterführende Informationen zum GeoJSON-Format:
>
> - Dougherty, J. & Ilyankou, I. (2024). Hands-On Data Visualization: Interactive Storytelling from
Spreadsheets to Code (Kapitel [Geospatial Data and GeoJSON](https://handsondataviz.org/geojson.html)). O'Reilly
> - GeoJSON-Specifications [RFC7946](https://datatracker.ietf.org/doc/html/rfc7946)

> ❗ Es ist **erforderlich** GeoJSON-Dateien über eine URL aufzurufen die mit https:// beginnt.
> CORS muss aktiviert sein. Siehe dazu [Options for hosting GeoJSON files](https://icon-map.com/blogs/blogs.html#hostinggeojson).

Quelle der Karten sind die kostenfreien Geodaten des Bundesamtes für Kartographie und Geodäsie,
insbesondere die [Verwaltungsgebiete 1:5 000 000](https://gdz.bkg.bund.de/index.php/default/open-data/verwaltungsgebiete-1-5-000-000-stand-01-01-vg5000-01-01.html).

Um den Datesatz herunterzuladen:

1. drücke auf Direktdownload
2. wähle eine der Shape-Formate aus mit [Georeferenzierung](https://gdz.bkg.bund.de/index.php/default/georeferenzierungen/) GK3, TM32 oder UTM32s

Extrahiere die Dateien.

> ❗ Notwendige Informationen sind in der entsprechenden Dokumentation enthalten. Zu finden im Verzeichnis *dokumentation/vg500.pdf*.

#### Konvertierung der Shape-Datei mit Mapshaper

Die einfachste Form der Konvertierung von Shape-Dateien in GeoJSON erfolgt mithilfe der Webseite [Mapshaper](https://mapshaper.org/).

1. Klicke auf **select**
2. Wähle die Dateien:
   - *VG5000_KRS.cpg*
   - *VG5000_KRS.dbf*
   - *VG5000_KRS.prj*
   - *VG5000_KRS.shp*
   - *VG5000_KRS.shx*

   aus (KRS = Kreisgrenze, siehe Dokumentation)
3. Drücke auf **Import**  

   Damit Power BI die Karte korrekt darstellt muss die World Geodetic System (WGS84)-Projektion eingestellt sein (Georeferenzierung UTM32w).

1. Klicke auf *Console*
2. Eingabe des Befehls `info` zeigt, unter anderem, folgende Informationen an:

    ```
        Type:     polygon
        Records:  400
        Bounds:   280353.08707179833,5235877.555857686,921261.5513513577,6101301.5766873015
        CRS:      +proj=utm +zone=32 +ellps=GRS80
        Source:   VG5000_KRS.shp
    ```

    Die Projektion wird unter CRS als GRS80 angezeigt.

3. Eingabe des Befehls `proj wgs84` ändert die Projektion.

    ```
    $ info
    [info] 
    ====================================
    Layer:    VG5000_KRS
    ------------------------------------
    Type:     polygon
    Records:  400
    Bounds:   5.8659987920803145,47.27036232637566,15.041531420423851,55.05706033463062
    CRS:      +proj=longlat +datum=WGS84
    Source:   VG5000_KRS.shp
    ```

4. Klicke auf *Export*
5. Wähle die Formatoption GeoJSON aus
6. Klicke auf die Schaltfläche *Export* und vergebe den Namen *landkreise_kreisfreie_staedte*

Wiederhole die Schritte 1 bis einschließlich 6 für die Dateien *VG5000_LAN* (LAN = Länder). Vergebe den Namen *bundeslaender*.

> 💡 Auswahl bestimmter Flächen, wie z. B. aller Landkreise in Hessen, ist möglich mithilfe des *select
features* von Mapshaper. Klicke dazu auf den Pfeil unter dem Minus-Zeichen (links).

### DEKRA Akademie Standorte

1. Daten stammen aus der DEKRA Akademie Homepage.
2. Breiten- und Längengrad (Latidude und Longidude) mithilfe von [LanLong.net](https://www.latlong.net/).

> ❗ TO-DO: In Zukunft mit Python, s. [StackOverflow](https://stackoverflow.com/questions/25888396/how-to-get-latitude-longitude-with-python#25890585)

> ⚠️ Breiten- und Längengrad sind Dezimalzahlen mit entsprechenden Dezimaltrennzeichen einen Punkt (.) und **nicht** wie in Deutschland üblich einem Komma.

### Agentur für Arbeit

Arbeitsmarktreport - Länder, Kreise, Regionaldirektionen und Agenturen für Arbeit

[1](https://statistik.arbeitsagentur.de/DE/Navigation/Statistiken/Statistiken-nach-Regionen/Statistiken-nach-Regionen-Nav.html)
[2](https://statistik.arbeitsagentur.de/SiteGlobals/Forms/Suche/Einzelheftsuche_Formular.html?nn=15024&topic_f=amr-amr&dateOfRevision=202201-202401)

TO-DO: Automatisierte Extraktion mit Python.

### Statistisches Bundesamt

[Kreisfreie Städte und Landkreise am 31.12.2022](https://www.destatis.de/DE/Themen/Laender-Regionen/Regionales/Gemeindeverzeichnis/Administrativ/04-kreise.html)
[Regionaldatenbank Deutschland](https://www.regionalstatistik.de/genesis/online?operation=previous&levelindex=0&step=0&titel=&levelid=1708952700718&acceptscookies=false)

## Power BI

Icon Map kann entweder:

- direkt aus AppSource [importiert](https://learn.microsoft.com/de-de/power-bi/developer/visuals/import-visual#import-a-power-bi-visual-directly-from-appsource) werden, oder
- die neueste -test- Version der App in Power BI Desktop [installiert](https://learn.microsoft.com/de-de/power-bi/developer/visuals/import-visual#import-a-visual-file-from-your-local-computer-into-power-bi) werden.

Importiere die GeoJSON-Dateien *bundeslaender* und *landkreise_kreisfreie_staedte*. Im Home Menü unter
Get Data > More > JSON.

Verwende die nachfolgenden Power Query M "Formeln":

```m
let
    Source = Json.Document(File.Contents("\path\to\bundeslaender.json")),
    #"Converted to Table" = Table.FromRecords({Source}),
    #"Expanded features" = Table.ExpandListColumn(#"Converted to Table", "features"),
    #"Expanded features1" = Table.ExpandRecordColumn(#"Expanded features", "features", {"type", "geometry", "properties"}, {"features.type", "features.geometry", "features.properties"}),
    #"Expanded features.geometry" = Table.ExpandRecordColumn(#"Expanded features1", "features.geometry", {"type", "coordinates"}, {"features.geometry.type", "features.geometry.coordinates"}),
    #"Expanded features.geometry.coordinates" = Table.ExpandListColumn(#"Expanded features.geometry", "features.geometry.coordinates"),
    #"Expanded features.properties" = Table.ExpandRecordColumn(#"Expanded features.geometry.coordinates", "features.properties", {"OBJID", "BEGINN", "ADE", "GF", "BSG", "ARS", "AGS", "SDV_ARS", "GEN", "BEZ", "IBZ", "BEM", "NBD", "SN_L", "SN_R", "SN_K", "SN_V1", "SN_V2", "SN_G", "FK_S3", "NUTS", "ARS_0", "AGS_0", "WSK"}, {"features.properties.OBJID", "features.properties.BEGINN", "features.properties.ADE", "features.properties.GF", "features.properties.BSG", "features.properties.ARS", "features.properties.AGS", "features.properties.SDV_ARS", "features.properties.GEN", "features.properties.BEZ", "features.properties.IBZ", "features.properties.BEM", "features.properties.NBD", "features.properties.SN_L", "features.properties.SN_R", "features.properties.SN_K", "features.properties.SN_V1", "features.properties.SN_V2", "features.properties.SN_G", "features.properties.FK_S3", "features.properties.NUTS", "features.properties.ARS_0", "features.properties.AGS_0", "features.properties.WSK"}),
    #"Changed Type" = Table.TransformColumnTypes(#"Expanded features.properties",{{"type", type text}, {"features.type", type text}, {"features.geometry.type", type text}, {"features.geometry.coordinates", type any}, {"features.properties.OBJID", type text}, {"features.properties.BEGINN", type datetime}, {"features.properties.ADE", Int64.Type}, {"features.properties.GF", Int64.Type}, {"features.properties.BSG", Int64.Type}, {"features.properties.ARS", type text}, {"features.properties.AGS", Int64.Type}, {"features.properties.SDV_ARS", Int64.Type}, {"features.properties.GEN", type text}, {"features.properties.BEZ", type text}, {"features.properties.IBZ", Int64.Type}, {"features.properties.BEM", type text}, {"features.properties.NBD", type text}, {"features.properties.SN_L", Int64.Type}, {"features.properties.SN_R", Int64.Type}, {"features.properties.SN_K", Int64.Type}, {"features.properties.SN_V1", Int64.Type}, {"features.properties.SN_V2", Int64.Type}, {"features.properties.SN_G", Int64.Type}, {"features.properties.FK_S3", Int64.Type}, {"features.properties.NUTS", type text}, {"features.properties.ARS_0", Int64.Type}, {"features.properties.AGS_0", Int64.Type}, {"features.properties.WSK", type datetime}}),
    #"Removed Columns" = Table.RemoveColumns(#"Changed Type",{"type", "features.type", "features.geometry.type", "features.geometry.coordinates", "features.properties.OBJID", "features.properties.BEGINN", "features.properties.ADE", "features.properties.GF", "features.properties.BSG", "features.properties.AGS", "features.properties.SDV_ARS", "features.properties.IBZ", "features.properties.BEM", "features.properties.NBD", "features.properties.SN_L", "features.properties.SN_R", "features.properties.SN_K", "features.properties.SN_V1", "features.properties.SN_V2", "features.properties.SN_G", "features.properties.FK_S3", "features.properties.NUTS", "features.properties.ARS_0", "features.properties.AGS_0", "features.properties.WSK"}),
    #"Removed Duplicates" = Table.Distinct(#"Removed Columns", {"features.properties.ARS"}),
    #"Renamed Columns" = Table.RenameColumns(#"Removed Duplicates",{{"features.properties.ARS", "BundeslandID"}, {"features.properties.GEN", "Name"}, {"features.properties.BEZ", "Bezeichnung"}})
in
    #"Renamed Columns"
```

```m
let
    Source = Json.Document(File.Contents("\path\to\landkreise_kreisfreie_staedte.json")),
    #"Converted to Table" = Table.FromRecords({Source}),
    #"Expanded features" = Table.ExpandListColumn(#"Converted to Table", "features"),
    #"Expanded features1" = Table.ExpandRecordColumn(#"Expanded features", "features", {"type", "geometry", "properties"}, {"features.type", "features.geometry", "features.properties"}),
    #"Expanded features.geometry" = Table.ExpandRecordColumn(#"Expanded features1", "features.geometry", {"type", "coordinates"}, {"features.geometry.type", "features.geometry.coordinates"}),
    #"Expanded features.geometry.coordinates" = Table.ExpandListColumn(#"Expanded features.geometry", "features.geometry.coordinates"),
    #"Expanded features.properties" = Table.ExpandRecordColumn(#"Expanded features.geometry.coordinates", "features.properties", {"OBJID", "BEGINN", "ADE", "GF", "BSG", "ARS", "AGS", "SDV_ARS", "GEN", "BEZ", "IBZ", "BEM", "NBD", "SN_L", "SN_R", "SN_K", "SN_V1", "SN_V2", "SN_G", "FK_S3", "NUTS", "ARS_0", "AGS_0", "WSK"}, {"features.properties.OBJID", "features.properties.BEGINN", "features.properties.ADE", "features.properties.GF", "features.properties.BSG", "features.properties.ARS", "features.properties.AGS", "features.properties.SDV_ARS", "features.properties.GEN", "features.properties.BEZ", "features.properties.IBZ", "features.properties.BEM", "features.properties.NBD", "features.properties.SN_L", "features.properties.SN_R", "features.properties.SN_K", "features.properties.SN_V1", "features.properties.SN_V2", "features.properties.SN_G", "features.properties.FK_S3", "features.properties.NUTS", "features.properties.ARS_0", "features.properties.AGS_0", "features.properties.WSK"}),
    #"Changed Type" = Table.TransformColumnTypes(#"Expanded features.properties",{{"type", type text}, {"features.type", type text}, {"features.geometry.type", type text}, {"features.geometry.coordinates", type any}, {"features.properties.OBJID", type text}, {"features.properties.BEGINN", type datetime}, {"features.properties.ADE", Int64.Type}, {"features.properties.GF", Int64.Type}, {"features.properties.BSG", Int64.Type}, {"features.properties.ARS", type text}, {"features.properties.AGS", Int64.Type}, {"features.properties.SDV_ARS", Int64.Type}, {"features.properties.GEN", type text}, {"features.properties.BEZ", type text}, {"features.properties.IBZ", Int64.Type}, {"features.properties.BEM", type text}, {"features.properties.NBD", type text}, {"features.properties.SN_L", Int64.Type}, {"features.properties.SN_R", Int64.Type}, {"features.properties.SN_K", Int64.Type}, {"features.properties.SN_V1", Int64.Type}, {"features.properties.SN_V2", Int64.Type}, {"features.properties.SN_G", Int64.Type}, {"features.properties.FK_S3", type text}, {"features.properties.NUTS", type text}, {"features.properties.ARS_0", Int64.Type}, {"features.properties.AGS_0", Int64.Type}, {"features.properties.WSK", type datetime}}),
    #"Removed Columns" = Table.RemoveColumns(#"Changed Type",{"type", "features.type", "features.geometry.type", "features.geometry.coordinates", "features.properties.OBJID", "features.properties.BEGINN", "features.properties.ADE", "features.properties.GF", "features.properties.BSG", "features.properties.AGS", "features.properties.SDV_ARS", "features.properties.IBZ", "features.properties.BEM", "features.properties.NBD", "features.properties.SN_L", "features.properties.SN_R", "features.properties.SN_K", "features.properties.SN_V1", "features.properties.SN_V2", "features.properties.SN_G", "features.properties.FK_S3", "features.properties.NUTS", "features.properties.ARS_0", "features.properties.AGS_0", "features.properties.WSK"}),
    #"Removed Duplicates" = Table.Distinct(#"Removed Columns", {"features.properties.ARS"}),
    #"Renamed Columns" = Table.RenameColumns(#"Removed Duplicates",{{"features.properties.ARS", "KreisID"}, {"features.properties.GEN", "Name"}, {"features.properties.BEZ", "Bezeichnung"}})
    #"Added Custom" = Table.AddColumn(#"Renamed Columns", "BundeslandID", each Text.Range([KreisID], 0, 2))
in
    #"Added Custom"

Der amtliche Regionalschlüssel (ARS) wird zum verlinken der Tabellen verwendet. Siehe Kapitel 2.1 der Dokumentation.

In *Table View* die **Data Category** der Spalte *Name* zu *State or Province* und *County* entsprechend ändern.

## FAQ