# Пошаговое руководство для себя
## 1. LiveISO
```bash
    # Partitions
    mkfs.fat -F 32 /dev/nvme0n1p1
    mkfs.ext4 /dev/nvme0n1p2
    mkfs.ext4 /dev/vg_raid0/raiden
    mkdir -p /mnt/{boot/efi,home}
    mount /dev/nvme0n1p2 /mnt
    mount /dev/nvme0n1p1 /mnt/boot/efi
    mount /dev/vg_raid0/raiden /mnt/home

    # SWAP-file
    cd /mnt/home && fallocate -l 24G .swapfile
    dd if=/dev/zero of=.swapfile bs=1G count=24 status=progress
    chmod 600 .swapfile
    mkswap .swapfile
    swapon .swapfile

    # Pacman Config
    sudo sed -i -e 's/#ParallelDownloads = [0-9]*/ParallelDownloads = 10/' \
        -e '/#\[multilib\]/s/^#//' -e '/#\[multilib\]/{n;s/^#//}' /etc/pacman.conf

    # Install packages
    pacstrap /mnt base linux linux-firmware linux-headers sudo dhcpcd lvm2 \
        vim nano btop htop fastfetch iwd samba openssh git base-devel \
        && cp /etc/pacman.conf /mnt/etc/pacman.conf

    # Generate FSTAB & enter to system
    genfstab -U /mnt >> /mnt/etc/fstab
    arch-chroot /mnt
```

## Chroot
## First Boot