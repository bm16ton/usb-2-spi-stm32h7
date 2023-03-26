Portenta h7 usb to spi and usb to i2c with gpio/irq support.

UPDATE: Ruff first draft of autoloading devicetree node, using a built-in dtb, it creates a new root node. It also creates an overlay folder in configfs that can be used to load dynamic overlays, even on devicetree systems without dynamic overlay support. Ill weed out unneeded changes to driver, incorperate the whole thing into my driver and add multi instance support for minor numbers(in this case cs number) and have it grab platform data from standard devicetree overlays. Still not sure on plan of attack for acpi yet.

UPDATE: Added i2c fast mode plus (1mb) support, and abilityt to switch i2c speed via sysfs attribute called "i2cspeed" simply echo 100, 400, or 1000 to it. This does internally disable the i2c bus during the switch on the stm device but not in driver, so depending, any currently connected/loaded i2c devices/drivers might freak. I would unload any drivers first.

Edit usb-spi-git/usb-spi/module/mod2/spi_tiny_usb.c


edit the struct "static struct spi_board_info usb_spi_bus_info[]" at time of writing is line 691. Set the spi device settings modalias, cs number, spi flags etc. Add any additional IO to static struct dev_io_desc_data usb_spi_bus_dev_io[] at time of writing line 677. Then any additional platform data add to/create a property_entry example line 668.


then goto the module folder run make clean ; make. Then either use insmod to load to two modules or copy them to your lib folder IE /lib/modules/(kernel version)/blah. Then run depmod and plug in the device and it should take off.


The i2c interface uses the kernels built in i2c-tiny driver so no special setup is required.


+ TODO
+ test bpw and lsb setting but requires spi devices I dont have
+ Switch firmware to use dma on transfers with size over 16
+ add microwire/3 wire support
+ Add spi-mem functions for spi flash
+ Try compression for higher speed spi freq's too fast for usbhs
+ Try to find a way to get more fine grained control over spi clock
+ Send back actual spi clock freq after setting it compared to what was requested
+ Additional cs control IE turn off completely, or toggle between every byte instead of message etc.
+ Hopefully remove control send b4 each receive and just do all receive stuff in callback
+ Add dynamic overlay support to the spi_tiny_usb driver for loading device tree overlays on the fly
+ Find a method thats both acpi/devicetree compatable to pass platform data from userland.
+ Add my usb adc firmware and kernel driver.
+ Add usb to serial (hopefully x2)
+ Additional protocols sdio/one wire, rs4xx, canbus, modbus etc.
+ Add custom folder to /sys for various settings
+ Try stm's various spi modes/settings
+ i2c-tiny driver needs some additions, i2c speed, specific usb interface binding etc.
+ Add adafruit seesaw to a secondary i2c bus to control neopixels etc.

