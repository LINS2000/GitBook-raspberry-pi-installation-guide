# KMS伺服器

> ## Linux上利用vlmcsd搭建KMS伺服器

{% embed url="https://wijtb.nctu.me/archives/54/" %}

```text
安裝vlmcsd
下載檔案

wget https://github.com/Wind4/vlmcsd/releases/download/svn1111/binaries.tar.gz
解壓縮檔案

tar -zxvf binaries.tar.gz
切換至該目錄

cd binaries
該目錄會有

Android  DragonFly  FreeBSD  Hurd  iOS  Linux  MacOSX  Minix  NetBSD  OpenBSD  Solaris  Windows
選擇自己需要的版本就可以了，這裡以ubuntu x64為例

cd Linux/intel/static
複製二進制至/usr/bin

cp vlmcsd-x64-musl-static kms && mv kms /usr/bin
賦予執行權利

chmod +x /usr/bin/kms
直接輸入kms即可運行服務
確認是否運行成功

netstat -an | grep 1688
firewall-cmd放行防火牆 1688 Port

firewall-cmd --zone=public --add-port=1688/tcp
firewall-cmd --zone=public --permanent --add-port=1688/tcp
firewall-cmd --reload
或者

使用iptables

iptables -I INPUT -p tcp -m state --state NEW -m tcp --dport 1688 -j ACCEPT
service iptables save
service iptables restart
systemctl建立開機啟動

vim /usr/lib/systemd/system/kms.service
貼上底下的內容

[Unit]  
Description=KMS Service 
After=network.target
Wants=network.target
[Service]  
Type=forking
ExecStart=/usr/bin/kms
ExecStop=/usr/bin/killall kms
     
[Install]  
WantedBy=multi-user.target  
激活kms.service

systemctl enable kms.service
啟動kms

systemctl start kms.service
停止kms

systemctl stop kms.service
加入開機自啟

systemctl enable kms.service
或者使用rc.local來加入開機自啟

vim /etc/rc.local
加入

/usr/bin/kms
激活金鑰
激活Windows7/Windows10專業版為例：

用管理員身份運行命令行

解除原先金鑰
slmgr.vbs -upk
選擇金鑰，請依照底下的金鑰列表作選擇
slmgr.vbs -ipk W269N-WFGWX-YVC9B-4J6C9-T83GX
設定KMS伺服器
slmgr.vbs -skms 172.17.0.29
激活
slmgr.vbs -ato
顯示KMS相關資訊
slmgr.vbs -dlv
激活Office2010/Office2013/Office2016:
用管理員身份運行命令行

if exist "C:\Program Files (x86)\Microsoft Office\Office14\ospp.vbs" (cd "C:\Program Files (x86)\Microsoft Office\Office14") else (cd "c:\Program Files\Microsoft Office\Office14")
if exist "C:\Program Files (x86)\Microsoft Office\Office15\ospp.vbs" (cd "C:\Program Files (x86)\Microsoft Office\Office15") else (cd "c:\Program Files\Microsoft Office\Office15")
if exist "C:\Program Files (x86)\Microsoft Office\Office16\ospp.vbs" (cd "C:\Program Files (x86)\Microsoft Office\Office16") else (cd "c:\Program Files\Microsoft Office\Office16")
cscript ospp.vbs /osppsvcauto
cscript ospp.vbs /sethst:172.17.0.29
cscript ospp.vbs /act
cscript ospp.vbs /dstatus
微軟官方KMS列表：

https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj612867(v=ws.11)
附表：各操作系統 KMS 激活方式所對應的激活碼

Windows 10
操作系統版本 KMS 客戶端設置密鑰

Windows 10 專業版 W269N-WFGWX-YVC9B-4J6C9-T83GX
Windows 10 Professional N MH37W-N47XK-V7XM9-C7227-GCQG9
Windows 10 企業版 NPPR9-FWDCX-D2C8J-H872K-2YT43
Windows 10 企業 N DPH2V-TTNVB-4X9Q3-TJR4H-KHJW4
Windows 10 教育 NW6C2-QMPVW-D7KKK-3GKT6-VCFB2
Windows 10 教育 N 2WH4N-8QGBV-H22JP-CT43Q-MDWWJ
Windows 10 企業版 2015 LTSB WNMTR-4C88C-JK8YV-HQ7T2-76DF9
Windows 10 Enterprise 2015 LTSB N 2F77B-TNFGY-69QQF-B8YKP-D69TJ
Windows 10 企業 2016 LTSB DCPHK-NFMTC-H88MJ-PFHPY-QJ4BJ
Windows 10 企業 2016 LTSB N QFFDN-GRT3P-VKWWX-X7T3R-8B639
Windows Server 2012 R2 和 Windows 8.1
操作系統版本 KMS 客戶端設置密鑰

Windows 8.1 專業版 GCRJD-8NW9H-F2CDX-CCM8D-9D6T9
Windows 8.1 專業版 HMCNV-VVBFX-7HMBH-CTY9B-B4FXY
Windows 8.1 企業版 MHF9N-XY6XB-WVXMC-BTDCT-MKKG7
Windows 8.1 企業 N TT4HM-HN7YT-62K67-RGRQJ-JFFXW
Windows Server 2012 R2 服務器標準 D2N9P-3P6X9-2R39C-7RTCD-MDVJX
Windows Server 2012 R2 數據中心 W3GGN-FT8W3-Y4M27-J84CP-Q3VJ9
Windows Server 2012 R2 要點 KNC87-3J2TX-XB4WP-VCPJV-M4FWM
Windows Server 2012 和 Windows 8
Windows 8
操作系統版本 KMS 客戶端設置密鑰

Windows 8 專業版 NG4HW-VH26C-733KW-K6F98-J8CK4
Windows 8 Professional N XCVCF-2NXM9-723PB-MHCB7-2RYQQ
Windows 8 企業版 32JNW-9KQ84-P47T8-D8GGY-CWCK7
Windows 8 企業 N JMNMF-RHW7P-DMY6X-RF3DR-X2BQT
Windows Server 2012 BN3D2-R7TKB-3YPBD-8DRP2-27GG4
Windows Server 2012 N 8N2M2-HWPGY-7PGT9-HGDD8-GVGGY
Windows Server 2012 單一語言 2WN2H-YGCQR-KFX6K-CD6TF-84YXQ
Windows Server 2012 具體國家 4K36P-JN4VD-GDC6V-KDT89-DYFKP
Windows Server 2012 服務器標準 XC9B7-NBPP2-83J2H-RHMBY-92BT4
Windows Server 2012 MultiPoint Standard HM7DN-YVMH3-46JC3-XYTG7-CYQJJ
Windows Server 2012 MultiPoint Premium XNH6W-2V9GX-RGJ4K-Y8X6F-QGJ2G
Windows Server 2012 數據中心 48HP8-DN98B-MYWDG-T2DCC-8W83P
Windows 7 和 Windows Server 2008 R2
操作系統版本 KMS 客戶端設置密鑰

Windows 7 專業版 FJ82H-XT6CR-J8D7P-XQJJ2-GPDD4
Windows 7 專業版 MRPKT-YTG23-K7D7T-X2JMM-QY7MG
Windows 7 Professional E W82YF-2Q76Y-63HXB-FGJG9-GF7QX
Windows 7 企業版 33PXH-7Y6KF-2VJC9-XBBR8-HVTHH
Windows 7 企業 N YDRBP-3D83W-TY26F-D46B2-XCKRJ
Windows 7 企業 E C29WB-22CC8-VJ326-GHFJW-H9DH4
Windows Server 2008 R2 Web 6TPJF-RBVHG-WBW2R-86QPH-6RTM4
Windows Server 2008 R2 HPC 版 TT8MH-CG224-D3D7Q-498W2-9QCTX
Windows Server 2008 R2 Standard YC6KT-GKW9T-YTKYR-T4X34，R7VHC
Windows Server 2008 R2 企業版 489J6-VHDMP-X63PK-3K798-CPX3Y
Windows Server 2008 R2 數據中心 74YFP-3QFB3-KQT8W-PMXWJ-7M648
Windows Server 2008 R2（用於基於 Itanium 的系統） GT63C-RJFQ3-4GMB6-BRFB9-CB83V
Windows Vista 和 Windows Server 2008
操作系統版本 KMS 客戶端設置密鑰

Windows Vista Business YFKBB-PQJJV-G996G-VWGXY-2V3X8
Windows Vista Business N HMBQG-8H2RH-C77VX-27R82-VMQBT
Windows Vista 企業版 VKK3X-68KWM-X2YGT-QR4M6-4BWMV
Windows Vista 企業 N VTC42-BM838-43QHV-84HX6-XJXKV
Windows Web Server 2008 WYR28-R7TFJ-3X2YQ-YCY4H-M249D
Windows Server 2008 Standard TM24T-X9RMF-VWXK6-X8JC9-BFGM2
沒有 Hyper-V 的 Windows Server 2008 Standard W7VD6-7JFBR-RX26B-YKQ3Y-6FFFJ
Windows Server 2008 企業版 YQGMW-MPWTJ-34KDK-48M3W-X4Q6V
沒有 Hyper-V 的 Windows Server 2008 Enterprise 39BXF-X8Q23-P2WWT-38T2F-G3FPG
Windows Server 2008 HPC RCTX3-KWVHP-BR6TB-RB6DM-6X7HP
Windows Server 2008 數據中心 7M67G-PC374-GR742-YH8V4-TCBY3
沒有 Hyper-V 的 Windows Server 2008 數據中心 22XQ2-VRXRG-P8D42-K34TD-G3QQC
Windows Server 2008（用於基於 Itanium 的系統） 4DWFP-JF3DJ-B7DTH-78FJB-PDRHK
Windows Server 2016
操作系統版本 KMS 客戶端設置密鑰

Windows Server 2016 數據中心 CB7KF-BWN84-R7R2Y-793K2-8XDDG
Windows Server 2016 標準 WC2BQ-8NRM3-FDDYY-2BFGV-KHKQY
Windows Server 2016 Essentials JCKRF-N37P4-C2D82-9YXRT-4M63B
Office 2016
操作系統版本 KMS 客戶端設置密鑰

Office Professional Plus 2016 - XQNVK-8JYDB-WJ9W3-YJ8YR-WFG99
Office Standard 2016 - JNRGM-WHDWX-FJJG3-K47QV-DRTFM
Project Professional 2016 - YG9NW-3K39V-2T3HJ-93F3Q-G83KT
Project Standard 2016 - GNFHQ-F6YQM-KQDGJ-327XX-KQBVC
Visio Professional 2016 - PD3PC-RHNGV-FXJ29-8JK7D-RJRJK
Visio Standard 2016 - 7WHWN-4T7MP-G96JF-G33KR-W8GF4
Access 2016 - GNH9Y-D2J4T-FJHGG-QRVH7-QPFDW
Excel 2016 - 9C2PK-NWTVB-JMPW8-BFT28-7FTBF
OneNote 2016 - DR92N-9HTF2-97XKM-XW2WJ-XW3J6
Outlook 2016 - R69KK-NTPKF-7M3Q4-QYBHW-6MT9B
PowerPoint 2016 - J7MQP-HNJ4Y-WJ7YM-PFYGF-BY6C6
Publisher 2016 - F47MM-N3XJP-TQXJ9-BP99D-8K837
Skype for Business 2016 - 869NQ-FJ69K-466HW-QYCP2-DDBV6
Word 2016 - WXY84-JN2Q9-RBCCQ-3Q3J3-3PFJ6
MS Office edition    XQNVK-8JYDB-WJ9W3-YJ8YR-WFG99
Office 2013
官網KMS列表

https://technet.microsoft.com/en-us/library/dn385360.aspx
操作系統版本 KMS 客戶端設置密鑰

Office 2013 Professional Plus  YC7DK-G2NP3-2QQC3-J6H88-GVGXT
Office 2013 Standard KBKQT-2NMXY-JJWGP-M62JB-92CD4
Project 2013 Professional  FN8TT-7WMH6-2D4X9-M337T-2342K
Project 2013 Standard  6NTH3-CW976-3G3Y2-JK3TX-8QHTT
Visio 2013 Professional  C2FG9-N6J68-H8BTJ-BW3QX-RM3B3
Visio 2013 Standard  J484Y-4NKBF-W2HMG-DBMJC-PGWR7
Access 2013  NG2JY-H4JBT-HQXYP-78QH9-4JM2D
Excel 2013  VGPNG-Y7HQW-9RHP7-TKPV3-BG7GB
InfoPath 2013  DKT8B-N7VXH-D963P-Q4PHY-F8894
Lync 2013  2MG3G-3BNTT-3MFW9-KDQW3-TCK7R
OneNote 2013  TGN6P-8MMBC-37P2F-XHXXK-P34VW
Outlook 2013  QPN8Q-BJBTJ-334K3-93TGY-2PMBT
PowerPoint 2013  4NT99-8RJFH-Q2VDH-KYG2C-4RD4F
Publisher 2013  PN2WF-29XG2-T9HJ7-JQPJR-FCXK4
Word 2013  6Q7VD-NX8JD-WJ2VH-88V73-4GBJ7
office 2010
官網KMS列表

https://technet.microsoft.com/en-us/library/ee624355(v=office.14).aspx#section2_3
操作系統版本 KMS 客戶端設置密鑰

Office Professional Plus 2010  VYBBJ-TRJPB-QFQRF-QFT4D-H3GVB
Office Standard 2010  V7QKV-4XVVR-XYV4D-F7DFM-8R6BM
Access 2010  V7Y44-9T38C-R2VJK-666HK-T7DDX
Excel 2010  H62QG-HXVKF-PP4HP-66KMR-CW9BM
SharePoint Workspace 2010  QYYW6-QP4CB-MBV6G-HYMCJ-4T3J4
InfoPath 2010  K96W8-67RPQ-62T9Y-J8FQJ-BT37T
OneNote 2010  Q4Y4M-RHWJM-PY37F-MTKWH-D3XHX
Outlook 2010  7YDC2-CWM8M-RRTJC-8MDVC-X3DWQ
PowerPoint 2010  RC8FX-88JRY-3PF7C-X8P67-P4VTT
Project Professional 2010  YGX6F-PGV49-PGW3J-9BTGG-VHKC6
Project Standard 2010  4HP3K-88W3F-W2K3D-6677X-F9PGB
Publisher 2010  BFK7F-9MYHM-V68C7-DRQ66-83YTP
Word 2010  HVHB3-C6FV7-KQX9W-YQG79-CRY7T
Visio Premium 2010  D9DWC-HPYVV-JGF4P-BTWQB-WX8BJ
Visio Professional 2010  7MCW8-VRQVK-G677T-PDJCM-Q8TCP
Visio Standard 2010  767HD-QGMWX-8QTDB-9G3R2-KHFGJ
```

> ## Vlmcsd-最小巧最简单最安全的KMS模拟器，支持Win10和Office2016！

{% embed url="https://www.zhaoyuguo.com/298.html" %}

