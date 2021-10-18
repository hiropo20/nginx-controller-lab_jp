Lab 6 - Security トラフィックジェネレータ を実行する
################################################

このラボのゴールはセキュリティ機能が有効となったデータパスに対し、トラフィックジェネレータを実行することです

.. NOTE::
    このラボの内容は少なく、動作環境の主な確認は次のラボで実施します
    ただし、今後のラボで必要となる作業のため必ず完了してください

.. IMPORTANT::
    想定時間: 5分

.. NOTE::
    このLabの手順はラボを実施する方がWindows jumphost -- ``jumphost-1`` から操作する手順を示しています。
    接続方法についてはこちらを参照ください。 :ref:`overview` 

    .. _loadgen:

Analytics に利用する WAF Traffic Generation の実行
-------------------------------------------

.. IMPORTANT::
   このステップは Module3 のAnalytics / 統計情報の確認のため必須の内容となります
   
#. "loadgen-1" インスタンスにログインしてください。"PuTTY"でsaved sessionに表示される **loadgen-1** を選択し **Open** をクリックしてください 

   .. image:: ./media/M2L6loadgenssh.png
      :width: 400

   .. IMPORTANT::
      もし、Puttyがサーバのホスト鍵に関する警告を示した場合、接続のため **Yes** をクリックしてください これは、ラボ環境の各ホストでユニークなhost keyを生成するため生じるものです

#. 以下の "docker" commandでラボでデプロイされたでもアプリケーションに対しトラフィックを生成してください

   .. code-block:: bash

      $ sudo docker start wrk_trading.acmefinancial.net-cas

#. コマンドの実行が完了するとコンテナ名が出力されます("wrk_trading.acmefinancial.net-cas").

   .. image:: ./media/M2L6loadgenresult.png
      :width: 600
