Lab 2 - ADC App 作成のプログラマビリティの確認
################################################

| このラボのゴールはNGINX Controller APIを用いたApp Componentの作成です。
| このラボはお客様環境で自動化ツールやCI/CD Pipelineで実施するプログラムによるデプロイのProxyとしてPostmanを利用します。

.. IMPORTANT::
    想定時間: 5分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 


Trading App の現在の状況を確認
---------------------------------

#. Chromeで、``http://trading.acmefinancial.net`` を開き、 **Login** ボタンをクリックしてください。 
   これは、"trading" アプリケーションの例となります

   .. image:: ./media/M2L2tradingGen.png
      :width: 800

#. 以下の認証情報を用いてアプリケーションにログインしてください

   +-------------------------+----------------------+
   |        Username         |      Password        |
   +=========================+======================+
   |  matt                   |  ``ilovef5``         |
   +-------------------------+----------------------+

   .. image:: ./media/M2L2tradingLogin.png
      :width: 200

   .. NOTE::
      右側のフレームに表示されている "Quick Money Transfer" が "Coming Soon" であることを確認してください

   .. image:: ./media/M2L2trading1.png
      :width: 200

Postmanを利用して Component のデプロイ
---------------------------------

#. Jumphostで **Postman** が起動していない場合、デスクトップのアイコンクリックしアプリケーションを開いてください。  **NGINX Controller 3.x** Collection を開いてください

   .. image:: ../media/PMcoll.png
      :width: 400

#. **Common Tasks**、 **Admin Logon** を開き、 **Login to Controller – admin – local** をクリックしてください

   .. image:: ../media/PMcoll2.png
      :width: 400

#. Postmanの **Send** を選択してください

   .. image:: ../media/PMsend1.png
      :width: 800

   .. NOTE::
      NGINX Controllerが "204 No Content" と 認証Cookie情報を応答します
      PostmanはこのCookieを以降のサブリクエストの認証情報として利用します
      (以下の例は、次の操作でRequest欄「・・・」＞Cookies＞MANAGE COOKIESよりsession欄を開いた結果です)

   .. image:: ./media/M2L2PMcookie.png
      :width: 400

#. **Retail-Development Environment**, **Application - trading** フォルダを開きます。
   **Application trading** サブフォルダを開き、リクエスト名 **4) Create Component – transfers** を選択してください

   .. image:: ./media/M2L2PMtransfer.png
      :width: 400

#. Postmanのリクエストエリアにある **Body** をクリックしてください。PUT リクエストのペイロードを確認してください。
   JSONの ``desiredState``, ``logging``, ``security``, ``backend`` 配下のプロパティ値は前回のラボでデプロイした Component に関する内容であることが確認できます

   .. image:: ./media/M2L2PMbody.png
      :width: 700

#. Postmanで **Send** を選択

   .. image:: ./media/M2L2PMsend2.png
      :width: 800

   .. NOTE::
      NGINX Controllerは "eventual consistency model" に従います。APIはPostmanのリクエストに "202 Accepted" ステータスコードを返します。
      NGINX Controllerは現状動作し、意図した状態であることが確認できます

   .. image:: ./media/M2L2PMconfig.png
      :width: 700

Trading App の変更を確認する
---------------------------

#. Chromeで、``http://trading.acmefinancial.net/trading/index.php`` を再度開きます。
   ”Quick Money Transfer" が"Comming Soon"からActiveに変わっていることを確認できます

   .. image:: ./media/M2L2result.png
      :width: 400
