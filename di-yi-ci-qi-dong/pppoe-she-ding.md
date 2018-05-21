# PPPOE 設定

> ## 安裝指令

```text
sudo apt-get -y install pppoeconf

pppoeconf
```

> 斷線自動連線設定

編輯 /etc/ppp/peers/dsl-provider，依下面的參數設定

```text
lcp-echo-interval 200
lcp-echo-failure 40
noauth
persist
mtu 1000
maxfail 0
holdoff 0
```

> 自行開發程式檢查PPPOE連線是否斷線，並且斷線自動重連

用Python開發二支程式，一支用來檢查PPPOE連線狀態，另一支用來斷線時重新連線，為什麼要分成二支程式？因設計上不想頻繁地檢查狀態而虛耗資源，但當斷線發生時又必須較密集的檢查恢復連線，所以這樣設計。

* PPPOE連線狀態檢查程式 **chk\_pppoe.py**（每30分鐘執行檢查一次）

```text
#!/usr/bin/python

import subprocess
import time
import datetime

def OpenProcess(cmd):
    output = ""
    bashCommand = cmd
    try:
       process = subprocess.Popen(bashCommand.split(), stdout=subprocess.PIPE, stderr=subprocess.PIPE)
       output, error = process.communicate()
    except Exception as ex:
       output = str(ex)

    return output + error

def WriteTxtFile(filename, status_msg):
    fp = open(filename, "w")
    fp.write(status_msg)
    fp.close()

def AppendTxtFileWithTimestamp(filename, status_msg):
    fp = open(filename, "a")
    fp.write("[" + datetime.datetime.now().strftime("%Y/%m/%d %H:%M:%S") + "] " + status_msg + "\n")
    fp.close()


log_file = "/home/pi/pppoe_log/pppoe_" + datetime.datetime.now().strftime("%Y%m%d") + ".log"
status_file = "/home/pi/pppoe_log/pppoe_status.txt"
ss=OpenProcess("ping -I ppp0 -c 1 1.1.1.1")

if ss.find("100% packet loss")>0 or ss.find("Invalid argument")>0:
    WriteTxtFile(status_file, "Disconnect")
    AppendTxtFileWithTimestamp(log_file, "PPPOE Disconnect")
else:
    WriteTxtFile(status_file, "Connected")
    AppendTxtFileWithTimestamp(log_file, "PPPOE OK")
```

* PPPOE重新連線程式 **auto\_repppoe.py**（每5分鐘執行一次）

```text
#!/usr/bin/python

import subprocess
import time
import datetime

def OpenProcess(cmd):
    output = ""
    bashCommand = cmd
    try:
       process = subprocess.Popen(bashCommand.split(), stdout=subprocess.PIPE, stderr=subprocess.PIPE)
       output, error = process.communicate()
    except Exception as ex:
       output = str(ex)

    return output + error

def AppendTxtFileWithTimestamp(filename, status_msg):
    fp = open(filename, "a")
    fp.write("[" + datetime.datetime.now().strftime("%Y/%m/%d %H:%M:%S") + "] " + status_msg + "\n")
    fp.close()

log_file = "/home/pi/pppoe_log/pppoe_" + datetime.datetime.now().strftime("%Y%m%d") + ".log"
status_file = "/home/pi/pppoe_log/pppoe_status.txt"
fp = open(status_file, "r")
line = fp.readline()
fp.close()

if line.find("Dis")>=0:
    print "Status: Disconnect"
    ss=OpenProcess("ping -I ppp0 -c 1 1.1.1.1")
    #ss=OpenProcess("ping -I ppp0 -c 1 10.8.0.201")

    if ss.find("100% packet loss")>=0 or ss.find("Invalid argument")>=0:
        AppendTxtFileWithTimestamp(log_file, "PPPOE Re-dial...")
        print "[Disconnect] re-connect..."
        OpenProcess("/usr/bin/poff dsl-provider")
        time.sleep(5)
        OpenProcess("/usr/bin/pon dsl-provider")
    else:
        print "[Connected]"
else:
    print "Status: Connected"

```

