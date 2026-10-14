# Collecte : collecteur de températures MQTT vers MySQL

Service Python qui écoute les messages de température publiés par des capteurs domestiques en MQTT et les enregistre dans une base MySQL. C'est la partie « collecte des données » de mon projet SAÉ 2.04 ; le tableau de bord est [SAe-projet-integratif](https://github.com/hussainshaqsi/SAe-projet-integratif).

```
Capteurs --MQTT--> collecte (ce dépôt) --SQL--> MySQL --> Tableau de bord Django
```

## Fonctionnement
- S'abonne aux topics MQTT des capteurs (broker public HiveMQ, `IUT/Colmar2026/SAE2.04/...`)
- Analyse chaque message, par exemple `Id=12A6B8AF6CD3,piece=sejour,date=15/06/2026,heure=12:13:14,temp=26,35`, pour en extraire l'identifiant du capteur, la pièce, la date et la température (la virgule décimale est gérée)
- Insère le capteur s'il est nouveau, puis la mesure, en SQL brut (sans ORM)
- **Continue de fonctionner si la base est indisponible :** les relevés non envoyés sont mis dans un fichier local `cache.jsonl`, puis renvoyés vers MySQL dès que la connexion revient
- Se reconnecte automatiquement au broker MQTT

## Lancer

```bash
pip install -r requirements.txt
cp config.example.py config.py      # puis renseigner les identifiants MySQL dans config.py
python main.py                      # Ctrl+C pour arrêter
```

`config.py` est ignoré par git : les identifiants n'arrivent jamais dans le dépôt.

## Technologies
Python, paho-mqtt, PyMySQL, MySQL
