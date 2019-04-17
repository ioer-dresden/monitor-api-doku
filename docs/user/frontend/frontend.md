---
layout: default
title: Frontend
parent: User
---
## Technologien
### Registrierung und Anmeldung
Die Gestaltung der Graphischen Benutzeroberfläche wurde mit **[Bootstrap](https://getbootstrap.com/docs/4.3/getting-started/introduction/){:target="_blank"}** realisiert.
![Legende]({{site.baseurl}}/assets/images/register_view.png)
Hiermit konnte das _Styling_ der GUI-Elemente an die Bibliothek abgegeben werden, welche auch den Responsive Part übernimmt.
Diese Technologie wurde auch in der Anmeldung eingesetzt.
### Nutzerbereich
1. **ApiKey.jsx** <br/>
Um einen _Key_ zu Generieren muss der Nutzer den entsprechenden Button klicken. Dieser wird vom _Flask_ Backend generiert. Die Kommunikation erfolgt dabei über **HTTPS-GET** mit der Unterstützung von **[JQuery-Ajax](http://api.jquery.com/jquery.ajax/)**. Der Generierungsvorgang wird im Abschnitt [Backend]({{site.baseurl}}/docs/user/backend/backend.html) dokumentiert.
Die Klasse **ApiKey** erbt dabei von _[React.Component](https://reactjs.org/docs/react-component.html)_.
In der folgenden Tabelle ist die Klasse **ApiKey** dokumentiert:

|Methode | Values | Return | Info |
|--------|--------|--------|-------|
|**constructur** | props | Constructor :laughing:	 |  |

