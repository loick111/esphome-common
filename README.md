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

## Quick Start

### 1. Create a Device Configuration

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

### 2. Validate Configuration

```bash
esphome config examples/my-device.yaml
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
