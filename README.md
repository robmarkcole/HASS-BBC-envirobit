# HASS-BBC-envirobit
Stream button presses and BME280 sensor readings over the serial from the BBC micropython [envirobit](https://github.com/pimoroni/micropython-envirobit) to [Home-Assistant](https://www.home-assistant.io/). Inspired by https://www.home-assistant.io/blog/2017/10/23/simple-analog-sensor/

## Usage
I'm using a Mac. To check which port the microbit is on I run:
```
ls /dev/cu.*
```
In my case the microbit is connected on **/dev/cu.usbmodem14342**. I now use [screen](https://geekinc.ca/using-screen-as-a-serial-terminal-on-mac-os-x/) to check the data transmitted over the serial. In the terminal I launch screen with:
```
screen /dev/cu.usbmodem14342 115200
```
Note that if you get any funnry errors when connecting, check that there is not already a screen session open using the Activity monitor, and kill any existing processes. You can also kill a screen session using **ctrl + a + k**.

We can now configure Home-Assistant to display the data feed. I add to my Home-Assistant config:
```yaml
sensor:
  - platform: serial
    serial_port: /dev/cu.usbmodem14342
    baudrate: 115200
```

We then use template sensors to break out the data fields. Under `sensor`:
```yaml
  - platform: template
    sensors:
      temperature:
        friendly_name: Temperature_microbit
        unit_of_measurement: "°C"
        value_template: "{{ states.sensor.serial_sensor.attributes.T }}"
```

The final config for all sensors is:
```yaml
  - platform: serial
    serial_port: /dev/cu.usbmodem14342
    baudrate: 115200
  - platform: template
    sensors:
      temperature:
        friendly_name: Temperature_microbit
        unit_of_measurement: "°C"
        value_template: "{{ states.sensor.serial_sensor.attributes.T }}"
      pressure:
        friendly_name: Pressure_microbit
        unit_of_measurement: "hPa"
        value_template: "{{ states.sensor.serial_sensor.attributes.P }}"
      humidity:
        friendly_name: Humidity_microbit
        unit_of_measurement: "%"
        value_template: "{{ states.sensor.serial_sensor.attributes.H }}"
      altitude:
        friendly_name: Altitude_microbit
        unit_of_measurement: "feet"
        value_template: "{{ states.sensor.serial_sensor.attributes.A }}"
```

<p align="center">
<img src="https://github.com/robmarkcole/HASS-BBC-envirobit/blob/master/usage.png" width="500">
</p>