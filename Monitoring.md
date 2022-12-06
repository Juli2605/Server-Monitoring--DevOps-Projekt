### **Inhaltsverzeichnis**

* [Was ist Monitoring](#was-ist-monitoring)
  * [Monitoringarten](#monitoringarten)
  * [Monitoringsteps](#monitoringsteps)
  * [Monitoringtools](#monitoringtools)
* [Prometheus Vorstellung](#prometheus-vorstellung)
* [Quellen](#quellen)

# **Was ist Monitoring?**
Monitoring ist die Überwachung von Vorgängen. Es ist ein Überbegriff für alle Arten von systematischen Erfassungen (Protokollierungen), Messungen oder Beobachtungen eines Vorgangs oder Prozesses mittels technischer Hilfsmittel oder anderer Beobachtungssysteme.
Eine Funktion des Monitorings besteht darin, bei einem beobachteten Ablauf oder Prozess festzustellen, ob dieser den gewünschten Verlauf nimmt und bestimmte Schwellwerte eingehalten werden, um andernfalls steuernd eingreifen zu können. Monitoring ist deshalb ein Sondertyp des Protokollierens. Quelle: [Wikipedia](https://de.wikipedia.org/wiki/Monitoring) 

## **Monitoringarten**
##### **Anwendungsmonitoring** 
Softwarequalitätsmanagement
Software testen (Geschwindigkeit, Aussehen, Case-Studies)

##### **Service Monitoring**
Überwachen des Serverstatus (Console output (sample code most for Linux/Ubuntu Setup, Node.js, other plugins)

##### **Business Active Monitoring** 
Sammlung von Analysen der Prozesse (Pakete gepackt, ausgeliefert, versendet, Anzahl der Retouren im laufenden Zeitraum 
Bsp.: Realtime Total Sales
auf uns bezogen bedeutet es erst mal ein Ziel festzulegen  (die Funktionen der Software festzulegen) 

##### **Netzwerkmonitoring**
Unter Netzwerk-Monitoring versteht man die Überwachung und regelmäßige Kontrolle von Netzwerken, deren Hardware (z.B. Server, Router, Switches) 
und Diensten (z. B. Webserver, DNS-Dienste, E-Mail-Dienste). Man unterscheidet hierbei zwischen externem und internem Monitoring.
Beim externen Monitoring wird ein zusätzliches Monitoring-Gerät an das Netz angeschlossen, beim internen Monitoring nicht.
Eine weitere Charakterisierung wird mit den Begriffen aktiv und passiv getroffen.*
Beim aktiven Monitoring werden zusätzliche Pakete in das Netz gesendet, beim passiven wird lediglich "mitgehört".

## **Monitoringsteps**
### **Was erarbeiten wir mit der DevOps Toolchain?**
![Monitoring Overview](/media/img/Monitoring/monitoring-overview.jpg "Monitoring Overview | Grafik von Marc Müller")

##### **Schritt 1 : Data Sammeln**

* Bestandsaufnahme dessen, was heute gesammelt wird.
* Analysieren von Bestandserfassung von Metriken mit Fragen wie: "Warum erfassen wir diese nicht? Wie passt das zu unseren Zielen? Haben wir jemals einen Fehler in X gesehen? Wie oft sollten wir das messen? Wie lange sollen wir es behalten?
* Die Teams sollten bewerten, wie sie die Informationen erfassen, und den besten architektonischen Ansatz für die Erfassung ihrer Metriken in Betracht ziehen, einschließlich der Frage, ob Pull-, Push- oder Pull-Erfassungsmethoden besser sind.(Im Pull-System beginnen die Produktionsaufträge mit dem Erreichen eines bestimmten Lagerbestands, während im Push-System die Produktion nach Bedarf beginnt.)

##### **Schritte 2: Correlate and Triage**

Daten kommen mit unterschiedlichen Frequenzen, in unterschiedlichen Zeitrahmen und aus unterschiedlichen Quellen an. Dementsprechend man braucht:
* Arbeit an Normalisierung von Daten, um sie zu verstehen.
* Auswertung mit den verschiedenen eingehenden Metriken durchzuführen.
* Daten in Perspektive setzen und in Beziehung sie zu Projektzielen stellen.

##### **Schritt 3:Trends identifizieren**

* Trends identifizieren, um Maßnahmen zu ergreifen, bevor Probleme auftreten.
* Festlegen von thresholds und Sicherheit thresholds für jede der Komponenten zusammen, die sich möglicherweise auf das Serviceangebot auswirken können. 
* Ergebnisse mit Teams und Geschäftsbereichsleitern kommunizieren,  Probleme bei der Servicebereitstellung vermeiden und Angebote basierend auf identifizierten Trends optimieren.

##### **Schritt 4: Benachrichtigen und handeln (Automatisierung)**

Der Wunsch, sich zu einem schnelleren Prozess zu verbessern, erfordert eine
Umstellung von manuellen auf automatisierte Praktiken.
* Wo können wir am besten mehr Automatisierung hinzufügen .
* Wie sammeln wir die richtige Telemetrie, die konsistente Antworten liefert
* Das Nutzen folgt von einem automatisierten Prozess oder Benachrichtigung an eine Person? 

## **Monitoringtools**
### **Welche Tools könnten wir verwenden?**

auf Testbasis / Freetrail:

* [Prometheus](https://prometheus.io/)
* [Grafana](https://grafana.com/)
* [Nagios](https://www.nagios.org/)
* [Jaeger](https://www.jaegertracing.io/)

## **Prometheus Vorstellung** 

1- Prometheus Server bezieht sich auf 3 Komponenten: 

* Retrieval - Zieht Metriken in Datenformat von Applikationen, Servers, Services etc.
* Storage - Ein Datenbank Komponente zum Storage von Metriken 
* HTTP Server - akzeptiert Queries. 

![Prometheus Architektur](/media/oldUploads/Diagram_von_Juljano_Aliaj.png)

2- Monitoring mit Prometheus:
* Servers wie Windows/Linux Servers, Apache Servers, einzelne Programme, Datenbanken etc. 

3- Anfragen an sogenannte Targets werden in Prometheus genutzt 
 um Metriken zu erstellen 

* z.B.: Für Windows Server ein Target kann CPU-Status, Memory usage etc, sein.
      Für eine Applikation sind : Number of exeptions, Number of Requests , Request Duration 
     (Latency)
* Die Einheit von einem Target ist letztendlich ein Metrik. 
       
4- Metrische Einträge sind: Type und Help Attribute
* Help - Bezeichnung von dem in der Datenbank Komponente gespeicherten Metrik
* Type - Ein von 3 Metrik Typen  
    * Typ 1 - Counter: Wie oft ist "das" passiert? z.B.: Nummer von Exceptions, die das App  
             hatte, Nummer von Requests etc .
    * Typ 2 - Gague : Was ist der aktuelle Wert? z. B.: Kapazität von CPU, Nummer von 
             aktuellen Requests, Kapazität von Disk Space etc. 
    * Typ 3 - Histogramm : Verfolgen wie lang oder wie groß? z.B.: die Größe oder der Dauer 
             eines Requests.

5- Pulling Data from Servers:

* Prometheus zieht Metriken über einen ein HTTP Endunkt, welcher standartmäßig "hostaddress/metrics" ist. Damit das funktioniert, muss ein Target mit diesen "/metrics" Endpunkt verbunden sein und die entsprechenden verfügbare Daten in diesen Endpunkt in ein Format, welches Prometheus erkennt, umwandeln. Einige Server exportieren bereits "/metrics", d.h. extra Arbeit diese Metriken zu sammeln, ist nicht nötig. 
Anderseits verfügen viele Server über keinen nativen Prometheus-Endpunkt , so ist ein extra Komponent erforderlich, diese Metriken zu exportieren . Dieser Komponent heißt Exporter , welcher ein Skript oder ein Service sein kann. Exporter sammeln die Metriken von Targets und wandelen sie in einem Format um, welches Prometheus erkennt und veröffentlicht. Diese umgewandelte Daten sind in seinem eigenen "/metrics" Endpoint zu finden, wobei Prometheus sie entschlüsseln kann. 
Prometheus hat eine Liste von Exportern für verschieden Services :
* Prometheus Exporters : https://prometheus.io/docs/instrumenting/exporters/
Sobald der jeweilige Exporter-Endpunkt zur Prometheus-Konfiguration hinzugefügt ist, beginnt Prometheus mit dem Sammeln dieser Meriken und speichert sie in seinem eigenen Format in der Prometheus-Datenbank.

6 - Application Monitoring :
* Für diesen Anwendungsfall gibt es verschiedene Client-Libraries für verschiedene Sprachen wie node.js, java usw.
* Prometheus Client Libraries : https://prometheus.io/docs/instrumenting/clientlibs/
Mithilfe von Client-libraries kann man Scraping-Endpunkte verfügbar machen und verschiedene Metriken bereitstellen, die auf diesem Endpunkt relevant sind.
*All dies wird in prometheus.yml (Konfigurationsdatei) konfiguriert, sodass man definieren können, welches target Prometheus in welchem Intervall entschlüsseln soll.


7 - Prometheus verwendet dann einen Diensterkennungsmechanismus, um diese Zielendpunkte zu finden. Wenn Prometheus zum ersten Mal heruntergeladen und installiert wird, wird eine Beispielkonfigurationsdatei mit einigen Standardwerten angezeigt. 

![Prometheus Config](/media/img/../../../media/img/Monitoring/prometheus_config.png)

* global : bezeichnet wie oft entschlüsselt Prometheus seine Targets und man kann dies mit seinen eigenen Targets überschreiben.
* rule _file: Bestimmt den Speicherort aller Regeln, die der Prometheus-Server laden soll. Die Regeln dienen im Wesentlichen entweder zum Aggregieren von Metrikregeln oder zum Erstellen von Warnungen, wenn eine Bedingung erfüllt ist, genutzt. Sollte die CPU-Auslastung beispielsweise 80% erreichen? Prometheus verwendet also Regeln, um neue Zeitreiheneinträge zu erstellen und Warnungen zu generieren. Die globale Konfiguration definiert dann, wie oft Prometheus diese Regeln auswertet.
* Die letzte Blockscrape-configuration steuert, welche Ressourcen Prometheus überwacht, hier kann man die Targets definieren. Da Prometheus über eigene Metrik-Endpunkte verfügt, um seine eigenen Daten verfügbar zu machen, kann es seinen eigenen Zustand überwachen. In dieser Standardkonfiguration wird also ein einzelner Job namens Prometheus verwendet, der die vom Prometheus-Server bereitgestellten Metriken entschlüsselt, sodass sein einzelnes Target auf localhost 9090 liegt. Prometheus stellt dann Metriken zur Verfügung, die auf einem Pfad "/metrics" verfügbar sind, bei dem es sich um einen Standardpfad handelt, der für diesen Endpunkt konfiguriert ist. 
* Anschließend kann man einen anderen Job erstellen und beispielsweise aus der globalen Konfiguration überschreiben, dann die Hostadresse des Targets identifizieren.

8- Wie löst Prometheus Warnungen aus, die durch Regeln definiert sind und wer sie empfängt?
* Prometheus verfügt ein Komponent namens Alert Manager, die für das Auslösen von Alerts über verschiedene Kanäle verantwortlich ist. Es kann sich um eine E-Mail, einen Slack-Kanal oder einen anderen Benachrichtigungsclient handeln. Prometheus liest also die Warnungsregeln. Wenn die Bedingung in den Regeln erfüllt ist, wird in diesem konfigurierten Kanal eine Warnung ausgelöst.

9- Data Storage 

* Prometheus speichert Metrikdaten auf der Festplatte (HDD / SSD), enthält also eine lokale Zeitreihendatenbank auf der Festplatte, lässt sich jedoch optional in das Remote-Speichersystem integrieren und die Daten werden in einem kostümierten Zeitreihenformat gespeichert. Aus diesem Grund kann man Prometheus-Daten nicht direkt in eine relationale Datenbank  schreiben. Sobald man die Metriken gesammelt hat, kann man mit Prometheus auch die sie über die API-Server mithilfe der Prompt QL Query Language abfragen. Prompt Ql wird verwenden, um den Status eines bestimmten Targets anzuzeigen, oder man kann leistungsfähigere Datenvisualisierungstools wie grafana Verwenden, um die Daten anzuzeigen, die auch Prompt QL verwenden, um die Daten aus Prometheus herauszuholen.

10- Grafana in Prometheus
*  Sobald man die Metriken gesammelt hat, kann man mit Prometheus auch die sie über die API-Server mit Hilfe der Prompt QL Query Language abfragen. Prompt Ql wird verwenden, um den Status eines bestimmten Targets anzuzeigen, oder man kann leistungsfähigere Datenvisualisierungstools wie Grafana verwenden, um die Daten anzuzeigen, die auch Prompt QL verwenden, um die Daten aus Prometheus herauszuholen.In Grafana sind anstelle von Prompt QL-Abfragen direkt auf dem Prometheus-Server die Grafana-Benutzeroberfläche, wo Dashboards erstellen werden können, in denen Prompt Ql im Hintergrund zum Abfragen der Daten verwendet werden kann. 

10- Prometheus Vorteile : 
* zuverlässig
* eigenständig
* funktioniert auch dann, wenn andere Teile der Infrastruktur unvollständig sind
* es ist keine umfangreiche Einrichtung erforderlich

## **Quellen**
* [Monitoring Schritte](https://devops.com/top-5-best-practices-devops-monitoring) .
* [Prometheus Vorstellung](https://www.youtube.com/watch?v=h4Sl21AKiDg&t=550s)
* [Monitoring](https://de.wikipedia.org/wiki/Monitoring) 
