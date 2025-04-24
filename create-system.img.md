🔧 Langkah Manual: Buat Image ala PMOS
1. Buat file kosong

dd if=/dev/zero of=qcom-msm89x7-openwrt.img bs=1M count=2500

2. Buat partisi

parted qcom-msm89x7-openwrt.img --script mklabel msdos \
  mkpart primary ext2 1MiB 257MiB \
  mkpart primary ext4 257MiB 100%

3. Setup loop device

sudo losetup -Pf qcom-msm89x7-openwrt.img
lsblk | grep loop

Misalnya muncul /dev/loop28p1 dan p2.

4. Format partisi

sudo mkfs.ext2 -L boot /dev/loop28p1
sudo mkfs.ext4 -L rootfs /dev/loop28p2

5. Mount dan copy isi

sudo mount /dev/loop28p1 /mnt/owrt_boot
sudo mount /dev/loop28p2 /mnt/owrt_rootfs

sudo cp -a /mnt/data/openwrt_boot/. /mnt/owrt_boot/
sudo cp -a /mnt/data/openwrt_rootfs/. /mnt/owrt_rootfs/

6. Sync, unmount, detach

sync
sudo umount /mnt/owrt_boot
sudo umount /mnt/owrt_rootfs
sudo losetup -d /dev/loop28

Setelah itu kamu bisa flash langsung:

fastboot flash system qcom-msm89x7-openwrt.img
fastboot reboot

Kalau kamu butuh bantuan ngetik skrip lengkapnya, aku siap bantu 🙌 ​

​
Anda bilang:
ok bantu ngetik skrip lengkapnya
ChatGPT bilang:

Siap! Berikut adalah skrip lengkap untuk membuat qcom-msm89x7-openwrt.img ala PMOS — bisa kamu simpan sebagai file build_openwrt_img.sh dan jalankan:
📜 build_openwrt_img.sh

#!/bin/bash

set -e

# --- Konfigurasi awal ---
IMG_NAME="qcom-msm89x7-openwrt.img"
BOOT_LABEL="boot"
ROOTFS_LABEL="rootfs"
BOOT_SRC="/mnt/data/openwrt_boot"
ROOTFS_SRC="/mnt/data/openwrt_rootfs"

# --- Buat image kosong 2.5GB ---
echo "[1/6] Membuat file image kosong..."
dd if=/dev/zero of=$IMG_NAME bs=1M count=2500

# --- Partisi ---
echo "[2/6] Membuat partisi (boot 256MB + rootfs sisanya)..."
parted $IMG_NAME --script mklabel msdos \
  mkpart primary ext2 1MiB 257MiB \
  mkpart primary ext4 257MiB 100%

# --- Setup loop device ---
echo "[3/6] Menyiapkan loop device..."
LOOPDEV=$(sudo losetup -Pf --show $IMG_NAME)
echo "Loop device: $LOOPDEV"

# --- Format partisi ---
echo "[4/6] Format partisi boot dan rootfs..."
sudo mkfs.ext2 -L $BOOT_LABEL ${LOOPDEV}p1
sudo mkfs.ext4 -L $ROOTFS_LABEL ${LOOPDEV}p2

# --- Mount dan salin isi ---
echo "[5/6] Menyalin file boot dan rootfs..."
sudo mkdir -p /mnt/owrt_boot /mnt/owrt_rootfs
sudo mount ${LOOPDEV}p1 /mnt/owrt_boot
sudo mount ${LOOPDEV}p2 /mnt/owrt_rootfs

sudo cp -a $BOOT_SRC/. /mnt/owrt_boot/
sudo cp -a $ROOTFS_SRC/. /mnt/owrt_rootfs/

# --- Unmount dan cleanup ---
echo "[6/6] Cleanup..."
sync
sudo umount /mnt/owrt_boot
sudo umount /mnt/owrt_rootfs
sudo losetup -d $LOOPDEV

echo "✅ Berhasil membuat $IMG_NAME. Siap untuk di-flash!"


✅ Cara Jalankan:

    Simpan sebagai build_openwrt_img.sh

    Jadikan executable:

chmod +x build_openwrt_img.sh

    Jalankan:

./build_openwrt_img.sh

Setelah selesai, kamu tinggal flash hasilnya:

fastboot flash system qcom-msm89x7-openwrt.img
fastboot reboot
