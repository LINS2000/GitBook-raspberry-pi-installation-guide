# **ShadowsocksR**

---

> 安裝套件（一鍵安裝腳本）

```
wget -N --no-check-certificate https://softs.fun/Bash/ssrmu.sh && chmod +x ssrmu.sh && bash ssrmu.sh
```

* 依腳本功能安裝，過程沒有太大問題，建議先安裝 libsodium\(chacha20\)，再安裝 ShadowsocksR。
* 腳本功能中有引用到一個 jq 套件 \(/usr/local/shadowsocksr/jq\)，直接下載的版本非 RPi 可用，必須下載源碼重新編譯過才能用，也可以從其他台 RPi 的複製已編譯好的過來即可。

* 一鍵安裝腳本出處: [https://doub.io/ss-jc60/](https://doub.io/ss-jc60/)



