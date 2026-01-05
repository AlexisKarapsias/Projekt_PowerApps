# Power-Apps-Projektdokumentation
## Ziel und Rahmen

### Ziel:
Umsetzung einer Power Apps Canvas App mit einer einfachen und übersichtlichen Benutzeroberfläche auf Basis bestehender Excel-Daten.

### Vorgaben:

- Nutzung von Power Apps

- Integration aller Werte aus der Excel-Datei

- Einfache Benutzeroberfläche

### Architektur:
Als Datenbankstruktur wurden SharePoint-Listen verwendet.

## Listen

Alexis entschied sich, als Datenbankstruktur einfache SharePoint-Listen zu verwenden und diese logisch aufzuteilen. Zur besseren Orientierung wurde die Struktur zunächst in Lucidchart modelliert, wie im entsprechenden Diagramm dargestellt.

### Datentyp-Entscheidungen:

- Person-Felder vs. Textfelder (z. B. Unummer = User().Email als Text)

- Choice-Felder: JA/NEIN als Choice oder Boolean
→ Die Umsetzung erfolgte aktuell über eine Kontextvariable.

## Namenskonvention

Zur besseren Übersicht wurde eine Namenskonvention eingeführt, die kennzeichnet, welche Frage auf welchem Bildschirm angezeigt wird und auf welcher Seite sie sich befindet.

### Beispiele:

00/1

1/2

1.1/3

1.5/4

2.2/5

1.3/6

1.4/7

Weitere folgen nach demselben Schema.

## Figma

Im nächsten Schritt begann Alexis mit der Modellierung der einzelnen Seiten in Figma. Dabei wurde darauf geachtet, dass die Verbindungen zwischen den Seiten klar ersichtlich sind und das Design möglichst nahe an der späteren Umsetzung in Power Apps liegt.
Einige UI-Elemente wurden erst während der Implementierung in Power Apps ergänzt.

## Power Apps

Nachdem die ersten Seiten in Power Apps erstellt waren, begann Alexis mit der Gestaltung der Startseite. Nach Rücksprache mit dem Auftraggeber wurden zunächst alle Seiten designt und in der geplanten Reihenfolge angeordnet.

Anschließend wurde ein Button erstellt, der zur nächsten Seite navigiert.

### Navigation:

```text
Navigate('1'; ScreenTransition.None)
```

Beim Betätigen des Buttons gelangt der Benutzer zur gewünschten Seite.

### Navigation basierend auf Auswahl

Danach wurde eine Navigation implementiert, die abhängig von einer Benutzerentscheidung unterschiedliche Seiten öffnet. Die Logik wurde mithilfe einer ``If-`` und ``Switch``-Funktion umgesetzt.

### Beispiel:

```text
If(
    IsBlank(selectedone);
    Notify("Bitte zuerst eine Auswahl treffen."; NotificationType.Warning);
    Switch(
        selectedone;
        1; Navigate('1.1/3');
        2; Navigate('1.5/4');
        3; Navigate('2.2/5');
        4; Navigate('1.3/6');
        5; Navigate('1.4/7')
    )
)
```

An bestimmten Stellen war nur eine JA/NEIN-Auswahl erlaubt. Der Aufbau erfolgte wie folgt:

### OnVisible:

```text
UpdateContext({ selectedone: Blank() })
```

### OnCheck (JA/NEIN-Feld):

```text
UpdateContext({ selectedone: 1 })
```

### Default-Wert:

```text
selectedone = 1
```

## Datenbank-Anbindung

Gegen Ende des Projekts begann Alexis mit der Implementierung der SharePoint-Datenbank-Anbindungen. Je nach Formular unterschied sich die Umsetzung.

### Beispiel:

```text
Set(Person; User());;
Set(
    Eintrag;
    Patch(
        tbl_General;
        Defaults(tbl_General);
        {
            Initiativ_name: DataCardValue21.Text;
            Epic_ID: DataCardValue13.Text;
            Sponsor: DataCardValue14.Text;
            PM: DataCardValue15.Text;
            BEC: DataCardValue16.Selected;
            Unummer: Person.Email
        }
    )
);;
```

