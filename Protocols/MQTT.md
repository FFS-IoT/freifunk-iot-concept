# Ingress: MQTT

Per MQTT werden JSON-Formatierte Objekte an den Channel "iot_input" gesendet.

#### Protokoll:

**Required**

- Sensor-ID: Bereits registrierte Sensor-ID, Erkennung des Sensors

**Optional**

- Channel: Ein oder mehrere Datenchannels, in die Daten geschrieben werden. Wird übermittelt im Format "{sensor-id}.{channel}"
- Error: Wenn ein Fehler aufgetreten ist, Fehlercode > 0 *noch nicht implementiert*
- timestamp: Unix-Zeitstempel des Messwertes
- timestamp_ms: Unix-Zeitstempel in ms *noch nicht implementiert*
- signature: Kryptografische Signatur der Daten, zur Definition siehe die Auth-Details *noch nicht implementiert*
- token: Auth-Token *noch nicht implementiert*

Generell muss entweder der Error-Code oder Werte für einen oder mehrere Channel vorhanden sein.

Je nach Konfiguration muss kein Feld zur Authentifizierung, eine Signatur oder ein Token vorhanden sein.

**Beispiel:**

{ "sensor_123.temperature": 23, "sensor_123.humidity": 100, "token": "abcd", "timestamp_ms": 1527861014000 }

Dieses Example pusht Temperatur und Luftfeuchtigkeit für den Sensor mit der id "sensor_123", einem Millisekundengenauen Timestamp und Tokenauthentifizierung.

#### Examples:

[ESP01 mit DHT22 als Temperatur/Luftfeuchtigkeitssensor](https://github.com/Freifunk-IoT/esp8266-examples/tree/master/dht22/esp01/esp01-dht22-sketch)