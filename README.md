# HASS-BBC-envirobit
Stream button presses and BME280 sensor readings over the serial from the BBC micropython [envirobit](https://github.com/pimoroni/micropython-envirobit) to [Home-Assistant](https://www.home-assistant.io/). 

## BBC-envirobit setup
`main.py` should be flashed to tbe BBC microbit. On pressing the `A` or `B` buttons the display will scroll the pressed button. The buttons are displayed as a binary sensor in Home-Assistant and can be used to trigger automations. 
Data from the BME (temperature, pressure, humidity, but not altitude as these values were meaningless in my testing) are publihsed to Home-Assistant every second (the sleep time is a variable `sleep_sec` which you can optimise). The number of decimal places that data is reported to is 1dp by default, but can be varied with the `rounding_digits` variable. Plug the microbit into the USB port on your Home-Assistant computer. 

## Home-Assistant Usage with HASSOS

On HASSOS, the microbit is on `/dev/ttyACM0`

We can now configure Home-Assistant to display the data feed. I add to my Home-Assistant config:
```yaml
sensor:
  - platform: serial
    serial_port: /dev/ttyACM0
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
sensor:
  - platform: serial
    serial_port: /dev/ttyACM0
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

binary_sensor:
  - platform: template
    sensors:
      button_a:
        friendly_name: Button_A_microbit
        value_template: "{{ states.sensor.serial_sensor.attributes.ba |float > 0}}"
      button_b:
        friendly_name: Button_B_microbit
        value_template: "{{ states.sensor.serial_sensor.attributes.bb |float > 0}}"

history_graph:
  microbit:
    entities:
      - sensor.temperature
      - sensor.pressure
      - sensor.humidity
```
## Mac usage
To check which port the microbit is on I run (on Mac/unix):
```
ls /dev/cu.*
```
In my case the microbit is connected on **/dev/cu.usbmodem14342**. I now use [screen](https://geekinc.ca/using-screen-as-a-serial-terminal-on-mac-os-x/) to check the data transmitted over the serial. In the terminal I launch screen with:
```
screen /dev/cu.usbmodem14342 115200
```
Note that if you get any funny errors when connecting, check that there is not already a screen session open using the Activity monitor, and kill any existing processes. You can also kill a screen session using **ctrl + a + k**.

## Notes
I discovered that you can't mix numerical and text data in the payload string. If you try Home-Assistant displays no data.

<p align="center">
<img src="https://github.com/robmarkcole/HASS-BBC-envirobit/blob/master/usage.png" width="900">
</p>