Lab 2 - NGINX Controller の冗長化
############################################

このラボのゴールはNGINX Controller clusterの3つ目のメンバーとしてホストを追加することです

本番環境では、我々のサービスに対し冗長性をもたせることが一般的です。複数のインスタンスを水平スケール・動作の管理を行うNGINX Controllerを用いた場合、単一の事象が様々な事象の引き金となることがあります。冗長構成の実装はコンプライアンスやルールの要素としても求められることがあります。この機能により複数のコントローラを用いて安定した環境を実現します

.. IMPORTANT::
    想定時間: 15分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 

追加するNGINX Controllerのノードを作成する
------------------------------------------

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

#. **Cluster** を開いてください

   .. image:: ./media/M1L2ClusterTile.png
      :width: 800

#. 現在の "Cluster Configuration" を確認してください

   .. image:: ./media/M1L2ClusterConfig.png
      :width: 800

.. NOTE::
     "Cluster Configuration" の項目は、クラスタを構成するNGINX Controllerインスタンスを示します。
     FQDNはAPI Gateway podに割り当てる証明書で利用するcommon nameに該当します
     例: APIエンドポイントやGUIの接続先として公開するサービスの名称

.. IMPORTANT::
    "load balancer"設定は今後リリースされるNGINX Controllerにて設定可能となる予定です
    追加の情報はラボの :ref:`Reference` を参照してください

.. NOTE::
    "Nodes"として現在2つのNGINX Controller インスタンスが表示されています
    ( "controller-1" および "controller-2" に該当するノード)

#. 画面右上の **Create Node** ボタンをクリックしてください

   .. image:: ./media/M1L2CreateNodeButton.png
      :width: 200

#. ダイアログに従って進め、"controller-3" インスタンスを追加するため、"Name" と "Hostname または IP Address" を指定してください
   Click the **Save** button.

   +-------------------+-----------------------+
   |        Field      |      Value            |
   +===================+=======================+
   |  Name             |  ``controller-3``     |
   +-------------------+-----------------------+
   |  Hostname or IP   |  ``10.1.1.10``        |
   +-------------------+-----------------------+

   .. image:: ./media/M1L2CreateNodeDialogue.png
      :width: 800

#. **View** にインストール手順が記載されています。インストールコマンドと "join key" をクリップボードにコピーしてください。 

   .. image:: ./media/M1L2NodeViewButton.png
      :width: 800

   .. image:: ./media/M1L2NodeJoinCommand.png
      :width: 800

Run the install command to join the instance to the cluster
-----------------------------------------------------------

#. "controller-3" インスタンスにログインしてください。"PuTTY" を開き、保存済みのホストより **controller-3** を選択し、**Open** をクリックしてください

   .. image:: ./media/M1L2puttyc3.png
      :width: 400

   .. IMPORTANT::
      もし、Puttyがサーバのホスト鍵に関する警告を示した場合、接続のため **Yes** をクリックしてください
      これは、ラボ環境の各ホストでユニークなhost keyを生成するため生じるものです

#. installerディレクトリより、install.sh コマンドを実行してください。そしてプロンプトの表示に対し "y" ("yes" の意味) を入力してください

   .. code-block:: bash

      $ cd controller-installer/
      $ ./install.sh --join-key {{base64 encoded key}}

   .. image:: ./media/M1L2InstallCommand.png
      :width: 800

#. コマンドの実行結果として、クラスタに追加が完了したことがノードに表示されます

   .. image:: ./media/M1L2NodeJoinSuccess.png
      :width: 300

View the results
----------------

#. Chromeを開き、**Cluster** の "Cluster Configuration" を確認してください

   .. image:: ./media/M1L2NodesConfigured.png
      :width: 800

(Optional) Kubernetes Cluster の確認
------------------------------------------

もし、Kubernetes (k8s) について確認されたい場合、NGINX Controllerによって作成される k8s クラスタの情報を確認することが可能です

#. 先程ログインした PuTTY の "controller-3" への接続を利用するか、新たにNGINX Controllerインスタンスのいずれか一つに接続してください

   .. image:: ./media/M1L2puttyc1.png
      :width: 400

   .. IMPORTANT::
      もし、Puttyがサーバのホスト鍵に関する警告を示した場合、接続のため **Yes** をクリックしてください
      これは、ラボ環境の各ホストでユニークなhost keyを生成するため生じるものです

#. クラスタノードを表示します

   .. code-block:: shell

      kubectl get nodes 

   .. image:: ./media/M1L2Nodes.png
      :width: 800

   .. NOTE::
      コマンドの出力結果として、k8s クラスタに3つのノードが存在することが確認できます

#. デプロイされたポッドを確認する

   .. code-block:: shell

      kubectl get pods -n nginx-controller -o wide
      
   .. image:: ./media/M1L2K8s.png
      :width: 1024

   .. NOTE::
      コマンドの出力結果として、NGINX Controllerが複数のPodを3つのノードに対してデプロイしていることが確認できます
      ("NODE"カラムを確認ください)

.. _Reference:

追加情報
--------------------
    "load balancer"設定は今後リリースされるNGINX Controllerにて設定可能となる予定です
    追加の情報はラボの :ref:`Reference` を参照してください
      The "load balancer" option will be configurable in a future Controller release.
      See this lab's Additional :ref:`Reference` for more details.
      
将来リリースされるNGINX Controllerでは、API Gateway Kubernetes serviceを公開するために利用するfloating self-ipが "load balancer" によって作成される予定です。
オンプレミス環境ではL2 Failoverをサポートする `MetalLB`_ の構成、クラウド環境では k8sの type  `LoadBalancer`_を用いたクラウドネイティブな外部向けロードバランサー機能を利用する想定となります。

.. _MetalLB: https://metallb.universe.tf/
.. _LoadBalancer: https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer
