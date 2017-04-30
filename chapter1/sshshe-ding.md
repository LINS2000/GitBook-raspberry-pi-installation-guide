# SSH 設定

---

> ##### 設定金鑰登入

* 建立 ~/.ssh 目錄

```
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

* 將公開金鑰寫入 ~/.ssh/authorized\_keys

```
cat super.pub >> authorized_keys
```

> ##### 只允許金鑰登入，不允許密碼登入

* 修改 /etc/ssh/sshd\_config 設定檔

```
PasswordAuthentication no
PubkeyAuthentication yes
```



