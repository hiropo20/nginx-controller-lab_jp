NGINX Controller Lab Guide
==========================

[NGINX Original Lab Guide](https://github.com/nginxinc/nginx-controller-lab)
[NGINX Controller Document](https://docs.nginx.com//nginx-controller/)

Introduction
------------

このレポジトリはNGINXが管理するNGINX Controller 3.x UDF向けドキュメントの日本語版です。最新の内容はOriginal Lab Guideを参照してください。

Release
------------

|Date||
|----|----|
|2021/10/18| 1.0.57の内容を日本語化。本レポジトリはこの内容を基準とします|


本Lab Guide 表示用 Container Setup 方法
-----

1. Download または `git clone` により 本 nginx-controller-lab のファイルを取得してください
1. Docker CE (https://docs.docker.com/engine/installation/) をダウンロード＆インストール
1. `./containthedocs-build.sh` を用いてサンプルドキュメントを作成。 初回作成時にはDocker Hubよりコンテナイメージ(~1GB)がダウンロードされます
1. `docs/_build/html/index.html` の内容をWebブラウザで開いてください


Build Scripts (Reffer to Original Lab Guide)
-------------

以下のコマンド群が管理されています

- `containthedocs-bash.sh`: BASH Scriptと共にコンテナを実行する
- `containthedocs-build.sh`: `make -C docs html` コマンドを使い、 `docs/_build/html` にドキュメントを生成する
- `containthedocs-clean.sh`: 作成してディレクトリを `make -C docs clean` コマンドを使い削除する
- `containthedocs-cleanbuild.sh`: 作成したディレクトリとHTLM Docsを `make -C docs clean html` コマンドを使い削除する
- `containthedocs-convert.sh`: `.docx` ファイルを rST にコンバートする
- `containthedocs-pdf.sh`: PDF ドキュメントを `make -C docs latexpdf` コマンドを用いて `docs/_build/latex` に作成する
