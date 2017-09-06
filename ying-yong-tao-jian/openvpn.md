# PiVPN \(OpenVPN\) Server 安裝

---

> 安裝指令

```
curl -L https://install.pivpn.io | bash
```

* 有UI，跟著步驟做，不會太難，或可以參考 [PiVPN Setup And Configuration](https://www.ostechnix.com/pivpn-simplest-openvpn-setup-configuration-designed-raspberry-pi/)。

> Route 設定 \(後來發現僅需最後一個，前方\#註解的都不需要\)

```
#sudo iptables -I FORWARD -i tun0 -o wlan0 -s 10.8.0.0/24 -d 192.168.5.0/24 -m conntrack --ctstate NEW -j ACCEPT
#sudo iptables -I FORWARD -i tun0 -o ppp0 -s 10.8.0.0/24 -m conntrack --ctstate NEW -j ACCEPT
#sudo iptables -I FORWARD -i wlan0 -o ppp0 -s 192.168.5.0/24 -m conntrack --ctstate NEW -j ACCEPT
#sudo iptables -I FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
#sudo iptables -t nat -I POSTROUTING -o ppp0 -s 10.8.0.0/24 -j MASQUERADE
#sudo iptables -t nat -I POSTROUTING -o ppp0 -s 192.168.5.0/24 -j MASQUERADE
sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o wlan0 -j MASQUERADE
```

* 上述設定主要是要能從VPN登入後Access到Lan，是參考 [OpenVPN - Bridging vs. Routing](https://community.openvpn.net/openvpn/wiki/BridgingAndRouting) 與 [PiVPN - Potential issues with your iptables](https://github.com/pivpn/pivpn/issues/182) 而找到的解決方案。
* 這樣的設定僅暫時生效，重新開機後將失效，為了讓設定永久保留，必須將設定回存/etc/iptables/rules.v4。

```
# 備份目前設定--以備還原
sudo cp /etc/iptables/rules.v4 /etc/iptables/rules-bak.v4

# 以目前iptables設定取代rules.v4--讓下次開機後仍有效
sudo iptables-save > /etc/iptables/rules.v4
```

> server.conf 設定

```
push "route 192.168.5.0 255.255.255.0"
```

* 修改/etc/openvpn/server.conf，加入上面設定，此設定主要是作用在Client端，讓Client端連上VPN時能套用此route rule。

> 分配固定IP設定

* 建立資料匣

```
sudo mkdir /etc/openvpn/ccd
```

* server.conf 檔新增一行

```
client-config-dir /etc/openvpn/ccd
```

* 在/etc/openvpn/ccd下新增檔名為使用者名稱的檔案 \(例：pi4201\)，內容為：\(指定固定IP為 10.8.0.201\)

```
ifconfig-push 10.8.0.201 255.255.255.0
```

> My asus 設定 \(有問題不要用\)

```
sudo iptables -I FORWARD -i tun21 -o br0 -s 10.8.0.0/24 -d 192.168.5.0/24 -m conntrack --ctstate NEW -j ACCEPT
sudo iptables -I FORWARD -i tun21 -o ppp0 -s 10.8.0.0/24 -m conntrack --ctstate NEW -j ACCEPT
sudo iptables -I FORWARD -i br0 -o ppp0 -s 192.168.5.0/24 -m conntrack --ctstate NEW -j ACCEPT
sudo iptables -I FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
sudo iptables -t nat -I POSTROUTING -o ppp0 -s 10.8.0.0/24 -j MASQUERADE
sudo iptables -t nat -I POSTROUTING -o ppp0 -s 192.168.5.0/24 -j MASQUERADE
sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o br0 -j MASQUERADE
```



