# Firmware

## Steps to update by using esptool (Mac OS X, Linux, or Windows)

1. Download and install CP210X driver. https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers
2. Install [ESPTool](https://github.com/espressif/esptool). You may need to install Python as well.
3. Connect USB cable to Lyrat's (base board's) "UART" port. Disconnect all MIDI connections.
4. Open terminal and check your USB Serial port name.  For example, `/dev/cu.SLAB_USBtoUART` . For Linux, something like `/dev/ttyUSB0`. For Windows, something like `COM4`. 
5. Download the firmware from here.
6. Press "Boot" button and "RST" button on Lyrat board, keep pressing it, then release "RST" button only, then release "Boot" button. Now it should be firmware uploading mode.
7. Execute the following command. The number 0x230000 is very important. Don't put any other number. Otherwise it will break other important data such as saved preset or license information.
```
esptool.py --port /dev/cu.SLAB_USBtoUART write_flash 0x230000 qun_v101.bin
```
5. If you stall at  `Connecting......____`, then the board is not in firmware uploading mode. 
6. Press "RST" button to reboot the board. You should be able to see firmware version in the booting message.
```
QUN V1.01
```



## Steps to update by using Flash downlod tool (Windows only)

1. Download and install CP210X driver. https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers
2. Download Flash download tool https://www.espressif.com/en/support/download/other-tools
3. Connect USB cable to Lyrat's (base board's) "UART" port. Disconnect all MIDI connections.
4. Open terminal and check your USB Serial port name. 
5. Download the firmware from here.
6. Press "Boot" button and "RST" button on Lyrat board, keep pressing it, then release "RST" button only, then release "Boot" button. Now it should be firmware uploading mode.
7. Execute flash download tool. Select ESP32 download tool. Follow the screenshot for the parameters.

![screenshot](../manual_images/flash_downloader.jpg)


5. If you stall at  `Connecting......____`, then the board is not in firmware uploading mode. 
6. Press "RST" button to reboot the board. You should be able to see firmware version in the booting message.
```
QUN V1.01
```