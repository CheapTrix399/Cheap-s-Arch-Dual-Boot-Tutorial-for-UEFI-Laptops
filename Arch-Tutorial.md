## Arch Tutorial

### Before entering live boot environment
```
 - When the grub menu screen opens press e (or Tab ) and add "pci=noaer" to the kernel parameters, ( on the line containing linux /boot/vmlinuz..etc )
```

### Phase-1 - While on live boot environment
```
 - Use USB tethering / LAN cable to get internet ( Or pray to god and try "ip link / iwctl" to connect to wifi )
 - lsblk - To check disk partitions
 - cfdisk - Graphical disk partitioner
 - Optimally you should have 3 partitions - swap, boot and root ( The partitions will be like /dev/sda1 /dev/sda2 etc ) ( Some ppl also set /home seperately )
 - mkfs.ext4 /dev/sda1 ( root partition )
 - mkswap /dev/sda2 ( swap partition )
 - mkfs.fat -F 32 /dev/sda3 ( boot partition )
 - Mount all the partitions to the live environment
   - mount /dev/sda1 /mnt
   - mount --mkdir /dev/sda3 /mnt/boot
   - swapon /dev/sda2
 - Install arch packages from internet to the main system
   - pacstrap /mnt base linux linux-firmware nano networkmanager ( NetworkManager is important so that ur wifi works after restart )
 - Create a Genfstab ( So that your main system remembers where the mount partitions are for root, boot and swap )
   - genfstab -U /mnt >> /mnt/etc/fstab
```
### Phase-2 - Chroot into main system
```
 - arch-chroot /mnt
 - nano /etc/hostname ( Create hostname )
 - passwd ( Create root password )
 - pacman -S ntfs-3g os-prober ( These packages are required to detect windows partition )
 - pacman -S efibootmgr grub ( Install grub )
 - grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB ( Since we mounted boot to /mnt/boot and our main root is /mnt ) # FOR UEFI SYSTEM
 - nano /etc/default/grub ( Edit GRUB )
   - In the line containing GRUB_CMDLINE_LINUX_DEFAULT="..." add "pci=noaer" at the end ( The reason for this is some laptops hang with pci error loops, so we disable them )
   - In the last line uncomment GRUB_DISABLE_OS_PROBER=false
 - grub-mkconfig -o /boot/grub/grub.cfg ( Create new GRUB config, your windows should be detected here )
 - systemcl enable NetworkManager ( Case sensitive )
 - pacman -S plasma sddm konsole ( Install kde )
```

### Phase-3 - Reboot
```
 - Your GRUB menu should have appeared
 - Intially login through root
 - useradd -m MyName ( Create user )
 - passwd MyName ( User password )
 - pacman -S sudo (Install sudo)
 - EDITOR=nano visudo ( Edit sudoers file )
   - Scroll down and below "root ALL=(ALL:ALL) ALL" add "MyName ALL=(ALL:ALL) ALL"
 - Now u can change user with "su MyName"
 - systemctl enable sddm ( and then reboot )
```

### Phase-4 NVIDIA Drivers
```
 - If u have AMD u should be bing chilling
 - pacman -S optimus-manager nvidia
 - systemctl enable optimus-manager
 - nano /etc/default/grub
   - To GRUB_CMDLINE_LINUX_DEFAULT="..." add "optimus-manager.startup=nvidia"
 - grub-mkconfig -o /boot/grub/grub.cfg
```