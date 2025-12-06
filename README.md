# debianX13s
Useful things for installing and maintaining Debian Linux on the Lenovo X13s

## UEFI config
efibootmgr can be used to reconfigure UEFI when a boot entry was removed.

## EFI partition
Sometimes the grub.cfg in the EFI partion goes blank.  
Make sure to copy the grub.cfg in the EFI partition as grub.cfg.orig.  
This way when grub.cfg happens to become an empty file it is possible to restore from the .orig file.  

## booting is based on the UUID of the respective partitions
use blkid to identify the partitions.  
ensure that in /etc/grub.d/ the correct partitions are set.  

## dtb file
ensure the file sc8280xp-lenovo-thinkpad-x13s.dtb is available in the root of the EFI partition
alternatively copy sc8280xp-lenovo-thinkpad-x13s.dtb to /boot/ and add ```devicetree /boot/sc8280xp-lenovo-thinkpad-x13s.dtb``` to ```/boot/grub/grub.conf```
