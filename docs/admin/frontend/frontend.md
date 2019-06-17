---
layout: default
title: Frontend
parent: Admin
---

Das Frontend wurde mit der JavaScript-Bibliothek [**React**](https://reactjs.org/){:target="blank"} verwirklicht. Es besteht hauptsächlich aus 3 Modulen, welche in diesem Kapitel vorgestellt werden. Alle vorgestellten Klassen erben von der Abstrakten Klasse [**React.Component**](https://reactjs.org/docs/react-component.html){:target="blank"}, bis auf den Request Manager

<span class="fs-5">
[Screenshot]({{site.baseurl}}/assets/images/admin_dashboard_signed.png){:target="_blank"}{: .btn }
</span>

# Module

- [**Seiten-Menü**](#seidnav)
- [**OGC-Dienste**](#ogc)
- [**Navigations-Menü**](#navbar)

## Seiten-Menü {#seidnav}

 Über das Seiten-Menü wird zwischen den einzelnen Diensten hin- und hergeschaltet werden. Um den entsprechenden Dienst bei der _OnClick_-Methode abzufragen, werden alle Menüpunkte mit der **Data-Flag** Service (wcs,wfs,wms,geosn) versehen, welche für jeden Menüpunkt festlegt, welcher Dienst erstellt werden soll. 
