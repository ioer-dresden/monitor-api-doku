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
