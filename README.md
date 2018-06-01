# freifunk-IoT Konzept

Aktueller Stand: https://github.com/FFS-IoT/freifunk-iot-concept/blob/master/Treffen-2018-06-01.md

Hier ist das Konzept für ein IoT-Projekt als interner Service im Freifunknetz zu finden.

Entstanden sind die Ideen von margau bei Freifunk Stuttgart, aber auch über andere Ausrichtungen/Aspekte kann man reden ;)

### Was soll freifunk-IoT machen?

Um Daten und Messwerte jeglicher Art sammeln zu können, bieten sich moderne, WLAN-basierte Umgebungen an. Potentiell geeignete Plattformen sind zum Beispiel der ESP8266/ESP32, Raspberry Pi oder das XDK von Bosch. Freifunknetze sind, aufgrund ihrer steigenden Verfügbarkeit, der Meshfunktion und vorallem der Ausrichtung als "experimentelles Bürgernetz" eine gute Plattform, um die Daten über eine WLAN-Schnittstelle "loszuwerden", und dann auch im Freifunk-Netz zu sammeln und zu verarbeiten. Zunächst ist eine reine Ausrichtung auf Sensorik gedacht, aber natürlich ist eine Erweiterung auch möglich.

### Komponenten von Freifunk-IoT

Generell ist dieses Freifunk-IoT-Konzept als "Leitfaden" bzw. "Arbeitsgrundlage" zu verstehen. Teile, wie z.B. Data Sink kann man sicherlich 1:1 ausführen, während insbesondere der Bereich der Hardware eher eine Art Inspiraton werden wird. Änderungsvorschläge/Erweiterungen gerne per Pull Request, wenn Interesse besteht kann man ja eine Mailingliste oder ein Forum eröffnen.

#### Data Sink

Dies ist der zentrale Dienst im Freifunknetz, hier laden die einzelnen Sensoren ihre Messwerte ab ("Datensenke"). Es bietet sich an, diesen Dienst als Microservice (z.B. mit node.js) zu konzipieren, und unter einer statischen IP mit passender DNS-Weiterleitung laufen zu lassen. IPv4 wird wohl zwingend notwendig sein, da einige Embedded-Plattformen (ESP8266) noch kein v6 können.

Abgespeichert werden die Daten dann in einer Time-Series-Datenbank (InfluxDB, Graphite, ...).

Ob eventuelle interne Aktionen von diesem Teil, oder dem "Data View/Data Access"-Layer ausgehen sollten, ist sicher noch zu diskutieren.

Ideen für Protokolle, mit denen man Daten loswerden kann:

- HTTP-GET (Daten in Parametern)
- HTTP-POST (REST)
- MQTT
- UDP

#### Data View/Data Access

Data View kümmert sich um die Präsentation der Daten, hier könnte man beispielsweise Grafana verwenden. Data Access stellt eine weitere Verarbeitung der Daten sowohl aktiv (=pusht neue Daten selbst) als auch passiv (Daten werden abgeholt) sicher. Hier bietet sich eine HTTP-REST-API an, vielleicht auch ein Export historischer Daten in einem anderen Format (csv).

Dieser Service sollte sowohl intern aus dem Freifunknetz heraus als auch extern (z.B. über einen Reverse Proxy) erreichbar sein.

**Eventuell macht es Sinn, Data Sink und Data View/Data Access in einem Microservice zusammenzufassen**

#### Aggregation

Software, die auf der Sensorhardware läuft und die eigentlichen Sensoren ausließt, evtl. Daten aufbereitet und zur Data Sink überträgt. Hier wird es wohl darauf hinauslaufen, examples bereitzustellen, die vom Enduser auf die verwendete Hardware angepasst wird. 

Einige Ideen:

- Arduino-Sketche für den ESP8266
- C-Software für das Bosch XDK
- Phyton für den Raspberry Pi

Eine möglichst einsteigerfreundliche Lösung ist natürlich anzustreben.

#### Hardware

Selbstverständlich benötigt man auch Hardware, also Sensoren, Controller und Stromversorgungen. Schaltpläne, Stücklisten, Bezugsquellen und Tutorials sind hier sicher sehr hilfreich. Einige Ideen:

- Standardumweltsensor (Temperatur + Luftfeuchte, NodeMCU und DHT22) mit USB-Versorgung
- Outdoor-Temperatursensor (ESP01 mit 18650)
- Standalone-Outdoor-Temperatursensor mit Solarzelle
- Beschleunigungssensor im Auto

Gerade in diesem Bereich kann man sicherlich auch gut Workshops durchführen.

### Features

#### Sicherheit/Authentifizierung

Eine total offene Bereitstellung von Daten ist nicht immer sinnvoll (z.B. bei Bewegungsdaten). Daher ist es sinnvoll, eine Authentifizierung (Key) für jeden Sensor in jedem Request Vorrauszusetzen, um einem Missbrauch/"gefälschten" Daten vorzubeugen. Ebenfalls ist eine nicht-öffentliche Anzeige bei bestimmten Daten sinnvoll, während andere (Umweltdaten) standardmäßig öffentlich zugänglich sein sollten. Hier muss man schauen, wie man insbesondere bei Grafana eine Autorisierung der Anzeige pro Sensor implementieren kann. 

Dafür benötigt man eine Art "Sensor-Verwaltung", um die Keys und Leserechte zu verwalten, eventuell gekoppelt mit einer Nutzerverwaltung.

#### Benachrichtigungen

Eine konfiguierbare Benachtichtigung für bestimmt Situationen (Wert über/unter Grenzwert, Sensor hat eine Gewisse Zeit keine Daten mehr geliefert) ist wünschenswert. Hier ist noch zu diskutieren, in welchen Dienst (Data Sink oder Data View/Data Access) diese eingebaut wird, da eine Überprüfung immer bei der Einlieferung eines neuen Wertes erfolgen sollte.

Mögliche Kanäle wären z.B. E-Mail, HTTP-Hook oder Signal (per signal-cli).

#### Zurückdatieren und Queue

Um Verbindungsausfälle überbrücken zu können, kann man in die Software der Clients eine FIFO-Warteschlange implementieren, die im Falle einer Verbindungsunterbrechung trotzdem weiter Werte aufnimmt und beim Wiederherstellen der Verbindung alle Werte zurückdatiert überträgt.



### Offene Fragen

- Welche Datenbank? Wie die Authentifizierung Lösung?
- Wenn Grafana verwendet wird, wie Authentifiziert man die Nutzer nichtöffentlicher Daten?
- Realisiert man Data Sink bzw. Data View/Data Access in ein oder zwei Applikationen?



### Weiterer Ausbau

In der Zukunft kann man möglicherweise auch Aktoren mit einbinden - diese erfordern aber ein sehr viel umfangreicheres Rechtemanagement.

Ein Kompatibilitätslayer mit dem TTN/LoRaWAN ist eine weitere Idee.
