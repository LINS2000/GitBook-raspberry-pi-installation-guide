# Mount USB Disk

> 建立嵌入點

```text
sudo mkdir /media/u
sudo chown -R pi:pi /media/u
```

> 修改 /etc/fstab

```text
sudo nano /etc/fstab
```

* 檔案內容新增一筆

```text
/dev/sda1       /media/u        ext4    defaults          0       0
```

* /etc/fstab 檔案內容可能會像這樣

```text
proc            /proc           proc    defaults          0       0
/dev/mmcblk0p5  /boot           vfat    defaults          0       2
/dev/mmcblk0p6  /               ext4    defaults,noatime  0       1
/dev/sda1       /media/u        ext4    defaults          0       0
# a swapfile is not a swap partition, so no using swapon|off from here on, use  dphys-swapfile swap[on|off]  for that
```

> 重新開機生效

```text
sudo reboot
```

> 新增捷徑 \(Optional\)

```text
ln -s /media/u/pi ~/u
```

* 新增捷徑在home目錄下，方便存取

