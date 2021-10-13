Lab 1 - GUIで ADC App の作成
#######################################

このラボのゴールはラボを実施する方がADCに関するNGINXコントローラのオブジェクトモデルやコンセプトを理解いただくことです。
NGINXコントローラを用いた「app-centric(アプリケーション中心)」のモデルは、いわゆる旧来のLBで実施していた「network-centric(ネットワーク中心)」モデルとは別のデザインであり、NGINXの設定・構成をベースにしたデザインとなります

.. IMPORTANT::
    想定時間: 15分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 


Controller Object Model Concepts
---------------------------------
このセクションではNGINXコントローラのオブジェクトについて説明します。BIG_IPやNGINXのコンセプトや記述方法と比較してください

   .. image:: ./media/M2L1ServOver.png
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
An "App" is the logical grouping of "Components" that represent the *whole* application in a microservices architecture.
Each "Component" in an "App" represents an individual microservice. 
As the object is a logical grouping, it's not represented in an NGINX Plus instances config.
Analogous BIG-IP family concepts include an AS3 tenant or a BIG-IQ "Application".

Component
^^^^^^^^^

A "Component" is the most basic representation of a single service. This includes URIs for ingress, a "Workload Group" to determine 
where backend traffic should be routed, HTTP manipulation rules (redirects, rewrites, header manipulation), logging config, etc.
Similar BIG-IP concepts include a "Virtual Server", a "Pool", limited features from "HTTP Profiles", "Local Traffic Policies", and "iRules".    


Workload Group
^^^^^^^^^^^^^^

A "Workload Group" is a collection of backend servers. NGINX config references this concept as an "upstream".
In BIG-IP terminology the group would be a "pool" and the individual members "pool members".


Deploy an Application
-----------------------
#. The jumphost should already have Chrome loaded with the controller UI at the login screen:

   .. image:: ../media/ControllerLogin.png
      :width: 400

#. If not, open Chrome Browser.

#. Access the NGINX Controller UI through the provided bookmark.

   .. image:: ../media/ControllerBookmark.png
      :width: 600

#. Login with the ``Peter Parker`` account who is an NGINX Controller admin.

   +-------------------------+-----------------+
   |      Username           |    Password     |
   +=========================+=================+
   | peter@acmefinancial.net | ``Peter123!@#`` |
   +-------------------------+-----------------+

   .. image:: ../media/ControllerLogin-Peter.png
      :width: 400

#. Navigate to the **Services** section. The items or "tiles" under this menu will be used to create the configuration for this lab.

   .. image:: ../media/Tile-Services.png
      :width: 200

Create an Environment
^^^^^^^^^^^^^^^^^^^^^^

#. Select the "Environments" tile.

   .. image:: ./media/M2L1EnvTile.png
      :width: 200

#. Click the "Create" button in the upper right.

   .. image:: ./media/M2L1EnvCreate.png
      :width: 700

#. Fill out the form.

   +---------------------+------------------------------+
   |        Field        |      Value                   |
   +=====================+==============================+
   |  Name               |  ``echo``                    |
   +---------------------+------------------------------+
   |  Display Name       | ``Echo Environment``         |
   +---------------------+------------------------------+

   .. image:: ./media/M2L1EnvDialogue.png
      :width: 700

3. Click **Submit** to complete.

   .. image:: ../media/Submit.png
      :width: 100


Add a Certificate
^^^^^^^^^^^^^^^^^

#. Select the "Certs" tile.

   .. image:: ./media/M2L1Certs.png
      :width: 200

#. Click the "Create" button in the upper right.

   .. image:: ./media/M2L1CertCreate.png
      :width: 800

#. Fill out the form and select the appropriate **Environment** from the drop-down. 

   +-----------------+----------------------------------+
   |        Field    |      Value                       |
   +=================+==================================+
   |  Name           |  ``echoapp.net``                 |
   +-----------------+----------------------------------+
   |  Environment    | ``Echo Environment``             |
   +-----------------+----------------------------------+

   .. image:: ./media/M2L1CertDialogue1.png
      :width: 700

#. Select the **Import PEM or PKC12** radio button and **Browse** for the cert and key.

   .. image:: ./media/M2L1CertDialogue2.png
      :width: 700

   The cert (**echoapp.net.crt**) and key (**echoapp.net.key**) can be found in **This PC -> Documents -> Certs** on "jumphost-1". 

   .. NOTE::
      You will need to browse and upload the cert and key individually as Controller does not allow simultaneous file uploads.

   .. image:: ./media/M2L1Cert&Key.png
      :width: 700

