Lab 3 - Advanced ADC Programmability Features
######################################################

このラボのゴールは、現在NGINX Controllerで利用可能な "advanced ADC" 機能を確認します。
以前、ｍこれらの機能はNGINX Controllerで対応できず、NGINX Insntanceを個別に設定した場合の設定のみで利用が可能だった内容です

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
     More advanced and ordered rulesets for URI modifications can be achieved through the use of the "After Execute" modifier.

#. Click **Submit** and verify the changes to the component are pushed to the "Gateway". The Component status should go from "Configuring" to "Configured". 

   .. image:: ../media/Submit.png
      :width: 100

   .. image:: ./media/M2L3RWconfigured.png
      :width: 800

#. In Chrome, Test the URI rewrite by sending another request to the echo application for "/example" (ie. just hit refresh on that tab). Observe the response.

   .. image:: ./media/M2L3afterURLRW.png
      :width: 800

   .. NOTE::
     The "echo" app's JSON response now shows it received a request for "/modified" as opposed to the URI in the browser bar ("/example").


Create a Request Header Modification
-------------------------------------

#. Back under the "echoapp" App in Controller, navigate to **Components**. **Edit** the "echoappcomponent" you created earlier.

   .. image:: ./media/M2L3echoappEdit.png
      :width: 800

#. Under the "Advanced" section, select **Programmability**.

   .. image:: ./media/M2L3program.png
      :width: 700

#. In Chrome, take note of the HTTP headers in the response from the previous requests to the "echo" app.

   .. image:: ./media/M2L3beforeHeaders.png
      :width: 800

#. On Controller, add a "Request Header Modification" to the component. This feature will inject an HTTP header into the request before it reaches the upstream/pool members.
   Click **Add Request Header Modification** from "Programmability" dialogue.

   .. image:: ./media/M2L3AddHM.png
      :width: 600

#. Complete the dialogue and click **Done** to save the rewrite. 

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

#. Click **Submit** and verify the changes to the component are pushed to the "Gateway". The Component status should go from "Configuring" to "Configured". 

   .. image:: ../media/Submit.png
      :width: 100

   .. image:: ./media/M2L3RWconfigured.png
      :width: 800

#. In Chrome, test the HTTP header insertion by sending another request to the echo application (ie. just hit refresh on that tab). Observe the response headers.

   .. image:: ./media/M2L3afterHM.png
      :width: 800

   .. NOTE::
     The "echo" app's JSON response shows the inserted header was added in the HTTP request.
     In this arbitrary example, we've added a header to show which NGINX Plus instance handled the request. 
     Request and Response HTTP headers can be added or deleted as needed by your application.

Additional Reference
--------------------

The "Programmability" section allows configuration of URI redirects, URI rewrites, request Header modifications, and response header modifications.
These features are powered by the NGINX `rewrite`_ module. Review the module documentation for more information. 

The NGINX REGEX validator can be helpful as you construct your own expressions: `regex`_ blog.  It is also useful to understand that NGINX uses Perl Compatible Regular Expressions (PCRE).

.. _rewrite: http://nginx.org/en/docs/http/ngx_http_rewrite_module.html
.. _regex: https://www.nginx.com/blog/regular-expression-tester-nginx/
