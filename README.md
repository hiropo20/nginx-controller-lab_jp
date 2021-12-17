NGINX Controller Lab Guide
==========================

- [NGINX Original Lab Guide](https://github.com/nginxinc/nginx-controller-lab)
- [NGINX Controller Document](https://docs.nginx.com//nginx-controller/)

Introduction
------------

このレポジトリはNGINXが管理するNGINX Controller 3.x UDF向けドキュメントの日本語版です。最新の内容はOriginal Lab Guideを参照してください。

Release
------------

|Date||
|----|----|
|2021/10/19| NGINX Plusインスタンス追加手順追加。その他アップデート|
|2021/10/18| 1.0.57の内容を日本語化。本レポジトリはこの内容を基準とします|

本Lab Guide HTML 生成 Container の実行方法
-----

1. Download または `git clone` により 本 nginx-controller-lab のファイルを取得してください
1. Docker CE (https://docs.docker.com/engine/installation/) をダウンロード＆インストール
1. `./containthedocs-build.sh` を用いてサンプルドキュメントを作成。 初回作成時にはDocker Hubよりコンテナイメージ(~1GB)がダウンロードされます
1. `docs/_build/html/index.html` のファイルが生成されます。生成された内容をWebブラウザで開いてください

本Lab Guide 表示用 Container の実行
-----

`docker-composeを利用しますので、予めホストにセットアップください`

1. `ls ./docs/_build/html/index.html` を実行し、正しく `index.html` が生成されていることを確認してください
2. `docker-compose up -d` で guide 用コンテナを起動してください
3. `Port 80` でサービスを公開します。正しくアクセスできることを確認してください

本Lab Guide 表示内容の更新
-----

1. `git pull` を実行し、内容を更新
2. `./containthedocs-build.sh` を実行し、コンテンツをHTMLに変換
3. `docker-compose restart` を実行し、更新内容をコンテナに反映


コンテナの停止
1. コンテナを停止する場合、`docker-compose down` を実行してください

Build Scripts (Reffer to Original Lab Guide)
-------------

以下のコマンド群が管理されています

- `containthedocs-bash.sh`: BASH Scriptと共にコンテナを実行する
- `containthedocs-build.sh`: `make -C docs html` コマンドを使い、 `docs/_build/html` にドキュメントを生成する
- `containthedocs-clean.sh`: 作成してディレクトリを `make -C docs clean` コマンドを使い削除する
- `containthedocs-cleanbuild.sh`: 作成したディレクトリとHTLM Docsを `make -C docs clean html` コマンドを使い削除する
- `containthedocs-convert.sh`: `.docx` ファイルを rST にコンバートする
- `containthedocs-pdf.sh`: PDF ドキュメントを `make -C docs latexpdf` コマンドを用いて `docs/_build/latex` に作成する
