# PiVPN \(OpenVPN\)

> 安裝指令

```text
curl -L https://install.pivpn.io | bash
```

* 有UI，跟著步驟做，不會太難，或可以參考 [PiVPN Setup And Configuration](https://www.ostechnix.com/pivpn-simplest-openvpn-setup-configuration-designed-raspberry-pi/)。

> Route 設定 \(後來發現僅需最後一個，前方\#註解的都不需要\)

```text
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

```text
# 備份目前設定--以備還原
sudo cp /etc/iptables/rules.v4 /etc/iptables/rules-bak.v4

# 以目前iptables設定取代rules.v4--讓下次開機後仍有效
sudo iptables-save > /etc/iptables/rules.v4
```

> server.conf 設定

```text
push "route 192.168.5.0 255.255.255.0"
```

* 修改 /etc/openvpn/server.conf，加入上面設定，此設定主要是作用在Client端，讓Client端連上VPN時能套用此route rule。

> 分配固定IP設定

* 建立資料匣

```text
sudo mkdir /etc/openvpn/ccd
```

* server.conf 檔新增一行

```text
client-config-dir /etc/openvpn/ccd
```

* 在 /etc/openvpn/ccd 下新增檔名為使用者名稱 \(即為 `pivpn -a` 所輸入的 Name for the Client\) 的檔案，例：使用者pi4201，固定IP為 10.8.0.201，內容為：

```text
ifconfig-push 10.8.0.201 255.255.255.0
```

> ~~My asus 設定 \(有問題不要用\)~~

```text
sudo iptables -I FORWARD -i tun21 -o br0 -s 10.8.0.0/24 -d 192.168.5.0/24 -m conntrack --ctstate NEW -j ACCEPT
sudo iptables -I FORWARD -i tun21 -o ppp0 -s 10.8.0.0/24 -m conntrack --ctstate NEW -j ACCEPT
sudo iptables -I FORWARD -i br0 -o ppp0 -s 192.168.5.0/24 -m conntrack --ctstate NEW -j ACCEPT
sudo iptables -I FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
sudo iptables -t nat -I POSTROUTING -o ppp0 -s 10.8.0.0/24 -j MASQUERADE
sudo iptables -t nat -I POSTROUTING -o ppp0 -s 192.168.5.0/24 -j MASQUERADE
sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o br0 -j MASQUERADE
```

> Debian 9 \(Stretch\) 環境下 OpenVPN Android 官方APP無法連接問題

* 參考解決方案：[Official OpenVPN Connect Android client error with PiVPN on Stretch: Requested encryption or digest alg not available \#364](https://github.com/pivpn/pivpn/issues/364)
* 以下節錄

```text
For the more impatient people, like me, here is how to re-encode the private key:

cd ~/ovpns
cp not-working-profile.ovpn not-working-profile-backup.ovpn
openssl rsa -aes256 -in not-working-profile.ovpn -out new-key-file.key
This will ask for the key password and will generate a new key file (asking for a new password - you can use the same one), containing ONLY the key, having the following header:

-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-256-CBC,XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

<private key content follows>
To compare, the old private key header (from the .ovpn file) looks like this:

-----BEGIN ENCRYPTED PRIVATE KEY-----
<private key content follows>
Now, after obtaining the new key, use your favorite text editor and open the .ovpn file and replace everything in between the <key> and </key> tags with the newly generated key.

Save the file, and then re-import the profile on your device. Enjoy!
```

