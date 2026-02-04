# Documentation Maintenance Skill

This skill defines the standard operating procedure for maintaining the `docs/` folder in the `esphome-common` repository.

## When to Use This Skill

Load this skill when:

- You have modified a YAML file in `common/`.
- You need to update the reference documentation.
- You are adding a new component (Board, Package, or Mixin).

## Workflow Overview

### Step 1: Identify Target File

Map the YAML file to its documentation file:

- Boards → `docs/boards.md`
- Packages → `docs/packages.md`
- Mixins → `docs/mixins.md`

### Step 2: Locate or Create Section

- Find the Header 2 (##) section matching the filename (e.g., `## water-meter.yaml`).
- If it doesn't exist, append a new section to the end of the file.

### Step 3: Update Content

- **Description:** Update the text description to match the top-level comments in the YAML file.
- **Inputs:** Update the "Configuration Inputs" table based on the `substitutions` block.
- **Hardware:** Update the "Hardware Details" list if the `esp32` block changed.

### Step 4: Verify

- Ensure all links to files are relative and correct.
- Ensure Markdown tables are properly aligned.
- Run `mkdocs build` locally to verify no rendering errors.

## Formatting Standards

### Section Header

```markdown
## filename.yaml

**Path:** `common/category/filename.yaml`

**Description:** [Clear, human-readable summary of the component]
```

### Configuration Inputs Table

Use this format when the component has `substitutions`:

```markdown
**Configuration Inputs:**

| Variable        | Default                   | Description                      |
| :-------------- | :------------------------ | :------------------------------- |
| `devicename`    | `water-meter-${uniqueid}` | Device identity pattern          |
| `pulse_gpio`    | `GPIO18`                  | GPIO pin connected to the sensor |
| `pulse_timeout` | `20s`                     | Timeout before sensor reads 0    |
```

**Rules:**

- Sort variables alphabetically.
- Include all substitutions defined in the component.
- For `devicename`, note that it uses `${uniqueid}` which is provided by the instance.

### Hardware Details List

Use this format if the component (typically a Board Preset) has `esp32` definitions:

```markdown
**Hardware Details:**

- **Board:** `esp32s3c-1`
- **Variant:** `esp32s3`
- **Framework:** `esp-idf`
```

## Example: Complete Component Entry

```markdown
## water-meter.yaml

**Path:** `common/packages/water-meter.yaml`

**Description:** Water Meter Package - Pure business logic for water flow measurement and consumption tracking. No hardware configuration (ESP32 variant/framework) - handled by instance files.

**Configuration Inputs:**

| Variable        | Default                   | Description                                 |
| :-------------- | :------------------------ | :------------------------------------------ |
| `devicename`    | `water-meter-${uniqueid}` | Device identity pattern (prefix + uniqueid) |
| `pulse_gpio`    | `GPIO18`                  | GPIO pin connected to the pulse meter       |
| `pulse_timeout` | `20s`                     | Timeout before sensor reads zero flow       |
```

## Special Cases

### Devices with No Inputs

If a component (like BLE Proxy mixin) has no `substitutions`, use:

```markdown
**Configuration Inputs:** None (no configuration required).
```

### Combo Devices (Override Cases)

For instances that override `devicename` (e.g., `vindriktning.yaml` combining Air Quality + BLE Proxy), document it in the instance file comment or in a separate "Examples" section if needed.

### Hardware with Nested Structure

If hardware details have nested keys (e.g., `framework: type: esp-idf`), flatten them:

```markdown
**Hardware Details:**

- **Board:** `esp32dev`
- **Framework Type:** `esp-idf`
```

## Verification Checklist

- [ ] YAML file has been modified or created.
- [ ] Corresponding `.md` file in `docs/` has been updated.
- [ ] Description matches the top-level comments in the YAML.
- [ ] All variables from `substitutions` are listed in the table.
- [ ] Hardware details (if applicable) are accurate and complete.
- [ ] Markdown tables are properly aligned (pipes `|` line up).
- [ ] All relative links work correctly.
- [ ] Run `mkdocs build` locally with no errors.

## Links & References

- Configuration files: `common/boards/*.yaml`, `common/packages/*.yaml`, `common/mixins/*.yaml`
- Documentation root: `docs/`
- Site configuration: `mkdocs.yml`
- CI/CD: `.github/workflows/docs.yml` (builds & deploys on push to master)
