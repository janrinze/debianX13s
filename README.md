# debianX13s
Useful things for installing and maintaining Debian Linux on the Lenovo X13s

## UEFI config
efibootmgr can be used to reconfigure UEFI when a boot entry was removed.  
Current EFI boot entry:  
```Boot0002* Debian local	HD(5,GPT,eb819d1a-33f8-460f-b5b8-c55663435ee9,0x18762800,0x40000)/\EFI\debian\shimaa64.efi```  
where ```eb819d1a-33f8-460f-b5b8-c55663435ee9``` is the EFI partition (number 5) of the Debian install.  
to recreate:  

```
efibootmgr --create --gpt --disk /dev/nvme0n1 --part 5 --write-signature --label "Debian on NVME" --loader '\EFI\debian\shimaa64.efi'
```

## EFI partition
Sometimes the ```/boot/efi/EFI/debian/grub.cfg``` in the EFI partion goes blank.  
Make sure to copy the grub.cfg in the EFI partition as grub.cfg.orig.  
This way when grub.cfg happens to become an empty file it is possible to restore from the .orig file. 

the contents are supposed to look like:  
```
search.fs_uuid a3423e1e-89b3-4fd0-a97b-ad643c8ae8c9 root 
set prefix=($root)'/boot/grub'
configfile $prefix/grub.cfg
```
where on my system ```a3423e1e-89b3-4fd0-a97b-ad643c8ae8c9``` is the UUID of my rootfs.  

## booting is based on the UUID of the respective partitions
use blkid to identify the partitions.  
ensure that in /etc/grub.d/ the correct partitions are set.  

## dtb file
ensure the file ```sc8280xp-lenovo-thinkpad-x13s.dtb``` is available in the root of the EFI partition.  
Alternatively copy ```sc8280xp-lenovo-thinkpad-x13s.dtb``` to ```/boot/``` and add ```devicetree /boot/sc8280xp-lenovo-thinkpad-x13s.dtb``` to ```/boot/grub/grub.conf```

## make a bootable linux USB disk
To be able to recover from mistakes or broken installs do an install of Debian to a USB disk.
The USB disk then can be used later for recovery purposes.

## help the initrd to find all the firmware
add a file ```/etc/initramfs-tools/hooks/x13s-firmware``` with the following contents:
```
#!/bin/sh

set -e

PREREQ=""

prereqs()
{
        echo "$PREREQ"
}

case \\$1 in
# get pre-requisites
prereqs)
        prereqs
        exit 0
        ;;
esac

. /usr/share/initramfs-tools/hook-functions

# Define a list of firmware files to be included
FIRMWARE_FILES="\
qcom/sc8280xp/LENOVO/21BX/qcadsp8280.mbn \
qcom/sc8280xp/LENOVO/21BX/qccdsp8280.mbn \
qcom/sc8280xp/LENOVO/21BX/qcdxkmsuc8280.mbn \
qcom/sc8280xp/LENOVO/21BX/qcvss8280.mbn \
qcom/sc8280xp/LENOVO/21BX/qcslpi8280.mbn \
qcom/a660_sqe.fw \
qcom/a660_gmu.bin"

# Copy each firmware file to initramfs
for file in $FIRMWARE_FILES; do
    dir=$(dirname "$file")
    mkdir -p "${DESTDIR}/lib/firmware/${dir}"
    cp "/lib/firmware/${file}" "${DESTDIR}/lib/firmware/${dir}/"
done
```
