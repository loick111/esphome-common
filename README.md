# ESPHome Common

A shared collection of reusable ESPHome configuration components organized by responsibility: infrastructure, connectivity, board hardware, optional capabilities (mixins), and device-specific business logic.

## Architecture

This repository follows a **strict 5-layer architecture**:

| Layer | Location | Purpose |
| :--- | :--- | :--- |
| **Infrastructure** | `common/base.yaml` | Core services (Logging, API, OTA, WiFi AP, Diagnostics) |
| **Board Presets** | `common/boards/` | Hardware standardization (GPIO pins, I2C/UART setup) |
| **Mixins** | `common/mixins/` | Optional side features (BLE Proxy, WiFi Station, Web Server) |
| **Packages** | `common/packages/` | Core business logic (Sensors, automations) |
| **Instances** | `examples/` | Device composition and identity |

## Usage

### Local Usage (Quick Start)

Create an instance file that composes layers:

```yaml
# examples/my-device.yaml
substitutions:
  uniqueid: my-device-001
  api_key: 'YOUR_API_KEY_HERE'
  ota_password: 'YOUR_OTA_PASSWORD'

packages:
  board: !include ../common/boards/esp32dev.yaml
  base: !include ../common/base.yaml
  wifi: !include ../common/mixins/wifi.yaml
  business_logic: !include ../common/packages/water-meter.yaml

esphome:
  name: ${devicename}
```

### External Usage (Remote Packages)

You can use these components in devices outside this repository using the `remote` package feature. This allows you to maintain a separate device configuration while leveraging the shared logic.

**Prerequisites:**
1. Define **Identity & Secrets** locally (substitutions).
2. Define **Hardware** locally (or import a board preset).
3. Import **Base & Logic** from the remote repo.

```yaml
substitutions:
  # Device Identity
  uniqueid: "device-001"
  devicename: my-device-${uniqueid}

  # Secrets (Must be in your local secrets.yaml)
  wifi_ssid: !secret wifi_ssid
  wifi_password: !secret wifi_password
  web_password: !secret web_password
  wifi_captive: !secret wifi_captive

  # Security
  api_key: "BASE64_KEY_HERE"
  ota_password: "OTA_PASSWORD_HERE"

packages:
  remote:
    url: https://github.com/loick111/esphome-common
    ref: master
    refresh: always
    files:
      - common/base.yaml
      - common/boards/esp32s3.yaml
      - common/mixins/wifi.yaml
      - common/packages/air-quality.yaml

# Hardware Definitions (if not fully covered by board preset)
i2c:
  sda: 12
  scl: 13
  scan: true
```

## Project Structure

```
common/
├── base.yaml              # Infrastructure
├── boards/                # Hardware Presets
├── mixins/                # Optional Capabilities
└── packages/              # Core Business Logic

examples/                   # Device configurations (validation & reference)
docs/                       # Component reference documentation
```

## Documentation

- **[Full Documentation](docs/index.md)** - Detailed component reference
- **[Agent Guidelines](AGENTS.md)** - Development workflow & architecture rules

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
