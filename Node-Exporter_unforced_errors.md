# **Node Exporter- shutdown des Wildau-VPN**
Author: [Juljano Aliaj](https://git.th-wildau.de/jual3747)\
Basiernd auf den Einstellungen der nodeExporter in der prometheus.yml, haben wir versucht auf einen Server von außerhalb zuzugreifen. Da man mit Hilfe eines VPN die Server der Hochschule erreichen kann, bedeutet das nicht, dass die Ihnen zugeteilte IP-Adresse fest ist. Diese dynamische IP-Adresse, die nun dem Server außerhalb Wildaus zugeteilt ist, kann keine Verbindung mit den Monitoring-Tools Prometheus und oder Grafana herstellen. Vielmehr entsteht folgendes...

Hierbei wird eine .log Datei auf Serverseite Wildaus erstellt, die mit gescheiterten Verbindungsversuchen gefüllt wird. Die Anfrage an den Servers mit dieser dynamischen IP-Adresse ist nicht möglich ist und dennoch wird stetig versucht eine Verbindung herzustellen.

##  Wie vermeiden/lösen wir das Problem?

Es werden Datasources in Grafana, sowie die entsprechenden Nodeexporter aus Prometheus gelöscht. Ebenfalls der wichtigste Teil abgesehen vom GUI, das prometheus.yml auf Monitoring-Serverseite werden entsprechend angepasst, indem die IP-Adresse und die dynamischen IP-Adressen aus der Konfigurationsdatei gelöscht werden('10.60.50.97:9182','10.60.50.24:9090', '192.168.---.--:9090'). Es muss darauf geachtet werden nur die Server innerhalb der TH-Wildau, die auch eine Feste IP-Adresse zugewiesen bekommen haben, vom Monitoring Team mit den nodeExportern anzufragen.


![VPN2Crash](media/oldUploads/VPN2Crash.png)


##  Wie hätten Wildauer-Systemadministratoren vorbeugen können?

Die Größe der geschrieben log Datei hätte eingeschränkt werden können, indem die log Datei gelöscht wird, sollte sie zu groß sein. Andererseits möchte man die log Dateien eigentlich behalten um Sie auswerten zu können, das wäre über ein senden des entstandenen logs über Cloud-Services  möglich gewesen. Lässt man das Log Files "rotieren", so hätte man den gleichen Effekt wie eben angesprochen. Hier eine kleine Einführung dazu: https://linuxpeter.de/2016/06/01/logrotate-fuer-windows/
