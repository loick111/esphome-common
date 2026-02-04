# Agent Operational Guidelines for ESPHome Common

This repository contains shared ESPHome configuration files organized by responsibility: infrastructure, connectivity, board hardware, optional capabilities (mixins), and device-specific business logic.

## 1. Architecture

The codebase follows a **strict separation of concerns** with 5 layers:

| Layer              | Location                 | Purpose                  | Owns                                                                          |
| :----------------- | :----------------------- | :----------------------- | :---------------------------------------------------------------------------- |
| **Instance**       | `examples/*.yaml`        | Device Identity          | Device name (via `uniqueid`), API keys, instance-specific overrides           |
| **Mixins**         | `common/mixins/*.yaml`   | Optional Capabilities    | Side features (BLE Proxy, WiFi, etc.) that can be mixed into any device       |
| **Business Logic** | `common/packages/*.yaml` | Core Functional Features | Default `devicename` pattern, sensors, filters, lambdas, globals, automations |
| **Board Presets**  | `common/boards/*.yaml`   | Hardware Standardization | ESP32 variant, pins, I2C/UART setup, LED pins                                 |
| **Infrastructure** | `common/base.yaml`       | Core Services            | Logging, API encryption, OTA, diagnostics                                     |

## 2. Directory Structure

```
common/
├── base.yaml              # Infrastructure: API, OTA, Logger, Diagnostics
├── boards/                # Hardware Presets (GPIO, variants, protocols)
│   ├── esp32dev.yaml      # ESP32 DevKit V1 (pins: I2C 21/22, UART 16/17)
│   └── esp32s3.yaml # ESP32-S3 DevKit (pins: I2C 8/9, UART 16/17)
├── mixins/                # Optional Capabilities (can be mixed into any device)
│   ├── ble-proxy.yaml     # BLE Tracker + Proxy capability
│   └── wifi.yaml          # WiFi connectivity with credentials
└── packages/              # Core Business Logic (no hardware config)
    ├── air-quality.yaml   # Environmental sensors (BME280, PM1006)
    └── water-meter.yaml   # Pulse meter + Flow calculations

examples/
├── secrets.yaml           # Mock secrets for validation
├── generic-device.yaml    # ESP32dev + Infrastructure + WiFi mixin
├── ble-proxy-a5800b6a.yaml # ESP32-S3 + BLE Proxy mixin + WiFi mixin (standalone device)
├── air-quality.yaml       # ESP32-S3 + Air Quality package + WiFi mixin
├── water-meter.yaml       # ESP32dev + Water Meter package + WiFi mixin
└── vindriktning.yaml      # ESP32-S3 + Air Quality package + BLE Proxy + WiFi mixins (combo)
```

## 3. Environment & Tooling

The environment is equipped with the `esphome` CLI and `yamllint`.

- **Version**: 2026.1.3 (or later)
- **Commands**:
  - `esphome config <file>`: Validates configuration
  - `yamllint .`: Validates YAML syntax and formatting

## 4. Verification Workflow (How to "Test")

Changes must be validated against example device configurations in the `examples/` directory.

### Step 1: Ensure Mock Secrets Exist

File: `examples/secrets.yaml`

```yaml
wifi_ssid: 'test_ssid'
wifi_password: 'test_password'
hcs_password: 'test_password'
wifi_captive: 'test_password'
```

### Step 2: Validate Using Relevant Examples

```bash
# Validate after modifying common/base.yaml:
esphome config examples/generic-device.yaml

# Validate after modifying common/packages/water-meter.yaml:
esphome config examples/water-meter.yaml

# Validate after modifying common/boards/esp32s3.yaml:
esphome config examples/air-quality.yaml
esphome config examples/ble-proxy-a5800b6a.yaml

# Validate after modifying common/mixins/ble-proxy.yaml:
esphome config examples/ble-proxy-a5800b6a.yaml
esphome config examples/vindriktning.yaml

# Validate after modifying common/mixins/wifi.yaml:
esphome config examples/generic-device.yaml
esphome config examples/water-meter.yaml
esphome config examples/air-quality.yaml
```

**Success Criteria**: Output ends with `INFO Configuration is valid!`

## 5. Code Style Guidelines

### YAML Formatting

- **Indentation**: Strictly **2 spaces**. Never use tabs.
- **Lists**: Use `- ` (dash + space).
- **Spacing**: Add empty lines between top-level components.

### Naming Conventions

- **Configuration Keys**: `snake_case` (e.g., `power_save_mode`).
- **Entity Names**: **Title Case** (e.g., `name: 'WiFi Signal'`).
- **Entity IDs**: `snake_case` (e.g., `id: bme280_temperature`).
- **Substitutions**: `snake_case` (e.g., `${pulse_gpio}`, `${pin_i2c_sda}`).

