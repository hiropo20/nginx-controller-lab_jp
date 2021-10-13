Lab 1 - NetOps/Adminm向けのAnalytics 
############################################

このラボのゴールはNGINX Controllerが提供するNGINX Plus instanceの統計状況を確認することです。
このAnalyticsのカテゴリは主にNetOpsの担当者向けとなります

.. IMPORTANT::
    想定時間: 5分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 


Dashboard概要
-------------------

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

#. ログイン後、Dashboardの"Overview"が表示されます。
Notice the "Overview" dashboard is displayed on login. The "System
   Metrics" section, prominently located at the top of the "Overview", appeals to NetOps personas.

   |Lab1MainDashboard|

Instance Analytics
-------------------

#. Select the navigation bar in the upper left of the screen then select **Infrastructure** from the drop-down list.

   .. image:: ../media/Tile-Infrastructure.png
      :width: 200

#. From the list of Instances, click on **Production NGINX East 01**. 
   This view is the "Instance Overview" page. Toggle the graphs
   from **Bytes In**, **Bytes Out**, **CPU Usage** and **Memory Usage** by clicking on the tiles.

   |Lab1InstanceSelection|

   |Lab1InstanceDashboard|

   This section provides a single pane with all useful graphs for the selected instance.
   Toggle the time ranges from the drop-down boxes to see the instance performance for a
   particular time range.

   |Lab1InstanceAnalytics|

.. IMPORTANT::
   Though out of scope for this lab, please see the custom dashboard features under **Analytics -> Dashboards**.
   Dashboard elements can include aggregated and filtered views of the hundreds of instance and application metrics collected by Controller.

.. |Lab1MainDashboard| image:: media/Lab1MainDashboard.png
   :width: 800
.. |ControllerBtn| image:: media/0ControllerBtn.png
   :width: 1.59722in
   :height: 0.98611in
.. |Infrastructure| image:: media/0Infrastructure.png
   :width: 2.46535in
   :height: 0.53394in
.. |Lab1InstanceSelection| image:: media/Lab1InstanceSelection.png
   :width: 800
.. |Lab1InstanceDashboard| image:: media/Lab1InstanceDashboard.png
   :width: 800
.. |Lab1InstanceAnalytics| image:: media/Lab1InstanceAnalytics.png
   :width: 800