Zusätzlich wurden einfache Berechnungen integriert, zum Beispiel:

```text
Coalesce(Value(DataCardValue4.Text); 0) 
+ Coalesce(Value(DataCardValue47.Text); 0)
```

Auf dem Bühler-Button wurde eine Home-Funktion implementiert, um den Prozess jederzeit neu starten zu können.
Der Info-Button enthält aktuell noch einen Platzhaltertext, der später angepasst wird.

Während der Entwicklung traten vereinzelt Fehler auf, unter anderem doppelte Einträge in der Datenbank. Diese konnten behoben werden, da lediglich ein Funktionsaufruf versehentlich doppelt vorhanden war.

## Verbesserung und Optimierung

Im letzten Teil des Projekts stieß Tobias Kuhn zum Team hinzu. Die restlichen Aufgaben wurden aufgeteilt, wobei Alexis hauptsächlich Bugfixing und Anpassungen am Design übernahm.

### Anpassungen an bestehenden Listen

- Die Listen sollten nach Epic ID und nicht nach Epic Name sortiert werden.

- Das Feld Description sollte den Initiative Name anzeigen.

- Auf Wunsch von Herrn Baumann wurde die Darstellung der Liste vergrößert.

### Seite S00

Auf der Seite S00 wurden mehrere kleinere Fehler korrigiert:

- Unterstriche wurden durch Leerzeichen ersetzt.

- „PM“ wurde in Epic Owner umbenannt.

- Bei der BEC-Auswahl wurde „Q.“ zu Q geändert.

- Eine neue Auswahl MLS wurde ergänzt.

- Die Epic ID sollte vor dem Initiative Name angezeigt werden.

### Seite 3.1

Es war geplant, bei Zahlen über 999 ein Apostroph zur besseren Lesbarkeit einzufügen (z. B. 1’000). Dies war jedoch nicht ohne eine vollständige Umstrukturierung der Seite möglich.
Stattdessen wurden:

- Alle Zahlenfelder zentriert

- Die Felder optisch sauber ausgerichtet und liniert

### Seiten 2.2.1 und 2.2.2

Auf den Seiten 2.2.1 und 2.2.2 sollten:

- Die Zahlenfelder unten weiß dargestellt werden

- Die Texte unterschiedlich eingefärbt werden:

  - 2.2.1 → „one time“ grün

  - 2.2.2 → „repetitiv“ grün

Da es in normalen Textfeldern nicht möglich ist, nur einen Teil des Textes einzufärben, wurde hierfür ein HTML-Textfeld verwendet, in dem die gewünschte Formatierung mithilfe von CSS umgesetzt werden konnte.

### SharePoint-Seite

Auf der SharePoint-Seite wurden die Tabellen ausgeblendet und stattdessen nur noch unter Site Contents angezeigt, sodass sie nicht direkt und einfach erreichbar sind.

## Arbeit von Tobias Kuhn:

Tobias Kuhn übernahm hauptsächlich die Umsetzung der Ausdrucksseiten. Ziel dieser Seiten war es, die Inhalte so darzustellen, dass sie übersichtlich aufgebaut sind und nur dann ausgefüllt bzw. angezeigt werden, wenn die entsprechenden Fragen im Questionnaire beantwortet wurden. Nicht relevante Inhalte sollten auf den Ausdrucksseiten vollständig ausgeblendet bleiben.

Die Ausdrucksseiten wurden von Tobias sehr klar und strukturiert gestaltet. Zusätzlich implementierte er:

- Einen Button zur Navigation auf die nächste Druckseite

- Einen Druck-Button, über den der Druckvorgang ausgelöst werden kann

Der Druckprozess ist mit einem Power-Automate-Flow verbunden, den Tobias ebenfalls umgesetzt hat. Nach dem Auslösen des Drucks werden die erzeugten Seiten automatisch an Olav versendet. Der Versand erfolgt per E-Mail mit einer vorgefertigten E-Mail-Vorlage, wodurch der Prozess vollständig automatisiert ist und keine manuelle Nachbearbeitung benötigt.