# Horizon MQTT Broker Service

![Latest Version][mqtt-version-shield]
![Supports amd64 Architecture][mqtt-amd64-shield]
![Supports arm Architecture][mqtt-arm-shield]
![Supports arm64 Architecture][mqtt-arm64-shield]
[![License][license-shield]](LICENSE)

## Description

The Horizon MQTT Broker Service provides a containerized Eclipse Mosquitto MQTT broker for inter-container communication in LF Edge Open Horizon edge computing environments. This service enables reliable message passing between edge services using the MQTT protocol with password-based authentication.

The broker is designed to run as a singleton service on edge nodes, providing a central message hub for distributed edge applications. It supports multiple architectures (amd64, arm, arm64) and can be easily deployed through the Open Horizon Exchange.

## Prerequisites

### Management Hub

- Access to an Open Horizon Management Hub
- Your organization ID and user credentials
- Exchange URL configured

### Edge Node

- **Hardware**: Any device supporting Docker (x86_64, ARM, or ARM64 architecture)
- **Operating System**: Linux-based OS with Docker support
- **Open Horizon Agent**: Version 2.27.0 or later installed and configured
- **Docker**: Version 18.06.0 or later

### Optional Utilities

The following tools are helpful for development and debugging:

- `make` - Build automation
- `git` - Version control
- `jq` - JSON processing
- `curl` - HTTP requests
- `mosquitto-clients` - MQTT testing tools

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/open-horizon-services/service-mqtt.git
cd service-mqtt
```

### 2. Verify Prerequisites

Check that you have the required tools installed:

```bash
make --version
hzn version
docker --version
```

### 3. Configure Environment

Set your MQTT credentials (optional, defaults to mqtt/password):

```bash
export MQTT_USERNAME=<desired-username>
export MQTT_PASSWORD=<desired-password>
```

Set your Horizon organization:

```bash
export HZN_ORG_ID=<your-org-id>
```

### 4. Verify Agent Status

Ensure your Horizon agent is running and configured:

```bash
hzn node list
hzn agreement list
```

## Usage

### Local Testing

To build and run the service locally for testing:

```bash
# Build the Docker image for your architecture
make build

# Run the service locally
make run
```

The MQTT broker will be available on the default port (1883) within the Docker network.

### Testing MQTT Communication

#### Publishing Messages

```bash
mosquitto_pub -h localhost -p 1883 -t test/topic -m "Hello MQTT" -u mqtt -P password
```

#### Subscribing to Topics

```bash
mosquitto_sub -h localhost -p 1883 -t test/topic -u mqtt -P password
```

#### Command Parameters

| Parameter | Description |
|-----------|-------------|
| `-h` | Hostname or IP address of the MQTT broker |
| `-p` | Port number (default: 1883) |
| `-t` | MQTT topic to publish/subscribe |
| `-m` | Message payload to send (publish only) |
| `-u` | MQTT username for authentication |
| `-P` | MQTT password for authentication |
| `-d` | Enable debug output |
| `-f` | Send contents of a file as message (publish only) |

### Production Deployment

To publish the service to the Horizon Exchange and deploy it to edge nodes:

```bash
# Build for all supported architectures
make build-all-arches

# Publish to Horizon Exchange
make publish
```

The service will be registered in the Exchange and can be deployed to edge nodes through deployment policies or patterns.

## Advanced Details

### Authentication Configuration

This service uses password-based authentication by default. The credentials can be customized via environment variables:

```bash
export MQTT_USERNAME=<your-username>
export MQTT_PASSWORD=<your-password>
```

**Default Credentials:**
- Username: `mqtt`
- Password: `password`

**Important:** Change the default credentials in production environments.

### SSL/TLS Configuration

For additional security, you can configure SSL/TLS encryption by editing the `mosquitto.conf` file. Follow the instructions in this [guide](https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-the-mosquitto-mqtt-messaging-broker-on-debian-10#step-3-%E2%80%94-configuring-mqtt-ssl) to set up SSL certificates.

### Mosquitto Configuration

The broker configuration (`mosquitto.conf`) includes:

- **Authentication**: Password file-based authentication
- **Persistence**: Messages stored in `/var/lib/mosquitto/`
- **Logging**: Logs written to `/var/log/mosquitto/mosquitto.log`
- **Additional Config**: Custom configurations in `/etc/mosquitto/conf.d/`

### Debugging

#### View Service Logs

```bash
docker logs <container-name>
```

For the running service:

```bash
docker logs $(docker ps -q --filter ancestor=<image-name>)
```

#### Access Container Shell

```bash
docker exec -it <container-name> /bin/sh
```

#### Check Broker Status

```bash
# View running containers
docker ps

# Check broker process
docker exec <container-name> ps aux | grep mosquitto
```

### Makefile Targets

The following `make` targets are available for building, testing, and publishing the service:

#### Build Targets

- `make build` - Build Docker image for current architecture
- `make build-all-arches` - Build Docker images for all supported architectures (amd64, arm, arm64)

#### Run Targets

- `make run` - Run the service locally for current architecture
- `make run-all-arches` - Run the service for all architectures

#### Publish Targets

- `make publish` - Publish service to Horizon Exchange for all architectures
- `make publish-service` - Publish service for current architecture
- `make publish-service-overwrite` - Publish with overwrite and pull flags
- `make publish-only` - Publish all architectures with overwrite (for ICP exchange)

#### Cleanup Targets

- `make clean` - Remove Docker image for current architecture
- `make clean-all-archs` - Remove Docker images for all architectures

### Architecture Support

This service supports the following architectures:

- **amd64** (x86_64) - Standard desktop and server processors
- **arm** (32-bit ARM) - Raspberry Pi and similar devices
- **arm64** (64-bit ARM) - Modern ARM devices and servers

Each architecture has its own Dockerfile (`Dockerfile.amd64`, `Dockerfile.arm`, `Dockerfile.arm64`) based on Alpine Linux with Eclipse Mosquitto.

### Service Configuration

The service is configured as a singleton in the Horizon Exchange, meaning only one instance will run per edge node. This ensures a single, centralized MQTT broker for all services on the node.

**Service Properties:**
- **Sharable**: `singleton` - One instance per node
- **Public**: `true` - Available to all users in the organization
- **Required Services**: None - Runs independently

## Authors

- **Troy Fine** - [t-fine](https://github.com/t-fine) - troy.fine@ibm.com
- **John Walicki** - [johnwalicki](https://github.com/johnwalicki) - walicki@us.ibm.com

See [MAINTAINERS.md](MAINTAINERS.md) for the complete list of maintainers.

## License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

## Resources

- [Open Horizon Documentation](https://open-horizon.github.io/)
- [Eclipse Mosquitto Documentation](https://mosquitto.org/documentation/)
- [MQTT Protocol Specification](https://mqtt.org/)
- [LF Edge Open Horizon](https://www.lfedge.org/projects/openhorizon/)

[mqtt-version-shield]: https://img.shields.io/badge/version-1.0.0-blue.svg
[mqtt-amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[mqtt-arm-shield]: https://img.shields.io/badge/arm-yes-green.svg
[mqtt-arm64-shield]: https://img.shields.io/badge/arm64-yes-green.svg
[license-shield]: https://img.shields.io/badge/License-Apache%202.0-blue.svg