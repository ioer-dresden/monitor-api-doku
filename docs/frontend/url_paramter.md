---
layout: default
title: URL Paramter
nav_order: 3
has_children: false
permalink: /docs/frontend/url_paramter
---
# URL Paramter

### Beschreibung
Dieses Objekt organisiert die Paramtrisierung der URL. Über den Paramter _urlparamter_ kann dieses aufgerufen werden.
In der folgenden Abbildung sind alle möglichen URL-Paramter abgebildet, welche gesetzt werden können

![Image]({{site.baseurl}}/assets/images/parameter_url.png)

### Funktionen

| Funktion |  Parameter | Beschreibung |
|-------------| -----------|-----------|
|**getAllUrlParamter**| |diese Funktion gibt alle gesetzten Paramter als *Array* zurück|
|**setUrlParameter**| String:key,String:value|Anhand der übergebenen Paramter, setzt diese Funktion einen Paramter. Der *key* steht für die Id, unter welchem der Wert abgelegt wird|
|**removeUrlParameter**| String:key| diese Funktion entfernt einen Paramter und deren Werteausprägung aus der URL|
|**getUrlParameter**|String:key| gibt den Wert eines Parameters aus der URL zurück|
|**updateURLParameter**|String:key,String:value| setzt einen Paramter mit der gewünschten ID neu|
|**getURLMonitor**| | gibt die Host Adresse zurück |