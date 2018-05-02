# 修改 Root Prompt Color

> 切換root身分，修改 ~/.bashrc

```text
sudo su
nano ~/.bashrc
```

* 檔案最後加一列

```text
export PS1="${debian_chroot:+($debian_chroot)}\[\033[01;33m\]\u@\h\[\033[00m\] \[\033[01;34m\]\w \$\[\033[00m\] "
```

* 存檔完成

