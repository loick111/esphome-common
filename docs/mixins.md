# Optional Mixins

Side features that can be mixed into any device

## ble-proxy.yaml

**Path:** `common/mixins/ble-proxy.yaml`

**Description:** BLE Proxy Mixin - Adds Bluetooth Low Energy tracking and proxy capabilities. Can be mixed into any device with an ESP32 that supports BLE.

No inputs or hardware configuration.

## wifi.yaml

**Path:** `common/mixins/wifi.yaml`

**Description:** WiFi Station Mixin - Adds WiFi Client (Station mode) connectivity to any device with pre-provisioned credentials. Use this to add hardcoded SSID/password. Without this mixin, devices start in AP (Access Point) mode only.

**Configuration Inputs:**

| Variable | Default | Description |
| :--- | :--- | :--- |
| `wifi_ssid` | `!secret wifi_ssid` | WiFi network SSID |
| `wifi_password` | `!secret wifi_password` | WiFi network password |

**Usage Note:**
- Include this mixin to add pre-provisioned WiFi credentials to your device firmware.
- Omit this mixin for devices that should rely on the Captive Portal (Access Point) for initial provisioning.
- Override `wifi_ssid` and `wifi_password` in your instance if you want to use substitutions instead of secrets.