### Strings & Quoting

- **Unquoted**: Simple alphanumeric (e.g., `admin`).
- **Single Quotes**: For strings with spaces/special chars (e.g., `name: 'WiFi Signal'`).
- **Double Quotes**: Only for escape sequences.

### ESPHome-Specific Rules

- **!secret**: Use for credentials and sensitive values.
- **!include**: Use for modular configuration.
- **Substitutions**: Define in boards/instances, consume with `${var}`.

## 6. Layers Explained

### Mixins vs Packages: When to Use Which?

**Mixins** (`common/mixins/`):

- Pure, reusable capabilities with no dependencies
- Can be mixed into _any_ device (if resources permit)
- Examples: BLE Proxy, WiFi Extender, Debug Logger
- No assumptions about what else will be in the device

**Packages** (`common/packages/`):

- Complete functional units that define a device's primary purpose
- Examples: Water Meter, Air Quality Monitor
- Often define multiple related sensors and automations
- More substantial logic than mixins

### Example: BLE Proxy Mixin

The `ble-proxy` mixin is a side feature that can be added to any ESP32:

```yaml
# examples/ble-proxy-a5800b6a.yaml (Dedicated BLE Proxy Device)
packages:
  board: !include ../common/boards/esp32s3.yaml
  base: !include ../common/base.yaml
  wifi: !include ../common/wifi.yaml
  ble_proxy: !include ../common/mixins/ble-proxy.yaml  ← Mixin

esphome:
  name: ble-proxy-a5800b6a
  project:
    name: 'loick111.ble-proxy'
```

```yaml
# examples/vindriktning.yaml (Air Quality Monitor PLUS BLE Proxy)
packages:
  board: !include ../common/boards/esp32s3.yaml
  base: !include ../common/base.yaml
  wifi: !include ../common/wifi.yaml
  air_quality: !include ../common/packages/air-quality.yaml  ← Primary
  ble_proxy: !include ../common/mixins/ble-proxy.yaml       ← Optional Mixin
```

## 7. How to Create a Custom Board Preset

Example: Adding a custom PCB board.

### Step 1: Create Board File

File: `common/boards/my-custom-pcb.yaml`

```yaml
# Hardware specifications for my-custom-pcb
# Define GPIO pins and ESP32 variant in one place

esp32:
  variant: esp32s3
  board: esp32s3c-1
  framework:
    type: esp-idf

substitutions:
  # Standardized names for common buses
  pin_i2c_sda: GPIO11
  pin_i2c_scl: GPIO12
  pin_uart_tx: GPIO13
  pin_uart_rx: GPIO14
  pin_status_led: GPIO2
  pin_extra_1: GPIO15
  pin_extra_2: GPIO16
```

### Step 2: Use Board in Instance

File: `examples/my-device-with-custom-pcb.yaml`

```yaml
packages:
  board: !include ../common/boards/my-custom-pcb.yaml
  base: !include ../common/base.yaml
  wifi: !include ../common/wifi.yaml
  logic: !include ../common/packages/air-quality.yaml

substitutions:
  devicename: my-custom-device-01
  api_key: '...'
  ota_password: '...'

esphome:
  name: ${devicename}

# Use board preset pins (they're already defined!)
i2c:
  sda: ${pin_i2c_sda}
  scl: ${pin_i2c_scl}
```

### Step 3: Verify

```bash
esphome config examples/my-device-with-custom-pcb.yaml
```

## 8. How to Create a New Device Type

Example: Adding a "temperature-sensor" device using existing board.

### Step 1: Create Business Logic Package

File: `common/packages/temperature-sensor.yaml`

```yaml
# No hardware config here - only sensors and logic
sensor:
  - platform: dht
    pin: ${dht_pin}
    temperature:
      name: 'Temperature'
    humidity:
      name: 'Humidity'
    update_interval: 60s
```

### Step 2: Create Instance File

File: `examples/temperature-sensor.yaml`

```yaml
packages:
  board: !include ../common/boards/esp32dev.yaml # Reuse existing board
  base: !include ../common/base.yaml
  wifi: !include ../common/wifi.yaml
  sensor: !include ../common/packages/temperature-sensor.yaml

substitutions:
  devicename: temp-sensor-01
  dht_pin: GPIO4 # Override if needed, or use board default
  api_key: '...'
  ota_password: '...'

esphome:
  name: ${devicename}
```

### Step 3: Verify

```bash
esphome config examples/temperature-sensor.yaml
```

## 9. How to Create a New Mixin

Example: Adding a "ota-web-update" mixin for OTA updates via web server.

### Step 1: Create Mixin File

File: `common/mixins/ota-web-update.yaml`

