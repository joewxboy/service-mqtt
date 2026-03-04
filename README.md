# Horizon MQTT Broker Service

![Latest Version][mqtt-version-shield]
![Supports amd64 Architecture][mqtt-amd64-shield]
![Supports arm Architecture][mqtt-arm-shield]
![Supports arm64 Architecture][mqtt-arm64-shield]

The MQTT Broker service provides an mqtt broker and publisher for inter-container commmunication. 

### **API:** 
---
#### Authentication:

This service is configured to use password-based authentication. By default, the username is set to `mqtt` and the password is `password`. To customize these attributes, please set these environment variables.

```
export MQTT_USERNAME=<desired-username>
export MQTT_PASSWORD=<desired-password>
```

If you want to additionally secure MQTT by configuring SSL, you may edit the `mosquitto.conf` file, adapting the instructions from step 3 of this [guide](https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-the-mosquitto-mqtt-messaging-broker-on-debian-10#step-3-%E2%80%94-configuring-mqtt-ssl).

#### Publishing:

```
mosquitto_pub [-d] [-h host] [-p port] {-f file | -m message} [-t topic] [-u username] [-P password]
```

#### Parameters:

| Name | Description |
| ---- | ---------------- |
| -h | host name | 
| -d | put the broker into the background after starting |
| -p | publish on the specified port |
| -f | send the contents of a file as the message | 
| -m | message payload to send | 
| -t | mqtt topic to publish to |
| -u | mqtt username |
| -P | mqtt password |

#### Subscribing:

```
mosquitto_sub [-h host] [-p port] [-t topic] [-u username] [-P password]
```

#### Parameters:

| Name | Description |
| ---- | ---------------- |
| -h | host name |  
| -p | subscribe on the specified port |
| -t | mqtt topic to subscribe to |
| -u | mqtt username |
| -P | mqtt password |

[mqtt-version-shield]: https://img.shields.io/badge/version-1.0.0-blue.svg
[mqtt-amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[mqtt-arm-shield]: https://img.shields.io/badge/arm-yes-green.svg
[mqtt-arm64-shield]: https://img.shields.io/badge/arm64-yes-green.svg
