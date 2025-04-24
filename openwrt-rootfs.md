
# extract openwrt rootfs
 # format gzip
 
  mkdir openwrt-rootfs
  tar -xzf openwrt-23.05.5-rootfs.tar.gz -C openwrt-rootfs
  
 # format ext4
 
  mkdir openwrt-rootfs
  sudo mount -o loop openwrt-rootfs.ext4 openwrt-rootfs
