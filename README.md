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

> ⚠️ Es ist **erforderlich** GeoJSON-Dateien über eine URL aufzurufen die mit https:// beginnt.
> [CORS](https://developer.mozilla.org/en-US/docs/Glossary/CORS) muss aktiviert sein.
> Siehe dazu [Options for hosting GeoJSON files](https://icon-map.com/blogs/blogs.html#hostinggeojson).

Quellenvermerk:
1. Geometrische Grundlage: [Verwaltungsgebiete 1:5 000 000](https://gdz.bkg.bund.de/index.php/default/open-data/verwaltungsgebiete-1-5-000-000-stand-01-01-vg5000-01-01.html), 
01.01.2023 © GeoBasis-DE/Bundesamt für Kartographie und Geodäsie
2. Lizenz: [Datenlizenz Deutschland – Namensnennung – Version 2.0](www.govdata.de/dl-de/by-2-0)

Um den Datesatz herunterzuladen:

1. drücke auf Direktdownload
2. wähle eine der Shape-Formate aus mit [Georeferenzierung](https://gdz.bkg.bund.de/index.php/default/georeferenzierungen/) GK3, TM32 oder UTM32s

Extrahiere die Dateien.

> ⚠️ Notwendige Informationen sind in der entsprechenden Dokumentation enthalten. Zu finden im Verzeichnis *./dokumentation/vg500.pdf*.

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

> ⚠️ Bestimmte Landkreise und kreisfreie Städte haben denselben Namen, z. B. Kassel.
> Dies **muss** in der GeoJSON-Datei geändert werden damit die entsprechenden Flächen auf der Karte aktiviert werden können.
> Zum Beispiel 'Kassel' (kreisfreie Stadt) wird durch 'Kassel, Stadt' ersetzt. Um die Anwendung zu vereinfachen, werden die Namen
> aller kreisfreien Städte mit der Zeichenkette ', Stadt' ergänzt.

### DEKRA Akademie Standorte

1. Daten stammen aus der DEKRA Akademie Homepage.
2. Breiten- und Längengrad (Latidude und Longidude) mithilfe von [LanLong.net](https://www.latlong.net/).

> 📝 TO-DO: In Zukunft mit Python, s. [StackOverflow](https://stackoverflow.com/questions/25888396/how-to-get-latitude-longitude-with-python#25890585)

> ⚠️ Breiten- und Längengrad sind Dezimalzahlen mit entsprechenden Dezimaltrennzeichen einen Punkt (.) und **nicht** wie in Deutschland üblich einem Komma.

### Agentur für Arbeit

Datenquelle: Beschäftigte nach Berufen am Arbeitsort der
[Datenbanken Beschäftigungsstatistik](https://statistik.arbeitsagentur.de/DE/Navigation/Statistiken/Interaktive-Statistiken/Datenbanken/Datenbanken-BST-Nav.html)

Auswahl der Dimensionen und Kennzahlen:

Dimensionen:

- Stichtag
- Bundesland
- Kreis
- Berufssektor
- Berufssegment
- Berufshauptgruppe - 2 Steller
- Anforderungsniveau
- Alter - Jahresgruppen 10er

Kennzahl:

- Sozialversicherungspflichtig Beschäftigte

Wenn in den Dimensionen das Feld "Keine Angabe" oder "Keine Zuordnung möglich" existiert, dieses deaktivieren.
Zum Schluß, die Summe auswählen und den Datensatz als CSV herunterladen.

Den Datensatz anschließend in Power BI importieren und nachfolgende Power Query einbinden:

```m
// let
//        Source = Csv.Document(File.Contents("path\to\data.csv"),[Delimiter=";", Columns=13, Encoding=65001, QuoteStyle=QuoteStyle.None]),
        #"Changed Type" = Table.TransformColumnTypes(Source,{
        {"Column1", type text}, {"Column2", type text}, {"Column3", type text}, {"Column4", type text}, {"Column5", type text}, {"Column6", type text}, 
        {"Column7", type text}, {"Column8", type text}, {"Column9", type text}, {"Column10", type text}, {"Column11", type text}, {"Column12", type text},
        {"Column13", type text}
        }),
    #"Renamed Columns" = Table.RenameColumns(#"Changed Type",{
        {"Column1", "BundeslandID"}, {"Column2", "Bundesland"}, {"Column3", "KreisID"}, {"Column4", "Kreis"}, {"Column5", "BerufssektorID"}, 
        {"Column6", "Berufssektor"}, {"Column7", "BerufssegmentID"}, {"Column8", "Berufssegment"}, {"Column9", "Berufshauptgruppe2erID"}, 
        {"Column10", "Berufshauptgruppe"}, {"Column11", "Anforderungsniveau"}, {"Column12", "Altersgruppe"}, {"Column13", "SozialversicherungspflichtigBeschäftigte"}
        }),
    #"Trimmed Text" = Table.TransformColumns(#"Renamed Columns",{
        {"BundeslandID", Text.Trim, type text}, {"Bundesland", Text.Trim, type text}, {"KreisID", Text.Trim, type text}, {"Kreis", Text.Trim, type text},
        {"BerufssektorID", Text.Trim, type text}, {"Berufssektor", Text.Trim, type text}, {"BerufssegmentID", Text.Trim, type text}, 
        {"Berufssegment", Text.Trim, type text}, {"Berufshauptgruppe2erID", Text.Trim, type text}, {"Berufshauptgruppe", Text.Trim, type text}, 
        {"Anforderungsniveau", Text.Trim, type text}, {"Altersgruppe", Text.Trim, type text}, {"SozialversicherungspflichtigBeschäftigte", Text.Trim, type text}
        }),
    #"Replaced Value" = Table.ReplaceValue(#"Trimmed Text", each [BundeslandID], each if Text.StartsWith([BundeslandID], "Summe:") then "" else [BundeslandID], Replacer.ReplaceValue, {"BundeslandID"}),
    #"Replaced Value1" = Table.ReplaceValue(#"Replaced Value", ".", "", Replacer.ReplaceText, {"SozialversicherungspflichtigBeschäftigte"}),    
    #"Filtered Rows" = Table.SelectRows(#"Replaced Value1", each ([BundeslandID] <> "")),
    #"Removed Bottom Rows" = Table.RemoveLastN(#"Filtered Rows", 2),
    #"Added Custom" = Table.AddColumn(#"Removed Bottom Rows", "Stichtag", each null),
    #"Replaced Value2" = Table.ReplaceValue(#"Added Custom", null, each if [BundeslandID] = "Stichtag" then [Bundesland] else null, Replacer.ReplaceValue, {"Stichtag"}),
    #"Filled Down" = Table.FillDown(#"Replaced Value2",{"Stichtag"}),
    #"Added Custom1" = Table.AddColumn(#"Filled Down", "Gebietsstand", each null),
    #"Replaced Value3" = Table.ReplaceValue(#"Added Custom1", null, each if [BundeslandID] = "Gebietsstand" then [Bundesland] else null, Replacer.ReplaceValue, {"Gebietsstand"}),
    #"Filled Down1" = Table.FillDown(#"Replaced Value3",{"Gebietsstand"}),
    #"Removed Top Rows" = Table.Skip(#"Filled Down1", 12),
    #"Replaced Value4" = Table.ReplaceValue(#"Removed Top Rows", ", Wissenschaftsstadt", ", Stadt", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value5" = Table.ReplaceValue(#"Replaced Value4", ", Landeshauptstadt", ", Stadt", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value6" = Table.ReplaceValue(#"Replaced Value5", "documenta-Stadt", "Stadt", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value7" = Table.ReplaceValue(#"Replaced Value6", ", Universitätsstadt", ", Stadt", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value8" = Table.ReplaceValue(#"Replaced Value7", ", Hansestadt", ", Stadt", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value9" = Table.ReplaceValue(#"Replaced Value8", ", Freie und Hansestadt", ", Stadt", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value10" = Table.ReplaceValue(#"Replaced Value9", ", Stadt der FernUniversität", ", Stadt", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value11" = Table.ReplaceValue(#"Replaced Value10", ", Hanse- und Universitätsstadt", ", Stadt", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value12" = Table.ReplaceValue(#"Replaced Value11", ", kreisfreie Stadt", ", Stadt", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value13" = Table.ReplaceValue(#"Replaced Value12", "Landkreis ", "", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value14" = Table.ReplaceValue(#"Replaced Value13", ", Klingenstadt", ", Stadt", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value15" = Table.ReplaceValue(#"Replaced Value14", " (Oldenburg), Stadt", ", Stadt", Replacer.ReplaceText, {"Kreis"}),
    #"Replaced Value16" = Table.ReplaceValue(#"Replaced Value15", "x", "-1", Replacer.ReplaceText, {"SozialversicherungspflichtigBeschäftigte"}),
    #"Changed Type1" = Table.TransformColumnTypes(#"Replaced Value16",{
        {"SozialversicherungspflichtigBeschäftigte", Int64.Type}
        }) 
in
    #"Changed Type1"
```

Erklärungen zu den im Datensatz der Bundesagentur für Arbeit verwendeten Zeichen sind [hier](https://statistik.arbeitsagentur.de/DE/Statischer-Content/Grundlagen/Definitionen/Zeichenerklaerung.html?nn=6698) zu finden.

Das relevante Glossar [hier](https://statistik.arbeitsagentur.de/DE/Navigation/Grundlagen/Definitionen/Glossar/Glossar-Nav.html).

## Power BI

Icon Map kann entweder:

- direkt aus AppSource [importiert](https://learn.microsoft.com/de-de/power-bi/developer/visuals/import-visual#import-a-power-bi-visual-directly-from-appsource) werden, oder
- die neueste -test- Version der App in Power BI Desktop [installiert](https://learn.microsoft.com/de-de/power-bi/developer/visuals/import-visual#import-a-visual-file-from-your-local-computer-into-power-bi) werden.

## Unicode

[Unicode](https://home.unicode.org) Charaktere werden mithilfe der DAX UNICHAR-Funktion dargestellt. Siehe [Wikipedia](https://en.wikipedia.org/wiki/List_of_Unicode_characters).

| Symbol        |   Beschreibug | Unicode (Dezimal)   |
| ------------- | ------------- | ------------------- |
|   &#8512;     | Summenzeichen | 8512 |

## FAQ
