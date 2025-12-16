# esphome-magnetometer-water-gas-meter [![Made for ESPHome](https://img.shields.io/badge/Made_for-ESPHome-black?logo=esphome)](https://esphome.io)

This [ESPHome](https://esphome.io) package allows reading your water meter or gas meter using the QMC5883L or QMC5883P or HMC5883L or MMC5603, a triple-axis magnetometer.

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
    refresh: 0s
```

<img src="https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/9363747e-ea4d-457b-b219-90f0192fcf8d" alt="Water meter in Home Assistant" width=40%>

## Compatibility

### Water meter

The magnetometer is used to read the rotating magnet inside your water meter.

This should be compatible will all the water meters the Flume water sensor is compatible with, which is [compatible](https://help.flumewater.com/en/articles/1618594-is-the-flume-device-compatible-with-all-water-meters) with about 95% of water meters in the United States.

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
- QMC5883L or QMC5883P or HMC5883L or MMC5603 magnetometer
  - I placed mine in the water meter box 20ft away from the garage
- Ethernet cable
  - I used 32.8ft or 10m direct burial CAT6. A user has reported they successfully used 75ft or 22.9m direct burial CAT6.
  - CAT6 is preferred because of its lower capacitance. CAT5 50ft or 15m [should work](https://www.youtube.com/watch?v=6v1KZBRZRCI). For 100ft you will need an active terminator such as [LTC4311](https://www.youtube.com/watch?v=nhWPxO7jx_o).
  - Do not use thermostat wire, bell wire, or any other low voltage wire. You will have communication errors or instability. You really need to be using twisted pair cables with proper shielding and lower capacitance such as CAT6.
- Some way to weather proof the magnetometer. Some options:
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
VCC | 5V
GND | GND
SCL | D1
SDA | D2

The ethernet cable has 4 twisted pairs of wires. Use any solid wire color for the 4 above pins. Tie the 4 white wires together with the GND solid wire. You might need to use a header pin for the GND. If you use a header pin cut the 5 GND wires shorter to avoid the ball of wires I had...

![magnetometer wiring](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/c7052171-eee1-44cb-90f4-76cad4e46334)
![magnetometer in adhesive heat shrink tubing](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/0ca8c738-63c2-4d38-ae35-42bb219b88d1)
![d1 mini wiring](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/b8c3df8d-8111-415b-aecc-64d9c5a290c1)
![d1 mini lego case](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/6d8d85a0-b00c-4db9-9484-3b345e73f848)
![driveway](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/69a47f3e-8d8f-4c2e-aec8-14cb729b48a4)

## Software installation

1. Setup **ESPHome**, if you don't have it already, by following [Getting Started with ESPHome and Home Assistant](https://esphome.io/guides/getting_started_hassio.html).
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
        # Or if you are using HMC5883L instead of QMC5883L:
        # files: [esphome-water-meter.yaml, hmc5883l.yaml]
        # Or if you are using MMC5603 instead of QMC5883L:
        # files: [esphome-water-meter.yaml, mmc5603.yaml]
        refresh: 0s
    ```

7. Change the values in the `substitutions` section based on your setting, e.g. if you have used different pins, or if you prefer a different unit.
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
        key: "L8408egzTATPCBT1nzvFpqj4YlVERRO31+GyB/yjf4E="

    ota:
      - platform: esphome
        password: "d44ed9df293facf65e288062d5c7a5e7"

    wifi:
      ssid: !secret wifi_ssid
      password: !secret wifi_password

      # Enable fallback hotspot (captive portal) in case wifi connection fails
      ap:
        ssid: "water-meter Fallback Hotspot"
        password: "8cSGOshkb2Rw"

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

Two detection algorithms are available, controlled by the **"Detection algorithm"** select:

Algorithm | Best For
--- | ---
**Threshold** (default) | Backward compatibility with existing installations
**Adaptive** | **Recommended for new users**. Handles temperature drift automatically

> **Recommendation:** New users should select the Adaptive algorithm. Threshold is the default only for backward compatibility with existing installations.

**Threshold algorithm**:

- Uses fixed upper/lower thresholds set during calibration
- Simple and reliable in stable temperature environments
- May require recalibration if temperature drift moves the baseline outside thresholds

**Adaptive algorithm**:

- Automatically tracks and adapts to thermal drift
- No recalibration needed when temperature changes
- Uses a "Smart Min/Max Tracker" with frozen window enforcement

### Running Calibration

1. Run a light stream of water/gas.
2. Press the "**Calibrate axis**" button in Home Assistant.
3. Wait for calibration to complete (default 5 seconds, configurable).

The system will automatically:

- Detect the axis with the strongest signal
- Set `Threshold lower` and `Threshold upper` (for threshold mode)
- Set `Magnet Span` (for adaptive mode)

You can switch between algorithms at any time without recalibrating.

If calibration fails, check the device logs. You might need to lower "Calibration minimal axis range".

### Volume per half rotation

This depends on your specific water/gas meter model and its size.

You can search for specifications of your specific water/gas meter and its size.

If you have the Flume water sensor you can use its lowest reported value. You can find it with:
`select min(min) from statistics_short_term, statistics_meta where statistics_meta.statistic_id = 'sensor.water_usage_current' and statistics_meta.id = metadata_id and min > 0;`

Alternatively:

1. Temporarily set `hide_half_rotations_total_sensor: 'false'` to show the "Half rotations total" sensor in HA.
2. Write it down and also write down the reading on your water/gas meter.
3. After a few hours or even days of regular water/gas usage, write down both of them again.
4. Set this to the result of: diff of readings in volume_unit divided by diff of half rotations.
5. Set `hide_half_rotations_total_sensor: 'true'`.

For water meters this defaults to `0.01008156 gal` which is for my 3/4" Badge Meter Model 35.
For gas meters this defaults to `0.125 ft³` which seems to be the most common in US.
If you have modified the `volume_unit` you have to manually convert this value.

### Temperature

Only supported if you are using a QMC5883L.
Place another temperature sensor next to the QMC5883L and adjust the temperature offset so that they match.

## Home Assistant Integration Examples

> **Disclaimer:** The following are advanced examples. You will need to adapt the entity IDs and thresholds to match your own setup and usage patterns.

### Leak Alert Automation

In `Settings > Devices & services > Helpers` I have created a template sensor: `sensor.water_meter_flow_minus_irrigation` with the following state template: `{{ max(0, states('sensor.water_meter_flow') | float - (0.3 if now().hour in range(7, 10) else 0)) }}`. My irrigation system consumes 0.28 gal/min between 7 to 9 am or 8 to 10 am depending on DST. You will need to adjust this to your irrigation system flow and times. If you don't have irrigation you can skip this and use `sensor.water_meter_flow` below.

In `Settings > Automations` I have created the following automation to get notified if water runs continuously for too long, which could indicate a leak. It has logic to allow for longer run times (like showers) if a bathroom light is on.

```yaml
# This automation is provided as an example.
# You MUST customize the following:
# - entity_id: sensor.water_meter_flow_minus_irrigation (or your main flow sensor)
# - The thresholds for flow rate (e.g., above: 1.7)
# - The durations for each trigger (e.g., for: minutes: 3)
# - The condition for exceptions (e.g., is_state('light.bathroom_upstairs_lights', 'off'))
# - The notification service (e.g., notify.all, notify.nikos_mobile)

alias: "Notify: water meter flow"
description: "Sends critical alerts if water is running for an extended period."
triggers:
  - trigger: numeric_state
    id: high_flow
    entity_id: sensor.water_meter_flow_minus_irrigation
    above: 1.7
    for:
      minutes: 3
  - trigger: numeric_state
    id: high_flow_bath_lights_on
    entity_id: sensor.water_meter_flow_minus_irrigation
    above: 1.7
    for:
      minutes: 8
  - trigger: numeric_state
    id: medium_flow
    entity_id: sensor.water_meter_flow_minus_irrigation
    above: 1
    for:
      minutes: 5
  - trigger: numeric_state
    id: medium_flow_bath_lights_on
    entity_id: sensor.water_meter_flow_minus_irrigation
    above: 1
    for:
      minutes: 10
  - trigger: numeric_state
    id: low_flow
    entity_id: sensor.water_meter_flow_minus_irrigation
    above: 0
    for:
      minutes: 15
  - trigger: numeric_state
    id: low_flow_bath_lights_on
    entity_id: sensor.water_meter_flow_minus_irrigation
    above: 0
    for:
      minutes: 20
actions:
  - variables:
      initial_duration_seconds: "{{ trigger.for.total_seconds() }}"
      alert_start_time: "{{ now() }}"
  - if:
      - condition: template
        value_template: >-
          {{ 'bath_lights_on' in trigger.id or
          is_state('light.bathroom_upstairs_lights', 'off') }}
    then:
      - repeat:
          until:
            - condition: numeric_state
              entity_id: sensor.water_meter_flow_minus_irrigation
              below: 0.001
          sequence:
            - action: notify.all
              data:
                title: "💧 Alert: Water Flow"
                message: >-
                  {% set time_since_alert_started = now() -
                  as_datetime(alert_start_time) %}

                  {% set total_duration_seconds = initial_duration_seconds +
                  time_since_alert_started.total_seconds() %}

                  Water flow is {{ states('sensor.water_meter_flow') | round(1)
                  }} gallons per minute.

                  Water has now been running for {{ (total_duration_seconds /
                  60) | round(0) }} minutes.
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

To find what thresholds and durations to use for your own water usage patterns, run this SQL query in the **SQLite Web** add-on with different `flow_threshold`:

```sql
-- This query calculates the longest continuous period the water meter was running each day,
-- based on a defined flow rate threshold. It includes special handling for a daily
-- "irrigation" window where the flow rate can be artificially reduced.

WITH variables AS (
  -- This is the main configuration block for the query.
  -- All user-adjustable parameters are defined here for easy modification.
  SELECT
    1.5 AS flow_threshold,          -- The flow rate (e.g., in GPM or L/min) above which the water is considered "running".
    0.3 AS irrigation_flow_reduction, -- The value to subtract from the flow rate during the irrigation window.
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
        above: 150 # Adjust this to your typical high usage
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

- **No data from sensors:**
  - Double-check your wiring. VCC, GND, SCL, and SDA must be correct.
  - Verify the I2C address of your sensor in the ESPHome logs.
  - Your cable might be too long or poor quality. Try a shorter, shielded cable.
- **Inaccurate readings:**
  - Recalibrate! Flow rate and totals depend entirely on correct calibration.
  - Ensure the sensor is mounted securely and hasn't shifted.
  - For high flow rates, an ESP8266 may not be able to keep up. Consider upgrading to an ESP32.
