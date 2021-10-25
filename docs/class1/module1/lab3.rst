Lab 3 - NGINX Plus の追加
############################################

このラボのゴールは新たにNGINX Controllerの管理対象としてNGINX Plusを追加することです。
NGINX Controllerで管理する対象となるNGINX Plusは通常のNGINX Plus Subscriptionと異なる手順でライセンスキーの取得を行います。

.. IMPORTANT::
    想定時間: 15分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 

NGINX Plus のインストール
----

#. "nginxplus-4" インスタンスにログインしてください。"PuTTY" を開き、保存済みのホストより **nginxplus-4** を選択し、**Open** をクリックしてください

   .. image:: ./media/L3Putty.png
      :width: 400

   .. IMPORTANT::
      もし、Puttyがサーバのホスト鍵に関する警告を示した場合、接続のため **Yes** をクリックしてください
      これは、ラボ環境の各ホストでユニークなhost keyを生成するため生じるものです

#. Curl コマンドを実行し、NGINX Controllerのログインに必要なCookie情報を取得します。ログインユーザとしてAdmin権限の ``Peter Parker`` でログインします

   .. code-block:: bash
   
      $ curl -k -c cookie.txt -X POST --url 'https://10.1.1.5/api/v1/platform/login' --header 'Content-Type: application/json' --data '{"credentials": {"type": "ACTIVE_DIRECTORY","providerName":"ad-acmefinancial-net", "username": "peter@acmefinancial.net","password": "Peter123!@#"}}'
      $ curl -k -b cookie.txt -c cookie.txt -X GET --url 'https://10.1.1.5/api/v1/platform/login'

#. NGINX Plus インストールに利用する証明書と鍵を取得します

   .. code-block:: bash
   
      $ curl -k -b cookie.txt -c cookie.txt -X GET --url 'https://10.1.1.5/api/v1/platform/licenses/nginx-plus-licenses/controller-provided' --output nginx-plus-certs.tar.gz
      $ tar zxvf nginx-plus-certs.tar.gz
      $ ls nginx-repo.*

#. NGINX Plus インストールに必要となる手順を実施します。手順の各コマンドの役割は `NGINX Plusのインストール手順(Ubuntu)`_ を参照してください

   .. code-block:: bash
   
      $ sudo mkdir -p /etc/ssl/nginx
      $ sudo cp nginx-repo.* /etc/ssl/nginx/
      $ sudo wget https://cs.nginx.com/static/keys/nginx_signing.key && sudo apt-key add nginx_signing.key
      $ sudo wget https://cs.nginx.com/static/keys/app-protect-security-updates.key && sudo apt-key add app-protect-security-updates.key
      $ sudo apt-get install apt-transport-https lsb-release ca-certificates
      $ printf "deb https://pkgs.nginx.com/plus/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee /etc/apt/sources.list.d/nginx-plus.list
      $ printf "deb https://pkgs.nginx.com/app-protect/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee /etc/apt/sources.list.d/nginx-app-protect.list
      $ printf "deb https://pkgs.nginx.com/app-protect-security-updates/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee -a /etc/apt/sources.list.d/nginx-app-protect.list
      $ sudo wget -P /etc/apt/apt.conf.d https://cs.nginx.com/static/files/90pkgs-nginx
      $ sudo apt-get update


#. NGINX Plus をインストールします。NGINX Controller に対応したVersionとしてR24をインストールします。NGINX Controllerに対応するNGINX PlusのVersionは `Tech Spec`_ を確認してください

   .. code-block:: bash
   
      $ sudo apt-get install nginx-plus=24-2~focal
      $ nginx -v


NGINX PlusのインスタンスをNGINX Controllerに追加する
----

#. jumphostのChromeで開かれているNGINX Controllerの管理画面を操作します。証明書エラーが表示されている場合には適切に操作をして画面を開いてください

   .. image:: ../media/ControllerLogin.png
      :width: 400

#. もし開かれていない場合、Chromeブラウザを開いてください

#. BookmarkからNGINX Controller UIにアクセスしてください

   .. image:: ../media/ControllerBookmark.png
      :width: 600

#. NGINX Controller のadmin accountである、``Peter Parker`` でログインしてください

   +-------------------------+-----------------+
   |      Username           |    Password     |
   +=========================+=================+
   | peter@acmefinancial.net | ``Peter123!@#`` |
   +-------------------------+-----------------+

   .. image:: ../media/ControllerLogin-Peter.png
      :width: 400

#. 画面左上のナビゲーションバーを開き、ドロップダウンリストから **Infrastructure** を選択してください

   .. image:: ../media/Tile-Infrastructure.png
      :width: 200

#. 画面右上の **Create** ボタンをクリックしてください

   .. image:: ./media/L3Controller-Infra-Create.png
      :width: 800

#. ``Add an existing instance`` を選択し、"nginxplus-4" インスタンスを追加するため、項目に以下の内容を指定してください

   .. image:: ./media/L3Controller-CreateInstance1.png
      :width: 800
    
   +----------------------------------------------------------------+---------------------------------------------+
   |Field                                                           | Value                                       |
   +================================================================+=============================================+
   | Name                                                           | ``nginxplus-4``                             |
   +----------------------------------------------------------------+---------------------------------------------+
   | Location                                                       | ``West Coast Data Center (OTHER_LOCATION)`` |
   +----------------------------------------------------------------+---------------------------------------------+
   | Allow insecure server connections to NGINX Controller using TLS| Enable(Check)                               |
   +----------------------------------------------------------------+---------------------------------------------+

   .. image:: ./media/L3Controller-CreateInstance2.png
      :width: 800
      
#. ``Instructions`` に表示されるCURLコマンドの内容をコピーしてください。次のステップで利用します。コピーが完了しましたら ``Close`` をクリックして画面を閉じてください

   .. image:: ./media/L3Controller-CreateInstance3.png
      :width: 800

#. 前の手順で利用した "nginxplus-4" のターミナル、または "PuTTY" を起動し再度 **nginxplus-4** を開いてください。``Instructions`` からコピーしたcurlコマンドを実行してください。コマンドを実行するとプロンプトで実行を進めて良いか確認するプロンプトが複数回表示されます。内容を確認して **y** を入力してください。以下の内容が表示されれば正常に完了です

   .. image:: ./media/L3ControllerAgent-InstallComplete.png
      :width: 400

#. ChromeでNGINX Controllerの **Infrastructure** を開き、新たに "nginxplus-4" が追加されていることを確認してください。その他ステータスが正しく閲覧できることを確認してください

   .. image:: ./media/L3Controller-Infra-Instances1.png
      :width: 800
      
   .. image:: ./media/L3Controller-Infra-Instances2.png
      :width: 800

   .. image:: ./media/L3Controller-Infra-Instances3.png
      :width: 800


#. 表示名(Display Name)などを変更する場合は対象インスタンスをクリックし、 **Edit** をクリックしてください

   .. image:: ./media/L3Controller-Infra-Instances4.png
      :width: 800
      
.. _NGINX Plusのインストール手順(Ubuntu): https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-plus/#installing-nginx-plus-on-ubuntu
.. _Tech Spec: https://docs.nginx.com/nginx-controller/admin-guides/install/nginx-controller-tech-specs/#nginx-plus-instances
