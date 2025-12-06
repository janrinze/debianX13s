# debianX13s
Useful things for installing and maintaining Debian Linux on the Lenovo X13s

## UEFI config
efibootmgr can be used to reconfigure UEFI when a boot entry was removed.

## EFI partition
Sometimes the grub.cfg in the EFI partion goes blank.
Make sure to copy the grub.cfg in the EFI partition as grub.cfg.orig.
This way when grub.cfg happens to become an empty file it is possible to restore from the .orig file.
