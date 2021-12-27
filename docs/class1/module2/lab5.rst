Lab 5 - 待ち受ける特定のアドレスを定義する
#######################################

このラボのゴールはGatewayで待ち受ける特定のIPアドレスの設定について理解することです。
NGINXインスタンスの特定のIPアドレスを使い制御することが望ましい場合が多々あります。
これはデータプレーンの冗長化や、NGINX plusインスタンスのIPアドレスを通じてトラフィックの管理をする際に有用です

.. IMPORTANT::
    想定時間: 5分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 


Gatewayの待ち受けるIPアドレスを定義する
-----------------------------------
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

#. **Services** セクションを開き、このラボではこちらのセキュションの項目を対象として設定を行います

   .. image:: ../media/Tile-Services.png
      :width: 200

Gatewayを作成する
^^^^^^^^^^^^^^^^^

#. "Gateways" を選択します

   .. image:: ./media/M2L1GatewayTile.png
      :width: 200

#. 右上の "Create" ボタンをクリックします

   .. image:: ./media/M2L1GWcreate.png
      :width: 600

#. **Configuration** に表示される項目に以下の内容を入力します。終了後 **Next** をクリックするか、次の項目名をクリックしてください

   +---------------------+----------------------------------+
   |        Field        |      Value                       |
   +=====================+==================================+
   |  Name               | ``specialapp``                   |
   +---------------------+----------------------------------+
   |  Environment        | ``Echo Environment``             |
   +---------------------+----------------------------------+

   .. image:: ./media/M2L5GWDialogue.png
      :width: 600

#. **Placements** で、``Development NGINX West 03 (CAS)`` を対象インスタンスとして選択します

   .. image:: ./media/M2L1Place.png
      :width: 700

#. **Placements** で、``10.1.20.213`` を待ち受けIPアドレスとして入力して下さい

   .. image:: ./media/M2L5Place.png
      :width: 700

   .. NOTE::
      これは "Development NGINX West 03 (CAS)" に予め設定されたSecondary IPアドレスです。この情報はNGINX Controller **Infrastructure** セクションのインスタンスの情報から確認いただけます

#. **Hostnames** の Hostname は空白としてください。
   これは、あなたが後にコンポーネントのURI設定でホスト名を指定すること、TCP や UDPのコンポーネントとしてIPアドレスに着信するすべてのトラフィックを扱うことを意図します

   .. image:: ./media/M2L5Hostnames.png
      :width: 700

#. Click **Submit** to complete.

   .. image:: ../media/Submit.png
      :width: 100

Component を作成する
^^^^^^^^^^^^^^^^^^^

#. echoapp を利用します: 右上の "Create Component" ボタンをクリックし、"Components" セクションを開きます

   .. image:: ./media/M2L5PlusCreateComponent.png
      :width: 200

#. 項目に以下の内容を入力し、ドロップダウンリストより **Gateway Refs** の内容を選択してください

   +-------------------------+--------------------------+
   |        Field            |      Value               |
   +=========================+==========================+
   |  Name                   | ``wildcard``             |
   +-------------------------+--------------------------+
   |  Gateway Refs           | ``specialapp``           |
   +-------------------------+--------------------------+

   .. image:: ./media/M2L5CompDiag.png
      :width: 700

#. **URIs** で、URI ``http://.*:8080`` を追加し、``REGEX`` を **Match Method** として選択してください

   .. image:: ./media/M2L5CompURI.png
      :width: 700

   .. NOTE::
      URIのPortが8080として定義されない場合、``ListenIP 10.1.20.213 on Port 80 conflicts with an existing gateway`` とエラーが出力されます
      同一のNGINX PlusインスタンスHTTP(port 80)やHTTPS(port 443)トラフィックを処理する別のGateway設定があり、設定したポートが唯一のポートでない場合にメッセージが出力されます。
      もしListen IPが定義されない場合、すべてのIPアドレスがGatewayで利用されます

#. **Workload Groups** で、以下の通り項目を埋めてください

   +-------------------------+-----------------------------+
   |        Field            |      Value                  |
   +=========================+=============================+
   |  Name                   | ``wildcard Backend``        |
   +-------------------------+-----------------------------+
   |  Backend Workload URIs  | ``http://10.1.20.21:8001``  |
   +-------------------------+-----------------------------+

   .. image:: ./media/M2L5WGdiag.png
      :width: 600

#. 作業を完了させるため **Submit** をクリックしてください

   .. image:: ../media/Submit.png
      :width: 100

Listen IP Component をテストする
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

#. ``jumphost-1`` のChromeで新しいタブを開き、 "Developer Tools" を有効にしてください

   .. image:: ./media/M2L1DevTools.png
      :width: 800

#. 先ほど作成したURI(``http://10.1.20.213:8080``)に接続し、specialapp GatewayでIPアドレスで待ち受けるよう設定した機能の動作を確認してください

   .. image:: ./media/M2L5DevTools2.png
      :width: 800 

   .. NOTE::
       ``.*`` の正規表現はURIに指定されたすべてのホスト名や、IPアドレスに該当することを示すルールとなります
