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
* 以前は、rules.v4 を直接書き換えていたが、今は「ip4_oracle.sh」を用いて変更するようにした。

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
```
# vim sshd_config

「#Port 22」を変更する
```

* 再起動（再起動は、以下の URL を用いて行った）
```
https://console.ap-osaka-1.oraclecloud.com/compute/instances
```
---
# iptables に関する syslog を分離する  
* rsyslog が入っているかの確認
```
# systemctl status rsyslog

もし、rsyslog が入っていなければ
# apt install rsyslog
```

* iptables のログ出力は syslog が担っているため、syslog の出力にフィルタを掛ける。  
/etc/rsyslog.d/ にフィルタを記述したファイルを置けば、フィルタが掛かるようになる。

\# vim /etc/rsyslog.d/10-my-iptables.conf
```
:msg,contains,"Drp_" -/var/log/iptables.log
& ~
```
ファイル指定の前の「-」は、ログ出力時のディスクとの同期の抑制を示す。（パフォーマンス向上が狙える）  
「& ~」は、対象としているログを廃棄する、ということを示す。  
```
& は、１つのセレクタに複数のアクションを指定するときに利用する。
FILTER ACTION
& ACTION
& ACTION

~ は、FILTER されたパケットを破棄する。
```

* rsyslog を再起動　# systemctl restart rsyslog  

参考URL：  
https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-basic_configuration_of_rsyslog  
https://vogel.at.webry.info/201311/article_4.html  

---
# ログローテーション（うまくいかなかったため保留中）  
* /etc/logrotate.d に設定ファイルを作成することにより、ローテートを行う  
2019-10-20　ローテーションの設定ファイルは、「/etc/logrotate.d/rsyslog」を参考にして作成した。設定ファイルの書き方に関するネットでの情報は、Linux のディストリビューションや、バージョンごとの差異があり不明な点が多かったため、現時点では、現OSが走っている設定ファイルを真似することにした。  

\# touch /etc/logrotate.d/iptables  
\# vim /etc/logrotate.d/iptables  

> /var/log/iptables.log  
> {  
> 　missingok  
> 　notifempty  
> 　daily  
> 　rotate 15  
> 　create 766  
> 　dateext  
> 　dateformat _%Y-%m-%d  
> 　postrotate  
>　　systemctl kill -s HUP rsyslog.service  
> 　endscript  
> 　su syslog adm  
> }  

* 設定ファイルの動作確認　# logrotate -d /etc/logrotate.d/iptables  
「-d」は dry run を表す。実際には実行せずに、動作確認だけを行う。  

---
# code-server v3 のインストール（v1 も v3 も重いような、、）
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
* 「Ctrl + Shift + p」でコマンドパレットを開き、「setting.json」を選択し、適切に設定する

---
# ~~code-server v1 のインストール~~（v1 も重かった、、）
```
# wget https://github.com/cdr/code-server/releases/download/1.1156-vsc1.33.1/code-server1.1156-vsc1.33.1-linux-x64.tar.gz  
# tar xvzf code-server1.1156-vsc1.33.1-linux-x64.tar.gz（z は gzip の指定）
```
* 以上で code-server は利用可能となる。（tar で展開すると、展開したフォルダの中に「code-server」という実行ファイルがある。それを実行すれば、code-server が立ち上がる。） 今回は、以下のような起動用バッチファイルを作成した。  
* 以下のバッチファイルを起動したフォルダが、エディットできるフォルダのルートとなる。
```
#!/bin/bash
code-server1.1156-vsc1.33.1-linux-x64/code-server -p 3010 -N

・-p でリスンするポート番号を指定
・-N でパスワード無しでログイン可能となる
・その他のオプションに関しては、code-server --help で調べると良い
```
* settings.json を、以下の位置にコピー
```
~/.local/share/code-server/User
```

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

---
# コマンドプロンプトの変更
* 以下を、~/.bashrc に追記  
一時的なお試しなら、`export PS1=""`
 
 ```
 PS1="\[\e[1;32m\][OCI-proxy: \w]\[\e[0m\]\n\\$ "
 
 色の変更は \[\e[ と m\] で囲む（非表示文字のエスケープシーケンスが \[, \]。エスケープシーケンスの出力が \e）
 緑色： 32、黄色： 33、青色： 34、パープル： 35、シアン： 36、白色: 37
 ```
参考 URL：  
https://www.atmarkit.co.jp/flinux/rensai/linuxtips/002cngprmpt.html  
https://shio-ax.hatenablog.com/entry/2019/05/27/174018  

---
# タイムゾーンの変更
* 通常は `# ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime` で良いが、そもそも `Asia/Tokyo` の情報がインストールされていなかったため、以下で変更。
```
# apt install tzdata
```

---
# Docker のインストール
```
https://docs.docker.com/engine/install/ubuntu/

# apt update

# apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common

Docker の公式 GPG鍵のインストール
# curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

公開鍵の確認
# apt-key fingerprint 0EBFCD88

公式リポジトリの追加
# add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# apt update

# apt install docker-ce docker-ce-cli containerd.io

インストールが成功したことの確認
# docker run hello-world
```

* アンインストールの方法
```
# apt purge docker-ce docker-ce-cli containerd.io

# rm -rf /var/lib/docker
```

---
# Docker Compose のインストール
```
https://docs.docker.com/compose/install/

# curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# chmod +x /usr/local/bin/docker-compose

インストールの確認
# docker-compose --version
```
