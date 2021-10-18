Lab 4 - TCP Load Balancing / Routing
######################################################

このラボのゴールは、L4 / TCPロードバランサを設定することです。
すべてのトラフィックがHTTPではなく、HTTPトラフィックを操作する方法は多くありますが、ときにはTCP/UDPトラフィックの操作をしなければいけません

.. IMPORTANT::
    想定時間: 5分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 

App Componentを開く
-------------------------

#. Chromeを開きます
#. BookmarkからNGINX ControllerのGUIを開きます

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

#. **Services** セクションを開いてください

   .. image:: ../media/Tile-Services.png
      :width: 200

#. "Apps" を選択してください

   .. image:: ../media/Services-Apps.png
      :width: 200

#. "Echo Environment"から Module 2 Lab 1 で作成した **echoapp** を選択してください

   .. image:: ./media/M2L3echoapp.png
      :width: 200

TCP Component を作成する
----------------------

#. echoapp を利用します: 右上の "Create Component" をクリックし、"Components" セクションを選択してください

   .. image:: ./media/M2L1CreateComponent.png
      :width: 800

#. 各項目を埋め、ドロップダウンリストから **Gateway Refs** を適切に選択してください

   +-------------------------+--------------------------+
   |        Field            |      Value               |
   +=========================+==========================+
   |  Component Type         | TCP/UDP                  |
   +-------------------------+--------------------------+
   |  Name                   | ``echoapptcp``           |
   +-------------------------+--------------------------+
   |  Gateway Refs           | ``echoappgw``            |
   +-------------------------+--------------------------+

   .. image:: ./media/M2L4CompDiag.png
      :width: 700

#. **URIs** ダイアログを開き、URI ``tcp://*:9443`` を追加してください

   .. image:: ./media/M2L4CompURI.png
      :width: 700

#. **Workload Groups** ダイアログを開、各項目を埋めてください

   +-------------------------+-----------------------------+
   |        Field            |      Value                  |
   +=========================+=============================+
   |  Name                   | ``TCP Backend``             |
   +-------------------------+-----------------------------+
   |  Backend Workload URIs  | ``tcp://10.1.20.21:8000``   |
   +-------------------------+-----------------------------+
   |  Backend Workload URIs  | ``tcp://10.1.20.11:8000``   |
   +-------------------------+-----------------------------+

   .. image:: ./media/M2L4WGdiag.png
      :width: 600

#. 操作を完了するため **Submit** をクリックしてください

   .. image:: ../media/Submit.png
      :width: 100

TCP Componentをテストします
^^^^^^^^^^^^^^^^^^^^^^
#. ``jumphost-1`` のChromeで新しいタブを開き、 "Developer Tools" を有効にしてください

   .. image:: ./media/M2L1DevTools.png
      :width: 900

#. 先程新たに作成したURL(``http://echoapp.net:9443`` )を開き、TCP設定で "echo" アプリケーションがどのように動作しているか確認してください
   **echoapp.net** にアクセスしたリクエストを選択し、表示結果を確認してください

   .. NOTE::
      これはHTTP Requestの情報を返す、シンプルなWebアプリケーションです

   .. image:: ./media/M2L1DevTools2.png
      :width: 800 

#. 同じURLのHTTPSページ(``https://echoapp.net:9443`` )を開き、TCP設定で "echo" アプリケーションがどのように動作しているか確認してください
   閲覧の結果、トラフィックはブロックされます
   もし、TCPトラフィックを暗号化したい場合、証明書を設定し、URLを指定する項目で ``tcp`` とした項目を ``tcp+tls`` とすることで、バックエンド転送前にゲートウェイでHTTPSトラフィックのSSL Offloadを実現することが可能です

   .. image:: ./media/M2L4DevTools2.png
      :width: 400 


追加情報
--------------------

"TCP/UDP" コンポーネントは、L4 / Stream Proxyを提供します。
これらの機能は NGINXの `stream`_ モジュールを利用しています。詳細についてはドキュメントを参照してください

.. _stream: http://nginx.org/en/docs/stream/ngx_stream_core_module.html
