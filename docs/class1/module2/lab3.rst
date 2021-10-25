Lab 3 - ADC によるより詳細なトラフィック制御機能(Programmability)
######################################################

このラボのゴールは、現在NGINX Controllerで利用可能な "advanced ADC" 機能を確認します。
以前、これらの機能はNGINX Controllerで対応できず、NGINX Insntanceを個別に設定した場合の設定のみで利用が可能だった内容です

.. IMPORTANT::
    想定時間: 10分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 

App Componentを開く
-------------------------

#. Chromeを開く

#. ブックマークよりNGINX Controller のGUIにアクセス

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

#. **Services** を開いてください

   .. image:: ../media/Tile-Services.png
      :width: 200

#. "Apps" を選択してください

   .. image:: ../media/Services-Apps.png
      :width: 200

#. "Echo Environment"から Module 2 Lab 1 で作成した **echoapp** を選択してください

   .. image:: ./media/M2L3echoapp.png
      :width: 200

URI Rewriteを設定する
---------------------

#. **Components** を開いてください。 以前作成した "echoappcomponent" を **Edit** を開いてください

   .. image:: ./media/M2L3echoappEdit.png
      :width: 800

#. "Advanced" セクション内の、 **Programmability** を選択してください

   .. image:: ./media/M2L3program.png
      :width: 600

#. Chrome内で、Componentによる構成変更前に、"echo" アプリケーションからどのような応答があるか確認してください
   このモジュールの前の項目で実施したように Chrome Developer tools を開き、``http://echoapp.net/example`` へアクセスし、結果を確認してください

   .. image:: ./media/M2L3URLbar.png
      :width: 300 

   .. image:: ./media/M2L3beforeURLRW.png
      :width: 800

.. NOTE::
     The app's JSON response confirms that the request received was to ``path: "/example"``. 

#. NGINX Controllerで、"URI Rewrite"をコンポーネントに追加してください。これはシームレスにすべての "/example*" 宛のリクエストを "/modified*" へ変更します
   "Programmability" ダイアログの **Add URI Rewrites** をクリックしてください

   .. image:: ./media/M2L3AddRW.png
      :width: 600

``http://echoapp.net/example``

#. 操作を完了し、変更内容を反映するため、**Done** をクリックしてください。
   NGINXの別に `rewrite`_ モジュールがあり、PCRE正規表現の記述を用いて、NGINX Controllerの設定変更を行います

   +-------------------------+---------------------------+
   |        Field            |      Value                |
   +=========================+===========================+
   | Incoming Pattern        |  ``~*^/example(.*)$``     |
   +-------------------------+---------------------------+
   | Rewrite Pattern         |  ``/modified$1``          |
   +-------------------------+---------------------------+

   .. image:: ./media/M2L3AddRWdialogue.png
      :width: 600

   .. image:: ./media/M2L3RWready.png
      :width: 600

   .. IMPORTANT::
     より詳細な順序を指定したURIを操作するルールセットが必要となる場合、 "After Execute" 機能を利用し実装を検討ください

#. **Submit** をクリックし、変更したComponentの内容を "Gateway" にプッシュしてください。コンポーネントのステータスが、"Configuring" から "Configured" に変わったことを確認してください

   .. image:: ../media/Submit.png
      :width: 100

   .. image:: ./media/M2L3RWconfigured.png
      :width: 800

#. Chromeで、echoapp に対し "/example" というリクエストを送信し、Rewrite動作のテストをしてください。応答データの内容を確認してください

   .. image:: ./media/M2L3afterURLRW.png
      :width: 800

   .. NOTE::
     "Echo" appのJSONレスポンスは、ブラウザのURIで入力した情報("/example")ではなく、"/modified(変更後)"のリクエストが表示されていることを確認ください


Request Header 変更機能を設定する
-------------------------------------

#. NGINX Controllerの "echoapp" App の画面を再度開き、**Components** を開いてください。先程作成した "echoappcomponent" で **Edit** をクリックしてください

   .. image:: ./media/M2L3echoappEdit.png
      :width: 800

#. "Advanced" セクション配下にある **Programmability** を選択してください

   .. image:: ./media/M2L3program.png
      :width: 700

#. Chromeで、前回 "echo" app にアクセスした際のレスポンスヘッダーの情報を確認してください

   .. image:: ./media/M2L3beforeHeaders.png
      :width: 800

#. NGINX Controllerで、コンポーネントの"Request Header Modification"を追加してください。この機能はupstream/pool memberに通信を転送する際に、ADCとして動作するNGINX PlusでHTTP Headerを追加する機能です
   "Programmability" の **Add Request Header Modification** をクリックしてください

   .. image:: ./media/M2L3AddHM.png
      :width: 600

#. 以下の内容を入力し、内容を保存するため **Done** クリックしてください 

   +-------------------------+--------------------------------------+
   |        Field            |      Value                           |
   +=========================+======================================+
   | Action                  |  ``Add``                             |
   +-------------------------+--------------------------------------+
   | Header Name             |  ``X-Controller-Instance``           |
   +-------------------------+--------------------------------------+
   | Header Value            |  ``Development NGINX West 03 (CAS)`` |
   +-------------------------+--------------------------------------+

   .. image:: ./media/M2L3HeaderDialogue.png
      :width: 600

   .. image:: ./media/M2L3Headerready.png
      :width: 600

#. **Submit** をクリックし、変更したComponentの内容を "Gateway" にプッシュしてください。コンポーネントのステータスが、"Configuring" から "Configured" に変わったことを確認してください 

   .. image:: ../media/Submit.png
      :width: 100

   .. image:: ./media/M2L3RWconfigured.png
      :width: 800

#. Chromeで、echoapp に対し再度リクエストを送信し(更新ボタンをクリックするなど)HTTP Headerの挿入について動作を確認してください。応答データの内容を確認してください

   .. image:: ./media/M2L3afterHM.png
      :width: 800

   .. NOTE::
     "echo" Appが応答するJSONデータは、HTTPリクエストに追加されたヘッダーの情報が表示されます。
     このヘッダー追加機能により、どのNGINX Plusインスタンスが通信の操作を行ったか示すHTTP Headerの追加をすることが可能です
     リクエストやレスポンスのHTTP Headerを追加・削除するなど、アプリケーションに求められる内容を実施することが可能です

追加情報
--------------------

"Programmability" セクションでは、URIリダイレクト、URI Rewrite、リクエストヘッダー操作、レスポンスヘッダー操作を行うことができます
これらの機能は、NGINXの`rewrite`_モジュールによって実現しています。より詳細な情報についてはmoduleのドキュメントを参照してください

NGINX REGEX validator は作成した正規表現を確認するのに便利です。こちらの記事を参照ください(`regex`_ blog)。また、NGINXが使うPerlの正規表現(PCRE)も理解に役立ちます。合わせてご確認ください

.. _rewrite: http://nginx.org/en/docs/http/ngx_http_rewrite_module.html
.. _regex: https://www.nginx.com/blog/regular-expression-tester-nginx/
