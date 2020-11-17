# RLogin で SSH接続後
```
$ sudo su -
# apt update
# apt upgrade
# apt install vim
```
---
# iptables で通信ポートの調整

---
# SSH ポート変更（iptables で、ポートを通信可能にしてから行うこと）
```
# cd /etc/ssh
# ls
# cp sshd_config sshd_config.old
# ls -la
```
* sshd_config の Port を変更（先頭付近にあるから、すぐに分かるはず）
* 再起動（再起動は、以下の URL を用いて行った）
```
https://console.ap-osaka-1.oraclecloud.com/compute/instances
```
