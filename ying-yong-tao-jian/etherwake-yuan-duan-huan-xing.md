# etherwake - 遠端喚醒

> 安裝套件

```text
sudo apt-get install etherwake
```

> 指令

```text
sudo etherwake -i wlan0 00:08:9B:F7:EF:65
```

**Options :** 

**-b** 

Send the wake-up packet to the broadcast address.

**-D** 

Increase the Debug Level.

 **-i ifname \(如果不是預設網卡eth0, 需指定網卡\) -- 很重要!!!**

Use interface ifname.

**-p passwd** 

Append a four or six byte password to the packet. Only a few adapters need or support this. A six byte password may be specified in Ethernet hex format \(00:22:44:66:88:aa\) or four byte dotted decimal \(192.168.1.1\) format. A four byte password must use the dotted decimal format.

**-V** 

Show the program version information.

