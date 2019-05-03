---
layout: default
title: Backend
parent: User
---
## Inhalt
- [routes.py](#routes)
    - [route**(/user)**](#main)
    - [route**(/login)**](#login)
    - [route**(/signup)**](#signup)
    - [route **(/confirm/token)**](#mail_confirm)
    - [route **(/services)**](#services)
    - [route **(/reset)**](#reset)
    - [route **(/reset/token)**](#reset-token)
- [Models](#models)
    - [Mailer](#mailer)
    - [User](#user)
    - [Token](#token)
- [Sicherheit](#security)
    - [URL-Manipulierung](#url-manipulation)
    - [SQL-Injektion](#sql-injection)
## routes.py {#routes}
_Flask selbst ist kaum mehr als ein Bindeglied, das die WSGI-Schicht und die Template-Bibliothek zusammenhält. Insbesondere kümmert es sich um das sogenannte Routing, das URLs auf Funktionen abbildet._ ([admin-magazin](https://www.admin-magazin.de/Das-Heft/2013/01/Webanwendungen-mit-Flask){:target="_blank"})
### route**(/)** {#main}
Dies ist die sogenannte **Main**-Route, hier wird lediglich aus dem Dateiorder **user** innerhalb des **[static]({{site.baseurl}}/docs/static)**-Verzeichnisses die _index.html_ gerendert.
### route**(/user)**
Mit der Funktion _get_service()_ werden dem Nutzer die OGC-Dienste über die **API** bereitgestellt. Über einen _GET_ request müssen die **URL-Parameter** an den Endpoint gesendet werden. Durch den vorgeschalteteten Service wird der direkte Zugriff auf den **MapServer** blockiert und nur angemeldete Nutzer können die OGC-Dienste nutzen.

Ein Beispiel für einen OGC-Zugriff wäre folgende Parametrisierung der URL: 
- https://monitor.ioer.de/monitor_api
- **/user?**
- **id**=_F13RG**&** : Indikator ID
- **key**=_5HbHOLs7eKQmHJ8r5og2P91qr9tAviBO_**&** : Generierter API-Key des Nutzers, welcher über die Website erstellt wurde
- **service**=_wcs_ : Gibt an, welcher OGC-Service genutzt werden soll (wfs,wcs, wms)

Alle URl und Indikatoren lassen sich auch direkt über die Tabellenansicht im **[Userbereich]({{site.baseurl}}/docs/user/frontend/frontend.html)** anzeigen.

Im folgenden Diagramm ist die Funktionsweise des Streaming-Dienstes dokumentiert.
![getService]({{site.baseurl}}/assets/images/get_service.png)

Im ersten Schritt werden aus der URL die entsprechenden Parameter herausgefiltert. Dann wird geprüft ob der API-Key korrekt ist, indem die in der Datenbank hinterlegten- mit den angefragten Werten verglichen werden. Ist der API-Key fehlerhaft, wird eine entsprechende Fehlermeldung an den Clienten zurück geschickt. Ist der Key korrekt wird die Anfrage an den [MapServer](https://mapserver.org/) für den entsprechenden Service gestellt und der Response an den Clienten gestreamt. 
### route**(/login)** {#login}

[Login](https://monitor.ioer.de/monitor_api/login){: .btn}{:target="_blank"}

Diese Methode baut auf die Flask-Bibliothek **[Flask-Login](https://flask-login.readthedocs.io/en/latest/)** auf und behandelt das einloggen des Nutzers über das entsprechende Formular. 
Innerhalb der Methode werden dabei nacheinander folgende Bedingungen überprüft:
1. Stimmen Username und Passwort überein
2. Welche Rechte hat der Nutzer &rarr; **1** für User und **2** für Admin, welche in der **[Datenbank]({{site.baseurl}}/docs/user/database/database.html)** unter der Tabelle _users_ in der Spalte _access: abgelegt.

![getService]({{site.baseurl}}/assets/images/login.png)
### route **(/signup)** {#signup}

[Registrierung](https://monitor.ioer.de/monitor_api/signup){: .btn}{:target="_blank"}

Diese Methode hat die Aufgabe die von Nutzer eingetragenen und an den HTTP-Endpoint geschickten Daten in der **[Datenbank]({{site.baseurl}}/docs/user/database/database.html)** zu speichern. Eine weitere Aufgabe besteht darin zu prüfen, ob der Nutzername oder die Mailadresse bereits in der Datenbank vorhanden ist, in diesem Fall wird eine entsprechende Fehlermeldung an den Clienten zurückgegeben.
Stimmen alle Felder überein wird eine neue Instanz der Klasse-[User](#user) erzeugt und diese in der Datenbank abgelegt. Für das Speichern in der **DB** wird die Bibliothek **[ Flask-SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/){:target="_blank"}** verwendet.

Ein Beispiel für das programmatische Anlegen eines neuen Nutzers ist im folgenden Beispiel aufgeführt:
```python
new_user = User(username=username, email=email, password=hashed_password, lastname=form.lastname.data,
                firstname=form.firstname.data, facility=form.facility.data, access=1,
                business=form.business.data, confirmed=False)
db.session.add(new_user)
``` 
Wurde der Nutzer angelegt, wird eine Mail an seine angegebene Adresse geschickt, um den Account zu verifizieren. Diese Aufgabe wird von der **Route** _[Mail-Confirm](#mail_confirm)_ übernommen.

Für den Clienten wird das _Template_ **signup.html** aus dem **[static]({{site.baseurl}}/docs/static)**-Verzeichnis gerendert, über dieses erfolgt die Eingabe. Fehler werden durch **[Flask-Markup]()** in das template eingefügt.

### route **(/confirm/token)** {#mail_confirm}
Diese **Route** ruft die Methode _confirm_email(token_pass)_ auf, ihr muss der generierte [Token](#token) übergeben werden. Hierbei wird die Email-Adresse anhand des generierten Tokens überprüft. Der Nutzer bekommt nach seiner Anmeldung eine Mail mit dem Registrierungslink, beim anklicken wird er an diesen Enpoint geleitet.
Ist der Token korrekt wird in der **[Datenbank]({{site.baseurl}}/docs/user/database/database.html)** in der Tabelle _users_ die Spalte _confirmed_ auf **True** gesetzt. 
Am Schluss wird der User ausgeloggt, um sich anzumelden.

Bei einem Fehler wird die entsprechende Fehlermeldung angezeigt.

Für den Clienten wird das _Template_ **confirmed_mail.html** aus dem **[static]({{site.baseurl}}/docs/static)**-Verzeichnis gerendert. 

### route **(/services)** {#services}
Diese **Route** hat die Aufgabe aus dem **[static]({{site.baseurl}}/docs/static)**-Verzeichnis dem Clienten alle Services aufzulisten. Hierfür wird die _service.html_ gerendert. Die Funktion prüft dabei ob der Nutzer angemeldet ist, um auch nur berechtigte Zugriffe zuzulassen. Ist der Nutzer nicht angemeldet, wird die _login.html_ gerendert.

### route **(/reset)** {#reset}

[Passwort zurücksetzen](https://monitor.ioer.de/monitor_api/reset){: .btn}{:target="_blank"}

![uml]({{site.baseurl}}/assets/images/reset.png)

Diese **Route** hat die Aufgabe das zurücksetzen des Passwortes zu steuern. Hat der Nutzer seine Emailadresse eingegeben, wird überprüft ob dieses in der Datenbank hinterlegt ist.  Trifft dies zu wird ein Token generiert und mit der Hilfe der [**Mailer**](#mailer)-Klasse das Passwort an die angegebene Mailadresse geschickt. der generierte Token wird dabei mit der Hilfe der [**Token**](#token)-Klasse erstellt und wird aus der Mailadresse generiert.

### route **(/reset/token)** {#reset-token}

Diese Route wird aufgerufen, wenn der Nutzer seine Mail zum zurücksetzen des Passwortes bekommen hat und den darin enthaltenen Link klickt.

Ein Beispiel Link wäre folgender:
```url
https://monitor.ioer.de/monitor_api/reset/ImwubXVjaGFAaW9lci5kZSI.D6xN-w.Ik_GXgQuTDrleuX_Skg_YHr9K5Y
``` 

Im ersten Schritt wird der übergebene Token überprüft, ist er korrekt kann das Passwort geändert werden. Ist der Token **nicht korrekt** oder **ausgelaufen** (siehe [**Token**](#token)) 

Durch das gerenderte HTML-Dokument _reset_passwort.html_ aus dem  **[static]({{site.baseurl}}/docs/static)** bekommt der Nutzer die Möglichkeit ein neues Passwort anzugeben.

## Models {#models}

### Mailer {#mailer}
Diese Klasse übernimmt im User-Service die Aufgabe alle notwendigen Mails zu verschicken, über die Methode **_send_mail_** wird ein neues **Mail**-Objekt instanziiert, aus der Bibliothek [**Flask-Mail**](https://pythonhosted.org/Flask-Mail/){:target="_blank"} und an den angegebenen User (_to_) gesendet. Der Inhalt der Mail ist dabei innerhalb des _template_ fest vorgegeben und befindet sich im **[static]({{site.baseurl}}/docs/static)** als _reset_password_mail.html_.

![uml]({{site.baseurl}}/assets/images/mailer.png)

### User {#user}
Diese Klasse erbt von **[UserMixin](https://flask-login.readthedocs.io/en/latest/)** und **[db.Model](https://flask-login.readthedocs.io/en/latest/)** und ist das _Grundgerüst_ eines neuen/angemeldeten Nutzers. Die KLassen-Variable **_confirmed_** gibt hierbei wieder ob dem Nutzer seine Mailadresse bestätigt ist oder nicht. Default ist Sie **None**.

![uml]({{site.baseurl}}/assets/images/user.png)

| Methodenname | Parameter | Return | Info |
|------------|---------|------|----|
| __init__ | username, password, email,lastname,firstname,facility,access,business,confirmed,confirmed_on=None | Constructor :wink: | Diese Methode erstellt eine neue Instanz der Klasse User, hierbei ist der Parameter confirmed_on als **Default** _None_. |
|  is_authenticated | |Boolean:state | Methode welche wiedergibt, ob der Nutzer authentifiziert ist, also ob seine Mail korrekt ist |
| is_active | | String:access | gibt wieder, welche rechte der Nutzer besitzt &rarr; **TODO**|
| is_anonymous | |Boolean:state | zeigt an ob der Nutzer einen Anonymen Status besitzt &rarr; **TODO** |
| get_id | | int:id | gibt die **Id** des Nutzers zurück |  

### Token {#token}
Diese Klasse regelt das Verifizieren und erstellen eines **Token**. Sie wird hierbei bei der Überprüfung der Mailadresse des Nutzers eingesetzt.  Für die Serilizierung wurde die Bibliothek **[itsdangerous](https://pythonhosted.org/itsdangerous/){:target="_blank"}** eingesetzt.

![token]({{site.baseurl}}/assets/images/token.png)

| Methodenname | Parameter | Return | Info |
|------------|---------|------|----|
| generate_confirmation_token() | String:email | String:token | Methode, welche aus der übergebenen Mailadresse einen Token erstellt |
| confirm_token | String:token, Int:expiration | String:email | Methode welche den Token zu der übergebenen Mail transformiert. Anhand der _expiration_ kann festgelegt werden, wie lange der Token gültig ist. Default ist **3600**|

## Sicherheit {#security}
In diesem Kapitel wird der Punkt Sicherheit der API-Dokumentiert, hierbei wurde größtenteils auf die _On-Board_ Mittel der _Flask_-Library zurückgegriffen.

### URL-Manipulierung {#url-manipulation}
Um auch nur berechtigte Nutzer die jeweilige URL zur Verfügung zu stellen, wurde der Dekorator _**@login_required**_ aus der Bibliothek **[Flask-Login](https://flask-login.readthedocs.io/en/latest/){:target="_blank"}** eingesetzt.
Ist der Nutzer nicht angemeldet wird er zurück auf die Anmeldeseite geleitet. Somit ist es nicht möglich unangemeldet auf die **[OGC-Service Seite](#services)** zuzugreifen.<br>
Anbei die Fallunterscheidung:
```python
    if current_user.is_authenticated:
        return render_template('user/services.html', key=current_user.api_key, access=current_user.access)
    else:
       return redirect("{}login".format(Config.URL_ENDPOINT))
```
### SQL-Injektion {#sql-injection}
_SQL-Injection (dt. SQL-Einschleusung) bezeichnet das Ausnutzen einer Sicherheitslücke in Zusammenhang mit SQL-Datenbanken, die durch mangelnde Maskierung oder Überprüfung von Metazeichen in Benutzereingaben entsteht._ [Wikipedia](https://de.wikipedia.org/wiki/SQL-Injection)

Dies kann beispielsweise auftreten wenn Passwort und Username von der Datenbank abgefragt werden und dabei der Template String für den Username so manipuliert wird, das immer **True** zurückgegeben wird.
In der Anwendung wurde die Bibliothek **[ Flask-SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/2.x/){:target="_blank"}** eingesetzt. Welche die entsprechenden Nutzer herausfiltert.<br/>
Am Besipiel wurde die Email übergeben und der Nutzer aus der **DB** authentifiziert.
```python
user = User.query.filter_by(email=email).first()
```