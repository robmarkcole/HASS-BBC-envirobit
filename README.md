# HASS-BBC-envirobit
Stream sensor readings over the serial from the BBC micropython [envirobit](https://github.com/pimoroni/micropython-envirobit) to Home-Assistant. 

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
```
sensor:
  - platform: serial
    serial_port: /dev/cu.usbmodem14342
    baudrate: 115200
```

We then use template sensors to break out the data fields. 

<p align="center">
<img src="https://github.com/robmarkcole/HASS-BBC-envirobit/blob/master/usage.png" width="500">
</p>