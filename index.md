---
layout: default
title: Start
nav_order: 1
permalink: /
---

# Dokumenation der Monitor API

Dieses Backend wurde mit dem _Web-Framework_ **[Flask](http://flask.pocoo.org/){:target="_blank"}** serverseitig umgesetzt, auf der Clienten-Seite kommt **[React](https://reactjs.org/){:target="_blank"}** zum Einsatz. Die Kommunikation via **HTTPS** erfolgt mit den Bibliotheken **[JQuery](https://jquery.com/){:target="_blank"}** und **[Axios](https://github.com/axios/axios){:target="_blank"}**.

Die einzelnen Module sind über sogenannte _[Blueprints](http://flask.pocoo.org/docs/1.0/blueprints/)_ voneinander getrennt.
In der folgenden Tabelle sind diese zusammengefasst:

|Name | Route | Zweck |
|-----|-------|-------|
|[Admin](https://ioer-dresden.github.io/monitor-api-doku/admin)| **/admin** | Verwaltung des Monitors und der OGC-Services, nur für angemeldetet Nutzer|
|[SoRa](https://ioer-dresden.github.io/monitor-api-doku/sora)| **/sora** | Dienste für das Forschungsporjekt [SoRa](http://www.sora-projekt.de/){:target="_blank"}|