#. Click **Submit** to complete.

   .. image:: ../media/Submit.png
      :width: 100


Create a Gateway
^^^^^^^^^^^^^^^^^

#. Select the "Gateways" tile.

   .. image:: ./media/M2L1GatewayTile.png
      :width: 200

#. Click the "Create" button in the upper right.

   .. image:: ./media/M2L1GWcreate.png
      :width: 600

#. Under the **Configuration** dialogue, fill out the form. When finished click **Next** or click the name of the next section.

   +---------------------+----------------------------------+
   |        Field        |      Value                       |
   +=====================+==================================+
   |  Name               |  ``echoappgw``                   |
   +---------------------+----------------------------------+
   |  Environment        | ``Echo Environment``             |
   +---------------------+----------------------------------+

   .. image:: ./media/M2L1GWDialogue.png
      :width: 600

#. Under the **Placements** dialogue, select the "Development NGINX West 03 (CAS)” Instance Ref.

   .. image:: ./media/M2L1Place.png
      :width: 700

#. Under the **Hostnames** dialogue, add the specified hostnames (``http://echoapp.net``, ``https://echoapp.net``). Do not specify a **Match Method** for either hostname. 
   Select the **echoapp.net** "Cert Reference".
   
   .. NOTE::
      You will need to use the **Add Hostname** link pictured below to add multiple hostnames.

   .. image:: ./media/M2L1Hostnames.png
      :width: 700

#. Click **Submit** to complete.

   .. image:: ../media/Submit.png
      :width: 100

Create an App
^^^^^^^^^^^^^

#. Select the "Apps" tile.

   .. image:: ../media/Services-Apps.png
      :width: 200

#. Click the "Create" button in the upper right.

   .. image:: ./media/M2L1AppsCreate.png
      :width: 600

#. Fill out the form and select the **Environment** from the drop-down.

   +---------------------+------------------------------+
   |        Field        |      Value                   |
   +=====================+==============================+
   |  Name               |  ``echoapp``                 |
   +---------------------+------------------------------+
   |  Environment        | ``Echo Environment``         |
   +---------------------+------------------------------+

   .. image:: ./media/M2L1Appdiag.png
      :width: 800

#. Click **Submit** to complete.

   .. image:: ../media/Submit.png
      :width: 100

Create a Component
^^^^^^^^^^^^^^^^^^^

#. Select the "Components" section followed by the "Create Component" button in center dialogue.

   .. image:: ./media/M2L1CreateComponent.png
      :width: 800

#. Fill out the form and select the **Gateway Refs** from the drop-down.

   +-------------------------+--------------------------+
   |        Field            |      Value               |
   +=========================+==========================+
   |  Name                   | ``echoappcomponent``     |
   +-------------------------+--------------------------+
   |  Gateway Refs           | ``echoappgw``            |
   +-------------------------+--------------------------+

   .. image:: ./media/M2L1CompDiag.png
      :width: 700

#. Under the **URIs** dialogue, add the URI "/". Do not specify a **Match Method**.

   .. image:: ./media/M2L1CompURI.png
      :width: 700

#. Under the **Workload Groups** dialogue, fill out the form.

   +-------------------------+-----------------------------+
   |        Field            |      Value                  |
   +=========================+=============================+
   |  Name                   | ``Echo Backend``            |
   +-------------------------+-----------------------------+
   |  Backend Workload URIs  | ``http://10.1.20.11:8000``  |
   +-------------------------+-----------------------------+

   .. image:: ./media/M2L1WGdiag.png
      :width: 600

#. Click **Submit** to complete.

   .. image:: ../media/Submit.png
      :width: 100

Test the Echo Application
^^^^^^^^^^^^^^^^^^^^^^^^^^

#. In Chrome on ``jumphost-1``, open a new tab and enable "Developer Tools". 

   .. image:: ./media/M2L1DevTools.png
      :width: 900

#. Browse to the App URLs you created earlier (``http://echoapp.net`` and ``https://echoapp.net``) to verify the "echo" application is functioning.
   Select the **echoapp.net** request to view the results.

   .. NOTE::
      This simple web application will "echo" back information about the HTTP request it is responding to.

   .. image:: ./media/M2L1DevTools2.png
      :width: 800 
