# no-ip安裝設定

---

> ##### 安裝NO-IP DUC

```
# 建立目錄
sudo mkdir /home/pi/noip
cd /home/pi/noip

# 下載 no-ip
sudo wget http://www.no-ip.com/client/linux/noip-duc-linux.tar.gz

# 解壓縮
sudo tar vzxf noip-duc-linux.tar.gz

# 編譯&安裝
cd noip-2.1.9-1
sudo make
sudo make install

# 啟動更新
sudo /usr/local/bin/noip2
```

> ##### 設定啟動時執行

網路上作法多為使用update-rc.d加入啟動服務，雖可成功更新IP沒問題，但實測更新週期設為5分鐘，則需等到第6分鐘後才有機會更新，導致有段空窗期，所以想辦法縮短這段空窗期。

首先想到的方法是把noip的更新週期縮短，但又怕週期太短機器會太忙，於是作罷。

再者就是探討服務啟動時為何沒有立即更新，猜測這個問題是系統剛開機時網路服務可能未啟動或IP還沒取得，導致無法成功更新。所以產生一個想法就是修改執行noip的執行時機，設定為開機後等待30秒後再執行noip服務，經實測後結果為可行，成功縮短等待空窗期，作法如下。

* 新增一個noip-auto.sh檔，執行noip服務前延遲30秒（需注意noip相關檔案執行身分與權限）

###### noip-auto.sh 檔案內容

```
#!/bin/bash
sleep 30
/usr/local/bin/noip2
```

* 加入crontab排程中，在開機時啟動

###### 編輯 crontab -e

```
@reboot bash ~/noip-auto.sh
```



