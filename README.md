# BLE_LED_strip introduction
A relatively short reverse engineering of a 12V LED strip controller based on (a powerfull) XC610 chip.

Controlling LED strip is super cool, but being forced to use an app is less cool.
Fortunatly, reversing-engineering is accessible, you just need:
- A smartphone with developer option and bluetooth debugging enabled
- A LED controller (the "device") with its LED strip
- A computer, I assume you're on a linux distro but you can work it out on windows too.
- A little bit of curiosity :)
I used some tutorials and reports from other people that succeeded to hack this type of device.
So this repo put together the informations and what I found on this particular device.

## The BLE controller
![ble](https://github.com/NotACoin/BLE_LED_strip/assets/60425329/f89ab1e7-64a5-41d1-937f-345e4480ba07)

The datasheet of the XC610 chip is in the repo.
One can notice that there is RX/TX and BOOT pins accessible, and only 3 of the 5(!) PWM pins are used.
This controller is used a lot for this application, a great video show in more details the same circuit, 
with slightly different design: https://www.youtube.com/watch?v=uWlzulQFb98
The 3.3V regulator is a HT7133-1 30mA Low Power LDO from Holtek.
## The android apps
- One can just use the happylighting app (https://play.google.com/store/apps/details?id=com.xiaoyu.hlight). 
It work really nice, and you can mix colors, blink, or have smooth gradients and even get it responsive to sound.
I will use this app to communicate with the controller. If you know an other one, tell me!
- nRF connect app, I will use it to send custom command to the controller.
## adb and wireshark
- A usefull ressource to get the log of the bluetooth communications from your phone, just follow what it say.
![medium.com post to get bluetooth logs from your android phone](https://medium.com/@charlie.d.anderson/how-to-get-the-bluetooth-host-controller-interface-logs-from-a-modern-android-phone-d23bde00b9fa)  
- To analyse the file, you'll need wireshark or other software, but wireshark is good. I followed this !(other article from medium.com](https://urish.medium.com/reverse-engineering-a-bluetooth-lightbulb-56580fcb7546) which is about reverse-engineering a BLE-lightbulb.
## The simple process
Once you installed everything you need, you just want to 
- (1) use the "official" app, like turning the led on/off, slecting color...Don't send too much command if you know nothing about wireshark like me.
- (2) export the bluetooth logs to your computer and open it with wireshark
- (3) Try to send commands you just found using nRFconnect app.
(4) rinse and repeat, and enjoy!
## What I've found
| Command | Turn On | Turn Off |
| ---|---|---|
| Bytes array | CC2333 | CC2433 |

You can choose the colors: send 56RRGGBB00F0AA. RR is red, GG is green, BB is blue. Red would be 56FF000000F0AA, 5600FF0000F0AA for green. 56FFFFFF00F0AA is white.

You can make some pulses by sending BB2XYY44. 
YY correspond to speed, 00 is fast and FF is very slow.
X correspond to the following patterns:
|4|5|6|7|8|9| A | B | C | D | E | F | 
|white|smooth R-V-B|R|G|B|G|B-G|R-B|R-G|White|Magenta|Turquoise?|
