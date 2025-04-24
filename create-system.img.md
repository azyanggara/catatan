🔧 Langkah Manual: Buat Image ala PMOS
**1.** Buat file kosong

dd if=/dev/zero of=qcom-msm89x7-openwrt.img bs=1M count=2500

**2.** Buat partisi

parted qcom-msm89x7-openwrt.img --script mklabel msdos \
  mkpart primary ext2 1MiB 257MiB \
  mkpart primary ext4 257MiB 100%

**3.** Setup loop device

sudo losetup -Pf qcom-msm89x7-openwrt.img
lsblk | grep loop

Misalnya muncul /dev/loop28p1 dan p2.

**4.** Format partisi

sudo mkfs.ext2 -L boot /dev/loop28p1
sudo mkfs.ext4 -L rootfs /dev/loop28p2

**5.** Mount dan copy

    chmod +x build_openwrt_img.sh
    chmod +x build_openwrt_img.sh

    chmod +x build_openwrt_img.sh
    chmod +x build_openwrt_img.sh
    
sudo mount /dev/loop28p1 /mnt/owrt_boot
sudo mount /dev/loop28p2 /mnt/owrt_rootfs

sudo cp -a /mnt/data/openwrt_boot/. /mnt/owrt_boot/
sudo cp -a /mnt/data/openwrt_rootfs/. /mnt/owrt_rootfs/

    
**6.** Sync, unmount, detach

sync
sudo umount /mnt/owrt_boot
sudo umount /mnt/owrt_rootfs
sudo losetup -d /dev/loop28

Setelah itu kamu bisa flash langsung:

fastboot flash system qcom-msm89x7-openwrt.img
fastboot reboot​

​


✅ Cara Jalankan:

**1.** Simpan sebagai build_openwrt_img.sh

**2.** Jadikan executable:

    chmod +x build_openwrt_img.sh
    
**3.** Jalankan:

    ./build_openwrt_img.sh
    
Setelah selesai, kamu tinggal flash hasilnya:

fastboot flash system qcom-msm89x7-openwrt.img
fastboot reboot
