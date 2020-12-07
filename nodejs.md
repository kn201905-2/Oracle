# Ubuntu に nodejs をインストールする
* `# apt install nodejs` とすると、古いバージョンがインストールされる（2020-12 時点では、v8.10）  
* n package を利用して、以下のようにインストールする
```
まず、n package を動かすために、古いものでいいので nodejs をインストールする
# apt install nodejs npm
# npm install n -g

安定バージョンをインストール（新しい nodejs は、/usr/local/bin にインストールされる）
# n stable

新しい nodejs がインストールされたため、古いものは削除
# apt purge -y nodejs npm

環境変数 node を更新するため、シェルを再起動
# exec $SHELL -l

バージョン確認
# node -v

（参考）
以下のようにすると、任意のバージョンに切り替えることができる
# n 9.11.2
```
