# Business Logic Packages

Core functional features & sensors

## air-quality.yaml

**Path:** `common/packages/air-quality.yaml`

**Description:** Air Quality Package (IKEA Vindriktning) Pure business logic for environmental sensors and calculations No hardware configuration (ESP32 variant/framework) - handled by instance files

**Configuration Inputs:**
- `devicename` = `air-quality-${uniqueid}`

## water-meter.yaml

**Path:** `common/packages/water-meter.yaml`

**Description:** Water Meter Package Pure business logic for water flow measurement and consumption tracking No hardware configuration (ESP32 variant/framework) - handled by instance files

**Configuration Inputs:**
- `devicename` = `water-meter-${uniqueid}`
- `pulse_gpio` = `GPIO18`
- `pulse_timeout` = `20s`
