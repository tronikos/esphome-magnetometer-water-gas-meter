# esphome-magnetometer-water-gas-meter [![Made for ESPHome](https://img.shields.io/badge/Made_for-ESPHome-black?logo=esphome)](https://esphome.io)

This [ESPHome](https://esphome.io) package allows reading your water meter or gas meter  using the QMC5883L, a triple-axis magnetometer.

TLDR; Add this to your ESPHome device configuration:

```yaml
substitutions:
  volume_unit: 'gal'
  i2c_scl: GPIO5  # D1
  i2c_sda: GPIO4  # D2

packages:
  water_meter: github://tronikos/esphome-magnetometer-water-gas-meter/esphome-water-meter.yaml@main

esp8266:
  board: d1_mini
  restore_from_flash: true

preferences:
  flash_write_interval: 60min
```

<img src="https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/1b857c5f-06f4-4a0e-8f37-64314acf0dd9" alt="Water meter in Home Assistant" width=40%>

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

### Gas meter

The magnetometer is used to read the diaphragm that expands and contracts inside your gas meter.

This should be compatible with all diaphragm/bellows meters which are the most common type of gas meter, seen in almost all residential and small commercial installations.

To verify compatibility install the Sensors app on your phone, place your phone next to the meter, and see if the Geomagnetic Field sensors are changing while gas is running.

[Video](https://www.youtube.com/watch?v=WKlVmXe46w8) showing the internals of a gas meter.

## Hardware installation

### Parts

- ESP8266 or ESP32 with power adapter
  - I placed mine inside the garage
- QMC5883L
  - I placed mine in the water meter box 25ft away from the garage
- Ethernet cable
  - I used 32.8ft or 10m direct burial CAT6.
  - CAT6 is preferred because of its lower capacitance. In theory CAT5 50ft or 15m [should work](https://www.youtube.com/watch?v=6v1KZBRZRCI). For 100m you will need an active terminator such as [LTC4311](https://www.youtube.com/watch?v=nhWPxO7jx_o).
- Some way to weather proof the QMC5883L. Some options:
  - Adhesive 4:1 heat shrink tubing
  - Silicone sealant
  - Nail polish
  - Hot glue
- Some way to mount the QMC5883L on the meter. Some options:
  - Cable zip tie
  - Duct tape
- Conduit for the ethernet cable. Can be skipped if used direct burial ethernet cable.

### Wiring

QMC5883L | ESP8266
--- | ---
VCC | 5V
GND | GND
SCL | D1
SDA | D2

The ethernet cable has 4 twisted pairs of wires. Use any solid wire color for the 4 above pins. Tie the 4 white wires together with the GND solid wire. You might need to use a header pin for the GND. If you use a header pin cut the 5 GND wires shorter to avoid the ball of wires I had...

![magnetometer](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/c7052171-eee1-44cb-90f4-76cad4e46334)
![magnetometer in adhesive heat shrink tubing](https://github.com/tronikos/esphome-magnetometer-water-gas-meter/assets/9987465/0ca8c738-63c2-4d38-ae35-42bb219b88d1)

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
      water_meter: github://tronikos/esphome-magnetometer-water-gas-meter/esphome-water-meter.yaml@main
      # Or for gas meter:
      # gas_meter: github://tronikos/esphome-magnetometer-water-gas-meter/esphome-gas-meter.yaml@main
    ```

7. Change the values in the `substitutions` section based on your setting, e.g. if you have used different pins, or if you prefer a different unit.
8. Your configuration should now look something like the following:

    ```yaml
    substitutions:
      volume_unit: 'gal'
      i2c_scl: GPIO5  # D1
      i2c_sda: GPIO4  # D2

    packages:
      water_meter: github://tronikos/esphome-magnetometer-water-gas-meter/esphome-water-meter.yaml@main

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

Defaulting to `0.01008156` which is for a 3/4" Badge Meter Model 35.

### Magnetic field axis and thresholds

To calibrate these just run a light stream of water/gas and press the "Calibrate axis" button. Within 5 seconds the proper axis and thresholds should be set.

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

### Temperature

Place another temperature sensor next to the QMC5883L and adjust the temperature offset so that they match.
