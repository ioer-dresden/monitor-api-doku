---
layout: default
title: Backend
parent: Admin
---

# Backend

Die Hauptlast bei für die Administrierung der Dienste liegt im Backend. Welche in diesem Kapitel anhand ihrer Klassen und Schichten dokumentiert werden soll.

- [**Services**](#services)
- [**Interfaces**](#interfaces)
- [**Models**](#models)
- [**routes**](#routes)

## Services {#services}

Die Services übernehmen die Instanziierung der Objekte und erstellen die vom Clienten über das Dashboard gewünschten Dienste über die _REST_-Schnittstelle ([**_routes.py_**](#routes)). Für die OGC-Dienste wurde das [_Factory_](https://jaxenter.de/mit-factory-pattern-designprobleme-losen-5040)-Pattern eingesetzt, welches je nach Parametrisierung, den entsprechenden **_OGC_**-Layer instanziiert.

### OGC-Factory {#ogc-factory}
Die OGC-Factory wird von der _routes.py_ aufgerufen um die gewünschten Dienste zu erstellen. _Die **Factory-Pattern-Methode** definiert dabei ein Interface für die Erstellung von Objekten und delegiert die Objekterstellung an die Sub-Klassen. Ziel ist, dass der Client nichts von der Objekt-Instantiierung mitbekommt und über eine gemeinsame Schnittstelle auf die Objekte zugreift. Factory-Patterns kapseln dadurch den Creation Code vom Client_ [[Quelle](https://jaxenter.de/mit-factory-pattern-designprobleme-losen-5040){:target="blank"}]. 

Um dies zu verdeutlichen ist nachfolgend der Code abgebildet:

```python
class OgcFactory:
    def __init__(self,_service):
        self.service = _service.lower()
        if("localhost" in request.url or "127.0.0.1:5000" in request.url):
            app.logger.debug("OGC Service for localhost")
            self.path ='G:\\mapsrv_daten\\detailviewer\\{}_mapfiles'.format(self.service)
        else:
            app.logger.debug("OGC Service for monitor.ioer.de")
            self.path = '/mapsrv_daten/detailviewer/{}_mapfiles'.format(self.service)

    def create_service(self):
        if self.service =='wms':
            return WmsService(self.path)
        elif self.service =='wcs':
            return WcsService(self.path)
        elif self.service=='wfs':
            return WfsService(self.path)
```

Der Konstruktor definiert dabei auf welchen Pfaden die Services erstellt werden sollen.

WCS-Services
{: .label }
WFS-Service
{: .label }
WMS-Service 
{: .label }

Die Klassen _**WCS-Services, WFS-Service und WMS-Service**_ implementieren die vom [**_OgcService_**](#ogc-interface)-Interface definierten Methoden.
Hierbei wird in der Methode _createAllServices_ alle für die **OGC-Rasterdienste bzw. Vektordienste** freigegebenen Indikatoren vom Backend abgefragt. Diese Aufgabe wird von der Klasse [**_Indicator-Values_**](#ind-values) übernommen.
Anhand einer Schleife wird für jeden verfügbaren Indikator ein neuer [**_IoerIndicator_**](#ind) instanziiert und an die _writeFile_ Methode übergeben. Diese erstellt anhand der _Getter_-Methoden des übergebenen Indikators das entsprechende _Mapfile_ in dem angegebenen Pfad. Der Pfad wird hierbei im Konstruktor übergegeben, diese Aufgabe übernimmt die [**Factory**](#ogc-factory).

<iframe src="{{site.baseurl}}/assets/html/wcs-service.html" frameborder="0" allowfullscreen onload="this.width=screen.width*0.5;this.height=screen.height*0.6;"></iframe>

Folgend sind die Parameter und Methoden dokumentiert.

|Methode | Parameter | Beschreibung |
|--------|-----------|--------------|
| public: __init__| _optional_-String:**Path**| Konstruktor :wink:, Der Pfad Paramter wird bei der Verwendung der [**Factory**](#ogc-factory) durch diese übernommen|
| public: createAllServices| Object: Indikator, _optional_-String:**Path** | Methode um einzelne Dienste für einen übergebenen Indikator zu erstellen |
| private: writeFile| _optional_-String:**Path**)|Diese Methode erstellt anhand des übergebenen Indikators das gewünschte _Mapfile_, primär wird der Pfad aus dem im Konstruktor definierten Pfad übergeben. Als Rückgabe wird ein Objekt mit dem Indikator und dessen Status (erstellt/Fehler) übergeben. |

### Indicator-Values {#ind-values}

## Interfaces {#interfaces}
### OgcService {#ogc-interface}

## Models {#models}
### IÖR-Indikator (#ind)