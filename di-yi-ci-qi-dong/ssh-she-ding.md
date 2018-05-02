# SSH 設定

> ## 設定金鑰登入

* 建立 ~/.ssh 目錄

```text
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

* 產生金鑰

```text
cd ~/.ssh
ssh-keygen
```

* 將公開金鑰寫入 ~/.ssh/authorized\_keys

```text
cat super.pub >> authorized_keys
```

> ## 只允許金鑰登入，不允許密碼登入

* 修改 /etc/ssh/sshd\_config 設定檔

```text
PasswordAuthentication no
PubkeyAuthentication yes
```

> 改 SSH Port

* 修改 nano /etc/ssh/sshd\_config

```text
Port 443
```

