# ShadowsocksR

> 安裝套件（一鍵安裝腳本）

```text
wget -N --no-check-certificate https://softs.fun/Bash/ssrmu.sh && chmod +x ssrmu.sh && bash ssrmu.sh
```

* 依腳本功能安裝，過程沒有太大問題，建議先安裝 libsodium\(chacha20\)，再安裝 ShadowsocksR。
* 此腳本 前置OS檢查並不包含Raspbian，需手動改/etc/issue檔案內容，將 Raspbian改成Debian。
* 腳本功能中有引用到一個 jq 套件 \(/usr/local/shadowsocksr/jq\)，直接下載的版本非 RPi 可用，必須下載源碼重新編譯過才能用，也可以從其他台 RPi 的複製已編譯好的過來即可。
* 一鍵安裝腳本出處: [https://doub.io/ss-jc60/](https://doub.io/ss-jc60/)

> 設定不能訪問內網

* 因有另一張網卡連接內網，為了安全起見直接限制SSR啟動帳戶\(root\)禁用該網卡

```text
/sbin/iptables -A OUTPUT -o wlan0 -m owner --uid-owner root -j REJECT --reject-with icmp-host-prohibited
/sbin/iptables -A OUTPUT -o tun0 -m owner --uid-owner root -j REJECT --reject-with icmp-host-prohibited
```

* 此處wlan0是無線網卡，tun0是OpenVPN的虛擬網卡，基本上我都希望禁用，讓SSR只有一個對外的網卡可用。
* iptables指令前端加上/sbin目錄主要用途是讓指令在排程\(crontab\)中也能正常運行
* 承上，若將指令設定在開機@reboot排程中須注意要延後執行，因我們阻擋的是root帳號，若當網卡DHCP還沒取得IP前就被擋掉，會造成其他帳號也會無法使用該網卡，故建議寫一個.sh，前面先睡個1分鐘。
* 網路上有人用其他帳號去啟動SSR，然後再封鎖此帳號對其他網卡的存取，這是比較好的做法\(把root的存取封鎖掉是有點怪\)，但礙於我使用的是第三方一鍵安裝腳本，必須以root帳號啟動SSR，所以才針對root帳號下手。

