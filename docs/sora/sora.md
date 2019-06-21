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

## Architektur

Alle Anfragen für das **SoRa**-Forschungsprojekt werden an die Monitor-API geschickt, wofür der [**Blueprints**](http://flask.pocoo.org/docs/1.0/blueprints/){:target="blank"} **sora** implementiert wurde. Innerhalb der _routes.py_ befindet sich das _RequestMapping_, welches für die entsprechenden **query strings** alle Anfragen beantwortet.

Auf dem [ESRI-Server](https://edn.ioer.de:6443/arcgis/manager/index.html){:target="blank"} wurden die Service-Layer implementiert. Der Flask Microservice auf _https://monitor.ioer.de/monitor_api/sora_ nimmt die Requests des _Gesis_- [**Location Mapping**]({{site.baseurl}}/assets/images/sora-arch.png){:target="blank"} an. Über die Klasse _Request-Manager_, werden die Anfragen.     

<iframe src="{{site.baseurl}}/assets/html/sora-architektur.html" frameborder="0" allowfullscreen onload="this.width=screen.width*0.5;this.height=screen.height*0.9;"></iframe>