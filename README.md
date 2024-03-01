# USBridge---Volumio-USB-ethernet-driver-update
Temporary fix for updating the Allo-supplied Asix AX88179 USB ethernet driver to work on current versions of Volumio

# how to update Allo ASIX driver to Volumio 3:

On a new install of Volumio, preferable on a Raspberry Pi 3b+ (not on the USBridge):

(1) Enable ssh access

(2) Login via ssh to Pi running latest Volumio

(3) get development tools for Volumio

volumio kernelsource
cd /usr/src/rpi-linux
make

(4) after waiting a long time for the tool installation to complete (several hours):

# put Allo's code and header (ax88179_178a.c & ax88179_178a.h) in a working directory /home/volumuio/USBridge

# edit C source code for ax88179_178a.c supplied by Allo
c source modify: see commented out line below (eliminated 3rd argument):
 /*     netif_napi_add(netdev, &axdev->napi, ax_poll, AX88179_NAPI_WEIGHT); */
        netif_napi_add(netdev, &axdev->napi, ax_poll);

# compile driver 
# (substitute current kernel name for "6.1.58-v7+" below; get kernel name by running 'uname -r')

make -C /lib/modules/6.1.58-v7+/build M=/home/volumio/USBridge modules

# compress resulting module file and fix permissions
xz -z ax88179_178a.ko
sudo chown root:root ax88179_178a.ko.xz
sudo chmod 644 ax88179_178a.ko.xz

# check for layout match with installed system version
sudo modprobe --dump-modversions /lib/modules/6.1.58-v7+/kernel/drivers/net/usb/ax88179_178a.ko.xz | grep module_layout

sudo modprobe --dump-modversions ax88179_178a.ko.xz | grep module_layout

# install if match
sudo install -p -m 644 ax88179_178a.ko.xz  /lib/modules/6.1.58-v7+/kernel/drivers/net/usb/
sudo depmod 6.1.58-v7+q
