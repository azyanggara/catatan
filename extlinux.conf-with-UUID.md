timeout 1
default openwrt
menu title OpenWrt Boot

label openwrt
	kernel /vmlinuz-openwrt
	fdtdir /
	initrd /initramfs-openwrt
	append root=UUID=... rootfstype=ext4 rw console=ttyMSM0,115200n8 earlycon=msm_serial.0,115200n8 ignore_loglevel
