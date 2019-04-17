---
layout: default
title: Frontend
parent: User
---
## Technologien
### Registrierung und Anmeldung
Die Gestaltung der Graphischen Benutzeroberfläche wurde mit **[Bootstrap](https://getbootstrap.com/docs/4.3/getting-started/introduction/){:target="_blank"}** realisiert.
![Register]({{site.baseurl}}/assets/images/register_view.png)
Hiermit konnte das _Styling_ der GUI-Elemente an die Bibliothek abgegeben werden, welche auch den Responsive Part übernimmt.
Diese Technologie wurde auch in der Anmeldung eingesetzt.
### Nutzerbereich
#### **ApiKey.jsx**
Um einen _Key_ zu Generieren muss der Nutzer den entsprechenden Button klicken. Dieser wird vom _Flask_ Backend generiert. Die Kommunikation erfolgt dabei über **HTTPS-GET** mit der Unterstützung von **[JQuery-Ajax](http://api.jquery.com/jquery.ajax/)**. Der Generierungsvorgang wird im Abschnitt [Backend]({{site.baseurl}}/docs/user/backend/backend.html) dokumentiert.
Die Klasse **ApiKey** erbt dabei von _[React.Component](https://reactjs.org/docs/react-component.html)_.
![APIKEY]({{site.baseurl}}/assets/images/api-key.png)
In der folgenden Tabelle ist die Klasse **ApiKey** dokumentiert:

|Methode | Values | Return | Info |
|--------|--------|--------|-------|
|**constructur** | _props_ | Constructor :wink: | werden erstmal keine Variablen gesetzte sondern nur default (_state,ref,methoden werden an das DOM gebunden_)|
|**copyToClipboard**| | | Methode um den generierten ApiKey zu Kopieren, womit der Nutzer den Schlüssel beliebig einfügen kann|
|_static_ **makekey**| | String:key|Methode welche einen Schlüssel generiert und zurückgibt|
| **insert**|String: (key,username,user_id) | | Methode welche anhand der übergebenen Parameter den Generierten Parameter an das Backend übergibt, welches den Schlüssel für den Nutzer in der **PostgreSQL** speichert.|
| **render**| | JSX | rendert die Komponente im [ReactDOM](https://reactjs.org/docs/react-dom.html) |

#### **Menü.jsx**
Auswahlmenü für den Nutzer, in welchem er zwischen den Seiten hin- und herschalten kann.
![Menu]({{site.baseurl}}/assets/images/menu.png)
Die Klasse an sich hat nur die Aufgabe das Menü zu rendern und damit wiederverwendbar zu machen.

#### **service.js**
![Services]({{site.baseurl}}/assets/images/services.png)
Innerhalb der Datei wird die Tabelle, welche dem Nutzer alle zur Verfügung gestellten OGC-Services zeigt, generiert und die notwendigen **Events** im DOM registriert.
Durch die Verwendung der _JQuery_ Bibliothek **[DataTables](https://datatables.net/)** wird hier nicht React eingesetzt sondern _JavaScript_-Objekte. Diese werden folgend dokumentiert.

- **const table**

| Methode | Values | Return | Info |
|---------|--------|--------|------|
|**getTableObject**| | | Gibt das JQuery-DOM Objekt der Tabelle zurück |
| **init** | Array: options | | Erstellt die Tabelle anhand der gesetzten Paramter. Hierbei kann _["raster","wcs"]_,_["raster","wms"]_,_["gebiete","wfs"]_ gesetzt werden. Je nach getztem Array wird die Tabelle mit den entsprechenden Indikatoren generiert. Dabei werden die notwendigen Informationen über die Funktion _getData_ geholt und innerhalb der Tabelle generiert. Das generierte HTML wird dabei als Template String dem DOM hinzugefügt.|
|**getData**| String:setting|Object:Response| Über **HTTP-POST** via _JQuery-Ajax_ werden alle notwendigen Indikator-Informationen vom Backend des Monitors ([Doku](https://ioer-dresden.github.io/monitor-doku/docs/backend){:target="_blank"}) abgefragt. Das Ergebnis wird als Return zurückgegeben.|
|**destroy**| | | entfernt das **DataTable** PlugIn, von der Tabelle.|

Innerhalb des **Table**-Objektes ist noch das **Button**-Objekt integriert. Die Buttons haben folgende Aufgaben:
- **GetCapabilities**: zeigt dem Nutzer das _Capabilities Dokument_ (XML) zurück, das alle Informationen enthält, die ein OGC Client benötigt, um Kartenanfragen an den Server zu beantworten
- **URL Kopieren**: kopiert die URL des Services in das Clipboard des Clienten
- **Karte**: öffnet den Indikator im IÖR-Monitor

| Methode | Values | Return | Info |
|---------|--------|--------|------|
|**init**| | | bindet die Button events an das DOM|
|**copyURL**|String:url| | kopiert die Service URL in das Clint-Clipboard|

- **const chechbox**

Diese Objekt hat die Aufgabe das Hin- und Herschalten zwischen den OGC-Services zu koordinieren.
![Checkbox]({{site.baseurl}}/assets/images/checkbox.png)

| Methode | Values | Return | Info |
|---------|--------|--------|------|
|**getContainerObject**| | | Gibt das JQuery-DOM Objekt des _Checkbox_-Containers zurück|
|**init**| | | bindet das _change_-Event an das DOM, welches je nach Auswahl das **table.init("wfs"/"wcs")** aufruft|
