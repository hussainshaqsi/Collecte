# Collecte: MQTT to MySQL temperature collector

Python service that listens to temperature messages published by home sensors over MQTT and stores them in a MySQL database. It is the data-ingestion half of my SAÉ 2.04 project; the dashboard is [SAe-projet-integratif](https://github.com/hussainshaqsi/SAe-projet-integratif).

```
Sensors --MQTT--> collecte (this repo) --SQL--> MySQL --> Django dashboard
```

## How it works
- Subscribes to the MQTT topics of the sensors (HiveMQ public broker, `IUT/Colmar2026/SAE2.04/...`)
- Parses each message, e.g. `Id=12A6B8AF6CD3,piece=sejour,date=15/06/2026,heure=12:13:14,temp=26,35`, into a sensor id, room, timestamp and temperature (it also handles the comma used as a decimal separator)
- Inserts the sensor if it is new, then the measurement, with raw SQL (no ORM)
- **Keeps working when the database is down:** unsent readings go to a local `cache.jsonl` file and are flushed to MySQL as soon as the connection is back
- Reconnects to the MQTT broker automatically

## Run it

```bash
pip install -r requirements.txt
cp config.example.py config.py      # then put your MySQL credentials in config.py
python main.py                      # Ctrl+C to stop
```

`config.py` is git-ignored so credentials never reach the repository.

## Stack
Python, paho-mqtt, PyMySQL, MySQL
