# ESPHome Common

A shared collection of reusable ESPHome configuration components organized by responsibility: infrastructure, connectivity, board hardware, optional capabilities (mixins), and device-specific business logic.

## Architecture

This repository follows a **strict 5-layer architecture**:

| Layer              | Location           | Purpose                                                      |
| :----------------- | :----------------- | :----------------------------------------------------------- |
| **Infrastructure** | `common/base.yaml` | Core services (Logging, API, OTA, WiFi AP, Diagnostics)      |
| **Board Presets**  | `common/boards/`   | Hardware standardization (GPIO pins, I2C/UART setup)         |
| **Mixins**         | `common/mixins/`   | Optional side features (BLE Proxy, WiFi Station, Web Server) |
| **Packages**       | `common/packages/` | Core business logic (Sensors, automations)                   |
| **Instances**      | `examples/`        | Device composition and identity                              |

## Usage

### Remote Usage (Recommended)

The preferred way to use this repository is as a remote package. This allows you to leverage the shared logic while maintaining your device configurations in their own repositories.

```yaml
# my-device.yaml
substitutions:
  # Device Identity
  uniqueid: 'device-001'

  # WiFi Credentials
  # Define these subtitutions only if you want to override the defaults
  # wifi_ssid: !secret wifi_ssid
  # wifi_password: !secret wifi_password
  # wifi_captive: !secret wifi_captive

  # ESPHome Access
  # Generate a 32-byte base64 key for API encryption, e.g. using `openssl rand -base64 32`
  api_key: 'BASE64_KEY_HERE'
  web_password: !secret web_password
  # Use a strong password for OTA updates, e.g. `openssl rand -base64 16`
  ota_password: 'OTA_PASSWORD_HERE'

  # Water Meter Specific
  pulse_gpio: GPIO4
  pulse_timeout: 10s

packages:
  remote:
    url: https://github.com/loick111/esphome-common
    ref: master
    refresh: always
    files:
      - common/base.yaml
      - common/boards/esp32dev.yaml
      - common/mixins/wifi.yaml
      - common/mixins/ble-proxy.yaml
```

### Local Usage (Development & Testing)

Use local includes when contributing to this library or validating changes against the `examples/` directory.

```yaml
# examples/my-device.yaml
substitutions:
  # Device Identity
  uniqueid: 'device-001'

  # WiFi Credentials
  # Define these subtitutions only if you want to override the defaults
  # wifi_ssid: !secret wifi_ssid
  # wifi_password: !secret wifi_password
  # wifi_captive: !secret wifi_captive

  # ESPHome Access
  # Generate a 32-byte base64 key for API encryption, e.g. using `openssl rand -base64 32`
  api_key: 'BASE64_KEY_HERE'
  web_password: !secret web_password
  # Use a strong password for OTA updates, e.g. `openssl rand -base64 16`
  ota_password: 'OTA_PASSWORD_HERE'

packages:
  - !include ../common/boards/esp32dev.yaml
  - !include ../common/base.yaml
  - !include ../common/mixins/wifi.yaml
  - !include ../common/mixins/ble-proxy.yaml
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
