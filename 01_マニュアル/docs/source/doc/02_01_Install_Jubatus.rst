===================================================
2.1. Jubatusシステムインストール手順
===================================================

本項では、Jubatusシステムのインストール手順を説明します。


2.1.1. 前提
==================================================

Jubatusは、Ubuntu Server 12.04 LTS(x86_64)とRed Hat Enterprise Linux 6.2以降(x86_64)が公式にサポートされています。これらのシステムでは、Jubatusのすべてのコンポーネントをバイナリパッケージでインストールすることができます。

ここでは、Ubuntu Server 12.04.1 LTS(x86_64)を想定して説明していきます。

対象とするシステム構成は、以下のような最もシンプルなサーバ1台の構成とします。


.. image:: ../images/server_client.png

2.1.2. インストールの流れ
==================================================

Ubuntu ServerへのJubatusシステムのインストールと事前準備は、以下の流れで作業を行います。

 ① JubatusのAptリポジトリにシステムを登録
 
 ② jubatusパッケージのインストール
 
 ③ 環境変数の読み込み

2.1.3. インストール手順詳細
==================================================


① JubatusのAptリポジトリにシステムを登録


 /etc/apt/sources.list.d/jubatus.listを作成し、作成したjubatus.listに以下の行を記述します。

 ::

  $ cd /etc/apt/sources.list.d
  $ sudo vi jubatus.list


 ::

  deb http://download.jubat.us/apt binary/


② jubatusパッケージのインストール


 apt-getコマンドを実行しjubatusパッケージをインストールします。

 ::

  $ sudo apt-get update
  $ sudo apt-get install jubatus


 ※プロキシを経由している場合、aptのプロキシ設定をしていないとエラーが出力されますので、/etc/apt/apt.confに以下の行を追加してください。

  ::

   $ cd /etc/apt
   $ sudo vi apt.conf
  

  ::

   Acquire::http::Proxy "http://username:password@proxy.example.com:port/";
  
  
 また、現在パッケージにはGPG署名が行われていません。以下の警告メッセージが表示された場合は、 y を入力してください。

 ::
  
  警告: 以下のパッケージは認証されていません!
   jubatus
  検証なしにこれらのパッケージをインストールしますか [y/N]? y
  
 インストールしているログが流れ、最後に以下のようなメッセージが出力されます。
 
 ::
  
  
  (.../jubatus_0.4.1-1_amd64.deb から) jubatus を展開しています...
  jubatus (0.4.1-1) を設定しています ...
  
  
 /opt/jubatus のフォルダが作成されていることを確認してください。これでJubatusがインストールされました。


③ 環境変数の読み込み

 Jubatusを使う前に、毎回profileスクリプトから環境変数を読み込む必要があります。
 Jubatus起動前に、以下のコマンドを実行してください。

 ::
  
  $ source /opt/jubatus/profile
  
 以上でJubatusシステムのインストールと事前準備が完了しました。