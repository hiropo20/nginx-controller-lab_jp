Lab 1 - GUIで ADC App の作成
#######################################

このラボのゴールはラボを実施する方がADCに関するNGINX Controllerのオブジェクトモデルやコンセプトを理解いただくことです。
NGINX Controllerを用いた「app-centric(アプリケーション中心)」のモデルは、いわゆる旧来のLBで実施していた「network-centric(ネットワーク中心)」モデルとは別のデザインであり、NGINXの設定・構成をベースにしたデザインとなります

.. IMPORTANT::
    想定時間: 15分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 


NGINX Controller が持つオブジェクトのコンセプト
---------------------------------
このセクションではNGINX Controllerのオブジェクトについて説明します。BIG_IPやNGINXのコンセプトや記述方法と比較してください

   .. image:: ./media/L1Controller-ObjectConcept.png
      :width: 600

Environments
^^^^^^^^^^^^

"Environment"は論理的なAppのグループです。これはRBAC(Role Based Access Control)の最上位の構成要素として利用されます
Environmentsは組織や構成管理の観点で利用されるものであり、実際に管理対象となるNGINX Plusインスタンスにデプロイされるコンフィグ上には表現されません。
概念的には、BIG-IPのパーティションに似たものとなります。

Cert
^^^^

SSL証明書と鍵をNGINX Controllerで一元的に管理・保存します。
これらは"Gateway"の機能に割り当てることが可能であり、設定に応じて利用します。

Gateway
^^^^^^^

"Gateway"は対象となるNGINX PlusインスタンスにデプロイするNGINX Config / Directiveに相当します
この機能では、待ち受けるFQDN、許可するHTTPメソッド、SSL/TLS設定、その他様々なHTTP Parameter(buffer , body size , TCP keepalive)が含まれます。
これらの設定は、対象となるNGINX Configの"server" directive配下に表記される情報となります。
BIG-IPのTCP / HTTP / ClientSSL Profile等に相当する設定です

App
^^^

"App"はマイクロサービス環境で動作する *すべての* アプリケーションの要素を論理的にまとめたグループです。
"App"に含まれるそれぞれの"Component"が各マイクロサービスを示します。
これらは論理的なグループを示しており、NGINX Plusインスタンスの設定情報を示すものではありません。
BIG-IPファミリのコンセプトでは、AS3のテナントや、BIG-IQの"Application"に相当します。

Component
^^^^^^^^^

"Component"は単一のサービスを示す最も基本的な表現です。これは通信を待ち受けるためのURIなどの情報を含み、分散先サーバである"Workload Group"等を管理し、
トラフィックの転送先、HTTPの操作に関する情報(redirect, rewrite, header 操作)、ロギング設定などを指定します
BIG-IPのVS、Poolや、HTTP Profile、Local Traffic PolicyやiRuleの一部の機能に該当します


Workload Group
^^^^^^^^^^^^^^

"Workload Group"はバックエンドサーバをまとめたグループです。NGINX Configでは"upstream"でこの内容を記述します。
BIG-IPの"pool"および個々の"pool member"に相当します

Applicationをデプロイする
-----------------------
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

#. **Services** を開いてください。このセクションおよび配下の項目がこのラボで必要となる設定を作成するために利用します

   .. image:: ../media/Tile-Services.png
      :width: 200

Environmentを作成する
^^^^^^^^^^^^^^^^^^^^^^

#. "Environments" を選択してください.

   .. image:: ./media/M2L1EnvTile.png
      :width: 200

#. 右上にある "Create" ボタンをクリックしてください

   .. image:: ./media/M2L1EnvCreate.png
      :width: 700

#. 以下の通り項目を埋めてください

   +---------------------+------------------------------+
   |        Field        |      Value                   |
   +=====================+==============================+
   |  Name               |  ``echo``                    |
   +---------------------+------------------------------+
   |  Display Name       | ``Echo Environment``         |
   +---------------------+------------------------------+

   .. image:: ./media/M2L1EnvDialogue.png
      :width: 700

#. **Submit** をクリックし、操作を完了させてください

   .. image:: ../media/Submit.png
      :width: 100


証明書の追加
^^^^^^^^^^^^^^^^^

#. "Certs" を選択してください

   .. image:: ./media/M2L1Certs.png
      :width: 200

#. 右上にある "Create" ボタンをクリックしてください

   .. image:: ./media/M2L1CertCreate.png
      :width: 800

#. 以下の通り項目を埋め、適切な **Environment** をドロップダウンリストから選択してください 

   +-----------------+----------------------------------+
   |        Field    |      Value                       |
   +=================+==================================+
   |  Name           |  ``echoapp.net``                 |
   +-----------------+----------------------------------+
   |  Environment    | ``Echo Environment``             |
   +-----------------+----------------------------------+

   .. image:: ./media/M2L1CertDialogue1.png
      :width: 700

#. **Import PEM or PKC12** ラジオボタンを選択し、**Browse** から証明書と鍵を選択します

   .. image:: ./media/M2L1CertDialogue2.png
      :width: 700

   証明書 (**echoapp.net.crt**) 鍵 (**echoapp.net.key**) をポップアップで表示される内容から選択してください ( **This PC -> Documents -> Certs** ) 

   .. NOTE::
      証明書と鍵はそれぞれアップロードをしてください。NGINX Controllerは複数のファイルアップロードに対応していません

   .. image:: ./media/M2L1Cert&Key.png
      :width: 700

