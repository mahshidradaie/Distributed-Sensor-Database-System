# Distributed Sensor Database System

A distributed database system for collecting and serving sensor data (temperature, humidity, motion, and other environmental sensors) across multiple floors of a hotel, built as coursework for the *Embedded Systems* course at Sharif University of Technology, Department of Electrical Engineering.

## Overview

Each node holds the sensor readings it collects locally. One node acts as **Master** and is the only node the operator talks to; the remaining **Slave** nodes only communicate with the Master and never respond to the operator directly. When the Master doesn't have the requested value, it forwards the request down the chain of Slaves until the data is found (or reports back that it isn't available anywhere), then returns the final answer to the operator.

```
      [sensors]      [sensors]      [sensors]
         |               |              |
   Sensor Server    Sensor Server   Sensor Server
      Master   ───────  Slave   ───────  Slave
         |
      Client
```

The project is built up in six parts, each adding a new capability on top of the base system:

| Part | Focus | Weight |
|------|-------|--------|
| 01 | Base distributed database (Master + 2 Slaves, SQLite, request forwarding) | 30% |
| 02 | Two-layer storage: Memcached caching in front of SQLite | 20% |
| 03 | MQTT integration (Mosquitto broker, pub/sub topics for sensor requests) | 15% |
| 04 | SNMP integration (snmpd, custom OIDs for sensor data) | 15% |
| 05 | REST API for historical sensor logs by date (Mongoose) | 5% |
| 06 | Alert daemon monitoring sensor values and logging alerts | 5% |

## Tech Stack

- **Language:** C / C++
- **Storage:** SQLite (persistent), Memcached (in-memory cache layer)
- **Networking/API:** [Mongoose](https://mongoose.ws/) for HTTP/API endpoints
- **Messaging:** MQTT via Mosquitto broker
- **Monitoring protocol:** SNMP via `snmpd` with custom OIDs
- **Build:** `make` (per-part `Makefile`s) + Bash scripts for build/run/test
- **Deployment target:** Ubuntu 22.04 nodes (VMs), IP/port configured via config file, CLI args, or environment variables — never hardcoded

## Project Structure

```
project/
├── 01/                 # Base distributed DB (Master/Slave)
│   ├── master/
│   ├── slave/
│   ├── scripts/
│   ├── report.md
│   └── README.md
├── 02/                 # Caching layer (Memcached)
│   ├── master/
│   ├── slave/
│   ├── scripts/
│   ├── report.md
│   └── README.md
├── 03/                 # MQTT integration
│   ├── scripts/
│   ├── report.md
│   └── README.md
├── 04/                 # SNMP integration
│   ├── snmp/
│   ├── scripts/
│   ├── report.md
│   └── README.md
├── 05/                 # Sensor log API
│   ├── api/
│   ├── scripts/
│   ├── report.md
│   └── README.md
└── 06/                 # Alert daemon
    ├── daemon/
    ├── scripts/
    ├── service/
    ├── report.md
    └── README.md
```

Each part is self-contained: it has its own `Makefile`, its own Bash build/run script, its own `report.md` (system design write-up), and its own `README.md` with exact install/build/run/test instructions for that part.

## Getting Started

There's no single build for the whole repo — build and run each part from inside its own folder, following that part's `README.md`:

```bash
cd 01/master && make && ./scripts/build_and_run.sh
cd 01/slave  && make && ./scripts/build_and_run.sh
```

General dependencies used across parts:

```bash
sudo apt install build-essential sqlite3 libsqlite3-dev memcached libmemcached-dev mosquitto mosquitto-clients snmpd snmp
```

## Notes

- IP addresses, ports, and database paths are always supplied through a config file, CLI argument, or environment variable — never hardcoded.
- Databases are seeded from the provided data files via each part's init script (never from hardcoded values in the source).
- A short demo video accompanies the submission, showing each part being built, run, and tested end-to-end.

## Author

Mahshid Radaie
