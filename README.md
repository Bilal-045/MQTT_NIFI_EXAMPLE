<h1 align="center">🚀 MQTT + Apache NiFi + InfluxDB Integration Example</h1>

<p align="center">Stream real-time data from multiple MQTT brokers into InfluxDB using Apache NiFi.</p>

---

## 📦 Project Overview

This project sets up a complete data streaming pipeline using:

- **MQTT brokers** (Mosquitto)
- **Apache NiFi** for data routing & transformation
- **InfluxDB** for time-series storage

---

## ⚡ Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/your-repo-name.git
cd your-repo-name
```
### 2. Copy & Configure .env
```bash
cp example.env .env
```
Edit .env to match your configuration.

### 3. Build & Start the Stack
```bash
make build
make start
```

NiFi takes ~30 seconds to start.
Use make logs NODE=nifi to follow NiFi logs.

### Access the Services

NiFi UI: https://localhost:8443/nifi
Click “allow unsafe” if prompted (self-signed cert)

### Login Credentials:

Username: admin
Password: nifipassword

### Stop Everything
```bash
make stop
```
### How It Works

When you run make start, the following happens:

Multiple MQTT clients start publishing messages

Two Mosquitto brokers are spun up

A custom NiFi instance auto-configures using nifi.bash

Messages from brokers are routed to InfluxDB

MQTT Clients → Mosquitto Brokers → Apache NiFi → InfluxDB

### Test It Yourself

You can send custom MQTT messages using tools like MQTTBox or MQTT Explorer.

✅ Example

Broker: tcp://mosquitto1:1883

Topic: /1/lp

Payload:

mqtt,mytag=tagvalue myfield="fieldvalue"


NiFi is also subscribed to:

/2/json → JSON data

/2/string → Plain text

🧱 Data Formats Handled
Client	Format	Topic
1️⃣	InfluxDB Line Protocol	/1/lp
2️⃣	JSON	/2/json
3️⃣	Plain Text	/2/string

Each process group in NiFi handles one format → transforms → sends to InfluxDB.

📁 Repository Structure
File / Folder	Description
Dockerfile.nifi	Custom NiFi with InfluxDB processor plugin
Dockerfile.nifipoc	NiFi image with auto-config script
docker-compose.yml	Defines the complete stack
nifi.bash	Bash script to auto-configure NiFi
nifi.http	Test file (works with VSCode REST Client)
MQTT-to-InfluxDB.xml	NiFi template with MQTT-to-Influx flow