#. **Submit** をクリックし、操作を完了させてください

   .. image:: ../media/Submit.png
      :width: 100


Gatewayの作成
^^^^^^^^^^^^^^^^^

#. "Gateways" を選択してください

   .. image:: ./media/M2L1GatewayTile.png
      :width: 200

#. 右上にある "Create" ボタンをクリックしてください

   .. image:: ./media/M2L1GWcreate.png
      :width: 600

#. **Configuration** セクションの内容を以下の通り項目を埋めてください。入力後、**Next** をクリックするか、次のセクションの名称をクリックしてください

   +---------------------+----------------------------------+
   |        Field        |      Value                       |
   +=====================+==================================+
   |  Name               |  ``echoappgw``                   |
   +---------------------+----------------------------------+
   |  Environment        | ``Echo Environment``             |
   +---------------------+----------------------------------+

   .. image:: ./media/M2L1GWDialogue.png
      :width: 600

#. **Placements** セクション配下のInstance Ref で "Development NGINX West 03 (CAS)” を選択してください

   .. image:: ./media/M2L1Place.png
      :width: 700

#. **Hostnames** セクション配下で、指定のホスト名を追加してください(``http://echoapp.net``, ``https://echoapp.net``). それぞれのホスト名で、 **Match Method** は指定しないでください。"Cert Reference"で **echoapp.net** を選択してください。ホスト名の追加操作が完了した場合、正しくそれぞれのメニュー右下部の"Done"をクリックしてください
   
   .. NOTE::
      You will need to use the **Add Hostname** link pictured below to add multiple hostnames.

   .. image:: ./media/M2L1Hostnames.png
      :width: 700

#. **Submit** をクリックし、操作を完了させてください

   .. image:: ../media/Submit.png
      :width: 100

Appを作成する
^^^^^^^^^^^^^

#. "Apps" を選択してください

   .. image:: ../media/Services-Apps.png
      :width: 200

#. 右上にある "Create" ボタンをクリックしてください

   .. image:: ./media/M2L1AppsCreate.png
      :width: 600

#. 以下の通り項目を埋め、ドロップダウンリストから **Environment** を選択してください

   +---------------------+------------------------------+
   |        Field        |      Value                   |
   +=====================+==============================+
   |  Name               |  ``echoapp``                 |
   +---------------------+------------------------------+
   |  Environment        | ``Echo Environment``         |
   +---------------------+------------------------------+

   .. image:: ./media/M2L1Appdiag.png
      :width: 800

#. **Submit** をクリックし、操作を完了させてください

   .. image:: ../media/Submit.png
      :width: 100

Componentを作成する
^^^^^^^^^^^^^^^^^^^

#. "Components" セクションを選択し、画面中央の "Create Component" をクリックしてください

   .. image:: ./media/M2L1CreateComponent.png
      :width: 800

#. 以下の通り項目を埋め、ドロップダウンリストから **Gateway Refs** を選択してください

   +-------------------------+--------------------------+
   |        Field            |      Value               |
   +=========================+==========================+
   |  Name                   | ``echoappcomponent``     |
   +-------------------------+--------------------------+
   |  Gateway Refs           | ``echoappgw``            |
   +-------------------------+--------------------------+

   .. image:: ./media/M2L1CompDiag.png
      :width: 700

#. **URIs** のセクションを開き、URIに ``/`` を指定します。**Match Method** は指定しないでください/

   .. image:: ./media/M2L1CompURI.png
      :width: 700

#. **Workload Groups** のセクションを開き、以下の通り項目を埋めてください。Backend URIの指定、Workload Group双方の操作が完了した場合、正しくメニュー右下部の"Done"をクリックしてください

   +-------------------------+-----------------------------+
   |        Field            |      Value                  |
   +=========================+=============================+
   |  Name                   | ``Echo Backend``            |
   +-------------------------+-----------------------------+
   |  Backend Workload URIs  | ``http://10.1.20.11:8000``  |
   +-------------------------+-----------------------------+

   .. image:: ./media/M2L1WGdiag.png
      :width: 600

#. **Submit** をクリックし、操作を完了させてください

   .. image:: ../media/Submit.png
      :width: 100

Echo Applicationのテスト
^^^^^^^^^^^^^^^^^^^^^^^^^^

#. ``jumphost-1`` のクロームで新しいタブを開き、"Developer Tools"を有効にしてください 

   .. image:: ./media/M2L1DevTools.png
      :width: 900

#. 先程の手順で作成したURLに対し (``http://echoapp.net`` and ``https://echoapp.net``) ブラウザからアクセスし、Echo Applicationが機能していることを確認してください。結果の確認のため **echoapp.net** をURLに入力いただくことで、結果の確認が可能です

   .. NOTE::
      これはHTTP Requestの情報を返す、シンプルなWebアプリケーションです
      
   .. image:: ./media/M2L1DevTools2.png
      :width: 800 
