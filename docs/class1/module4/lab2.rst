Lab 2 - Application Security (API)
##################################

このラボのゴールは、NGINX Controller Application Security ModuleをAPIを通じて操作することです

.. IMPORTANT::
    想定時間: 5分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 


Alter Security Settings in API
------------------------------

あなたはNGINX ControllerのREST APIを通じてWAFを有効にしたり設定を変更したりする方法がわかりますか？
これを達成するのが、PostmanやRed Hat Ansibleなど構成管理ツールを用いた自動化です。
このラボでは、PostmanをWindows jumphostから操作し動作を確認します

Postmanを用いて、Component の Security Settings を更新する
----------------------------------------------------

#. jumphostで **Postman** を開きます。**NGINX Controller 3.x** Collection を開きます

   .. image:: ../media/PMcoll.png
      :width: 400


#. **Common Tasks**、 **User Logon** を開き **Login to Controller – Natasha Romanoff - AD** を選択します

   .. image:: ../media/PMcoll2.png
      :width: 400

#. Postman **Send** を選択します

   .. image:: ../media/PMsend1.png
      :width: 600

   .. NOTE::
      Controller responds with a "204 No Content" response and an authentication cookie. 

#. **Retail-Development Environment**、 **Application - trading** を開きます 
   **Application trading** サブフォルダを開き、**2) Create Component - main (CAS monitoring)** を選択します

   .. image:: ./media/PMTradingMainCASMonitoring.png
      :width: 400

#. Postmanのリクエストエリアにある **Body** をクリックしてください。PUT リクエストのペイロードを確認してください。
   JSONの ``desiredState``, ``security`` 配下のプロパティ値は前回のラボでデプロイした Component に関する内容であることが確認できます

   .. image:: ./media/PMTradingMainCASMonitoringBody.png

#. Postmaneで **Send** を選択します

   .. image:: ./media/PMTradingMainCASMonitoringSend.png
      :width: 800

   .. NOTE::
      NGINX Controllerは "eventual consistency model" に従います。APIはPostmanのリクエストに "202 Accepted" ステータスコードを返します。
      NGINX Controllerは現状動作し、意図した状態であることが確認できます
      
   .. image:: ./media/PMTradingMainCASMonitoringConfiguring.png
      :width: 600

コンポーネントの変更を確認する
------------------------

#. Chromeを開きます。NGINX Controllerに接続しているタブで以下手順に従ってログインします

#. BookmarkからNGINX Controller UIにアクセスしてください

   .. image:: ../media/ControllerBookmark.png
      :width: 600

#. NGINX Controllerの特権を持たないユーザである  ``Natasha Romanoff`` でログインしてください

+---------------------------+-------------------+
|      Username             |    Password       |
+===========================+===================+
| natasha@acmefinancial.net | ``Natasha123!@#`` |
+---------------------------+-------------------+

   .. image:: ../media/ControllerLogin-Natasha.png
        :width: 400

#. **Services** メニューを開いてください

    .. image:: ../media/Tile-Services.png
        :width: 200

#. **Apps** を選択します

   .. image:: ../media/Services-Apps.png
      :width: 200

#. **Trading Application (CAS)** appを開きます。**Trading Main** コンポーネントの**WAF Enablement Status** 、**WAF Monitoring Only Status**が "On" であることを確認できます

    .. image:: ./media/PMTradingMainCASMonitoringVerifyApp.png

#. **Components** セクションをクリックしてください

    .. image:: ./media/PMTradingMainCASMonitoringVerifyComponent.png


おめでとうございます！！ |classbold| ラボは完了完了しました！！
--------------------------------------------------------------------------------------------------------------------
