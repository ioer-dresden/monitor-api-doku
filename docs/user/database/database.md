---
layout: default
title: Datenbank
parent: User
---

# Datenbank

Das Backend verwendet für die Nutzerverwaltung die [**Postgre Datenbank**](https://www.postgresql.org/){:target="blank"}, welche auf dem Server installiert ist. Sie hat den Namen **_monitor_ogc_**.
Nachfolgend sind die Tabellen abgebildet, deren Persistenz über den [**_User_Loader_**](https://flask-login.readthedocs.io/en/latest/){:target="blank"} sichergestellt ist und damit den [**Nutzer**]({{site.baseurl}}/docs/user/backend/backend.html#user) abbildet.

Die Datenbank verwendet zum aktuellen Zeitpunk nur die Tabelle **_user_**, deren Struktur folgende abgebildet ist. Das Passwort ist verschlüsselt abgelegt und kann nicht eingesehen werden.


|#|Name|Typ|Länge|Null|Voreinstellung| Info |
|---|---|---|-----|----|--------------|-----|
|1|email|varchar| |Y| | Mail des Nutzers |
|2 |username | |varchar|Y| | Nutzername  |
|3 |password|varchar| |Y | | verschlüsselt abgelegtes Passwort |
|4 |lastname|varchar| |Y | | Familienname |
|5 |firstname|varchar| |Y | |  Name |
|6 |facility |varchar| |Y | | Einrichtung |
|8 |api_key |varchar| |Y | | generierter API-Key für doe OGC-Dienste |
|9 |id |int4|4|N|nextval('users_id_seq'::regclass) | |
|10|access|int4|4|Y|1 | **1** für Nutzer und **2** für Admin |
|11|business|varchar| |Y| | Geschäftsfeld |
|12|confirmed|bool |1|Y| | ist die Mail bestätigt ? Ja/Nein|
|13|confirmed_on|date|4|Y| | an welchem DAtum wurde die Mail bestätigt|
 
