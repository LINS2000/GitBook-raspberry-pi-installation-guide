# **ShadowsocksR**

---

> 安裝套件（一鍵安裝腳本）

```
wget -N --no-check-certificate https://softs.fun/Bash/ssrmu.sh && chmod +x ssrmu.sh && bash ssrmu.sh
```

* 依腳本功能安裝，過程沒有太大問題，建議先安裝 libsodium\(chacha20\)，再安裝 ShadowsocksR。
* 腳本功能中有引用到一個 jq 套件 \(/usr/local/shadowsocksr/jq\)，直接下載的版本非 RPi 可用，必須下載源碼重新編譯過才能用，也可以從其他台 RPi 的複製已編譯好的過來即可。

* 一鍵安裝腳本出處: [https://doub.io/ss-jc60/](https://doub.io/ss-jc60/)

> 設定不能訪問內網

* 因有另一張網卡連接內網，為了安全起見直接限制SSR啟動帳戶\(root\)禁用該網卡

```
/sbin/iptables -A OUTPUT -o wlan0 -m owner --uid-owner root -j REJECT --reject-with icmp-host-prohibited
/sbin/iptables -A OUTPUT -o tun0 -m owner --uid-owner root -j REJECT --reject-with icmp-host-prohibited
```

* 此處wlan0是無線網卡，tun0是OpenVPN的虛擬網卡，基本上我都希望禁用，讓SSR只有一個對外的網卡可用。
* iptables指令前端加上/sbin目錄主要用途是讓指令排程\(crontab\)中也能正常運行



