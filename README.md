# esphome-magnetometer-water-gas-meter [![Made for ESPHome](https://img.shields.io/badge/Made_for-ESPHome-black?logo=esphome)](https://esphome.io)

This [ESPHome](https://esphome.io) package allows reading your water meter or gas meter using the QMC5883L or QMC5883P or HMC5883L or MMC5603 or MLX90393, a triple-axis magnetometer.

TLDR; Add this to your ESPHome device configuration:

```yaml
substitutions:
  volume_unit: 'gal'
  i2c_scl: GPIO5  # D1
  i2c_sda: GPIO4  # D2
  # Set to false only if needed during manual calibration.
  # Do not keep them at false since these slow down the ESP device
  # and reduce the accuracy during high flow.
  hide_magnetic_field_strength_sensors: 'true'
  hide_half_rotations_total_sensor: 'true'

packages:
  meter:
    url: https://github.com/tronikos/esphome-magnetometer-water-gas-meter
    ref: main
    file: esphome-water-meter.yaml
    # Or for gas meter:
    # file: esphome-gas-meter.yaml
    # Or if you are using QMC5883P instead of QMC5883L:
    # files: [esphome-water-meter.yaml, qmc5883p.yaml]
    # Or if you are using HMC5883L instead of QMC5883L:
    # files: [esphome-water-meter.yaml, hmc5883l.yaml]
    # Or if you are using MMC5603 instead of QMC5883L:
    # files: [esphome-water-meter.yaml, mmc5603.yaml]
    # Or if you are using MLX90393 instead of QMC5883L:
    # files: [esphome-water-meter.yaml, mlx90393.yaml]
    refresh: 0s
```

<img src="https://github.com/user-attachments/assets/eec2ee3b-b133-458f-bf28-de8dc780e3d4" alt="Water meter in Home Assistant" width=40%>

## Compatibility

### Water meter

The magnetometer is used to read the rotating magnet inside your water meter.

This should be compatible with all the water meters the Flume water sensor is compatible with, which is [compatible](https://help.flumewater.com/en/articles/1618594-is-the-flume-device-compatible-with-all-water-meters) with about 95% of water meters in the United States.

To verify compatibility follow [this](https://help.flumewater.com/en/articles/1618594-is-the-flume-device-compatible-with-all-water-meters). Alternatively, install the Sensors app on your phone, place your phone next to the meter, and see if the Geomagnetic Field sensors are changing while water is running.

[Video](https://www.youtube.com/watch?v=M9nVkSZ6_H4) showing the internals of a water meter.

See [Figure 1: Nutating disc operation](https://www.instrumart.com/assets/RCDL-manual.pdf)

"The metering principle, known as positive displacement, is based on the continuous filling and discharging of the measuring
chamber. Controlled clearances between the disc and the chamber provide precise measurement of each volume cycle.
As the disc nutates, the center spindle rotates a magnet. The movement of the magnet is sensed through the meter wall
by a follower magnet or by various sensors. Each revolution of the magnet is equivalent to a fixed volume of fluid, which is
converted to any engineering unit of measure for totalization, indication or process control."

#### Magnetometer position for water meter

<img src="https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/130f871c-dfd5-45e2-9837-b23bf8f545e7" alt="water meter sensor position" width=40%>

### Gas meter

> **⚠️ Safety Disclaimer:** Placing DIY electronics near gas infrastructure carries inherent risks. Ensure you are compliant with local regulations and are personally comfortable with the safety implications before proceeding. Use at your own risk.

The magnetometer is used to read the diaphragm that expands and contracts inside your gas meter.

This should be compatible with all diaphragm/bellows meters which are the most common type of gas meter, seen in almost all residential and small commercial installations.

To verify compatibility install the Sensors app on your phone, place your phone next to the meter, and see if the Geomagnetic Field sensors are changing while gas is running.

[Video](https://www.youtube.com/watch?v=WKlVmXe46w8) showing the internals of a gas meter.

#### Magnetometer position for gas meter

<img src="https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/9d5a469f-6b92-442e-b2ec-e0e2b57eead3" alt="gas meter sensor position" width=40%>

## Hardware installation

### Parts

- ESP8266 or ESP32 with power adapter
  - I placed mine inside the garage
  - For high flow meters a dual core ESP32 is strongly preferred
- QMC5883L or QMC5883P or HMC5883L or MMC5603 or MLX90393 magnetometer
  - I placed mine in the water meter box 20ft away from the garage
- Ethernet cable
  - I used 32.8ft or 10m direct burial CAT6. A user has reported they successfully used 75ft or 22.9m direct burial CAT6.
  - CAT6 is preferred because of its lower capacitance. CAT5 50ft or 15m [should work](https://www.youtube.com/watch?v=6v1KZBRZRCI). For 100ft you will need an active terminator such as [LTC4311](https://www.youtube.com/watch?v=nhWPxO7jx_o).
  - Do not use thermostat wire, bell wire, or any other low voltage wire. You will have communication errors or instability. You really need to be using twisted pair cables with proper shielding and lower capacitance such as CAT6.
- Some way to weatherproof the magnetometer. Some options:
  - Adhesive 4:1 heat shrink tubing (this is what I used)
  - Liquid electrical tape
  - Silicone sealant
  - Nail polish
  - Hot glue
- Some way to mount the magnetometer on the meter. Some options:
  - Cable zip tie (this is what I used)
  - Duct tape
- Conduit for the ethernet cable. Can be skipped if using direct burial ethernet cable.

### Wiring

QMC5883L | ESP8266
--- | ---
VCC | 3.3V
GND | GND
SCL | D1
SDA | D2

If your magnetometer module has its own 3.3V regulator you can connect the sensor's VCC to 5V.
The ethernet cable has 4 twisted pairs of wires. Use any solid wire color for the 4 above pins. Tie the 4 white wires together with the GND solid wire. You might need to use a header pin for the GND. If you use a header pin cut the 5 GND wires shorter to avoid the ball of wires I had...

![magnetometer wiring](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/c7052171-eee1-44cb-90f4-76cad4e46334)
![magnetometer in adhesive heat shrink tubing](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/0ca8c738-63c2-4d38-ae35-42bb219b88d1)
![d1 mini wiring](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/b8c3df8d-8111-415b-aecc-64d9c5a290c1)
![d1 mini lego case](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/6d8d85a0-b00c-4db9-9484-3b345e73f848)
![driveway](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/69a47f3e-8d8f-4c2e-aec8-14cb729b48a4)

## Software installation

1. Set up **ESPHome**, if you don't have it already, by following [Getting Started with ESPHome and Home Assistant](https://esphome.io/guides/getting_started_hassio.html).
2. In the **ESPHome Dashboard** select **New device**, **Continue**, give a name: e.g. Water meter, **Next**, select device type based on the ESP chip used e.g. ESP8266.
3. In the **Configuration created!** page select **Skip** to skip installation for now until we make a few changes.
4. Select **Edit** on the created configuration e.g. water-meter.yaml.
5. Skip this step if you used an `esp32`. Change `esp8266` section to:

    ```yaml
    esp8266:
      board: d1_mini
      restore_from_flash: true

    preferences:
      flash_write_interval: 60min
    ```

6. Add the following (either at the beginning or the end of the file):

    ```yaml
    substitutions:
      # For water one of: CCF, ft³, gal, L, m³
      # For gas one of: CCF, ft³, m³
      # For better accuracy avoid using large units like CCF and m³.
      # You can always change the unit later in Home Assistant.
      volume_unit: 'gal'
      i2c_scl: GPIO5  # D1
      i2c_sda: GPIO4  # D2
      # Set to false only if needed during manual calibration.
      # Do not keep them at false since these slow down the ESP device
      # and reduce the accuracy during high flow.
      hide_magnetic_field_strength_sensors: 'true'
      hide_half_rotations_total_sensor: 'true'

    packages:
      meter:
        url: https://github.com/tronikos/esphome-magnetometer-water-gas-meter
        ref: main
        file: esphome-water-meter.yaml
        # Or for gas meter:
        # file: esphome-gas-meter.yaml
        # Or if you are using QMC5883P instead of QMC5883L:
        # files: [esphome-water-meter.yaml, qmc5883p.yaml]
        # Or if you are using HMC5883L instead of QMC5883L:
        # files: [esphome-water-meter.yaml, hmc5883l.yaml]
        # Or if you are using MMC5603 instead of QMC5883L:
        # files: [esphome-water-meter.yaml, mmc5603.yaml]
        # Or if you are using MLX90393 instead of QMC5883L:
        # files: [esphome-water-meter.yaml, mlx90393.yaml]
        refresh: 0s
    ```

7. Change the values in the `substitutions` section based on your settings, e.g. if you have used different pins, or if you prefer a different unit.
8. Your configuration should now look something like the following:

    ```yaml
    substitutions:
      volume_unit: 'gal'
      i2c_scl: GPIO5  # D1
      i2c_sda: GPIO4  # D2
      # Set to false only if needed during manual calibration.
      # Do not keep them at false since these slow down the ESP device
      # and reduce the accuracy during high flow.
      hide_magnetic_field_strength_sensors: 'true'
      hide_half_rotations_total_sensor: 'true'

    packages:
      meter:
        url: https://github.com/tronikos/esphome-magnetometer-water-gas-meter
        ref: main
        file: esphome-water-meter.yaml
        # Or for gas meter:
        # file: esphome-gas-meter.yaml
        refresh: 0s

    esphome:
      name: water-meter
      friendly_name: Water meter

    esp8266:
      board: d1_mini
      restore_from_flash: true

    preferences:
      flash_write_interval: 60min

    # Enable logging
    logger:

    # Enable Home Assistant API
    api:
      encryption:
        key: "<keep the key the ESPHome Dashboard generated for you>"

    ota:
      - platform: esphome
        password: "<keep the password the ESPHome Dashboard generated for you>"

    wifi:
      ssid: !secret wifi_ssid
      password: !secret wifi_password

      # Enable fallback hotspot (captive portal) in case wifi connection fails
      ap:
        ssid: "water-meter Fallback Hotspot"
        password: "<keep the password the ESPHome Dashboard generated for you>"

    captive_portal:
    ```

9. Select **Save** and then **Install**.
10. Only for the first install select **Plug into this computer**. For subsequent updates/installs you can install **Wirelessly**.
11. Select **Download project** to save a bin file.
12. Select **Open ESPHome Web**, **Connect**, **Install downloaded project**.
13. In the **Install your existing ESPHome project** page select **Choose File**, select the previously downloaded bin file, and select **Install**.
14. Home Assistant should auto-discover your new device.

## Calibration

### Detection Algorithm

Three detection algorithms are available, controlled by the **"Detection algorithm"** select:

Algorithm | Best For
--- | ---
**Quadrature** (default) | **Recommended**. Uses all 3 axes for direction-aware counting, thermal slosh rejection, and 4x faster low-flow leak detection
**Adaptive** | Handles temperature drift automatically on the primary axis
**Threshold** | Backward compatibility with existing installations

> **Recommendation:** Use the Quadrature or Adaptive algorithm. Threshold is available for backward compatibility with existing installations.

**Quadrature algorithm**:

- Tracks all 3 axes (`x`, `y`, `z`) of a single magnetometer with per-axis adaptive min/max trackers and projects them onto the 2D magnet rotation plane (`I = cos`, `Q = sin`) via Gram–Schmidt orthogonalization and Maximal-Ratio Combining
- Avoids the "top-2 amplitude" trap where the radial and vertical axes both vary as `cos` (in-phase or 180° anti-phase) while the true `sin` quadrature component is on the third axis or split across tilted axes
- Works with a single sensor on any multipole magnet (unlike two-sensor setups placed 90° apart around the meter body, which land 180° out of phase on 4-pole magnets; see [Discussion #132](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/discussions/132))
- Decodes 4 evenly spaced 90° Gray-code steps per cycle: forward motion adds and reverse motion subtracts, so disc rocking from plumbing thermal expansion and contraction cancels out instead of accumulating as phantom usage
- Commits 1 count per 4 net forward steps (keeping `volume_per_half_rotation` unchanged), clamps published `Total` to its high-water mark so Home Assistant's `total_increasing` statistics never see a decrease as a meter reset, updates `Flow` on every new forward 90° step for 4x faster slow-leak detection, and reports **Flow Direction** (`Forward`, `Reverse`, `Stopped`)
- Automatically falls back to the 1-axis **Adaptive** algorithm if selected before 3-axis calibration or if secondary axes lack sufficient quadrature signal

**Adaptive algorithm**:

- Automatically tracks and adapts to thermal drift on the primary axis
- No recalibration needed when temperature changes
- Uses a "Smart Min/Max Tracker" with window clamping and auto-centering

**Threshold algorithm**:

- Uses fixed upper/lower thresholds set during calibration
- Simple and reliable in stable temperature environments
- May require recalibration if temperature drift moves the baseline outside thresholds

### Running Calibration

1. Run a light stream of water/gas.
2. Press the "**Calibrate axis**" button in Home Assistant.
3. Wait for calibration to complete (default 5 seconds for water, 180 for gas, configurable).

Calibration has to span at least one full rotation of the magnet, or the recorded
min/max is only a fragment of the real swing and the detection window comes out too
narrow. Water meters rotate in seconds; a slow gas index can take ~140 seconds per
rotation at low flow, which is why the gas package defaults to 180.

> **Upgrading:** the "Calibration time" number uses `restore_value: true`, so a device
> that already stored a value keeps it after flashing. Existing gas meters will still be
> on 5 seconds — change it in Home Assistant, then recalibrate.

The system will automatically:

- Detect the primary axis with the strongest signal
- Set `Threshold lower` and `Threshold upper` (for threshold mode)
- Set `Magnet Span` (for adaptive mode)
- Measure all three axis spans, cross-axis phase correlations, and forward rotation direction (for quadrature mode)

You can switch between algorithms at any time without recalibrating.

If calibration fails, check the device logs. You might need to lower "Calibration minimal axis range".

### Volume per half rotation

This depends on your specific water/gas meter model and its size.

You can search for specifications of your specific water/gas meter and its size. Specs are
usually published as pulses per unit volume, from which `volume_per_half_rotation = 2 / pulses`.
That factor of two is empirical, matching three meters so far, so confirm it with a measured
container before trusting it.

For the Badger Recordall Disc Series, from "Table 3: Transmitter pulses per unit" in the
[Badger ER-10 manual](https://www.instrumart.com/assets/badger-er10_manual.pdf):

Model | Size | Pulses per gal | Pulses per ft³ | Pulses per L | `volume_per_half_rotation`
--- | --- | --- | --- | --- | ---
25 RCDL | 5/8" | 198.340 | 1484.689 | 52.396 | `0.01008369` gal
35 RCDL | 3/4" | 126.671 | 947.566 | 33.463 | `0.01578893` gal
55 RCDL | 1" | 58.064 | 434.351 | 15.339 | `0.03444475` gal
70 RCDL | 1" | 46.752 | 349.726 | 12.350 | `0.04277892` gal
170 RCDL | 2" | 14.565 | 108.955 | 3.848 | `0.13731548` gal

The 55 and 70 are both 1", so pick the row by the size stamped on the meter, not by the model.
For another `volume_unit` divide 2 by the matching column: a 35 RCDL in ft³ is `2 / 947.566`.

If you have the Flume water sensor you can use its lowest reported value. You can find it with:
`select min(min) from statistics_short_term, statistics_meta where statistics_meta.statistic_id = 'sensor.water_usage_current' and statistics_meta.id = metadata_id and min > 0;`
Flume assigns the meter model from the photo you send at setup, so verify it against your meter.

Alternatively:

1. Temporarily set `hide_half_rotations_total_sensor: 'false'` to show the "Half rotations total" sensor in HA.
2. Write it down and also write down the reading on your water/gas meter.
3. After a few hours or even days of regular water/gas usage, write down both of them again.
4. Set this to the result of: diff of readings in volume_unit divided by diff of half rotations.
5. Set `hide_half_rotations_total_sensor: 'true'`.

For water meters this defaults to `0.01578893 gal` which is for my 3/4" Badger Meter Model 35.
For gas meters this defaults to `0.125 ft³`, which is common where the magnet couples to
the bellows/diaphragm drive. Do not assume it: on many AMR/ERT-equipped meters (e.g.
Aclara STAR) the magnet sits on the index drive shaft instead, where one rotation equals
the meter's drive rate — commonly 1.0 or 2.0 ft³, up to 16x the default.

You can read the drive rate off the small proving/test dials on the index:

Proving dials | Drive rate | Volume per full rotation
--- | --- | ---
"1 FT" and "1/4 FT" | 1-foot drive | 1.0 ft³
"2 FT" and "1/2 FT" | 2-foot drive | 2.0 ft³

Those are volumes per full rotation, but this setting is per half rotation. How many
counts one rotation produces depends on the magnet, so do not assume a factor of two.
Measure it: set `hide_half_rotations_total_sensor: 'false'`, watch "Half rotations
total" across one full revolution of the smallest proving dial under steady flow, and
divide the drive rate by the number of counts it advanced.

If you have modified the `volume_unit` you have to manually convert this value.

> **Upgrading:** this number uses `restore_value: true`, so a device that already stored a
> value keeps it after flashing. Change it in Home Assistant, then re-anchor Total with
> [`set_total`](#setting-total-volume).

### Setting Total Volume

If you would like your "Total" reading to match the reading displayed on your physical meter, you can use the `set_total` action.

1. From Home Assistant, navigate to **Settings** > **Tools** > **Actions**.
2. Locate the `esphome.water_meter_set_total` action.
3. Enter the desired total volume you wish your device to report in the `new_total` field.
4. Click **Perform Action**.

This stores an offset rather than rewriting the half-rotation counter, so the counter
keeps running uninterrupted and a later change to `volume_per_half_rotation` only
rescales the volume that was actually counted.

Total is `total_increasing`, so raising it lands in your statistics as one huge hour of
consumption. Correct it in **Settings** > **Tools** > **Statistics** > "Adjust a statistic".

### Temperature

Only supported if you are using a QMC5883L.
Place another temperature sensor next to the QMC5883L and adjust the temperature offset so that they match.

## Home Assistant Integration Examples

> **Disclaimer:** The following are advanced examples. You will need to adapt the entity IDs and thresholds to match your own setup and usage patterns.

### Water Running Too Long Alert

In `Settings > Devices & services > Helpers` I have created a template sensor: `sensor.water_meter_flow_minus_irrigation` with the following state template: `{{ max(0, states('sensor.water_meter_flow') | float - (0.44 if now().hour in range(7, 10) else 0)) }}`. My irrigation system consumes 0.44 gal/min between 7 to 9 am or 8 to 10 am depending on DST. You will need to adjust this to your irrigation system flow and times. If you don't have irrigation you can skip this and use `sensor.water_meter_flow` below.

I have also created a template **binary sensor**, `binary_sensor.water_meter_running_minus_irrigation`, with the state template `{{ states('sensor.water_meter_flow_minus_irrigation') | float(0) > 0 }}` and **no delay off**. The automation below reads its `last_changed` to know how long water has been running. Do not add a delay off unless you have measured your own usage: bridging the gaps between separate draws merges them into one long session and causes false alerts.

In `Settings > Automations` I have created the following automation to get notified if water runs for longer than the flow rate justifies, which could indicate a burst pipe, a tap left running, or a toilet that will not stop filling. It allows longer run times when a bathroom light is on, since that is where long deliberate draws happen.

```yaml
# This automation is provided as an example.
# You MUST customize the following:
# - The flow sensor and its matching "running" binary sensor
# - The flow thresholds (2.5 and 1.5 below) and the durations
# - The exception condition (e.g. is_state('light.bathroom_upstairs_lights', 'on'))
# - The notification services

alias: "Notify: water meter flow"
description: Alerts if water runs for longer than the flow rate justifies.
triggers:
  # High flow: a burst pipe or a wide-open tap. Least patience.
  - trigger: template
    value_template: >-
      {% set flow = states('sensor.water_meter_flow_minus_irrigation') | float(0) %}
      {% set lights = is_state('light.bathroom_upstairs_lights', 'on') %}
      {% set r = states.binary_sensor.water_meter_running_minus_irrigation %}
      {% set elapsed = ((as_timestamp(now()) - as_timestamp(r.last_changed)) / 60)
                       if (r is not none and r.state == 'on') else 0 %}
      {{ flow > 2.5 and ((not lights and elapsed >= 3) or (lights and elapsed >= 8)) }}
  # Medium flow.
  - trigger: template
    value_template: >-
      {% set flow = states('sensor.water_meter_flow_minus_irrigation') | float(0) %}
      {% set lights = is_state('light.bathroom_upstairs_lights', 'on') %}
      {% set r = states.binary_sensor.water_meter_running_minus_irrigation %}
      {% set elapsed = ((as_timestamp(now()) - as_timestamp(r.last_changed)) / 60)
                       if (r is not none and r.state == 'on') else 0 %}
      {{ flow > 1.5 and flow <= 2.5 and ((not lights and elapsed >= 5) or (lights and elapsed >= 10)) }}
  # Any flow at all, running far too long: a seeping toilet or a forgotten tap.
  - trigger: template
    value_template: >-
      {% set flow = states('sensor.water_meter_flow_minus_irrigation') | float(0) %}
      {% set lights = is_state('light.bathroom_upstairs_lights', 'on') %}
      {% set r = states.binary_sensor.water_meter_running_minus_irrigation %}
      {% set elapsed = ((as_timestamp(now()) - as_timestamp(r.last_changed)) / 60)
                       if (r is not none and r.state == 'on') else 0 %}
      {{ flow > 0 and flow <= 1.5 and ((not lights and elapsed >= 15) or (lights and elapsed >= 20)) }}
conditions: []
actions:
  - variables:
      start_ts: >-
        {{ as_timestamp(states.binary_sensor.water_meter_running_minus_irrigation.last_changed) }}
  - repeat:
      until:
        - condition: numeric_state
          entity_id: sensor.water_meter_flow_minus_irrigation
          below: 0.0016
      sequence:
        - action: notify.all
          data:
            title: "💧 Alert: Water Flow"
            message: >-
              {% set total = as_timestamp(now()) - start_ts %}
              Water flow is {{ states('sensor.water_meter_flow') | round(1) }} gallons per minute.
              Water has now been running for {{ (total / 60) | round(0) }} minutes.
            data:
              tag: water-flow-alert
              push:
                sound:
                  name: default
                  critical: 1
                  volume: 1
              ttl: 0
              priority: high
              media_stream: alarm_stream_max
        - action: notify.nikos_mobile
          data:
            message: TTS
            data:
              ttl: 0
              priority: high
              media_stream: alarm_stream_max
              tts_text: Water flow alert
        - delay:
            seconds: 30
mode: single
```

The group notifiers are defined in `/homeassistant/configuration.yaml`:

```yaml
notify:
  - platform: group
    name: nikos
    services:
      - service: persistent_notification
      - service: mobile_app_pixel_7a
      - service: mobile_app_le2125
  - platform: group
    name: nikos_mobile
    services:
      - service: mobile_app_pixel_7a
      - service: mobile_app_le2125
  - platform: group
    name: wife
    services:
      - service: mobile_app_wife_iphone
  - platform: group
    name: all
    services:
      - service: nikos
      - service: wife
      - service: google_assistant_sdk
      - service: alexa_media_garage_ecobee_switch
```

To find what thresholds and durations to use for your own water usage patterns, run this SQL query in the **SQLite Web** add-on with different `flow_threshold`. Start at `0`: the binary sensor turns on at any flow, so that gives the session lengths the automation's `elapsed` actually measures. Then raise it to see which flow rates your household sustains, and for how long:

```sql
-- This query calculates the longest continuous period the water meter was running each day,
-- based on a defined flow rate threshold. It includes special handling for a daily
-- "irrigation" window where the flow rate can be artificially reduced.

WITH variables AS (
  -- This is the main configuration block for the query.
  -- All user-adjustable parameters are defined here for easy modification.
  SELECT
    1.5 AS flow_threshold,          -- The flow rate (e.g., in GPM or L/min) above which the water is considered "running".
    0.44 AS irrigation_flow_reduction, -- The value to subtract from the flow rate during the irrigation window.
    '07:00' AS irrigation_start_time,  -- The start time of the daily irrigation window (HH:MM).
    '10:00' AS irrigation_end_time    -- The end time of the daily irrigation window (HH:MM).
),
all_corrected_states AS (
  -- Step 1: Get all states for the target sensor and create an "effective_state".
  -- This step applies the special logic for the irrigation window.
  SELECT
    state_id,
    old_state_id,
    last_updated_ts,
    CASE
      -- If the state's timestamp falls within the irrigation window, reduce its value.
      WHEN STRFTIME('%H:%M', last_updated_ts, 'unixepoch', 'localtime') BETWEEN (SELECT irrigation_start_time FROM variables) AND (SELECT irrigation_end_time FROM variables)
        THEN MAX(0, CAST(state AS REAL) - (SELECT irrigation_flow_reduction FROM variables)) -- Subtract the reduction, ensuring it doesn't go below zero.
      -- Otherwise, just use the state's normal value.
      ELSE CAST(state AS REAL)
    END AS effective_state
  FROM states
  WHERE
    -- Filter the states table to only include our specific water meter sensor.
    metadata_id = (
      SELECT metadata_id FROM states_meta WHERE entity_id = 'sensor.water_meter_flow'
    )
),
state_pairs AS (
  -- Step 2: Get the current state and the immediately preceding state on the same row.
  -- This is done by joining the table to itself using the old_state_id, which links each state to the previous one.
  SELECT
    current_state.last_updated_ts,
    current_state.effective_state AS effective_current_state,
    prev_state.effective_state AS effective_prev_state
  FROM
    all_corrected_states AS current_state
  JOIN
    all_corrected_states AS prev_state ON current_state.old_state_id = prev_state.state_id
),
run_events AS (
  -- Step 3: Analyze the state pairs to identify the exact moments a "run" starts or stops.
  -- A "run" is defined by the flow rate crossing the 'flow_threshold' defined in the variables.
  SELECT
    last_updated_ts,
    CASE
      -- A "start" event (1) is when the flow crosses *above* the threshold.
      WHEN effective_current_state > (SELECT flow_threshold FROM variables) AND effective_prev_state <= (SELECT flow_threshold FROM variables) THEN 1
      -- A "stop" event (-1) is when the flow crosses *below* or becomes equal to the threshold.
      WHEN effective_current_state <= (SELECT flow_threshold FROM variables) AND effective_prev_state > (SELECT flow_threshold FROM variables) THEN -1
      -- Otherwise, it's not a significant event.
      ELSE 0
    END AS event_type
  FROM state_pairs
),
run_periods AS (
  -- Step 4: Match up each "start" event with its corresponding "stop" event.
  -- This defines a complete, continuous run period.
  SELECT
    last_updated_ts AS start_time,
    -- For every start event, look forward in time to find the timestamp of the very next stop event.
    (
      SELECT MIN(e2.last_updated_ts)
      FROM run_events e2
      WHERE e2.last_updated_ts > e1.last_updated_ts AND e2.event_type = -1
    ) AS end_time
  FROM run_events e1
  -- We only care about the "start" events to begin our periods.
  WHERE e1.event_type = 1
),
daily_ranked_runs AS (
  -- Step 5: Calculate the duration of each run and rank them within each day.
  SELECT
    STRFTIME('%Y-%m-%d', start_time, 'unixepoch', 'localtime') AS run_day,
    (end_time - start_time) AS duration_seconds,
    start_time,
    end_time,
    -- The RANK() window function assigns a rank to each run (1 for the longest)
    -- within each day (PARTITION BY run_day).
    RANK() OVER (
      PARTITION BY STRFTIME('%Y-%m-%d', start_time, 'unixepoch', 'localtime')
      ORDER BY (end_time - start_time) DESC
    ) as rank_num
  FROM run_periods
  -- Ignore any runs that may not have a corresponding stop event (e.g., if the water is still running).
  WHERE end_time IS NOT NULL
)
-- Final Step: Select the longest run for each day and format the output for readability.
SELECT
  run_day,
  duration_seconds / 60 AS duration_minutes,
  DATETIME(start_time, 'unixepoch', 'localtime') AS run_start_time,
  DATETIME(end_time, 'unixepoch', 'localtime') AS run_end_time
-- Filter for only the top-ranked (longest) run for each day.
FROM daily_ranked_runs
WHERE rank_num = 1
-- Order the results with the most recent day first.
ORDER BY run_day DESC;
```

### Slow Leak Detection Automation

This automation runs every 30 minutes and checks whether a small volume arrived spread across most of the last hour, which is what separates a leak from normal use: a drip touches nearly every 5-minute period, briefly running a sink touches one.

```yaml
alias: "Notify: Slow Water Leak"
description: Detects if water volume increased while flow rate remained low.
triggers:
  - trigger: time_pattern
    minutes: /30
actions:
  - action: sql.query
    data:
      query: |-
        WITH b AS (
          SELECT s."sum" - LAG(s."sum") OVER (ORDER BY s.start_ts) AS d
            FROM statistics_short_term s
            JOIN statistics_meta m ON s.metadata_id = m.id
           WHERE m.statistic_id = 'sensor.water_meter_total'
             AND s.start_ts >= strftime('%s', 'now') - 4500
             AND s.start_ts <  strftime('%s', 'now') -  600
        )
        SELECT (SELECT SUM(d) FROM b) AS volume_delta,
               (SELECT COUNT(*) FROM b WHERE d > 0) AS active_periods
    response_variable: sql_result
  - if:
      - condition: template
        value_template: |-
          {% set row = sql_result['result'][0] %}
          {{
             row['volume_delta'] is not none and
             0 < row['volume_delta'] < 1.0 and
             row['active_periods'] >= 8
          }}
    then:
      - action: notify.nikos
        data:
          title: 💧 Slow Leak Detected
          message: >-
            {% set row = sql_result['result'][0] %} Usage in last hour: {{
            row['volume_delta'] | round(3) }} gal, spread across {{
            row['active_periods'] }} of 12 five-minute periods.

            This indicates a leak of approx {{ (row['volume_delta'] / 60) |
            round(4) }} gal/min.
mode: single
```

- The window ends 10 minutes ago because the current 5-minute statistics period isn't compiled yet; without that the automation fires twice with identical numbers.
- The upper volume bound keeps an hour of irrigation from lighting up every period.
- The smallest leak this can find is about 8 counts an hour, so it scales with `volume_per_half_rotation` — roughly 3 gal/day on a 35 RCDL.

### Daily Usage Alert

This automation checks your total consumption at the end of the day and notifies you if it's unusually high (possible leak) or low (possible sensor issue).

First, create a **Utility Meter** helper in Home Assistant (`Settings > Devices & Services > Helpers`) to track the daily total from your `sensor.water_meter_total` entity.

```yaml
# This automation requires a Utility Meter helper, e.g., 'sensor.water_meter_daily_total',
# configured to track your main sensor's total volume with a daily cycle.
# You MUST customize the high/low thresholds and notification service.

alias: "Notify: Daily Water Usage"
description: "Alerts if daily water consumption is abnormally high or low."
triggers:
  - trigger: time
    at: "23:59:00"
conditions: []
actions:
  - if:
      - condition: numeric_state
        entity_id: sensor.water_meter_daily_total
        above: 200 # Adjust this to your typical high usage
    then:
      - action: notify.nikos # Change to your notification service
        data:
          title: High daily water usage
          message: >-
            Consumed {{ states('sensor.water_meter_daily_total') }} gal today.
            Is there a leak?
  - if:
      - condition: numeric_state
        entity_id: sensor.water_meter_daily_total
        below: 10 # Adjust this to your typical low usage
    then:
      - action: notify.nikos # Change to your notification service
        data:
          title: Low daily water usage
          message: >-
            Consumed {{ states('sensor.water_meter_daily_total') }} gal today.
            Do you need to reposition or recalibrate the sensor?
mode: single
```

## Troubleshooting

- **No data from sensors or connection instability:**
  - Double-check your wiring. VCC, GND, SCL, and SDA must be correct.
  - Verify the I2C address of your sensor in the ESPHome logs.
  - Your cable might be too long or poor quality. Try a shorter, shielded cable.
  - If you are experiencing instability with a longer cable, you may consider adding `i2c_frequency: 10kHz` to the substitution in the YAML.
  - Additionally, you may wish to consider reducing the SCL/SDA pull-up resistors to 1.2kΩ (many devices ship with 4.7kΩ pre-installed). For 3.3V I2C logic, 1kΩ is the minimum.
- **Inaccurate readings:**
  - Recalibrate! Flow rate and totals depend entirely on correct calibration.
  - Ensure the sensor is mounted securely and hasn't shifted.
  - For high flow rates, an ESP8266 may not be able to keep up. Consider upgrading to an ESP32.
  - If everything is off by the same factor, it's [Volume per half rotation](#volume-per-half-rotation), not calibration. Calibration decides when a half rotation is counted; that setting decides what it's worth. Nothing in Home Assistant will reveal it, since every derived value scales together — compare against your meter reading.
  - If it's only a few percent low and worse at high flow, you're losing counts. Don't compensate with `volume_per_half_rotation`: that would fix Total at your average flow while leaving Flow wrong everywhere.
