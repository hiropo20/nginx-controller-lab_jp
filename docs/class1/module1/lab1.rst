Lab 1 - Active Directory Authentication
#################################################

このラボではActivce Directory認証で設定されている内容とRole Based Access Controlの設定に追加確認します。
設定変更は行わず、設定済みの内容を確認します。

この機能により組織の既存環境で利用している認証のプロセスをNGINX Controllerに統合することが可能です

.. IMPORTANT::
    想定時間: 5分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 

Authentication Providersの設定を確認する
--------------------------------------------------

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

#. 画面左上のナビゲーションバーを開き、ドロップダウンリストから **Platform** を選択してください

   .. image:: ../media/Tile-Platform.png
      :width: 200

#. **Auth Providers** を開いてください

   .. image:: ./media/M1L1AuthProviders.png
      :width: 800

#. **Edit** をクリックし、``ad-acmefinancial-net`` の設定を確認してください 

   .. image:: ./media/M1L1ProviderEdit.png
      :width: 800

Inspect the Authentication Provider Configuration
--------------------------------------------------

In this section, students will walk through the "Authentication Provider Configuration" tabs.
Click the tab for the relevant area.

.. image:: ./media/M1L1ADwalkthrough.png

#. **Configuration** tab

   This section defines the basic settings for the authentication provider.  The required parameters are:

   +--------------------+---------------------------------------------------------------------------------------+
   | Attribute          | Description                                                                           |
   +====================+=======================================================================================+
   | Auth Provider Type | Define the  authentication provider being used                                        |
   +--------------------+---------------------------------------------------------------------------------------+
   | User Format        | Define if the user will login with username@domain (UPN) or domain/user (User Domain) |
   +--------------------+---------------------------------------------------------------------------------------+

   .. NOTE::
      As of release 3.15, Active Directory is currently the only supported Auth Provider Type.


   .. image:: ./media/M1L1ProviderConfig.png
      :width: 800

#. **Connection** tab

   This section is used to specify the Domain, URL, and SSL settings.  

   .. NOTE::
        There is no option under **SSL Parameters** to allow for an unencrypted connection. 

   In this example, the AD certificate has been provided for verification purposes. 


   .. image:: ./media/M1L1Connection.png
      :width: 800

#. **User Binding** tab

   This section specifies the "Bind" account credentials for NGINX Controller to authenticate to Active Directory.


   .. image:: ./media/M1L1UserBinding.png
      :width: 800

#. **Group Setup** tab

   This section determines the caching and query parameters for Active Directory groups which can be used for Role Based Access Control.


   .. image:: ./media/M1L1GroupSetup.png
      :width: 800

#. **Group Mappings** tab

This section maps Active Directory groups to "Internal" NGINX Controller "Roles Groups".


   .. image:: ./media/M1L1GroupMappings.png
      :width: 800

Controller "Roles Groups" can be configured under **Platform** -> **Roles**. 
See the NGINX Controller documentation on managing `roles`_ for more information.

   .. image:: ./media/M1L1RolesGroups.png
      :width: 800

The Active Directory groups used in the **Group Mapping** configuration can be viewed on the Domain Controller. 

   .. NOTE::
     The following pictures are for reference -- you don't need to login to the Domain Controller.

   .. image:: ./media/M1L1ADGroups.png
      :width: 800

   .. NOTE::
     You are currently logged in as "Peter Parker". "Peter" is member of "nginx-controller-admins".  This Active Directory group is mapped to the "admin_group" roles group in NGINX Controller. 

   .. image:: ./media/M1L1ADUsers.png
      :width: 600

   .. image:: ./media/M1L1ADControllerAdmins.png
      :width: 400

Additional Reference
--------------------

The published NGINX Controller documentation walks through configuring an Active Directory authentication provider in `detail`_.



.. _detail: https://docs.nginx.com/nginx-controller/platform/access-management/manage-active-directory-auth-provider/
.. _roles: https://docs.nginx.com/nginx-controller/platform/access-management/manage-roles/
