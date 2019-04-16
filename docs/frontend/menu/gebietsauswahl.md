---
layout: default
title: Gebietsauswahl
parent: Menu
grand_parent: Frontend
---

# Gebietsauswahl

![Image]({{site.baseurl}}/assets/images/gebietsauswahl.png)

Das Menü dient dem Nutzer dazu, dem Nutzer die Möglichkeit zu geben seine getroffene räumliche Analyseebene zu verfeinern. Es können einzelne Gebiete ausgewählt und visualisiert werden.
Es ist ein GUI-Element der [Toolbar]({{site.baseurl}}/docs/frontend/menu/toolbar.html).
### Parameter

Über das Objekt _**gebietsauswahl**_ stehen folgende Parameter zur Verfügung:

| Parameter    | Beschreibung |
|-------------| -------------|
| **mapLayer**| Array in welchem die zur Karte hinzugefügten Gebiete gespeichert werden |
|**mapLayerGrund**| Array in welchem die zur Karte hinzugefügten Layer der Grundaktualität gespeichert werden, diese werden automatisch generiert |
| **addedAGS** | Array in welchem die entsprechenden AGS der Auswahl gespeichert werden |
| **paramter**| URL Parameter, welcher die durch den Nutzer ausgewählten Gebiete-getrennt durch ein Komma, beinhaltet. |

### Funktionen

![UML]({{site.baseurl}}/assets/images/uml_gebietsauswahl.png)

#### Object
____

|  Funktion |  Parameter | Beschreibung |
|-------------| -----------|-----------|
| **getDOMObject**| | Gibt den JQuery Paramter zurück |
|**getParameter**| |siehe [Url Paramter]({{site.baseurl}}/docs/frontend/url_paramter)|
|**setParameter**| String:value | siehe [Url Paramter]({{site.baseurl}}/docs/frontend/url_paramter)|
|**updateParamter**| String:value| siehe [Url Paramter]({{site.baseurl}}/docs/frontend/url_paramter)|
| **getMapLayer** | | gibt den Paramter **mapLayer** zurück |
| **setMapLayer**| Array:array | setzt den Paramter **mapLayer** neu |
| **setMapLayerGrund** | Array:array | setzt den Paramter **mapLayerGrund** neu |
| **setSelection** | Array:array | setzt den Paramter **addedAGS** neu, damit werden die gesetzten Tags des Menüs aktualisiert. |
| **getContainer**| | Gibt das HTML Element zurück |
| **getMapLayerGrund** | | gibt den Paramter **mapLayerGrund** zurück |
| **getSelection** | | gibt den Paramter **addedAGS** zurück, also die vom Nutzer gewählten Gebietseinheiten, welche als Tags dargestellt sind. |
| **init** | | Diese Funktion prüft ob das Menü noch nicht initialisiert wurde, indem geschaut wird, wie viele _Tags_ vorhanden sind (Funktion **countTags**). Sind keine Vohanden, erfolgt die Initialisierung. <br/> 1. **fill**: (siehe Funktion _fill_) <br/> 2. **bind**: hier wird die Klasse **dropdown** der JS-Bibliothek [SemanticUI](https://semantic-ui.com/){:target="_blank"} referenziert, um das hinzufügen (**onAdd**) und entfernen (**onRemove**) von Gebieten zu realisieren. |
|**addSelectedLayersToMap**| | Fügt das ausgewählte Gebiet der Karte hinzu, diese Funktion wird beim wählen einer Gebietseinheit ausgelöst.|
|**removeSelectedLayersFromMap**| Integer:ags | entfernt anhand des übergebenen _ags_ das Gebiet aus der Karte. Wird beim entfernen aus den Dropdown ausgelöst|
|**fill**| | füllt das Dropwdown durch parsen des _indikatorJSON.getJSONFile()_, dabei werden alle Gebiete **alphapetisch** sortiert und dem Dropdown **hinzugefügt**|
|**clear**| | diese Funktion leert **nur** das Dropdown Menü|
|**countTags**| | es werden alle gewählten Gebiete (Tags im Dropdown Menü) gezählt und als **Integer** zurückgegeben|
|**getSelectionAsString**| | gibt alle Namen der gewählten Gebiete, Komma-getrennt , als **String** zurück|

____


#### Controller
____

Wird über _gebietsauswahl_.**controller** aufgerufen und steuert die GUI-Interaktionen.

|  Funktion |  Parameter | Beschreibung |
|-------------| -----------|-----------|
|**set**| | registriert alle Events am GUI-Element im DOM |