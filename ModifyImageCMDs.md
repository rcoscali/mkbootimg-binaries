# How to modify android boot.img image

 For this procedure you need two utils from github:
 - unpackbootimg: util that extract images & metadata 
   from the image file
 - mkbootimg: the revert operation that will take images
   and metadata from the command line and create an image 
   file
 These commands are available from:
   https://github.com/osm0sis/mkbootimg.git
 Clone the repository and build with make. You'll get 
 the binaries.
 Some pre compiled are available for ubuntu 16.04 amd64 at:
 https://github.com/rcoscali/mkbootimg-binaries

### From a factory image (angler-nmf26f-factory-ef607244.zip for ex)
$ mkdir angler-nmf26f-factory-ef607244
$ cd angler-nmf26f-factory-ef607244
$ unzip ../angler-nmf26f-factory-ef607244.zip
Archive:  ../angler-nmf26f-factory-ef607244.zip
   creating: angler-nmf26f/
 extracting: angler-nmf26f/image-angler-nmf26f.zip  
  inflating: angler-nmf26f/bootloader-angler-angler-03.62.img  
  inflating: angler-nmf26f/flash-all.bat  
  inflating: angler-nmf26f/flash-all.sh  
  inflating: angler-nmf26f/radio-angler-angler-03.78.img  
  inflating: angler-nmf26f/flash-base.sh  
### Extract images
$ cd angler-nmf26f
$ mkdir image-angler-nmf26f
$ cd image-angler-nmf26f
$ unzip ../image-angler-nmf26f.zip
Archive:  ../image-angler-nmf26f.zip
  inflating: android-info.txt        
  inflating: system.img              
  inflating: boot.img                
  inflating: vendor.img              
  inflating: recovery.img            
  inflating: cache.img               
  inflating: userdata.img            

## boot.img & system.img
Here are the boot.img & system.img images
Unpack boot image

$ mkdir boot
$ unpackbootimg -i boot.img -o boot
BOARD_KERNEL_CMDLINE androidboot.hardware=angler androidboot.console=ttyHSL0 msm_rtb.filter=0x37 ehci-hcd.park=3 lpm_levels.sleep_disabled=1 boot_cpus=0-3 no_console_suspend buildvariant=user
BOARD_KERNEL_BASE 00000000
BOARD_NAME 
BOARD_PAGE_SIZE 4096
BOARD_KERNEL_OFFSET 00008000
BOARD_RAMDISK_OFFSET 02000000
BOARD_TAGS_OFFSET 01e00000
BOARD_OS_VERSION 7.1.1
BOARD_OS_PATCH_LEVEL 2016-12
$ cd boot
### Uncompress ramdisk cpio image
$ gzip -dc boot.img-ramdisk.gz > boot.img-ramdisk.cpio
### Extract ramdisk cpio image
$ mkdir boot.img-ramdisk
$ cd boot.img-ramdisk
$ cat ../boot.img-ramdisk.cpio | cpio -i
##
# Modify ramdisk tree (default.prop, init.*.rc)
# Changed properties to allow debuggable and root adb
# and also to remove the recovery patching mechanism (install-recovery.sh) service 
##
find . -print0 | cpio -o0a -H newc -R root.root -O ../boot.img-ramdisk2.cpio
$ cd ..
$ gzip -c9 boot.img-ramdisk2.cpio > boot.img-ramdisk2.gz
$ mkbootimg --kernel boot.img-zImage --ramdisk boot.img-ramdisk2.gz --cmdline "androidboot.hardware=angler androidboot.console=ttyHSL0 msm_rtb.filter=0x37 ehci-hcd.park=3 lpm_levels.sleep_disabled=1 boot_cpus=0-3 no_console_suspend buildvariant=user" --board "Nexus 6P" --base 00000000 --pagesize 4096 --kernel_offset 00008000 --ramdisk_offset 02000000 --tags_offset 01e00000 --os_version 7.1.1 --os_patch_level 2016-12 -o ../boot2.img
# Then flash it
$ cd ..
$ fastboot flash boot boot2.img
$ fastboot reboot

