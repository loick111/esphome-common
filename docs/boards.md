# Board Presets

Hardware standardization & pin definitions

## esp32dev.yaml

**Path:** `common/boards/esp32dev.yaml`

**Description:** ESP32 DevKit V1 Board Preset Generic ESP32-WROOM-32D development board Provides standardized GPIO pin mappings

**Hardware Details:**

- `board`: esp32dev
- `type`: esp-idf

**Configuration Inputs:**

- `pin_extra_1` = `GPIO23`
- `pin_extra_2` = `GPIO19`
- `pin_i2c_scl` = `GPIO22`
- `pin_i2c_sda` = `GPIO21`
- `pin_status_led` = `GPIO2`
- `pin_uart_rx` = `GPIO16`
- `pin_uart_tx` = `GPIO17`

## esp32s3.yaml

**Path:** `common/boards/esp32s3.yaml`

**Description:** ESP32-S3 DevKit Board Preset ESP32-S3-DevKitC-1 development board Provides standardized GPIO pin mappings optimized for S3

**Hardware Details:**

- `board`: esp32
- `type`: esp-idf
- `variant`: esp32s3

**Configuration Inputs:**

- `pin_extra_1` = `GPIO12`
- `pin_extra_2` = `GPIO13`
- `pin_i2c_scl` = `GPIO9`
- `pin_i2c_sda` = `GPIO8`
- `pin_status_led` = `GPIO2`
- `pin_uart_rx` = `GPIO16`
- `pin_uart_tx` = `GPIO17`
