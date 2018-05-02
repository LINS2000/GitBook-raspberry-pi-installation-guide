# NO-IP 安裝設定

> ## 安裝NO-IP DUC

* 建立目錄

```text
mkdir /home/pi/noip
cd /home/pi/noip
```

* 下載 no-ip

```text
wget http://www.no-ip.com/client/linux/noip-duc-linux.tar.gz
```

* 解壓縮

```text
tar vzxf noip-duc-linux.tar.gz
```

* 編譯&安裝（安裝過程要輸入帳密、更新哪一個網域）

```text
cd noip-2.1.9-1
sudo make
sudo make install
```

* 變更檔案擁有者為pi（預設由pi啟動）

```text
sudo chown pi /usr/local/etc/no-ip2.conf
```

* 啟動更新

```text
/usr/local/bin/noip2
```

到這邊安裝與設定已完成，以下是其他指令參考：

* 顯示設定資訊

```text
/usr/local/bin/noip2 -S    #大寫S
```

* 停止已啟動的程序（可使用 -S 查詢 PID）

```text
/usr/local/bin/noip2 -K {PID}    #PID=Process ID
```

* 重新設定（未啟動的情況下才能做，如果已經在執行中，可使用 -K 停止程序）

```text
sudo /usr/local/bin/noip2 -C    #大寫C

sudo chown pi /usr/local/etc/no-ip2.conf    #重新設定後須再做一次變更擁有者, 才能由pi來啟動
```

> ## 設定啟動時執行

網路上作法多為使用update-rc.d加入啟動服務，雖可成功更新IP沒問題，但實測更新週期設為5分鐘，則需等到第6分鐘後才有機會更新，導致有段空窗期，所以想辦法縮短這段空窗期。

首先想到的方法是把noip的更新週期縮短，但又怕週期太短機器會太忙，於是作罷。

再者就是探討服務啟動時為何沒有立即更新，猜測這個問題是系統剛開機時網路服務可能未啟動或IP還沒取得，導致無法成功更新。所以產生一個想法就是修改執行noip的執行時機，設定為開機後等待30秒後再執行noip服務，經實測後結果為可行，成功縮短等待空窗期，作法如下。

* 新增一個noip-auto.sh檔，執行noip服務前延遲30秒（需注意noip相關檔案執行身分與權限）

### noip-auto.sh 檔案內容

```text
#!/bin/bash
sleep 30
/usr/local/bin/noip2
```

* 加入crontab排程中，在開機時啟動

### 編輯 crontab -e

```text
@reboot bash ~/noip-auto.sh
```

