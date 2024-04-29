# esphome-magnetometer-water-gas-meter [![Made for ESPHome](https://img.shields.io/badge/Made_for-ESPHome-black?logo=esphome)](https://esphome.io)

This [ESPHome](https://esphome.io) package allows reading your water meter or gas meter  using the QMC5883L, a triple-axis magnetometer.

TLDR; Add this to your ESPHome device configuration:

```yaml
substitutions:
  volume_unit: 'gal'
  i2c_scl: GPIO5  # D1
  i2c_sda: GPIO4  # D2

packages:
  meter:
    url: https://github.com/tronikos/esphome-magnetometer-water-gas-meter
    ref: main
    file: esphome-water-meter.yaml
    # Or for gas meter:
    # file: esphome-gas-meter.yaml
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
  - For high flow meters ESP32 is preferred because it's faster
- QMC5883L
  - I placed mine in the water meter box 20ft away from the garage
- Ethernet cable
  - I used 32.8ft or 10m direct burial CAT6.
  - CAT6 is preferred because of its lower capacitance. CAT5 50ft or 15m [should work](https://www.youtube.com/watch?v=6v1KZBRZRCI). For 100ft you will need an active terminator such as [LTC4311](https://www.youtube.com/watch?v=nhWPxO7jx_o).
- Some way to weather proof the QMC5883L. Some options:
  - Adhesive 4:1 heat shrink tubing (this is what I used)
  - Silicone sealant
  - Nail polish
  - Hot glue
- Some way to mount the QMC5883L on the meter. Some options:
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
      volume_unit: 'gal'
      i2c_scl: GPIO5  # D1
      i2c_sda: GPIO4  # D2

    packages:
      meter:
        url: https://github.com/tronikos/esphome-magnetometer-water-gas-meter
        ref: main
        file: esphome-water-meter.yaml
        # Or for gas meter:
        # file: esphome-gas-meter.yaml
        refresh: 0s
    ```

7. Change the values in the `substitutions` section based on your setting, e.g. if you have used different pins, or if you prefer a different unit.
8. Your configuration should now look something like the following:

    ```yaml
    substitutions:
      volume_unit: 'gal'
      i2c_scl: GPIO5  # D1
      i2c_sda: GPIO4  # D2

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

### Magnetic field axis and thresholds

To calibrate these just run a light stream of water/gas and press the "Calibrate axis" button. After 5 seconds (configurable) the proper axis and thresholds should be set.

Alternatively:

1. Temporarily enable the Magnetic Field Strength X, Y, and Z sensors in HA.
2. Run a light stream of water/gas.
3. Observe which axis changes the most and its range.
4. Set the axis and thresholds. e.g. if y axis ranges from min to max use:

    ```raw
    Axis = y
    Threshold lower = min + 0.25 * (max - min)
    Threshold upper = max - 0.25 * (max - min)
    ```

5. Disable the Magnetic Field Strength X, Y, and Z sensors in HA. Otherwise HA recorder will get overwhelmed.

### Volume per half rotation

This depends on your specific water/gas meter model and its size.

To calibrate:

1. Temporarily enable the "Half rotations total" sensor in HA.
2. Write it down and also write down the reading on your water/gas meter.
3. After a few hours or even days of regular water/gas usage, write down both of them again.
4. Set this to the result of: diff of readings in volume_unit divided by diff of half rotations.
5. Disable the "Half rotations total" sensor in HA.

Alternatively you can search for specifications of your specific water/gas meter and its size. e.g.
for [Neptune T-10](https://www.riotronics.com/wp-content/uploads/2019/11/NT10-4P-WaterRead-pdf3.01.pdf):

Meter size | Pulses/Gallon
--- | ---
5/8"       | 231.24
3/4"       | 129.04
1"         | 60.32
1 1/2"     | 27.03
2"         | 14.92

So for a 5/8" Neptune T-10 you will set this to `0.00864902` (2 / 231.24)

If you have the Flume water sensor you can use its lowest reported value. You can find it with:
`select min(min) from statistics_short_term, statistics_meta where statistics_meta.statistic_id = 'sensor.water_usage_current' and statistics_meta.id = metadata_id and min > 0;`

For water meters this defaults to `0.01008156` which is for my 3/4" Badge Meter Model 35.
For gas meters this defaults to `0.125` which seems to be the most common in US.

### Temperature

Place another temperature sensor next to the QMC5883L and adjust the temperature offset so that they match.

## Home Assistant alerts

I'm using the [Alert integration](https://www.home-assistant.io/integrations/alert/) to get alerted if there is a leak.

In `/homeassistant/configuration.yaml` I have:

```yaml
alert: !include alerts.yaml

template:
  - sensor:
    - name: Water running for 45 minutes
      unique_id: water_running_45min
      device_class: "moisture"
      icon: mdi:waves
      delay_on:
        minutes: 45
      # Subtract irrigation system that consumes 0.28 gal/min between 7 to 9 am or 8 to 10 am depending on DST
      state: "{{ max(0, states('sensor.water_meter_flow') | float - (0.3 if now().hour in range(7, 10) else 0)) > 0 }}"
    - name: Water running for 20 minutes at more than 1.5 gal/min
      unique_id: water_running_20min
      device_class: "moisture"
      icon: mdi:waves
      delay_on:
        minutes: 20
      state: "{{ max(0, states('sensor.water_meter_flow') | float - (0.3 if now().hour in range(7, 10) else 0)) > 1.5 }}"

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

In `Settings > Devices & services > Helpers` I have created a group `binary_sensor.water_leak_sensors_group` with the above 2 sensors together with all my water leak sensors.

In `Settings > Automations` I have created the following automation to get notified if I ever forget to add a new sensor to the group:

```yaml
alias: "Notify: incomplete groups"
description: ""
trigger:
  - platform: time
    at: "10:01:00"
action:
  - if:
      - condition: template
        value_template: "{{ missing_moisture_sensors != '' }}"
    then:
      - service: notify.nikos
        data:
          message: |-
            binary_sensor.water_leak_sensors_group is missing:
            {{missing_moisture_sensors}}
variables:
  missing_moisture_sensors: |
    {{ states.binary_sensor
        | rejectattr('attributes.device_class', 'undefined')
        | selectattr('attributes.device_class', '==', 'moisture')
        | rejectattr('attributes.entity_id', 'defined')
        | map(attribute='entity_id')
        | reject('in', states.binary_sensor.water_leak_sensors_group.attributes.entity_id)
        | join('\n') }}
mode: single
```

In `/homeassistant/alerts.yaml` I have the following to keep alerting me every 5 minutes in case of a leak:

```yaml
water_leak:
  name: Water leak detected
  message: "Water leak detected at {{ expand('binary_sensor.water_leak_sensors_group') | selectattr('state', '==', 'on') | map(attribute='attributes.friendly_name') | join(', ') | lower() | replace(': water leak sensor', '') }} {{ relative_time(states.binary_sensor.water_leak_sensors_group.last_changed) }} ago"
  done_message: Water leak not detected anymore
  entity_id: binary_sensor.water_leak_sensors_group
  state: "on"
  repeat: 5
  can_acknowledge: true
  skip_first: false
  notifiers:
    - all
  data:
    push:
      sound:
        name: "default"
        critical: 1
        volume: 1.0
    ttl: 0
    priority: high
    media_stream: alarm_stream_max
water_leak_tts:
  name: Water leak detected (TTS)
  message: TTS
  done_message: Water leak not detected anymore
  entity_id: binary_sensor.water_leak_sensors_group
  state: "on"
  repeat: 5
  can_acknowledge: true
  skip_first: false
  notifiers:
    - nikos_mobile
  data:
    ttl: 0
    priority: high
    media_stream: alarm_stream_max
    tts_text: "Water leak detected"
```

In my main dashboard I have the following [auto-entities](https://github.com/thomasloven/lovelace-auto-entities) cards, which are typically hidden when empty:

```yaml
type: custom:auto-entities
show_empty: false
card:
  title: Active Alerts
  type: entities
  state_color: true
filter:
  include:
    - domain: alert
      not:
        state: idle
sort:
  method: friendly_name
```

```yaml
type: custom:auto-entities
show_empty: false
card:
  title: Active alert sensors
  type: entities
  state_color: true
filter:
  include:
    - attributes:
        device_class: moisture
      state: 'on'
    - attributes:
        device_class: smoke
      state: 'on'
    - attributes:
        device_class: carbon_monoxide
      state: 'on'
```

In `Settings > Devices & services > Helpers` I have created a Utility Meter `sensor.water_meter_daily_total` to keep track of my daily water usage.

In `Settings > Automations` I have created the following automation to get notified if my daily water usage is abnormal:

```yaml
alias: "Notify: water usage"
description: ""
trigger:
  - platform: time
    at: "23:59:00"
condition: []
action:
  - if:
      - condition: numeric_state
        entity_id: sensor.water_meter_daily_total
        above: 100
    then:
      - service: notify.nikos
        metadata: {}
        data:
          title: High daily water usage
          message: >-
            Consumed {{ states('sensor.water_meter_daily_total') }} gal today.
            Is there a leak?
  - if:
      - condition: numeric_state
        entity_id: sensor.water_meter_daily_total
        below: 10
    then:
      - service: notify.nikos
        metadata: {}
        data:
          title: Low daily water usage
          message: >-
            Consumed {{ states('sensor.water_meter_daily_total') }} gal today.
            Do you need to reposition or recalibrate the sensor?
mode: single
```
