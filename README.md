# CamundaBPM - Implementierung mit Fokus auf Send and Receive Task (Modeler only!)
## Allgemein
In dieser Implementierung wird ein Prozess in der Camunda Business Process Engine (CamundaBPM) implementiert. Der implementierte Prozess beinhaltet die folgenden Prozesselemente. 

1. Send Task
2. Receive Task
3. User Task
4. Default Sequence Flow
5. Inclusive Gateway
6. Message Startevent
7. Message Endevent

In diesem Beispiel werden keine zusätzlichen Java-Klassen oder NodeJS Scripte geschrieben. Der geschriebene Code beläuft sich auf wenige Zeilen (3) und ist darüberhinaus hoch wiederverwendbar. Alle die für den implementierten Prozess vorgesehenen Ressourcen befinden sich eingebettet in der XML der .bpmn-Datei dieses Repositories. 

## Requirements

Um den Prozess Lokal lauffähig zu bekommen, werden folgende Tools benötigt.

1. CamundaBPM (Apache Tomcat i.d.r unter `camunda-bpm-tomcat-7.10.0\server\apache-tomcat-9.0.12\bin`)
2. Camunda Modeler
3. Editor (Notepad++ o.ä)
4. Gmail Adresse
5. Konfigurationen und Einstellungen

Zunächst ist CamundaBPM zu installieren. Es ist sicherzustellen, dass die Umgebungsvariablen JAVA_HOME und JRE_HOME gesetzt sind.

Um CamundaBPM richtig (Konfiguration und Einstellungen) aufzusetzen werden folgende Pakete benötigt, welche i.d.r. unter folgendem Pfad abgelegt werden können. `..\camunda-bpm-tomcat-7.10.0\server\apache-tomcat-9.0.12\lib`. Ggf. befinden sich bereits einige Bibliotheken im Verzeichnis. Doppelte Bibliotheken (auch mit unterschiedlichen Versionen!) sind zu vermeiden. 

1. camunda-bpm-mail-core-1.2.0.jar
2. camunda-connect-core-1.1.0.jar
3. javax.mail-1.5.5.jar
4. slf4j-api-1.7.7.jar

Außerdem ist eine Konfigurationsdatei in das Verzeichnis camunda-bpm-tomcat-7.10.0\server\apache-tomcat-9.0.12\conf abzulegen:
Das folgende Beispiel kann so übernommen und mit einem Texteditor als `mail-config.properties` abgespeichert werden.

```
# send mails via SMTP
mail.transport.protocol=smtp

mail.smtp.host=smtp.gmail.com
mail.smtp.port=465
mail.smtp.auth=true
mail.smtp.ssl.enable=true
mail.smtp.socketFactory.port=465
mail.smtp.socketFactory.class=javax.net.ssl.SSLSocketFactory

# poll mails via IMAPS
mail.store.protocol=imaps

mail.imaps.host=imap.gmail.com
mail.imaps.port=993
mail.imaps.timeout=10000

# additional config
mail.poll.folder=INBOX
mail.sender=ContactTest@irgendwas.com
mail.sender.alias=CamundaUser

mail.attachment.download=true
mail.attachment.path=attachments

# credentials
mail.user=NUTZER9@gmail.com
mail.password=PASSWORT
```

