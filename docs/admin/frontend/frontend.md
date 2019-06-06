---
layout: default
title: Frontend
parent: Admin
---

Das Frontend wurde mit der JavaScript-Bibliothek [**React**](https://reactjs.org/){:target="blank"} verwirklicht. Es besteht hauptsächlich aus 3 Modulen, welche in diesem Kapitel vorgestellt werden. Zur Kommunikation mit dem [**Backend**]({{site.baseurl}}/docs/admin/backend/backend.html) wurde ein [**Request Manager**](#rqm) implementiert, welcher [**Axios**](https://github.com/axios/axios){:target="blank"} einsetzt.

# Inhalt

- [**Seiten-Menü**](#seidenav)
- [**OGC-Dienste**](#ogc)
- [**Navigations-Menü**](#navbar)
- [**Request Manager**](#rqm)

##  React-Module
### Seitenmenü
Das Seitenmenü hat die Hauptaufgabe die Funktionalitäten zum hin- und herschalten zwischen den einzelnen Funktionen zu ermöglichen und bei einem _OnClick_-Event ein neues Modul [**_OGC_**](#ogc) zu erstellen. Hierfür wird in dem **_Data_**-Paramter der entsprechende Schlüssel abgefragt und übergeben.
In der nachfolgenden Tabelle sind die Paramter für die Erstellung aufgelistet:

|id|Funktion|
|--|--------------|
|wms| **_Web Map Service_** Modul|
|wfs|**_Web Feature Sservice_** Modul|
|wcs|**_Web Coverage Service_** Modul|
|geosn|**_GeoMIS_** Modul|

Das Schlüsselwort wird dass als _React_-Property an das **OGC-Modul** übergeben. 