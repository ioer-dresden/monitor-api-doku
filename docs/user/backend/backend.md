---
layout: default
title: Backend
parent: User
---
## Komponenten
### _routes.py_
Der _**route**_ Dekorateur nimmt eine Zeichenfolge , die die URL übereinstimmen. Wenn eine Anfrage nach einer URL, die dieser Zeichenfolge entspricht, von der Anwendung empfangen wird, wird die dekorierte Funktion (auch Ansichtsfunktion genannt ) aufgerufen.
### route**('/')**
Dies ist die sogenannte **Main**-Route, hier wird lediglich aus dem Dateiorder **user** innerhalb des **[static]({{site.baseurl}}/docs/static)**-Verzeichnisses die _index.html_ gerendert.
### route**(/user)**
Mit der Funktion _get_service()_ werden dem Nutzer die OGC-Dienste über die **API** bereitgestellt. Über einen _GET_ request müssen die **URL-Parameter** an den Endpoint gesendet werden. Durch den vorgeschalteteten Service wird der direkte Zugriff auf den **MapServer** blockiert und nur angemeldete Nutzer können die OGC-Dienste nutzen.

Ein Beispiel für einen OGC-Zugriff wäre folgende Parametrisierung der URL: 
- https://monitor.ioer.de/monitor_api
- **/user?**
- **id**=_F13RG**&** : Indikator ID
- **key**=_5HbHOLs7eKQmHJ8r5og2P91qr9tAviBO_**&** : Generierter API-Key des Nutzers, welcher über die Website erstellt wurde
- **service**=_wcs_ : Gibt an, welcher OGC-Service genutzt werden soll (wfs,wcs, wms)

Alle URl und Indikatoren lassen sich auch direkt über die Tabellenansicht im **[Userbereich]({{site.baseurl}}/docs/user/frontend/frontend.html)** anzeigen.

Im folgenden Diagramm ist die Funktionsweise des Streaming-Dienstes dokumentiert.
![getService]({{site.baseurl}}/assets/images/get_service.png)

Im ersten Schritt werden aus der URL die entsprechenden Parameter herausgefiltert. Dann wird geprüft ob der API-Key korrekt ist, indem die in der Datenbank hinterlegten- mit den angefragten Werten verglichen werden. Ist der API-Key fehlerhaft, wird eine entsprechende Fehlermeldung an den Clienten zurück geschickt. Ist der Key korrekt wird die Anfrage an den [MapServer](https://mapserver.org/) für den entsprechenden Service gestellt und der Response an den Clienten gestreamt. 
### route**(/login)**