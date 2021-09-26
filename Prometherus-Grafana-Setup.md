Author: [Juljano Aliaj](https://git.th-wildau.de/jual3747)

### **Inhalt**

* [Prometheus Setup](#prometheus-setup)
  * [Prometheus HerunterLaden und Installieren](#prometheus-herunterladen-und-installieren)
  * [Prometheus Konfiguration](#prometheus-konfiguration)
  * [Start der Anwendung](#start-der-anwendung)
  * [Web-Konsole Starten](#web-konsole-starten)
* [Wmi-Exporter](#wmi-exporter)
  * [Installation von WMI Exporter](#installation-von-wmi-exporter)
  * [Prometheus an den WMI Exporter binden](#prometheus-an-den-wmi-exporter-binden)
* [Grafana Setup](#grafana-setup)
  * [Grafana Herunterladen](#grafana-herunterladen)
  * [Grafana Installation](#grafana-installation)
  * [Grafana Konfigurieren](#grafana-konfigurieren)
  * [Start der Grafana Web-Konsole](#start-der-grafana-web-konsole)
  * [Erstellen einer Prometheus-Datenquelle](#erstellen-einer-prometheus-datenquelle)
  * [Erstellen eines Prometheus-Diagramms](#erstellen-eines-prometheus-diagramms)
* [Alerting](#alerting)
  * [Alerting Rules](#alerting-rules)
  * [Alertmanager Einrichten](#alertmanager-einrichten)
  * [Alert Meldungen in Slack](#alert-meldungen-in-Slack)
* [Quellen](#quellen)

# **Prometheus Setup**

## **Prometheus Herunterladen und Installieren** 
Um die neueste Version von Prometheus herunterzuladen rufen wir auf [Prometheus Website](https://prometheus.io/download/) die aktuellen ZipDatei für Windows aus. Das steht aktuell unter *prometheus-2.19.2.windows-amd64.tar.gz*. Die Datei muss nach Herunterladen entpackt werden um die Installationsmedien zu entnehmen. 

![Prometheus_Archiv](media/img/oldUploads/Prometheus_Archiv.PNG)

*Archiv mit den heruntergeladenen Dateien für Prometheus*

## **Prometheus Konfiguration** 
Die YAML-Datei enthält die Prometheus Konfigurationen.

![Prometheus_Konfiguration](media/img/oldUploads/Prometheus_Konfiguration.PNG)

Die wichtigsten Konfigurationen sind *global* und *scrape_configs* wie in der obigen Abbildung markiert sind. Erstes enthält den Paramater *scrape_interval*, welcher die Pull-Intervale für den Abruf der Metriken festlegt. Die Abstände zwischen Intervale werden demnächst erfahrungsgemäß durch *evaluation_interval* erwiesen.
In die *scrape_configs* werden Jobs bzw Targest definiert. Automatisch wird hier der eigene Prometheus Server eingetragen, da Prometheus seine eigene Metriken erfasst. 
Der *job_name* ist hierbei ein Tag, in unserem Fall "node_exporter", für Pull-Metriken von unseren Wildau Servers. Die Targets werden dann mittels IP des Servers und seines eigenständigen Ports zur Ermittlung dieser Metriken festgelegt. 

## **Start der Anwendung**
Nach angepasste Konfiguration starten wir die Anwendung durch *prometheus.exe*. Aus ihren Output geht hervor, dass die  Konfigurations­datei erfolgreich geladen wurde und dass die Anwendung auf Port 9090 hört.

![Prometheus_Port](media/img/oldUploads/Prometheus_Port.PNG)
![Prometheus_Starten](media/img/oldUploads/Prometheus_Starten.PNG)

## **Web-Konsole Starten**

Das Admin-Interface ist demnächst unter http://localhost:9090 erreichbar.

<img align="center" src="https://www.percona.com/blog/wp-content/media/oldUploads/Screen-Shot-2016-02-28-at-22.47.08.png">


Über das Feld *Expression* setzt man PromQL-Statements (ähnlich SQL) ab. Über das Drop-down-Menü lassen sich Metriken auswählen. Diese PrompQL-Statements für spezifische Metriken kann man auch von Navigation Bar unter **Status**-->**Targets** auf **Endpoint** kopieren und dementesprecend hier einfügen.
Nach *Execute* kann die Ausgabe über die Console oder durch einen Graphen mit zeitlichem Verlauf darstellen lassen.

<img align="center" src="https://prometheus.io/assets/blog/2019-10-08/10kseries8hours-2.12.png">

Da diese Graphen Rudimentär sind empfielt Prometheus-Community für Dashboards Erstellung die Verwendung von Grafana. Prometheus wird dann nur als Pull-Collector genutzt. 

# **WMI Exporter**

## **Installation von WMI Exporter**
Der WMI-Exporter ist ein großartiger Exporter für Windows-Server.
Es werden Metriken wie die CPU-Auslastung, der Speicher und die Festplatten E/A Auslastung exportiert.
Um den WMI-Exporter zu installieren, gehen wir zur [WMI-Releases Seite](https://github.com/prometheus-community/windows_exporter/releases) auf GitHub und laden  auf der Release-Seite die MSI-Datei herunter, die unserer CPU-Architektur entspricht.
In meinem Fall werde ich die Datei *wmi_exporter-0.8.1-amd64.msi* herunterladen.
Wenn der Download abgeschlossen ist, klicken wir einfach auf die MSI-Datei und starten das Installationsprogramm.

<img align="center" src="https://devconnected.com/wp-content/media/oldUploads/wmi-exporter-msi.png">

`Dienst Aktivierung:` Die MSI-Installation sollte ohne Bestätigungsfeld beendet werden. Der WMI-Exporter sollte jetzt jedoch als Windows-Dienst auf dem Host ausgeführt werden.
Um dies zu überprüfen, wechselt man zum Windows-Bedienfeld „**Dienste**“ (indem man im Windows-Suchmenü "**Dienste**" eingibt).
In dem Bedienfeld suchen wir in der Liste nach dem Eintrag „**WMI-Exporter**“. Hierbei stellen wir sicher, dass der Dienst ordnungsgemäß ausgeführt wird.

![wmi-exporter-service](media/img/oldUploads/wmi-exporter-service.png)

Nachdem der Exporter ausgeführt wird, sollte er Metriken unter http://localhost:9182 /metriken verfügbar machen
Wir öffnen unserem Webbrowser und navigieren zur URL des WMI-Exporters. Dies sehen wir demnächst.
Einige Metriken sind sehr allgemein und werden von allen Exportern exportiert, aber einige der Metriken sind sehr spezifisch für den Windows-Host (wie zum Beispiel die Metrik wmi_cpu_core_frequency_mhz).

![prom-metrics](media/img/oldUploads/prom-metrics.png)

## **Prometheus an den WMI-Exporter binden**
Infolgedessen besteht die Möglichkeit, dass die Scrape-Anforderung beim Versuch, die Metriken abzurufen, abgelaufen ist.
Aus diesem Grund werden wir in unserer Konfigurationsdatei ein Zeitlimit für hohe Scrapes festlegen.
Wenn man ein geringes Zeitlimit für Scrapes beibehalten möchte, muss man den WMI-Exporter so konfigurieren, dass weniger Metriken exportiert werden (indem man beispielsweise nur einige wenige Kollektoren angibt).
Gehen wir zu unserer Konfigurationsdatei (meine befindet sich unter C:\Users\devopsadmin\prometheus/prometheus.yml) und bearbeiten wir die folgenden Änderungen an unserer Datei, wobei wir den Port, wo WMI-Exporter hört, einfügen.

![Node_exporter_konfigurieren](media/img/oldUploads/Node_exporter_konfigurieren.PNG)

Wir speichern die Datei und starten den Prometheus-Dienst (prometheus.exe) neu.
Kehren wir zur Prometheus-Benutzeroberfläche zurück und wählen wir die Registerkarte "**Targets**", um sicherzustellen, dass Prometheus ordnungsgemäß mit dem WMI-Exporter verbunden ist. Wenn die folgende Fehlermeldung "*context deadline exeeded*" angezeigt wird, muss  sichergestellt werden, dass das Scrape-Timeout in der Konfigurationsdatei festgelegt ist.

Wenn alles soweit ist und die Servers in config Datei eingetragen wurden, kann man die Instanzen überprüffen und ihre entsprechenden Metriken in Prometheus UI auf Status/Targets ausrufen.

![Targets](media/img/oldUploads/Targets.PNG)

# **Grafana Setup**

## **Grafana Herunterladen**
Um das Windows Version von Grafana herunterzuladen muss man auf [Grafana Website](https://grafana.com/grafana/download?platform=windows) die aktuellen Datei *grafana-7.0.6.windows-amd64.msi* für Windows ausrufen.
Wenn das Herunterladen fertig ist, kann man einfach das MSI-Installationsprogramm ausführen.

![grafana-msio](media/img/oldUploads/grafana-msio.png)

## **Grafana Installation**
* Klicken Sie auf „**Weiter**“.
* Akzeptieren Sie die Bedingungen der Lizenzvereinbarung und klicken Sie auf „**Weiter**“.
Sicher gestellt werden muss, dass das Grafana OSS (der Grafana-Server) und die Grafana als ein Service-Option korrekt ausgewählt sind.
* Wenn dies der Fall ist: Klicken Sie auf "**Weiter**" und auf "**Installieren**".
Zu diesem Zeitpunkt sollte die Grafana-Installation beginnen.
Wenn irgendwann zu einer Firewall-Ausnahme aufgefordert wird, muss Grafana autorisiert werden, Änderungen an dem System vorzunehmen.
Wenn die Installation erfolgreich abgeschlossen wurde, ist dies der Bildschirm, den man sehen sollte.

![Grafana_Installation_erfolgreich_abgeschlossen](media/img/oldUploads/Grafana_Installation_erfolgreich_abgeschlossen.png)

`Dienst Aktivierung:` Bevor wir fortfahren, müssen wir überprüfen, ob unser Grafana-Server ordnungsgemäß als Windows-Dienst ausgeführt wird.
* Geben Sie im Windows-Suchmenü "**Dienste**" ein und öffnen Sie das Fenster "**Dienste**".
* Scrollen Sie im Fenster "Dienste", bis Sie den Grafana-Dienst erreichen.

![grafana-service](media/img/oldUploads/grafana-service.png)
**Toll! Der Grafana-Dienst ist in Betrieb.**

## **Grafana Konfigurieren**

Für mich steht die Konfigurations datei unter *C:\Users\devopsadmin\grafana\conf\sample*

![grafana-conf](media/img/oldUploads/grafana-conf.PNG)

`Hinweis:` Der Standard-Grafana-Port ist 3000. Für diesen Port sind unter Windows möglicherweise zusätzliche Berechtigungen erforderlich. Wenn es nicht im Standardport angezeigt wird, können wir versuchen, zu einem anderen Port zu wechseln.
Wir gehen in das Verzeichnis `conf` und kopieren `sample.ini` in `custom.ini`. 

`Hinweis:` Man sollte `custom.ini` bearbeiten, niemals `defaults.ini`.
Wir bearbeiten die Datei `custom.ini`, kommentieren die Konfigurationsoption `http_port` aus (; ist das Kommentarzeichen in INI-Dateien) und ändern sie in `8080` oder ähnliches. Dieser Port sollte keine zusätzlichen Windows-Berechtigungen erfordern.

![http_port](media/oldUploads/http_port.PNG)

## **Start der Grafana Web-Konsole**
Nach der Konfiguration, und wenn der Dienst ordnungsgemäß ausgeführt wird, sollte man auf die Grafana v6-Benutzeroberfläche zugreifen können. **Nicht zu vergessen:** Es muss immer erstmal im Grafana Ordner die *grafana-server.exe* Datei gestartet werden.

`Zur Erinnerung:` Grafana wird standardmäßig auf dem Port 3000 ausgeführt. Da wir den Port zu 8080 geändert haben, öffnen wir daher einen Webbrowser und navigieren wir zu http://localhost:8080.

Dies ist der Bildschirm, den wir sehen sollten.

![grafana-default-ui](media/img/oldUploads/grafana-default-ui.png)

In Grafana sind die Standardanmeldeinformationen standardmäßig *admin* (als Benutzername) und *admin* (als Kennwort).

Nach dem Login werden wir im nächsten Fenster aufgefordert, unser Passwort zu ändern. Man kann ein ein sicheres Passwort wählen, um Sicherheitsverletzungen zu vermeiden.

Wenn fertig, klicken wir auf **„Speichern“**.

**Toll!** Wir sehen jetzt den Standardbildschirm für Grafana v6.3 unter Windows.

![web-ui-2](media/img/oldUploads/web-ui-2.png)

## **Erstellen einer Prometheus-Datenquelle**
So erstellt man eine Prometheus-Datenquelle in Grafana:

1) Klicken Sie in der Seitenleiste auf das "Zahnrad", um das Konfigurationsmenü zu öffnen.
2) Klicken Sie auf "**Data Sources**".
3) Klicken Sie auf "**Add data source**".
4) Wählen Sie als Typ "**Prometheus**".
5) Stellen Sie die entsprechende Prometheus-Server-URL ein (z. B. http: // localhost: 9090 /).
6) Passen Sie andere Datenquelleneinstellungen wie gewünscht an (z. B. Auswahl der richtigen Zugriffsmethode).
7) Klicken Sie auf "**Save & Test**", um die neue Datenquelle zu speichern.

Das Folgende zeigt eine beispielhafte Datenquellenkonfiguration:

![grafana_configuring_datasource](media/img/oldUploads/grafana_configuring_datasource.png)


## **Erstellen eines Prometheus-Diagramms**

Wir befolgen die Standardmethode zum Hinzufügen eines neuen Grafana-Diagramms:

1) Klicken Sie auf den Diagrammtitel und dann auf "**Edit**".
2) Wählen Sie auf der Registerkarte "**Metrics**" Ihre Prometheus-Datenquelle aus (unten rechts).
3) Geben Sie einen beliebigen Prometheus-Ausdruck in das Feld "**Query**" ein, während Sie das Feld "**Metric**" verwenden, um Metriken über die automatische Vervollständigung zu suchen.
4) Verwenden Sie die Eingabe "**Legend format**", um die Legendennamen von Zeitreihen zu formatieren. Um beispielsweise nur die `method` und `status` bezeichnungen eines zurückgegebenen Abfrageergebnisses anzuzeigen, die durch einen Bindestrich getrennt sind, können Sie die Zeichenfolge `{{method}} - {{status}}` im Legendenformat verwenden.
5) Passen Sie andere Diagrammeinstellungen an, bis Sie ein funktionierendes Diagramm haben.
Das Folgende zeigt ein Beispiel für eine Prometheus-Diagrammkonfiguration:

![grafana_qps_graph](media/img/oldUploads/grafana_qps_graph.png)

# **Alerting**

## **Alerting Rules**

Es ist sehr wichtig zu wissen, wann eine unserer Instanzen ausgefallen ist. Daher werde ich dies als unsere Bedingung verwenden, indem ich die Metrik *up{instance="...",job="..."}* verwende. Wenn wir diese Metrik in der Prometheus-Benutzeroberfläche (http://localhost:9090) auswerten, sehen wir, dass alle laufenden Instanzen den Wert 1 haben, während alle Instanzen, die derzeit nicht ausgeführt würden, den Wert 0 hätten.

![up_instances](media/img/oldUploads/up_instances.PNG)

Nachdem wir uns für eine Warnbedingung entschieden haben, müssen wir diese in eine neue erstellte Datei auf Prometheus Ordner, rules.yml angeben. Sein Inhalt wird folgender sein:

```yml
groups:
- name: AllInstances
 rules:
 - alert: InstanceDown
   # Condition for alerting
   expr: up == 0
   for: 1m
   # Annotation - additional informational labels to store more information
   annotations:
     title: 'Instance {{ $labels.instance }} down'
     description: '{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 1 minute.'
   # Labels - additional labels to be attached to the alert
   labels:
       severity: 'critical'
```

Zusammenfassend heißt es, dass die Warnung ausgelöst wird, wenn eine der Instanzen für eine Minute ausfällt (up == 0).
Sobald wir rules.yml bereit haben, müssen wir die Datei mit prometheus.yml verknüpfen und eine Warnkonfiguration hinzufügen. Unser prometheus.yml wird folgendermaßen aussehen:

```yml
global:
  scrape_interval:     15s 
  evaluation_interval: 15s

alerting:
  alertmanagers:
  - static_configs:
    - targets:
      #- alertmanager:9093
      - localhost:9093

rule_files:
  - rules.yml

scrape_configs:

  - job_name: 'prometheus'
    static_configs:
    - targets: ['localhost:9090']

  - job_name: 'node_exporter'
    scrape_interval: 15s
    static_configs:
    - targets: ['localhost:9182','194.95.44.169:9100']
```
Nach Speichern der *prometheus.yml* versuchen wir wieder Prometheus UI zu öffnen. Auf Alerts wird, wenn alle Instanzen aktiv sind folgendes gezeigt:

![alerting_rules_Prom](media/img/oldUploads/alerting_rules_Prom.PNG)

`Note:`*Weiterer Teil der Aufgabe ist in Bearbeitung*


## **Alertmanager Einrichten**
Wir gehen zu [Prometheus Website](https://prometheus.io/download/) und laden die Alertmanager Datei für Windows *alertmanager-0.21.0.windows-amd64.tar.gz* herunter. Wir speichern die Datei in unserem Prometheus Ordner z.B: *C:\Users\devopsadmin\prometheus* . Nachdem müssen wir die Datei extrahieren. Das kann mittels **CMD**, wobei in die Prometheus Ordner der untere Befehl eingegeben wird:
 ```shell
tar xzvf alertmanager-0.21.0.windows-amd64.tar.gz
```
`Note:`*Weiterer Teil der Aufgabe ist in Bearbeitung*
## **Alert Meldungen in Slack**

Man kann **Webhooks** erstellen, die im Wesentlichen Adressen für externe Quellen sind, um Slack zu erreichen.

Infolgedessen sendet Prometheus die Warnung an die Webhook-Adresse und sie wird in unserem Slack-Kanal angezeigt.

Um einen Slack-Webhook zu erstellen, gehen wir zu unserer [Slack App Seite](https://api.slack.com/apps) und klicken wir oben rechts "**Create New App**".
Geben wir die Name unseres neuen Apps und bestätigen unsere Slack Workspace.

![create_new_app](media/img/oldUploads/create_new_app.PNG)

Demnächst wählen wir "**Incoming Webhooks**" und aktivieren sie um Benachrichtigungen von externen Quellen als Post-Messages in Slack zu ermöglichen. 

![activate_incoming_webhooks](media/img/oldUploads/activate_incoming_webhooks.PNG)

Klicken wir am Ende auf "**Add New Webhook to Workspace**". 
Wählen wir ein Slack-Channel, wo diese App Benachrichtigungen posten kann und klicken wir "**Allow**". Wenn dafür kein geignetes Channel zu verfügung ist, kann man ein erstellen. 

![Slack_channel](media/img/oldUploads/Slack_channel.PNG)

Jetzt sehen wir das ein neues Webhook für diese App zuständig ist . Das werden wir zur unseren weiteren Konfigurationen auf Prometheus Alertmanager brauchen .

![webhook](media/img/oldUploads/webhook.PNG)


### **Quellen:**

* [windowspro.de(Prometheus Installieren und Konfigurieren)](https://www.windowspro.de/philip-lorenz/prometheus-windows-installieren-konfigurieren), 
* [devconected.com (windows server monitoring mit Prometheus und WMI Exporter, Slack webhook erstellen)](https://devconnected.com/windows-server-monitoring-using-prometheus-and-wmi-exporter/),  
* [devconnected.com (grafana conf-file)](https://devconnected.com/how-to-install-grafana-on-windows-8-10/), 
* [grafana.com(Windows Installation)](https://grafana.com/docs/grafana/latest/installation/windows/), 
* [prometheus.io(Grafana Visualisation)](https://prometheus.io/docs/visualization/grafana/).
