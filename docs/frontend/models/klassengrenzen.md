---
layout: default
title: Klassengrenzen
parent: Models
grand_parent: Frontend
---
# Klassengrenzen
Dieses Objekt übernimmt die Verwaltung der Klassengrenzen, welche angeben welche farbliche Ausprägung der jeweilige Indikatorwert bekommt.

### Paramter

| Paramter | Beschreibung |
| -------- | ------------ |
| **klassen**:Object |alle Klassen und deren farbliche Ausprägung als JSON-Objekt |

Beispiel für ein generiertes **_klassen_**-JSON Objekt:
```json
[{
	"Untergrenze": "0",
	"Obergrenze": 3,
	"Wert_Untergrenze": 06.3,
	"Wert_Obergrenze": 08.3,
	"Farbwert": "ffff99"
}, {
	"Untergrenze": 3,
	"Obergrenze": 6,
	"Wert_Untergrenze": 08.3,
	"Wert_Obergrenze": 10.2,
	"Farbwert": "f8db83"
}, {
	"Untergrenze": 6,
	"Obergrenze": 8,
	"Wert_Untergrenze": 10.2,
	"Wert_Obergrenze": 11.4,
	"Farbwert": "f0b66d"
}, {
	"Untergrenze": 8,
	"Obergrenze": 10,
	"Wert_Untergrenze": 11.4,
	"Wert_Obergrenze": 12.7,
	"Farbwert": "e99257"
}, {
	"Untergrenze": 10,
	"Obergrenze": 12,
	"Wert_Untergrenze": 12.7,
	"Wert_Obergrenze": 14,
	"Farbwert": "e26d42"
}, {
	"Untergrenze": 12,
	"Obergrenze": 23,
	"Wert_Untergrenze": 14,
	"Wert_Obergrenze": 20.9,
	"Farbwert": "db492c"
}, {
	"Untergrenze": 23,
	"Obergrenze": 82,
	"Wert_Untergrenze": 20.9,
	"Wert_Obergrenze": 58.3,
	"Farbwert": "d32416"
}, {
	"Untergrenze": 82,
	"Obergrenze": 100,
	"Wert_Untergrenze": 58.3,
	"Wert_Obergrenze": 69.7,
	"Farbwert": "cc0000"
}]
```

### Funktionen

![Legende]({{site.baseurl}}/assets/images/uml_klassengrenzen.png)

| Funktion | Paramter | Beschreibung |
| ---------|----------|--------------|
|**setKlassen**| Object| setzt das Klassenobjekt neu |
|**getKlassen**| | gibt das gesetzte oder generierte Klassen-Objekt zurück |
|**getMax** | | gibt die Maximale Klassengrenze als Wert aus |
|**getMin** | | gibt die Minimale Klassengrenze als Wert aus |
|**getMaxColor** | | gibt den Minimalen Farbwert zurück |
|**getMinColor** | | gibt die Maximalen Farbwert zurück| 
|**getColor** | float/int:indikatorwert| gibt den Farbwert als **HEX** entsprechend des übergebenen Wertes zurück z.B. ``#28aa11``|
|**toString**| | gibt das Klassen-JSON Objekt als String zurück |