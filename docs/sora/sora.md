---
layout: default
title: Sora
nav_order: 3
has_children: true
permalink: /docs/sora
---
# [SoRa](http://www.sora-projekt.de/){:target="blank"} - Sozial-Raumwissenschaftliche Forschungsdateninfrastruktur

Da es sich bei Sora um ein reines Backend handelt, wird auch nur dieses vorgestellt. 
Das Backend hat vor allem die Aufgabe die im SoRa Projekt gestellten Anforderungen zu erfüllen indem **freie Nutzeranfragen auf topografischen Geobasisdaten beantwortet werden. Diese werden vom Clienten gestellt.

# Inhalt

- [**Flask** (monitor.ioer.de)](#flask)
    - [**REST-Schnittstelle**](#rest)
- [**Esri-Server** (edn.ioer.de)](#edn)

## Architektur

Alle Anfragen für das **SoRa**-Forschungsprojekt werden an die Monitor-API geschickt, wofür der [**Blueprints**](http://flask.pocoo.org/docs/1.0/blueprints/){:target="blank"} **sora** implementiert wurde. Innerhalb der _routes.py_ befindet sich das _RequestMapping_, welches für die entsprechenden **query strings** alle Anfragen beantwortet.

Auf dem [ESRI-Server](https://edn.ioer.de:6443/arcgis/manager/index.html){:target="blank"} wurden die Service-Layer implementiert. Der Flask Microservice auf _https://monitor.ioer.de/monitor_api/sora_ nimmt die Requests des _Gesis_- [**Location Mapping**]({{site.baseurl}}/assets/images/sora-arch.png){:target="blank"} an. Über die Klasse _Request-Manager_, werden die Anfragen gestellt. Das die Kommunikation mit einem _WPS_ teilweise sehr kompliziert sein kann, wurde diese Service-Schicht implementiert.       

<iframe src="{{site.baseurl}}/assets/html/sora-architektur.html" frameborder="0" allowfullscreen onload="this.width=screen.width*0.5;this.height=screen.height*0.9;"></iframe>

## **Flask** {#flask}

Dieser Microservice wird für die Kommunikation zwischen Clienten und den _WPS_ eingesetzt, um die Anfragen so leicht wie möglich zu gestalten. Eine Dokumentation zu **Flask** ist unter dem folgenden [Link](http://flask.pocoo.org/){:target="blank"} zu finden.

### REST-Schnittstelle {#rest}
_REST steht für REpresentational State Transfer, API für Application Programming Interface. Gemeint ist damit ein Programmierschnittstelle, die sich an den Paradigmen und Verhalten des World Wide Web (WWW) orientiert und einen Ansatz für die Kommunikation zwischen Client und Server in Netzwerken beschreibt._ [Quelle](https://www.cloudcomputing-insider.de/was-ist-eine-rest-api-a-611116/){:target="blank"}

Diese Schnittstelle wurde sowohl auf dem **Esri-Server** als auch auf dem **Flask**-Microservice eingesetzt. Da bei allen durch das Projekt genutzten Diensten z.T. sehr große 

### Abfrage von Indikatorwerten zu Koordinaten {#coordinates}

Mit diesem Service können für vorgegebene Koordinaten die jeweiligen Indikatorwerte abgefragt werden. Wird ein Bufferwert gesetzt, ermittelt der Service den Durchschnitt des Indikators innhalb des Buffer-Bereichs. Hierbei wird ein quadratische Buffer um jede Koordinate gesetzt, dessen Räumlicher Durchschnitt über den **Paramter** _buffer_ gesetzt werden kann ([siehe Paramter JSON](#json-paramter-coord)).

<iframe src="{{site.baseurl}}/assets/html/sora-coordinates.html" frameborder="0" allowfullscreen onload="this.width=screen.width*0.5;this.height=screen.height*0.55;"></iframe>

Über die **REST**-Schnittstelle [_https://monitor.ioer.de/monitor_api/sora/services_](https://monitor.ioer.de/monitor_api/sora/services){:target="blank"} wird der Dienst angesprochen.


## Esri-Server {#edn}

[Github](https://github.com/ioer-dresden/sora-services){:target="blank"}{: .btn .btn-purple }

Um arcpy auf dem Server einzubinden, ist man auf einen **ESRI-Server** angewiesen. Alle Geo-Prozessing Services wurden in **OOP-Python** geschrieben und mit [**_ArcMap_**](http://desktop.arcgis.com/de/arcmap/){:target="blank"} getestet. Die Veröffentlichung eines _Geo-Processing Services_ erfolgte nach der Offiziellen [Anleitung](https://enterprise.arcgis.com/de/server/latest/publish-services/windows/a-quick-tour-of-publishing-a-geoprocessing-service.htm){:target="blank"}.
