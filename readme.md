# ネットワークの調整（ファイアウォール）
* インスタンスの画面から、サブネットをクリック -> セキュリティ・リスト をクリック
* イングレス・ルール と エグレス・ルール を設定

# RLogin で SSH接続後
```
$ sudo su -
# apt update
# apt upgrade
# apt install vim
```
---
# iptables で通信ポートの調整

```
（参考）
https://www.virment.com/how-to-change-port-for-ssh-in-oracle-cloud/

* /etc/iptables/rules.v4 を直接書き換えるのが簡単
* 以下を実行して、iptablesを再読込して完了
/etc/init.d/netfilter-persistent reload

* 現在は、以下の２つのみを設定
-A INPUT -s '自分のアドレス' -j ACCEPT
-A OUTPUT -p tcp --dport 80 -j ACCEPT   # proxy の監視用
-A OUTPUT -d '自分のアドレス' -j ACCEPT   # 下りの通信量の監視用
-A OUTPUT -j ACCEPT   # 不要な通信がないかの監視用
```

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
