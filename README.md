# DIRIGERA

> This page and its content is not affiliated with IKEA of Sweden AB.

## General Information

### Hardware

| Chip   | Part No.                                                                                                         |       |
| ------ | ---------------------------------------------------------------------------------------------------------------- | ----- |
| SoC    | [STM32MP157CAB3](https://www.st.com/resource/en/datasheet/stm32mp157c.pdf)                                       |       |
| RAM    | [M15T4G16256A](https://www.alldatasheet.com/datasheet-pdf/pdf/1284521/ESMT/M15T4G16256A.html)                    | 500MB |
| e-MMC  | [THGBMNG5D1LBAIL](https://www.alldatasheet.com/datasheet-pdf/pdf/1244282/TOSHIBA/THGBMNG5D1LBAIL.html)           | 4GB   |
| Wi-Fi  | muRata Type2AW                                                                                                   |       |
| Zigbee | [MGM210L](https://www.silabs.com/documents/public/data-sheets/mgm210l-datasheet.pdf) based on  [EFR32MG21](https://www.silabs.com/documents/public/data-sheets/efr32mg21-datasheet.pdf) |       |

### Software

The DIRIGERA hub runs a Linux-based operating system. Key services identified from boot logs:

- **mDNS/DNS-SD**: Avahi is used for service discovery.
- **MQTT**: Mosquitto broker (v3.1/v3.1.1) handles internal communication.
- **Updates**: RAUC (Robust Auto-Update Controller) is used for system updates.
- **Home Integration**: Daemons for Apple HomeKit and Sonos.
- **API**: A Nexus REST API server runs on port `8443`.
- **Zigbee**: A dedicated ZigBee IoTC daemon manages the mesh network.

## Discovery

The hub advertises itself on the network using mDNS. You can find it by scanning for `_ihsp._tcp.local` services:

```bash
mdns-scan
# Look for: XXX._ihsp._tcp.local -> host "XXX.local"
```

## Authentication

The API uses OAuth 2.0 with Proof Key for Code Exchange (PKCE) over HTTPS (port 8443). Note that the hub uses a self-signed certificate.

1. **Initiate**: Send a GET request to `/v1/oauth/authorize` with a code challenge.
2. **Authorize**: Physically press the action button on the DIRIGERA hub.
3. **Token**: Send a POST request to `/v1/oauth/token` with the code and code verifier to receive an access token.

## Usage

### 1. Get an Auth Token
Use the `get_auth_token.py` script to generate a bearer token. You will need to edit the script to set the correct IP address of your hub.

```bash
python3 scripts/get_auth_token.py
```
Follow the prompts: press the action button on the hub when requested.

### 2. Control Devices
Use the `Client` class in `scripts/dirigera.py` to interact with the hub.

```python
from scripts.dirigera import Client

host = "DIRIGERA-IP-OR-HOSTNAME"
access_token = "YOUR_ACCESS_TOKEN"

client = Client(host, access_token)

# List all devices
devices = client.list_devices()
print(devices)

# Example: Turn on a light
# client.edit_device_state(id='DEVICE_ID', new_state={'attributes': {'isOn': True}})
```

## Images

Images of the circuit board are stored in the [images](images/) folder. This includes detailed images of the main chips and the UART port.

## Logs

The [logs](logs/) folder contains system logs, such as `boot.log`, which provides insights into the startup process and hardware initialization.

## Scripts

- `scripts/dirigera.py`: A Python client for interacting with the DIRIGERA hub API.
- `scripts/get_auth_token.py`: A script for getting an auth token from the DIRIGERA hub.

## Other repos worth looking into

- [mattias73andersson/dirigera-client-poc](https://github.com/mattias73andersson/dirigera-client-poc)
- [dvdgeisler/DirigeraClient](https://github.com/dvdgeisler/DirigeraClient)

## License

The MIT License (MIT)

Copyright (c) 2022 Wouter van der Wal
