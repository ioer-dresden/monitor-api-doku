---
layout: default
title: Backend
parent: Admin
---

# Backend

Die Hauptlast bei für die Administrierung der Dienste liegt im Backend. Welche in diesem Kapitel anhand ihrer Klassen und Schichten dokumentiert werden soll.

- [**Services**](#services)
    - [**OGC-Dienste**](#ogc-factory)
    - [**Indicator-Values**](#ind-values)
    - [**GeoMIS**](#geosn)
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
Hierbei werden in der Methode _createAllServices_ alle für die **OGC-Rasterdienste bzw. Vektordienste** freigegebenen Indikatoren vom Backend abgefragt. Diese Aufgabe wird von der Klasse [**_Indicator-Values_**](#ind-values) übernommen.
Anhand einer Schleife wird für jeden verfügbaren Indikator ein neuer [**_IoerIndicator_**](#ind) instanziiert und an die _writeFile_ Methode übergeben. Diese erstellt anhand der _Getter_-Methoden des übergebenen Indikators das entsprechende _Mapfile_ in dem angegebenen Pfad. Der Pfad wird hierbei im Konstruktor übergegeben, diese Aufgabe übernimmt die [**Factory**](#ogc-factory).

<iframe src="{{site.baseurl}}/assets/html/wcs-service.html" frameborder="0" allowfullscreen onload="this.width=screen.width*0.5;this.height=screen.height*0.6;"></iframe>

Folgend sind die Parameter und Methoden dokumentiert.

|Methode | Parameter | Beschreibung |
|--------|-----------|--------------|
| public: __init__| _optional_-String:**Path**| Konstruktor :wink:, Der Pfad Paramter wird bei der Verwendung der [**Factory**](#ogc-factory) durch diese übernommen|
| public: createAllServices| Object: Indikator, _optional_-String:**Path** | Methode um einzelne Dienste für einen übergebenen Indikator zu erstellen |
| private: writeFile| _optional_-String:**Path**)|Diese Methode erstellt anhand des übergebenen Indikators das gewünschte _Mapfile_, primär wird der Pfad aus dem im Konstruktor definierten Pfad übergeben. Als Rückgabe wird ein Objekt mit dem Indikator und dessen Status (erstellt/Fehler) übergeben. |

### Indicator-Values {#ind-values}

Diese Klasse hat die Aufgabe für die Übergebene Raumgliederung alle verfügbaren Indikatoren vom [**Monitor-Backend**](https://ioer-dresden.github.io/monitor-doku/docs/backend){:target="blank"} abzurufen. Über die Methode _getAllAvaliableServiceValues_ werden anhand des Paramters Raumgliederung (raster,gebiete) alle verfügbaren Indikatoren abgerufen und ein JSON-Objekt zurückgegeben. Der Code der Klasse ist nachfolgend abgebildet.

```python
class IndicatorValues:
    def __init__(self,format):
        self.url = Config.URL_BACKEND_MONITOR
        self.json = '{"format":{"id":"%s"},"query":"getAllIndicators"}' % format
        self.format=format
        req = requests.post(self.url, data={'values':self.json})
        self.values = json.loads(req.text)
        print (self.values)

    #methon to return all possible indicator values which are possible for an indicator
    def getAllAvaliableServiceValues(self,service):
        res =[]
        for x in self.values:
            cat_name = self.values[x]['cat_name']
            cat_name_en = self.values[x]['cat_name_en']
            values = self.values[x]['indicators']
            ind_values = []
            for i in values:
                # if 1: the service is avaliable else not
                if int(values[i]["ogc"][service]) == 1:
                    ind_val=dict(values[i])
                    ind_id = dict({"id":i})
                    merge = dict()
                    merge.update(ind_id)
                    merge.update(ind_val)
                    del merge['atkis']
                    del merge['ogc']
                    ind_values.append(merge)

            res.append({'cat_id':x,'cat_name':cat_name,'cat_name_en':cat_name_en,"values":ind_values})

        return res
```

### GeoSN {#geosn}

<iframe src="{{site.baseurl}}/assets/html/geosn.html" frameborder="0" allowfullscreen onload="this.width=screen.width*0.5;this.height=screen.height*0.2;"></iframe>

Diese Klasse implementiert das Interface [**GeoSN-Service**](#geosn-service) und hat die Aufgabe, die Dienste des IÖR-Monitors mit dem [**GeoMIS**](https://geomis.sachsen.de/terraCatalog/Start.do;jsessionid=C1A9B5D0D432772FDC164C57CB57845B){:target="blank"} zu verknüpfen. Der Service wurde notwendig, da zum aktuellen Zeitpunkt keine **API** auf der Seite des _GeoMIS_ vorhanden ist, welche diese Aufgabe übernimmt.

Anhand der vom <a href="mailto:geomis@geosn.sachsen.de">GeoMIS :email:</a> bereitgestellten XML-Dateien, werde diese mit den aktuellen Informationen der Datenbank synchronisiert. Hierfür parst die Methode _updateFile_ alle _XML-Dateien_ in dem vorgegebenen Verzeichnis. Passt die Parametrisierung mit der gesetzten Klassenvariable _Indikator_ überein, wird dieses aktualisiert. Hierfür  fällt der händische Update-Vorgang in der GUI des GeoMIS weg. Durch die Freigabe des [_Verzeichnisses_](https://monitor.ioer.de/ogc/){:target="blank"} nach außen, kann das GeoMIS die in den XML-Dateien definierten Dienste **_harvesten_**.   

Die Methode _update_ ruft von Dienst [_Indicator-Values_](#ind-values) alle Raster und Vektor Indikatoren auf, welche das IÖR nach außen freigibt und ruft auf deren Basis die private Methode _updateFile_ auf. Iterativ wird dabei die Klassenvariable _Indikator_ gesetzt, welche jeweils das Model [_IÖR-Indikator_](#ind) ist. 

## Interfaces {#interfaces}
_Eine **Schnittstelle** (englisch interface) gibt in der objektorientierten Programmierung an, welche Methoden in den unterschiedlichen Klassen vorhanden sind oder vorhanden sein müssen._ [Quelle](https://de.wikipedia.org/wiki/Schnittstelle_(Objektorientierung)){:target="blank"}

### OgcService {#ogc-interface}

<iframe src="{{site.baseurl}}/assets/html/ogc-services.html" frameborder="0" allowfullscreen onload="this.width=screen.width*0.5;this.height=screen.height*0.18;"></iframe>

Dieses Interface definiert die notwendigen Methoden für die Erstellung eines IÖR-OGC Dienstes.

### GeoSN-Service {#geosn-service}

<iframe src="{{site.baseurl}}/assets/html/geosn-service.html" frameborder="0" allowfullscreen onload="this.width=screen.width*0.5;this.height=screen.height*0.18;"></iframe>

Dieses Interface definiert die notwendigen Methoden für die Erstellung eines GeoSN Dienstes.

## Models {#models}
### IÖR-Indikator {#ind}