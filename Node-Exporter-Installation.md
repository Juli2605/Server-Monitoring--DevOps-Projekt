# **Wie installiert man Node Exporter?**

Author: [Juljano Aliaj](https://git.th-wildau.de/jual3747)\
*Diese Dokumentation basiert auf der Nutzung von einem Linux Server*

## **Installation**

* Gehen Sie zu [Prometheus Website](https://prometheus.io/download/) und laden Sie die neueste Binärdatei für den node exporter herunter (aktuell *node_exporter-1.0.1.linux-amd64.tar.gz*).
``` shell
$ wget https://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz
```
Das Archiv enthält eine einzelne Binärdatei, node_exporter.
Dies ist die Binärdatei, die wir starten werden, um Metriken auf unserem System zu erfassen.
* Nachdem Sie das Archiv erstellt haben, extrahieren Sie es und überprüfen Sie den Inhalt.
``` shell
$ tar xvzf node_exporter-1.0.1.linux-amd64.tar.gz
```
Um es jetzt als Dienst zu installieren, folgen Sie den Anweisungen:
* Erstellen Sie einen Node Exporter-Benutzer
``` shell
$ sudo useradd -rs /bin/false node_exporter
```
* Kopieren Sie die Binärdatei in Ihren Ordner /usr/local/bin.
```shell
sudo cp node_exporter-1.0.1.linux-amd64/node_exporter /usr/local/bin
```
* Wenden Sie die richtigen Berechtigungen auf Ihre Binärdatei an
``` shell
$ chown node_exporter:node_exporter /usr/local/bin/node_exporter
```
`Hinweis:` Sie benötigen einen node_exporter-Benutzer, um die Binärdatei ausführen zu können.

* Navigieren Sie zu /etc/systemd/system und erstellen Sie eine neue Servicedatei
``` shell
$ cd /etc/systemd/system
$ sudo vim node_exporter.service
```
* Fügen Sie dann die folgende Konfiguration für Ihren Dienst ein.
``` shell
[Unit]
Description=Node Exporter
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
``` 
* Beenden Sie vi, laden Sie Ihren Daemon neu und starten Sie Ihren Dienst.
``` shell
$ sudo systemctl daemon-reload
$ sudo systemctl start node_exporter
```

## **Aktivierung**

* Überprüfen Sie Ihren Dienst, indem Sie den folgenden Befehl ausführen
```shell
$ sudo systemctl status node_exporter.service
```

* Aktivieren Sie Ihren Dienst für den Systemstart
``` shell 
$ sudo systemctl enable node_exporter
```
* Stellen Sie mit einem einfachen Curl-Befehl sicher, dass Ihr Node Exporter ordnungsgemäß ausgeführt wird
```shell
$ curl http://localhost:9100/metrics
```
Können Sie die key-value-metrics Ihrer Systemmetriken sehen?

* Wir sind mit der Node Exporter-Installation fertig!

`Quellen:` [devconnected.com](https://devconnected.com/complete-node-exporter-mastery-with-prometheus/#a_Installing_the_Node_Exporter)
