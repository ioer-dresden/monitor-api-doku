---
layout: default
title: Sora
nav_order: 3
has_children: true
permalink: /docs/sora
---
# [SoRa](http://www.sora-projekt.de/){:target="blank"} - Sozial-Raumwissenschaftliche Forschungsdateninfrastruktur

Da es sich bei Sora um ein reines Backend handelt, wird auch nur dieses vorgestellt. 
Das Backend hat vor allem die Aufgabe die im SoRa Projekt gestellten Anforderungen zu erfüllen indem **freie Nutzeranfragen** auf topografischen Geobasisdaten beantwortet werden. Diese werden vom Clienten gestellt.

# Inhalt

- [**Rest-API**](#rest)
    - [Abfrage von Indikatorwerten zu Koordinaten](#service-coord)
    - [Routing zum nächstgelegenen POI](#service-routing)
    - [Routing zwischen zwei Koordinaten](#service-poi)
- [**RDF**](#rdf)
    - [Indicators](#ind)
    - [Category](#cat)
- [**Flask** (monitor.ioer.de)](#flask)
- [**Esri-Server** (edn.ioer.de)](#edn)
    - [Abfrage von Indikatorwerten zu Koordinaten](#coordinates)

## Architektur

Alle Anfragen für das **SoRa**-Forschungsprojekt werden an die Monitor-API geschickt, wofür der [**Blueprints**](http://flask.pocoo.org/docs/1.0/blueprints/){:target="blank"} **sora** implementiert wurde. Innerhalb der _routes.py_ befindet sich das _RequestMapping_, welches für die entsprechenden **query strings** alle Anfragen beantwortet.

Auf dem [ESRI-Server](https://edn.ioer.de:6443/arcgis/manager/index.html){:target="blank"} wurden die Service-Layer implementiert. Der Flask Microservice auf _https://monitor.ioer.de/monitor_api/sora_ nimmt die Requests des _Gesis_- [**Location Mapping**]({{site.baseurl}}/assets/images/sora-arch.png){:target="blank"} an. Über die Klasse _Request-Manager_, werden die Anfragen gestellt. Das die Kommunikation mit einem _WPS_ teilweise sehr kompliziert sein kann, wurde diese Service-Schicht implementiert.       

<iframe src="{{site.baseurl}}/assets/html/sora-architektur.html" frameborder="0" allowfullscreen onload="this.width=screen.width*0.5;this.height=screen.height*0.9;"></iframe>

## REST-Schnittstelle {#rest}
_REST steht für REpresentational State Transfer, API für Application Programming Interface. Gemeint ist damit ein Programmierschnittstelle, die sich an den Paradigmen und Verhalten des World Wide Web (WWW) orientiert und einen Ansatz für die Kommunikation zwischen Client und Server in Netzwerken beschreibt._ [Quelle](https://www.cloudcomputing-insider.de/was-ist-eine-rest-api-a-611116/){:target="blank"}

Diese Schnittstelle wurde sowohl auf dem **Esri-Server** als auch auf dem **Flask**-Microservice eingesetzt. Da bei allen durch das Projekt genutzten Diensten z.T. sehr große _Koordinaten-Anfragen_ gestellt werden, unterstützt die **API** nur [**POST**](https://en.wikipedia.org/wiki/POST_(HTTP))-Requests. Da auf orginären Rasterkarten gerechnet wird, kann die Anfrage je nach Menge der Koordinaten länger Dauern, wird <a href="https://subscription.packtpub.com/book/application_development/9781783287963/6/ch06lvl1sec43/polling" target="_blank"><b>Polling</b></a> angewendet. Hierfür wird dem Clienten auf seine **POST** Anfrage eine JobID übermittelt. Jetzt kann über eine entsprechend parametrisierte **GET** Anfrage periodisch angefragt werden, ob das Ergebnis bereitgestellt wurde.

____

Nachfolgend ist die Kommunikation mit der **REST-API** dokumentiert, um _Requests_ und _Responses_ zu verdeutlichen wurden Beispiele hinterlegt. Der Genau Code für die _WPS_ ist im Punkt [Esri-Server](#edn) dokumentiert. Die Code-Beschreibung für Flask ist [hier](#flask) zu finden.    

1. [Abfrage von Indikatorwerten zu Koordinaten](#1-abfrage-der-koordinaten)
2. [Routing zum nächstgelegenen POI](#2-routing-zum-nächstgelegenen-poi)
3. [Routing zwischen zwei Koordinaten](#3-routing-zwischen-zwei-koordinaten)

### 1. Abfrage von Indikatorwerten zu Koordinaten {#service-coord}
#### Beschreibung
Mit diesem Service können für vorgegebene Koordinaten die jeweiligen Indikatorwerte abgefragt werden. Wird ein _Buffer-Wert_ gesetzt, ermittelt der Service den Durchschnitt des Indikators innhalb des _Buffer_-Bereichs. Hierbei wird ein quadratische Buffer um jede Koordinate gesetzt, dessen Räumlicher Durchschnitt über den _Paramter_ buffer gesetzt werden kann

**Endpoint:** https://monitor.ioer.de/monitor_api/sora/services

1. **Post** der JSON-Datei:
    - **Query String**: **job**=coordinates
    - **Datei**: folgend eine beispielhafte JSON Datei, welche alle notwendigen Parameter übermittelt

        ```json
        {
            "coordinates":
                [{"id":8291,"x":"4340896,28050814","y":"2764378,83432727"},
                {"id":3058,"x":"4340657,55155301","y":"2765292,51267246"},
                {"id":3438,"x":"4342453,82536125","y":"2764230,5410444"},
                {"id":249,"x":"4339813,12908578","y":"2765946,78532257"},
                {"id":3800,"x":"4339879,51749317","y":"2765648,13305142"}],
            "indicators":
                [{"id":"S12RG","year":"2012"},
                {"id":"S12RG","year":"2015"},
                {"id":"F01RG","year":"2015","buffer":"100"}],
            "epsg":"3035"
        }
        ```

        - **coordinates**: _x/y_ Koordinaten mit der gesetzten _id_, hierbei ist es egal ob die Koordinaten als Zahlenwert(.) oder String übergeben werden. Es würde genauso funktionieren:

            ```json
            ...
            {"id":8291,"x":"4340896.28050814","y":"2764378.83432727"}
            ...

            ```
        - **indicators**: _id_ des Indikators und der gewünschte Zeitschnitt, optional ist der _buffer_, welcher in Meter angegeben wird.
        - **epsg**: zugrundeliegendes Koordinatensystem
        - Als Antwort auf den request erfolgt eine **JSON**, welche die _JobID_ beinhaltet und den _Status_.

        ```json
        {
         "jobId": "j14585674d2434aada462cffe22862324",
         "jobStatus": "esriJobSubmitted"
        }
        ```

        **Beispiel** mit <a href="https://curl.haxx.se/" target="_blank"><b>curl</b></a>:

        ```curl
        curl --header "Content-Type: application/json" \
             --request POST \
             --data '{"coordinates":[{"x":"12.067024122630599","y":"54.1015602797111","id":66},{"x":"12.141539756251","y":"54.1154796784391","id":99}],"indicators":[{"id":"S12RG","year":"2017"}],"epsg":"4326"}' \
             https://monitor.ioer.de/monitor_api/sora/services?job=coordinates
        ```

        Als Ergebnis wird dann folgende JSON mit einer Job-ID geliefert:

        ```json
        {
        "jobId": "j19e57f40c4a44755a32078d9ad7097f2",
        "jobStatus": "esriJobSubmitted"
        }
        ```

Wurde dem Clienten die **JobID** als Response übermittelt, kann via HTTP-GET das Ergebnis periodisch angefragt werden. Hat das Backend ein Ergebnis zur Verfügung, wird dieses bereitgestellt.

2. **GET** des Ergebnisses:
    - **Query Strings**:
        - **job**=coordinates 
        - **job_id**=id des Jobs aus dem Post-Request, in diesem Beispiel **j14585674d2434aada462cffe22862324**
    - Wird der Job noch berechnet, sendet der Service eine entsprechende Mitteilung als JSON zurück und muss weiter angefragt werden. Liegt das Ergebnis vor, wird das Ergebnis als JSON gesendet.

    **Beispiel** mit <a href="https://curl.haxx.se/" target="_blank"><b>curl</b></a>:

    ```curl
    curl --request GET \
         -d 'job=coordinates'
         -d 'job_id=j19e57f40c4a44755a32078d9ad7097f2'
         https://monitor.ioer.de/monitor_api/sora/services
    ```

    rechnet der Service noch wird z.B. folgende JSON als Response geliefert:
    ```json
    {
        "jobId": "jc7e4847561014440a06cd6e4cf2e7fb2",
        "jobStatus": "esriJobExecuting",
        "messages": [
        {
        "type": "esriJobMessageTypeInformative",
        "description": "Submitted."
        },
        {
        "type": "esriJobMessageTypeInformative",
        "description": "Executing..."
        }
        ]
    }
    ```

    ist der Service fertig wird z.B. folgender Response geliefert:
    ```json
        {
    "paramName": "outputJSON",
    "dataType": "GPString",
    "value": [
    {
    "y":  "54.1015602797111",
        "x":  "12.067024122630599",
        "values":  [
        {
        "indicator":  "S12RG",
        "indicator_value":  "100.0",
        "time":  "2017"
        }
    ],
        "id":  "66"
    },
    {
    "y":  "54.1154796784391",
        "x":  "12.141539756251",
        "values":  [
        {
        "indicator":  "S12RG",
        "indicator_value":  "100.0",
        "time":  "2017"
        }
    ],
        "id":  "99"
    }
    ]
    }    
    ```


Die folgenden Dienste berechnen mit Hilfe des [Open Route Service](https://github.com/GIScience/openrouteservice), die Wegstrecken zwischen den übergebenen Koordinaten und der Anforderung. Hierbei ist es möglich durch die Variierung der Parameter, die Berechnung zu beeinflussen.

### 2. Routing zum nächstgelegenen POI {#service-routing}
#### Beschreibung
Dieser Service berechnet auf der Grundlage des übergebenen Punktes, die Distanz zum nächst gelegenen POI.

**Endpoint:** https://monitor.ioer.de/monitor_api/sora/services

1. **Post** der JSON-Datei:
    - **Query String**: **job**=routing_poi
    - **Datei**: folgend eine beispielhafte JSON Datei, welche alle notwendigen Parameter übermittelt

    ```json
    {"coordinates":
        [{"id":5297,"x":"4339535,6687453","y":"2765964,35707297"},
        {"id":4453,"x":"4339873,89116431","y":"2764805,05240828"},
        {"id":4989,"x":"4339655,35336738","y":"2765570,54764701"},
        {"id":7789,"x":"4339816,61177691","y":"2764105,56506073"}
        ......
        ],
    "options":[{"profile":"walking","epsg":{"input":"3035","output":"25833"}}
    ]}
    ```
    - **Optionen innerhalb der JSON:**
        - **options**:
           - **profile**: gibt an, wie der gefundene POI erreicht werden soll. Unterstützt werden im Moment: _walking_, _driving_
           - **epsg**: gibt an, in welchem Koordinatensystem die Punkte sich befinden und können optional auch in eine anderes Koordinatensystem transformiert werden. Hierfür muss der optionale Parameter _output_ angegeben werden.
           - **poi**: gibt an, welcher nächst gelegene POI berechnet werden soll. Aktuell werden **Grünflächen** als _green_areas_ und der **Öffentliche Nahverkehr** als _public_transport_.

     **Beispiel** mit <a href="https://curl.haxx.se/" target="_blank"><b>curl</b></a>:

    ```curl
    curl --header "Content-Type: application/json" \
            --request POST \
            --data '{"coordinates":[{"id":5297,"x":"4339535,6687453","y":"2765964,35707297"}],"options":[{"profile":"walking","epsg":{"input":"3035"},"poi":"public_transport"}]}' \
            https://monitor.ioer.de/monitor_api/sora/services?job=routing_poi
    ```

    Als Ergebnis wird dann folgende JSON mit einer Job-ID geliefert:

    ```json
    {
    "jobId": "j19e57f40c4a44755a32078d9ad7097f2",
    "jobStatus": "esriJobSubmitted"
    }
    ```

2. **GET** des Ergebnisses:
  - Wie im Service _coodinates_, nur jetzt als job **routing_poi** angegeben

    **Beispiel** mit <a href="https://curl.haxx.se/" target="_blank"><b>curl</b></a>:

    ```curl
    curl --request GET \
        -d 'job=routing_poi'
        -d 'job_id=j19e57f40c4a44755a32078d9ad7097f2'
        https://monitor.ioer.de/monitor_api/sora/services
    ```

    rechnet der Service noch wird z.B. folgende JSON als Response geliefert:
    
    ```json
    {
        "jobId": "jc7e4847561014440a06cd6e4cf2e7fb2",
        "jobStatus": "esriJobExecuting",
        "messages": [
        {
        "type": "esriJobMessageTypeInformative",
        "description": "Submitted."
        },
        {
        "type": "esriJobMessageTypeInformative",
        "description": "Executing..."
        }
        ]
    }
    ```
    ist der Service fertig wird z.B. folgender Response geliefert:

    ```json
    {
        "paramName": "outputJSON",
        "dataType": "GPString",
        "value": [
            {
                "x": "4339535,6687453",
                "y": "2765964,35707297",
                "id": "5297",
                "values": [
                    {
                        "endpoint": {
                            "x": "4339346.90443",
                            "y": "2766960.30919"
                        },
                        "distance_open_route": {
                            "value": "1559.4",
                            "unit": "m"
                        },
                        "duration_open_route": {
                            "value": "1122.8",
                            "unit": "s"
                        }
                    }
                ]
            }
        ]
    }
    ```

Die Ergebnis-JSON beinhaltet die Koordinaten der nächstgelegenen Grünfläche und die Distanz zu dieser in Meter. Auf der Grundlage des definierten Profiles wird auch die benötigte Zeit angegeben.

### 3. Routing zwischen zwei Koordinaten {#service-poi}
#### Beschreibung
Ähnlich des Services _**Suche nach den räumlich nächst gelegenen POI**_ berechnet dieser Service die Route und die Distanz zwischen zwei Punkten. Hier werden jedoch der Anfangs- und der Endpunkt übergeben, wodurch die Suche nach dem nächst gelegenen POI entfällt.

**Endpoint:** https://monitor.ioer.de/monitor_api/sora/services

1. **Post** der JSON-Datei:
    - **Query String**: **job**=routing_xy
    - **Datei**: folgend eine beispielhafte JSON Datei, welche alle notwendigen Parameter übermittelt

    ```json
        {"coordinates":
            [{"id":5297,
                "startpoint":
                     {"x":"4583809.29","y":"3108954.51"},"endpoint":{"x":"4584822.66","y":"3109658.40"}
            },
            {"id":5298,
                "startpoint":
                    {"x":"4580902.38","y":"3110862.75"},"endpoint":{"x":"4583809.29","y":"3108954.51"}
            }],
        "options":[{"profile":"walking","epsg":{"input":"3035","output":"25833"}}]}
    ```
    - Die Optionen innerhalb der JSON sind gleich des Servies **routing_poi**

2. **GET** des Ergebnisses:
   - Wie im Service _coodinates_, nur jetzt als job **routing_xy** angegeben

## RDF {#rdf}

_Das Resource Description Framework (RDF, engl. sinngemäß „System zur Beschreibung von Ressourcen“) bezeichnet eine technische Herangehensweise im Internet zur Formulierung logischer Aussagen über beliebige Dinge (Ressourcen). Ursprünglich wurde RDF vom World Wide Web Consortium (W3C) als Standard zur Beschreibung von Metadaten konzipiert. Mittlerweile gilt RDF als ein grundlegender Baustein des Semantischen Webs. RDF ähnelt den klassischen Methoden zur Modellierung von Konzepten wie UML-Klassendiagramme und Entity-Relationship-Modell. Im RDF-Modell besteht jede Aussage aus den drei Einheiten Subjekt, Prädikat und Objekt, wobei eine Ressource als Subjekt mit einer anderen Ressource oder einem Wert (Literal) als Objekt näher beschrieben wird._ [Quelle](https://de.wikipedia.org/wiki/Resource_Description_Framework){:target="blank"}

Mit der Beschreibung der Indikatoren und deren Kategorien in **RDF**, wurde der Beschluss innerhlab der SoRa Projektmitglieder umgesetzt, eine Beschreibung der Metadaten in RDF durchzuführen. Zur Arbeit mit diesem Format wurde die _Python_-Bibliothek [_rdflib_](https://github.com/RDFLib/rdflib){:target="blank"} eingesetzt.
### Indikator {#ind}

[RDF-Abfrage]({{site.baseurl}}/assets/data/ind.ttl){:target="blank"}{: .btn .btn-purple }
[Code](https://github.com/ioer-dresden/monitor-api/blob/master/app/sora/model/Indicator.py){: .btn .btn-green }{:target="blank"}

Diese Klasse fragt bei der Instaziierung vom [**Monitor-Backend**](https://ioer-dresden.github.io/monitor-doku/docs/backend){:target="blank"} alle verfügbaren Indikatoren ab, welche im **Raster**-Format vorliegen. Iterativ werden diese Indikatoren und deren Metadaten dem Graphen hinzugefügt. 
### Category {#cat}

[RDF-Abfrage]({{site.baseurl}}/assets/data/cat.ttl){:target="blank"}{: .btn .btn-purple }
[Code](https://github.com/ioer-dresden/monitor-api/blob/master/app/sora/model/Category.py){: .btn .btn-green }{:target="blank"}

Ähnlich wie die Indikatoren fragt diese Klasse bei der Instaziierung vom [**Monitor-Backend**](https://ioer-dresden.github.io/monitor-doku/docs/backend){:target="blank"} alle verfügbaren Kategorien ab, welche für das **Raster**-Format vorliegen. Iterativ werden diese Kategorien und deren Metadaten dem Graphen hinzugefügt. 

## **Flask** {#flask}

[Github](https://github.com/ioer-dresden/monitor-api/tree/master/app/sora){:target="blank"}{: .btn .btn-purple }

Dieser Microservice wird für die Kommunikation zwischen Clienten und den _WPS_ eingesetzt, um die Anfragen so leicht wie möglich zu gestalten. Eine Dokumentation zu **Flask** ist unter dem folgenden [Link](http://flask.pocoo.org/){:target="blank"} zu finden.
Das nochfolgende UML bildet die verwnedeten Klassen ab, welche nachfolgend genauer Dokumentiert werden.

<iframe src="{{site.baseurl}}/assets/html/sora-flask.html" frameborder="0" allowfullscreen onload="this.width=screen.width*0.5;this.height=screen.height*0.55;"></iframe>

### routes.py

Hier erfolgt des **Request-Mapping**, indem genau defineirt wird, was bei welchen **query** passiert. Im nachfolgenden Code-Block ist der Quellcode abgebildet.

```python
#RDF-Schnittstelle um sich die Indikatoren als .ttl auszugeben
@sora.route("/indicator", methods=['GET', 'POST'])
def get_indicators():
    indicator = Indicator(json_url=url)
    try:
        res = indicator.g
    except Exception as e:
        return abort(500)
    if len(res) == 0:
        return abort(404)
    else:
        return Response(res.serialize(format="turtle"), mimetype="text/n3")

#RDF-Schnittstelle um sich die Kategorien als .ttl auszugeben
@sora.route("/category", methods=['GET', 'POST'])
def get_categories():
    category = Category(json_url=url)
    try:
        res = category.g
    except Exception as e:
        return abort(500)
    if len(res) == 0:
        return abort(404)
    else:
        return Response(res.serialize(format="turtle"), mimetype="text/n3")

#Ausgabe der RDF-Ontologie des IÖR
@sora.route("/ontology", methods=['GET', 'POST'])
def get_ontology():
    dir = os.getcwd()
    graph = Graph().parse("{}/app/sora/data/ontology.ttl".format(dir), format="turtle")
    response = Response(graph.serialize(format="turtle"), mimetype='text/n3')
    response.headers['Access-Control-Allow-Origin'] = '*'
    return response

#Schnittstelle um die Esri-Geoprocessing Dienste zu nutzen
@sora.route('/services', methods=['GET', 'POST'])
def get():
    job = request.args.get('job') or None
    values = request.get_data() or None
    job_id = request.args.get('job_id') or None
    # test if JSON is valid
    try:
        # validate json
        # set request and get response from esri server
        request_handler = ESRIServerManager(job, values=values, job_id=job_id)
        app.logger.debug("result: \n%s", str(request_handler.get_request()))
        return request_handler.get_request()
    except Exception as e:
        if job == None:
            return jsonify(error='no job query, API-Doku: https://ioer-dresden.github.io/monitor-api-doku/docs/sora')
        else:
            return InvalidUsage(e,status_code=410)

# Error handling
@sora.errorhandler(InvalidUsage)
def handle_invalid_usage(error):
    response = jsonify(error.to_dict())
    response.status_code = error.status_code
    return response
```

Da die Code Kommentare schon sehr gut wiedergeben was funktionale Betsandteile sindb g, soll nicht einzeln beschrieben werden was die instanziierten Klassen tun, dies ist nachfolgende dokumentiert.

### EsriServerManager

[Code](https://github.com/ioer-dresden/monitor-api/blob/master/app/sora/ESRIServerManager.py){: .btn .btn-green }{:target="blank"}

Diese Klasse hat die Aufgabe anhand der übergebenen _Job-ID_ den entsprechenden Service auf dem [**Esri-Server**](#edn) aufzurufen und das Ergebnis an den Clienten zu streamen. Treten dabei Fehler (Exceptions) auf oder ist die Anfrage durch den Clienten falsch formuliert, wird eine entsprechende Meldung als Response gesendet. Somit ist es dem Clienten möglich darauf zu reagieren.

## Esri-Server {#edn}

[Github](https://github.com/ioer-dresden/sora-services){:target="blank"}{: .btn .btn-purple }

Um arcpy auf dem Server einzubinden, ist man auf einen **ESRI-Server** angewiesen. Alle Geo-Prozessing Services wurden in **OOP-Python** geschrieben und mit [**_ArcMap_**](http://desktop.arcgis.com/de/arcmap/){:target="blank"} getestet. Die Veröffentlichung eines _Geo-Processing Services_ erfolgte nach der Offiziellen [Anleitung](https://enterprise.arcgis.com/de/server/latest/publish-services/windows/a-quick-tour-of-publishing-a-geoprocessing-service.htm){:target="blank"}.

### Abfrage von Indikatorwerten zu Koordinaten {#coordinates}

[**Rest-API**](https://edn.ioer.de/arcgis/rest/services/SORA/coordinates/GPServer){:target="blank"}{: .btn .btn-blue }

Mit diesem Service können für vorgegebene Koordinaten die jeweiligen Indikatorwerte abgefragt werden. Wird ein Bufferwert gesetzt, ermittelt der Service den Durchschnitt des Indikators innhalb des Buffer-Bereichs. Hierbei wird ein quadratische Buffer um jede Koordinate gesetzt, dessen Räumlicher Durchschnitt über den **Paramter** _buffer_ gesetzt werden kann ([siehe Paramter JSON](#json-paramter-coord)). Aus diesem _Buffer_ wird durch eine Umgebungsanalyse alle enthaltenen Pixelwerte ermittelt und deren Durchschnittliches Ergebnis an den Response für jede Koordinate angehangen. 
Nachfolgend ist für den Service das **UML** abgebildet:

<iframe src="{{site.baseurl}}/assets/html/sora-coordinates.html" frameborder="0" allowfullscreen onload="this.width=screen.width*0.5;this.height=screen.height*0.55;"></iframe>



