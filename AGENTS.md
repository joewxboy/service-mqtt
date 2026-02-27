# AGENTS.md

This file provides guidance to agents when working with code in this repository.

## Project Overview

This repository contains a **Horizon MQTT Broker Service** designed for edge computing deployments. It provides a containerized Mosquitto MQTT broker that enables inter-container communication in LF Edge Open Horizon edge environments.

### Key Technologies
- **MQTT Broker**: Eclipse Mosquitto
- **Container Platform**: Docker (multi-architecture support)
- **Edge Platform**: Open Horizon Exchange
- **Base Image**: Alpine Linux
- **Supported Architectures**: amd64, arm, arm64

### Architecture
The service is structured as a simple containerized MQTT broker with:
- Password-based authentication (configurable via environment variables)
- Persistent message storage
- Multi-architecture Docker images
- Horizon Exchange integration for edge deployment

## Building and Running

### Prerequisites
- Docker installed
- Open Horizon CLI (`hzn`) for publishing to Horizon Exchange
- Environment variables set (optional, defaults provided):
  - `MQTT_USERNAME` (default: "mqtt")
  - `MQTT_PASSWORD` (default: "password")

### Build Commands

```bash
# Build for current architecture
make build

# Build for all supported architectures
make build-all-arches

# Build for specific architecture
ARCH=amd64 make build
ARCH=arm make build
ARCH=arm64 make build
```

### Run Commands

```bash
# Run locally for current architecture
make run

# Run for all architectures
make run-all-arches

# Run for specific architecture
ARCH=amd64 make run
```

### Publishing to Horizon Exchange

```bash
# Publish service for all architectures
make publish

# Publish for specific architecture
ARCH=amd64 make publish-service

# Publish with overwrite and pull (for ICP exchange)
make publish-only
```

### Cleanup

```bash
# Clean current architecture
make clean

# Clean all architectures
make clean-all-archs
```

## Configuration

### Authentication
The MQTT broker uses password-based authentication. Configure credentials via environment variables:

```bash
export MQTT_USERNAME=<desired-username>
export MQTT_PASSWORD=<desired-password>
```

### Mosquitto Configuration
The broker configuration (`mosquitto.conf`) includes:
- Anonymous access disabled
- Password file authentication
- Persistent message storage at `/var/lib/mosquitto/`
- Logging to `/var/log/mosquitto/mosquitto.log`
- Additional configuration directory at `/etc/mosquitto/conf.d/`

## MQTT Client Usage

### Publishing Messages

```bash
mosquitto_pub -h <host> -p <port> -t <topic> -m <message> -u <username> -P <password>
```

### Subscribing to Topics

```bash
mosquitto_sub -h <host> -p <port> -t <topic> -u <username> -P <password>
```

## Development Conventions

### Docker Images
- Each architecture has its own Dockerfile (`Dockerfile.amd64`, `Dockerfile.arm`, `Dockerfile.arm64`)
- All Dockerfiles follow the same pattern: Alpine base + Mosquitto + startup script
- Images are tagged as `${DOCKER_IMAGE_BASE}_${ARCH}:${SERVICE_VERSION}`

### Horizon Service Definition
- Service configuration is defined in `horizon/service.definition.json`
- Uses variable substitution from `horizon/hzn.json`
- Service is marked as `public` and `sharable: singleton`
- User inputs for MQTT_USERNAME and MQTT_PASSWORD are defined with defaults

### Startup Process
The `mqtt.sh` script:
1. Sets up MQTT passwords using `mosquitto_passwd`
2. Starts the Mosquitto broker with the provided configuration

### Testing
The Makefile includes test configuration:
- `MATCH='Starting mqtt broker...'` - Expected output to verify successful startup
- `TIME_OUT=60` - Maximum wait time for service startup

## File Structure

```
service-mqtt/
├── Dockerfile.{amd64,arm,arm64}  # Architecture-specific Docker builds
├── Makefile                       # Build, run, and publish automation
├── mqtt.sh                        # Broker startup script
├── mosquitto.conf                 # Mosquitto broker configuration
├── README.md                      # User-facing documentation
└── horizon/                       # Horizon Exchange configuration
    ├── hzn.json                   # Service variables
    ├── service.definition.json    # Service definition for Horizon
    └── userinput.json             # User input configuration
```

## Important Notes

- This service is designed for Open Horizon edge computing environments
- The broker runs as a singleton service (only one instance per edge node)
- Default credentials (mqtt/password) should be changed in production
- The service requires no other services (empty `requiredServices` array)
- All architectures share the same configuration and startup logic
