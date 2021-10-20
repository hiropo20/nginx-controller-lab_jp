.. _overview:

F5 Labs - Index
=======================

ようこそ！
-------

ようこそ、 |classbold| labへ


はじめに
---------------

インストラクターより提供される手順に従ってラボを進めてください。
すでにこのガイドを閲覧されている状況であれば、UDFの環境を実行しているかもしれません。
   
.. IMPORTANT::
    このラボのすべての作業はWindows Jumphost(RDP)から行います。皆様のローカルのシステムに対するインストールや操作は必要ありません

"jumphost-1"というUDFインスタンスにRDPを使用し、adminアカウントでログインしてください。
提供されるUDF環境によって画面のデザインが異なる場合がありますがご了承ください

   .. image:: ./class1/media/IntrojumpHostAccessMethod.png
      :width: 600

   +---------------+---------------+
   |   Username    |  Password     |
   +===============+===============+
   | Administrator | ``BZ8D8MCVR`` |
   +---------------+---------------+

Lab Topology
------------

このラボ環境では主に以下のコンポーネントが実行されます：

- 3 X NGINX Controller Instances (v3.x)
- 1 X Postgres Database Instance (NGINX Controllerで使用)
- 3 X NGINX Plus Instances w/ agents installed (CentOS 7)
- 1 X Application Servers (apps running in Docker on Ubuntu 18.04)
- 1 X Windows Domain Controller (Windows 2019 Server)
- 1 X Load Generator Instances (Ubuntu 18.04)


NGINX Controller Credentials
----------------------------

NGINXコントローラーは、ActiveDirectoryドメインコントローラーに対して認証を実行するように構成されています。
このラボでは、次のアカウントを使用します。

.. list-table::
    :widths: 40 60 20 40
    :header-rows: 1
    :stub-columns: 1

    * - **Employee**
      - **Login UPN**
      - **Password**
      - **Active Directory Security Group**
    * - Peter Parker
      - peter@acmefinancial.net
      - ``Peter123!@#``
      - nginx-controller-admins
    * - Natasha Romanoff
      - natasha@acmefinancial.net
      - ``Natasha123!@#``
      - nginx-controller-user
    * - admin istrator (fallback account)
      - admin@acmefinancial.net
      - ``Admin123!@#``
      - NA/Local User

このラボ環境でNGINXコントローラのRBAC機能を調査したい場合、以下のアカウントをご利用ください

.. list-table::
    :widths: 40 60 20 40
    :header-rows: 1
    :stub-columns: 1

    * - **Employee**
      - **Login UPN**
      - **Password**
      - **Active Directory Security Group**
    * - Wanda Maximoff
      - wanda@acmefinancial.net
      - ``Wanda123!@#``
      - nginx-controller-admins
    * - Clint Barton
      - clint@acmefinancial.net
      - ``Clint123!@#``
      - nginx-controller-user
    * - Luke Cage
      - luke@acmefinancial.net
      - ``Luke123!@#``
      - nginx-controller-readers


.. toctree::
   :maxdepth: 3
   :caption: Lab Contents:
   :glob:

   class*/module*/module*
