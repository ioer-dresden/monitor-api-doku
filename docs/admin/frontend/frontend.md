---
layout: default
title: Frontend
parent: Admin
---

Das Frontend wurde mit der JavaScript-Bibliothek [**React**](https://reactjs.org/){:target="blank"} verwirklicht. Es besteht hauptsächlich aus 3 Modulen, welche in diesem Kapitel vorgestellt werden. Zur Kommunikation mit dem [**Backend**]({{site.baseurl}}/docs/admin/backend/backend.html) wurde ein [**Request Manager**](#rqm) implementiert, welcher [**Axios**](https://github.com/axios/axios){:target="blank"} einsetzt.

# Inhalt

- [**Seiten-Menü**](#seidenav)
- [**OGC-Dienste**](#ogc)
	- [**Update der Dienste**](#ogc-update)
- [**Navigations-Menü**](#navbar)
- [**Request Manager**](#rqm)
- [**ModalDialog**](#modal)

##  React-Module
### Seitenmenü {#seidenav}
Das Seitenmenü hat die Hauptaufgabe die Funktionalitäten zum hin- und herschalten zwischen den einzelnen Funktionen zu ermöglichen und bei einem _OnClick_-Event ein neues Modul [**_OGC_**](#ogc) zu erstellen. Hierfür wird in dem **_Data_**-Paramter der entsprechende Schlüssel abgefragt und übergeben.
In der nachfolgenden Tabelle sind die Paramter für die Erstellung aufgelistet:

|id|Funktion|
|--|--------------|
|wms| **_Web Map Service_** Modul|
|wfs|**_Web Feature Sservice_** Modul|
|wcs|**_Web Coverage Service_** Modul|
|geosn|**_GeoMIS_** Modul|

Das Schlüsselwort wird als _React_-Property an das **OGC-Modul** übergeben.
 
```javascript
const ogc =  < OGC service={service}/>;
            ReactDOM.render(
            ogc,
            document.getElementById('page_content'));
 ```

### OGC-Dienste {#ogc}
 
Über dieses Modul können die einzelnen Funktionalitäten ausgewählt werden, mit welchen der jeweils gewählte Service manipuliert werden kann. Im Moment sind alle dafür notwendige Funktionen:
 - **Update der Dienste**
 - **Neuen Dienst erstellen**
 - **Dienst löschen**
 - **Übersicht**
 
vorhanden, jedoch ist nur der **Upate der Dienste** implementiert, die anderen Funktionen dienen als Platzhalter.
 
#### Update der Dienste {#ogc-update}

Die Funktion hat die Aufgabe über den [**Request-Manager**](#rqm) den Update Prozess auf dem Backend anzustoßen. War dies erfolgreich gibt das _Backend_ die **_Ergebnis_**-JSON zurück. Diese wird zum besseren Verständnis mit dem [**Modal-Dialog**](#modal) als **Bootstrap**-Card View dargestellt. Aus der JSON-Datei können dabei die Informationen entnommen werden, welche verwendet werden um den Status der Erstellung zu visualisieren.
 In der folgenden Abbildung ist ein Dialogfenster für den **_GeoDN_** Service abgebildet und zeigt dabei auch eine fehlgeschlagene Erstellung eines Diesntes, welcher rot **hinterlegt** wurde.
 
![Geosn]({{site.baseurl}}/assets/images/geosn_dialog.jpeg)
 
### Request Manager {#rqm}
 
Diese Klasse übernimmt die Kommuikation mit dem **Backend** und weißt dieses an, die Aktualisierung der Dienste anzustoßen. Dafür wurde die Methode _updateOGCService()_ implementiert, welcher als Paratemeter der zu erstellende Dienst (wms,wcs,wfs,geosn) übergeben wird. Als **Ajax**-Bibliothek wurde [**Axios**](https://github.com/axios/axios){:target="blank"} eingesetzt. Nachfolgend ist der Code abgebilet.
 
```javascript

class RequestManager{
    static updateOGCService(_service){
        console.info("create services for: ",_service);
        return axios.post('https://monitor.ioer.de/monitor_api/admin/'+_service);
    }
}
```