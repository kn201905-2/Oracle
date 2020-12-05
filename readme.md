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

---
# code-server のインストール
参考URL:  
https://github.com/cdr/code-server  
https://github.com/cdr/code-server/blob/v3.7.4/doc/install.md
```
# curl -fsSL https://code-server.dev/install.sh | sh
...
To have systemd start code-server now and restart on boot:
  sudo systemctl enable --now code-server@$USER
Or, if you don't want/need a background service you can run:
  code-server
```
* 設定ファイルの書き換え
```
# cd ~/.config/code-server
# vim config.yaml

bind-addr: 0.0.0.0:****
（auth: none）
```
* code-server の起動
```
# code-server
```
* 右下の設定アイコンから「Color Theme」を選択 -> Dark テーマに変更  
* 「Ctrl + p」でコマンドパレットを開き、「setting.json」を選択し、適切に設定する


---
# プロキシ（squid）の設定
```
# apt install squid
# systemctl status squid
```
* vim で http_access を検索し（ノーマルモードで /http_access + リターン。その後、「n」or「N」で検索）

```
設定ファイルは /etc/squid/squid.conf
以下のみに。それ以外は、コメントアウト
http_access allow all
```
* vim で http_port を検索し、http プロキシのポート番号を変更
```
http_port 3128 を変更
```
* 以上の設定で、とりあえずはプロキシで接続可能となる