Hier muss lediglich in den "credentials" die E-Mail Adresse sowie Passwort eingesetzt werden. Ggf. muss die Sicherheitseinstellungen des Google Kontos angepasst werden. Die Sicherheitseinstellungen können hier gesetzt werden. [https://www.google.com/settings/security/lesssecureapps](https://www.google.com/settings/security/lesssecureapps)

Anschließend muss im Verzeichnis das Programm `startup.bat` für Windows oder `startup.sh` für Linux/MacOs im Verzeichnis `..\camunda-bpm-tomcat-7.10.0\server\apache-tomcat-9.0.12\bin` angepasst werden. Diese wird mit einem Editor geöffnet und zu beginn folgender Befehl eingefügt.

* Für Windows in der .bat Variante -> `set "MAIL_CONFIG=../conf/mail-config.properties"`
* Für Linux/MacOS in der .sh Variante -> `export MAIL_CONFIG="../conf/mail-config.properties"`

Damit ist die Umgebung konfiguriert. 

## Implementierung der Tasks im Camunda Modeler

### 1. User Task

Erlaubt das direkte Eingeben von Daten durch einen Benutzer auf der Camunda BPM Plattform durch Formulare.
Im Camunda Modeler wird nachdem die User Task im Prozess eingefügt wurde folgende Einstellung vorgenommen.

#### 1.1 General/Assignee 
Hier wird der Bearbeiter festgelegt der im System dazu berechtigt ist die Aufgabe zu bearbeiten. Zur Vereinfachung wurde hier "demo" eingetragen, damit die Aufgabe nicht erst angenommen werden muss.

#### 1.2 Forms/Forms Fields 
Hier können die Input Felder für die Nutzer eingefügt werden. Diese können auch im weiteren Verlauf innerhalb des Prozesses aufgegriffen werden um diese z.B. in Mailseinzubinden oder um Regeln zu definieren. Hier können verschiedene DatenTypen ausgewählt werden. Innerhalb des Prozesses werden vor allem die Datentypen `long` und `boolean` verwendet.

### 2. Inclusive Gateway
In diesem Gateway wird keine konkrete Logik konfiguriert. Das Gateway gibt dem Modellierenden lediglich die Regeln vor wie die ein- und ausgehenden Sequenzflüsse zu konfigurieren sind. 

### 3. Sequence Flow (Default)
Der Sequenzfluss bildet die eigentliche Logik vom Gateway ab.

#### 3.1 General/Details
Eine sehr elegante und leichte Art Regeln für Sequenzflüsse einzubinden ist im Feld "Condition Type" Expression aus dem Dropdown-Menü auszuwählen. Die Expression wird im anschließend folgenden Expression Feld bestimmt. Im Beispiel wurde im Formular der User Task ein Boolean verwendet der dazu genutzt werden kann. Dabei wird die Formular ID (interesst) abgeglichen mit der Wahrscheitswert abgefragt. `${interesst == true}`

#### 3.2 Default Sequence Flow
Im Rahmen der Implementierung bedarf es neben der grafischen Einbindung in BPMN keine weiteren konfigurationen, da dieser Sequenzfluss immer triggert, wenn sonst keine Expression true ist. 

### 4. Send Task
Erlaubt z.B. des automatisierte Senden von E-Mails auf der Camunda BPM Plattform. Konkret wird dies durch einen Connector implementiert, was keinerlei Programmierkenntnisse benötigt.
Dokumentation von Camunda: [klick](https://github.com/camunda/camunda-bpm-mail#camunda-bpm-mail)

#### 4.1 Connectors und Input Parameters
Im Camunda Modeler wird die Task als Send Task definiert. Task auswählen und "Properties Panel" öffnen. Anschließend im Reiter "General" auf "Connector" setzen und als Connector-ID `mail-send` setzen.

Im Reiter "Connector" innerhalb "Input Parameters" auf "+" klicken und folgende Parameter anlegen
      
| Name    | Type | Value |
| ----    | ---- | ----- |
| to      | Text | Empfänger E-Mail-Adresse |
| subject | Text | Betreff |
| text    | Text | Inhalt der E-Mail |
        
### 5. Receive Task
Hier beginnt der schwierigste Teil der Implementierung. Laut der Dokumentation von Camunda werden für receive Tasks eher Service Tasks verwendet, weil hierbei die technischen Möglichkeiten offener sind und die Connector-ID `mail-poll` gesetzt werden kann.

#### 5.1 Implementierung in der Send Task.
Die Logik der Receive Task wird im Listener der Send Task abgebildet. Es wird dort die korrelation zur Receive Task aufgebaut die momentan eine "Running Process Instance" offen hat. Der Prozess wartet dort. Message Name in der Receive Task vorher umbenennen auf einen wiederverwendbaren Namen, welcher im nächsten Abschnitt im Script aufgegriffen wird.


#### 5.1.1 Script
Es wird in der Send Task, welche an die Receive sendet folgende einstellungen vorgenommen. 
Reiter Listeners öffnen, Listeners auf "+" klicken. Execution Listener "start". Listener Type auf "Script" setzen. Script Format (in diesem Fall) "Javascript" eintippen. Script Type auf "Inline Script" setzen und als Script folgendes Script einbinden.

Message Name in der Receive Task vorher umbenennen auf einen wiederverwendbaren Namen

```
var service = execution.getProcessEngineServices().getRuntimeService();
service.createMessageCorrelation("MessageName");
var id = service.createExecutionQuery().messageEventSubscriptionName("MessageName").singleResult();
service.messageEventReceived("MessageName", id.getId());
```

### 6. Message Event
Das Message Event wird genauso konfiguriert wie die Send Task aus 4. und weißt keinerlei technische Unterschiede dazu auf. 

Anschließend wird der Prozess hochgeladen und kann ausgeführt werden. 
