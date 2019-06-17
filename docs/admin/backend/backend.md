---
layout: default
title: Backend
parent: Admin
---

# Backend

Die Hauptlast bei für die Administrierung der Dienste liegt im Backend. Welche in diesem Kapitel anhand ihrer Klassen und Schichten dokumentiert werden soll.

- [**Services**](#services)
- [**Interfaces**](#interfaces)
- [**Models**](#models)
- [**routes**](#routes)

## Services {#services}

Die Services übernehmen die Instanziierung der Objekte und erstellen die vom Clienten über das Dashboard gewünschten Dienste über die _REST_-Schnittstelle ([**_routes.py_**](#routes). Für die OGC-Dienste wurde das [_Factory_](https://jaxenter.de/mit-factory-pattern-designprobleme-losen-5040)-Pattern eingesetzt, welches je nach Parametrisierung, den dafür notwendigen **_OGC_**-Layer instanziiert.