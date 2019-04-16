---
layout: default
title: Legende
parent: Kartenansicht
grand_parent: Frontend
---
# Legende
Dieses Element stellt alle relevanten Informationen zu dem von Nutzer gewählten Indikator bereit. Über den Datenalter Button kann sich der Nutzer das Datenalter als Karte darstellen lassen.
Das Objekt wird über **_legende_** aufgerufen.

## Funktionen

![Legende]({{site.baseurl}}/assets/images/uml_legende.png)


|  Funktion |  Paramter | Beschreibung |
|:-------------|:-----------|:-----------|
|**getDOMobject**| |gibt die gesamte Legende als JQuery Objekt zurück|
|**getCloseIconObject**| | gibt das Icon zum schließen als JQuery Objekt zurück |
|**getShowButtonObject**| | gibt den Button, welcher die Legende öffnet, als JQ-Objekt zurück|
|**getDatenalterContainerObject**| |gibt den gesamten Datenalter-Container als JQ-Objekt zurück|
|**getLegendeColorsObject**| |gibt den gesamten Container als JQ-Objekt zurück, welcher für die farbliche Visualisierung der Klassen zuständig ist|
|**getIndikatorInfoObject**| |gibt das JQ-Objekt zurück, welches die Informationen zum gewählten Indikator beinhalten.|
|**getDatengrundlageObject**| |gibt das JQ-Objekt zurück, welches die Datengrundlage beinhaltet|
|**getEinheitObject**| | gibt das JQ-Objekt zurück, welches die Einheit beinhaltet|
|**getKlasseneinteilungObject**| |gibt das JQ-Objekt zurück, welches die Klasseneinteilung beinhaltet|
|**getHistogrammObject**| |gibt das JQ-Objekt zurück, welches das Histogramm beinhaltet|
|**open**| | öffnet die Legende|
|**init**| Boolean:open | Diese Funktion initialisiert die Legende, mit allen Interaktionsmöglichkeiten. Wurde der Paramter open gesetzt, wird die Legende nach erfolgter Initialisierung geöffnet.|
|**resize**| |Setzt die Dimensionen der Legende neu, beispielsweise wenn in den Rastermodus umgeschaltet wurde oder das Browser-Fenster verkleinert wurde.|
|**fillContent**| |Füllt die Legende je nachdem, welche [Raeumliche-Visualisierung](Raeumliche-Visualisierung) gewählt wurde. Hierbei werden alle wichtigen Informationen zum jeweiligen Indikator vom Server geholt. Die Funktion wird nach jeder Wahl eines Indikators aufgerufen.|
|**remove**| | entfernt die Legende|
|**isOpen**| | gibt als Boolean zurück, ob die Legende offen ist|
|**close** | | schließt die Legende|