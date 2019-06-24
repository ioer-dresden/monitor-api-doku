---
layout: default
title: Home
nav_order: 1
permalink: /
---

# Dokumenation der Monitor API

Dieses Backend wurde mit dem _Web-Framework_ **[Flask](http://flask.pocoo.org/){:target="_blank"}** serverseitig umgesetzt, auf der Clienten-Seite kommt **[React](https://reactjs.org/){:target="_blank"}** zum Einsatz. Die Kommunikation via **HTTPS** erfolgt mit den Bibliotheken **[JQuery](https://jquery.com/){:target="_blank"}** und **[Axios](https://github.com/axios/axios){:target="_blank"}**.

Die einzelnen Module sind über sogenannte _[Blueprints](http://flask.pocoo.org/docs/1.0/blueprints/)_ voneinander getrennt.
In der folgenden Tabelle sind diese zusammengefasst:

|Name | Route | Zweck |
|-----|-------|-------|
|[Admin]({{site.baseurl}}/docs/admin)| **/admin** | Verwaltung des Monitors und der OGC-Services, nur für angemeldete Nutzer|
|[User]({{site.baseurl}}/monitor-api-doku/user)| **/user** | Nutzerverwaltung für die Anmeldung an der API |

Die anderen Module haben folgende Aufgaben:

| Name | Zweck |
|------|-------|
| **static** | Implementierung auf der Clientenseite |
|**templates** | html Dateien für die jeweilige Seite + Email **Vorlagen**|

Das **Doplyoment** mit allen notwendigen Tools und Server-Pfaden ist in folgenden [**PDF**]({{site.baseurl}}/assets/data/deployment.pdf){:target="blank"} festgehalten.