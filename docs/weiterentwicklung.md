---
layout: default
title: Weiterentwicklung
has_children: false
nav_order: 10
---

# Weiterentwicklung der API

In diesem Kapitel wird die Weiterentwicklung der Monitor-API diskutiert, welche möglichen Aufgabe diese übernehmen kann und was noch verändert werden muss.

- ### Aufsplitten der Blueprints

Durch die große Arbeitslast durch das SoRa-projekt sollte dieses in einen neuen Microservice gekapselt werden. Allegemein wäre zu überlegen ob nicht die API in Ihrer Gesamtheit aufgesplittet wird, in mehrere Microservices.

- ### Einführung von [OAuth](https://docs.authlib.org/en/latest/){:target="blank"}

_OAuth (Open Authorization) ist ein offenes Protokoll, das eine standardisierte, sichere API-Autorisierung für Desktop-, Web- und Mobile-Anwendungen erlaubt._[Quelle](https://de.wikipedia.org/wiki/OAuth){:target="blank"}

Mit der Verwendung dieser API wäre es möglich, die Nutzerverwaltung auch für den Monitor auszuweiten und dem **angemeldeteten** Nutzer spezifische Inhalte und Funktionen zu bieten.

- ### Ausbau des Admin-Bereichs und OGC-Services

Im Moment ist es nur möglich einen Update der Dienste anzubieten. Perspektivisch wäre es auch wichtig die API um die REST-[**CRUD**](https://de.wikipedia.org/wiki/CRUD){:target="blank"} Methoden zu erweitern und diese in der GUI anzubieten. 

Ein weiterer Punkt wäre es, alle Aufgaben des [**IÖR-Monitor-Backends**](https://ioer-dresden.github.io/monitor-doku/docs/backend){:target="blank"} auf den **Flask**-Microservice zu übertragen und viele kleine Dienste zu orchestrieren.