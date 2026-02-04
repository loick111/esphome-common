# ESPHome Common Documentation

Welcome to the ESPHome Common library documentation.

This is a shared collection of reusable ESPHome configuration components
organized by responsibility: infrastructure, connectivity, board hardware,
optional capabilities (mixins), and device-specific business logic.

## Quick Start

Choose a category below to explore available components:

- **[Board Presets](boards.md)** - Hardware standardization & pin definitions
- **[Business Logic Packages](packages.md)** - Core functional features & sensors
- **[Optional Mixins](mixins.md)** - Side features that can be mixed into any device

## Architecture Overview

The repository follows a strict 5-layer architecture:

| Layer | Purpose |
| :--- | :--- |
| **Infrastructure** | Core services (Logging, API, OTA, WiFi AP, Diagnostics) |
| **Board Presets** | Hardware standardization (GPIO pins, variants) |
| **Mixins** | Optional side features (BLE Proxy, WiFi Station, Web Server) |
| **Packages** | Core business logic (Sensors, Automations) |
| **Instances** | Device composition & identity |

## Infrastructure: base.yaml

The `common/base.yaml` file provides the foundation:

**Configuration Inputs:**

| Variable | Default | Description |
| :--- | :--- | :--- |
| `log_level` | `INFO` | Logging level (DEBUG, INFO, WARNING, ERROR) |
| `web_username` | `admin` | Web server authentication username |
| `web_password` | `!secret web_password` | Web server authentication password |

**Features:**
*   **Logger** - Configurable logging level
*   **API** - Encryption key for Home Assistant communication
*   **OTA** - Over-the-air updates with password protection
*   **Web Server** - Authenticated web interface (port 80)
*   **WiFi AP (Access Point)** - Captive portal for initial provisioning
*   **Diagnostics** - Uptime, WiFi signal, internal temperature sensors

**Note:** WiFi Station (Client) credentials are *not* included in base.yaml. Use the WiFi mixin to add pre-provisioned credentials, or rely on the AP for provisioning.