```yaml
# Lightweight mixin: adds web-based OTA update capability
# Can be mixed into any device without conflicts

web_server:
  port: 8888
  version: 3
```

### Step 2: Use in Any Device

File: `examples/my-device.yaml`

```yaml
packages:
  board: !include ../common/boards/esp32dev.yaml
  base: !include ../common/base.yaml
  wifi: !include ../common/wifi.yaml
  packages/water_meter: !include ../common/packages/water-meter.yaml
  ota_update: !include ../common/mixins/ota-web-update.yaml  ← Optional Mixin
```

## 10. Error Handling & Troubleshooting

**"Component not found: packages"**

- _Cause_: Attempting to validate a partial file directly.
- _Fix_: Validate an example file that includes it.

**"Secret 'xyz' not defined"**

- _Cause_: `examples/secrets.yaml` missing required key.
- _Fix_: Add the key to `examples/secrets.yaml`.

**"Substitution 'abc' not defined"**

- _Cause_: Instance file doesn't provide substitution needed by a package/board/mixin.
- _Fix_: Add substitution to instance file's `substitutions:` block or check board preset.

**"Component X requires component Y"**

- _Cause_: Missing hardware configuration (I2C, UART, pins) in instance or board.
- _Fix_: Add hardware config (e.g., `i2c:`, `uart:`, `spi:`) to board file or instance.

## 11. General Agent Behavior

- **Read First**: Read `common/boards/`, `common/mixins/`, `common/packages/`, and relevant `examples/` files.
- **Atomic Changes**: Edit one component/package/board/mixin at a time.
- **Verify Always**: Run `esphome config examples/...` before confirming completion.
- **Board Rule**: Create new board presets in `common/boards/` for custom hardware.
- **Mixin Rule**: Create new mixins in `common/mixins/` for optional, reusable side features.
- **No Hardware in Packages/Mixins**: Never add ESP32 variant, board, pin definitions, or hardware buses to package or mixin files.
- **Instance-Only Overrides**: If device needs pin override, add to instance file substitutions (don't edit board).

### Device Naming Convention

The repository follows a **mandatory naming pattern** for consistency:

1. **Every instance MUST define `uniqueid`:**

   ```yaml
   substitutions:
     uniqueid: water-meter-001
   ```

2. **Packages define default `devicename` pattern:**

   ```yaml
   # common/packages/water-meter.yaml
   substitutions:
     devicename: water-meter-${uniqueid} # Prefix is package-specific
   ```

3. **Instances can override `devicename` if needed:**
   ```yaml
   # examples/my-device.yaml
   substitutions:
     uniqueid: my-id-123
     devicename: custom-name-${uniqueid} # Override default pattern
   ```

**Why?** The `uniqueid` is the source of truth for device identity. The `devicename` pattern allows:

- Automatic, consistent naming without duplication
- Package-specific prefixes (water-meter, air-quality, etc.)
- Override capability for special cases (e.g., combo devices like vindriktning)

## 12. Documentation Workflow

Documentation is maintained **manually** in the `docs/` directory to allow for rich context, nuanced examples, and human insight.

### Protocol

**Rule:** When editing any YAML file in `common/`, you **MUST** manually update the corresponding reference in `docs/`.

**How to Update Docs:**

1.  Load the **Documentation Skill** for detailed formatting standards:
    ```
    Load the documentation skill: .opencode/skills/documentation/SKILL.md
    ```
2.  Follow the standard format (Section Header, Configuration Inputs table, Hardware Details list).
3.  Verify by running `mkdocs build` locally.

### Files Structure

The `docs/` directory is organized by component type:

- `docs/index.md` - Landing page & architecture overview
- `docs/boards.md` - Board presets with pin mappings & hardware details
- `docs/packages.md` - Business logic packages with required inputs
- `docs/mixins.md` - Optional mixins with capabilities

### Deployment

The documentation is automatically built and deployed to GitHub Pages on every push to `master`:

- The CI pipeline (`.github/workflows/docs.yml`) runs `mkdocs build` and deploys to the `gh-pages` branch.
- Live documentation is available at: https://loick.github.io/esphome-common

### YAML File Requirements

All YAML files in `common/` **should** include:

1.  **Top-level header comments** (before any YAML keys):

    ```yaml
    # Component Title
    # Brief description of what this component does
    # Additional context if needed
    ```

2.  **Substitutions block** (for inputs/configuration):

    ```yaml
    substitutions:
      # Comment describing the input
      key_name: default_value

      # Another input
      another_key: another_default
    ```

3.  **Hardware details** (for boards only, in `esp32:` block):
    ```yaml
    esp32:
      variant: esp32s3
      board: esp32s3c-1
      framework:
        type: esp-idf
    ```

These comments serve as reference material for human-written documentation and help maintainers understand the intent of each component.
