### MQTT + Apache NiFi + InfluxDB Integration Demo

This project demonstrates streaming data from multiple MQTT brokers into InfluxDB using Apache NiFi as the data flow manager.

### Quick Setup Summary

1-Configure your environment by creating a .env file

2-Build the necessary Docker images

3-Launch the full system with make start

### Building the NiFi Image with InfluxDB 2.0 Plugin

Clone this repository locally

Build the custom NiFi image (which includes the InfluxDB processor) by running:
```bash
make build-nifi
```

To run a plain NiFi instance without pre-configured flows, use:
```bash
docker run -p 8443:8443 influxdb/nifi:latest ../scripts/start.sh
```

You can import the provided NiFi template (MQTT-to-InfluxDB.xml) to experiment with streaming MQTT messages to InfluxDB.

### Launching the Full Example with Multiple MQTT Brokers

1-Duplicate the example environment file:
```bash
cp example.env .env
```

2-Modify the .env file to suit your configuration

3-Build all Docker images:
```bash
make build
```
4-Start all containers and services:
```bash
make start
```
5-Wait about 30 seconds for NiFi to become available. To check logs for any service, use:
make logs NODE=[service_name]
For example:
```bash
make logs NODE=nifi
```
6-Verify that data is being ingested into your InfluxDB bucket as expected
Access NiFi at https://localhost:8443/nifi — accept the security warning to proceed

7-Log in with the credentials:

Username: admin

Password: nifipassword

8-To shut down the running containers, run:
```bash
make stop
```
### What’s Happening Under the Hood?

When you execute make start, several MQTT clients and two Mosquitto brokers spin up alongside a NiFi instance. Once running, the nifi.bash script configures NiFi via its REST API to set up process groups that subscribe to the MQTT topics provided by the Mosquitto brokers.

- The MQTT clients publish messages in different formats:

		One sends InfluxDB Line Protocol

		Another publishes JSON payloads

		The third sends simple text strings
- NiFi’s flow groups are each tailored to handle one of these data formats, converting all incoming data into Line Protocol before forwarding it to InfluxDB.

Repository Contents

Dockerfile.nifi: Dockerfile to build a NiFi image bundled with the InfluxDB processor plugin

Dockerfile.nifipoc: Dockerfile containing the nifi.bash script to automate NiFi configuration

docker-compose.yml: Docker Compose file defining the services used in this example

nifi.bash: Bash script that configures NiFi’s process groups and connections via API calls

nifi.http: REST Client file for VSCode allowing you to send HTTP requests to NiFi’s API

MQTT-to-InfluxDB.xml: NiFi flow template included in the image demonstrating MQTT to InfluxDB streaming
