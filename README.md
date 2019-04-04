# CamundaBPM - Implementierung mit Fokus auf Send and Receive Task (Modeler only!)
======
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

...Zunächst ist CamundaBPM zu installieren. Es ist sicherzustellen, dass die Umgebungsvariablen JAVA_HOME und JRE_HOME gesetzt sind.

...Um CamundaBPM richtig (Konfiguration und Einstellungen) aufzusetzen werden folgende Pakete benötigt, welche i.d.r. unter folgendem Pfad abgelegt werden können. `..\camunda-bpm-tomcat-7.10.0\server\apache-tomcat-9.0.12\lib`. Ggf. befinden sich bereits einige Bibliotheken im Verzeichnis. Doppelte Bibliotheken (auch mit unterschiedlichen Versionen!) sind zu vermeiden. 

...1. camunda-bpm-mail-core-1.2.0.jar
...2. camunda-connect-core-1.1.0.jar
...3. javax.mail-1.5.5.jar
...4. slf4j-api-1.7.7.jar

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

...Hier muss lediglich in den "credentials" die E-Mail Adresse sowie Passwort eingesetzt werden. Ggf. muss die Sicherheitseinstellungen des Google Kontos angepasst werden. Die Sicherheitseinstellungen können hier gesetzt werden. [https://www.google.com/settings/security/lesssecureapps](https://www.google.com/settings/security/lesssecureapps)

Anschließend muss im Verzeichnis das Programm `startup.bat` für Windows oder `startup.sh` für Linux/MacOs im Verzeichnis `..\camunda-bpm-tomcat-7.10.0\server\apache-tomcat-9.0.12\bin` angepasst werden. Diese wird mit einem Editor geöffnet und zu beginn folgender Befehl eingefügt.

Für Windows in der .bat Variante -> `set "MAIL_CONFIG=../conf/mail-config.properties"`
Für Linux/MacOS in der .sh Variante -> `export MAIL_CONFIG="../conf/mail-config.properties"`

Anschließend ist die Umgebung gewährleistet. 

------




