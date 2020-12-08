```
# apt update
# apt upgrade

# apt install -y iputils-ping net-tools
# apt install -y vim

# vim .bashrc

PS1="\[\e[1;33m\][OCI-VM-docker: \w]\[\e[0m\]\n\\$ "

# apt install -y curl
```

---
# nodejs をインストールする
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
---
```
* chatbot フォルダは、host と共有の位置にあるものを。
# npx create-react-app chatbot

# vim package.json

"start": "PORT=80 react-scripts start",

# npm install --save @material-ui/core @material-ui/icons @material-ui/system

/public/index.html に、以下を追加
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700&display=swap" />
<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons" />
```

---
# firebase への CUI によるログイン
```
# npm install -g firebase-tools

# npm install --save firebase

# firebase login --no-localhost

自分のブラウザで開くと、authorization code が発行される。
このコードを貼り付ければ、login できる。

# firebase init

・Firestore、Functions、Hosting の３つを選択
・Use an existing project を選択
・既存のプロジェクトを選択
・firesotre.rules を選択
・firestore.indexes.json を選択
・Typescript を選択
・ESLint はどちらでも
・dependency -> yes

（重要）
・public directory -> build を入力
```


