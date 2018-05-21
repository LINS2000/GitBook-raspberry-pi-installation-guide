# crontab v.s. scp

scp指令在crontab排程執行不成功原因是：遠端ssh登入認證問題，解決方案是來源端的~/.ssh目錄必須有私鑰，並且把相對應公鑰寫入目的端的~/.ssh/authorized\_keys中。

```text
mkdir –p ~/.ssh
chmod 0700 ~/.ssh
ssh-keygen –t dsa –f ~/.ssh/id_dsa –P ''

#then copy ~/.ssh/id_dsa.pub to the backup server

#then do this in your backup server
cat id_dsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
```

