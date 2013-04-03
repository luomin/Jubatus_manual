====================================================
3.1. Jubatusクライアントライブラリのインストール方法
====================================================

本項では、以下に示すシステム構成におけるJubatusクライアントライブラリのインストール手順を説明します。

.. image:: ../images/server_client.png

3.1.1 前提
==========

Jubatusを使ったクライアントアプリケーションはC++,Python,RubyまたはJavaで記述することができます。
クライアントアプリケーションからJubatusを使うには、各言語のクライアントライブラリをインストールする必要があります。
クライアントライブラリはMIT Licenseの下で配布されています。


JubatusとJubatusクライアントのバージョンは異なることがあります。
これは、JubatusのAPIが変更されない場合はクライアント側のアップデートが不要なためです。

次節以降では、C++、Python、Ruby、Javaのクライアントライブラリのインストール方法を順に説明します。


3.1.2. C++
===========

クライアントはJubatusフレームワークに含まれているため、インストールは不要です。(``$PREFIX/include/jubatus/client/*_client.hpp``)

コンパイラや開発用のヘッダがインストールされていない場合は、以下の手順でセットアップを行ってください。

Ubuntuでは、以下のコマンドを実行します。

::

 $ sudo apt-get install build-essential


3.1.3. Python
==============

クライアント(Python 2.7以降が必要)は `PyPl <http://pypi.python.org/pypi/jubatus>`_ で配布されています。
以下のコマンドを実行して、インストールします。

::

 $ sudo pip install jubatus

※プロキシを経由している場合、以下のようなエラーが出力されることがあります。その場合はオプションでプロキシを指定して実行してください。

 ::
  
  Cannot fetch index base URL http://pypi.python.org/simple/
  Could not find any downloads that satisfy the requirement jubatus
  No distributions at all found for jubatus
  Storing complete log in /home/nttat/.pip/pip.log

 ::

  $ sudo pip --proxy=http://username:password@proxy.example.com:port/ install jubatus

以下のようなログが出力されればインストール完了です。

::

 Successfully installed jubatus msgpack-rpc-python msgpack-python tornado
 Cleaning up...

pipコマンドがインストールされていない場合は、以下の手順でインストールしてください。

 ::

  $ sudo apt-get install python-pip
 
 ※プロキシを経由している場合、aptのプロキシ設定をしていないとエラーが出力されますので、\ ``/etc/apt/apt.conf``\に以下の行を追加してください。

  ::

   $ cd /etc/apt
   $ sudo vi apt.conf
 
  ::

   Acquire::http::Proxy "http://username:password@proxy.example.com:port/";
 

3.1.4. Ruby
============

クライアント（Ruby 1.9以降が必要）は `RubyGems <http://rubygems.org/gems/jubatus>`_ で配布されています。以下のコマンドを実行して、インストールしてください。

::

 $ sudo -E gem install json
 $ sudo -E gem install jubatus

gemコマンドがインストールされていない場合は、以下の手順でインストールしてください。

::

 $ sudo -E apt-get install ruby1.9.1-full

gem installで以下のようなエラーが出力された場合、プロキシの設定がない為なので以下のコマンドで環境変数を設定します。

 ::

  export http_proxy=http://username:password@proxy.example.com:port/

3.1.5. Java
============

クライアントはJubatusの `Maven <http://download.jubat.us/maven/>`_ リポジトリで配布されています。以下の記述を、あなたのプロジェクトのpom.xmlに追加してください。

::

 <repositories>
  <repository>
   <id>jubat.us</id>
   <name>Jubatus Repository for Maven</name>
   <url>http://download.jubat.us/maven</url>
  </repository>
 </repositories>

 <dependencies>
  <dependency>
   <groupId>us.jubat</groupId>
   <artifactId>jubatus</artifactId>
   <version>0.4.0</version>
  </dependency>
 </dependencies>

JavaでJubatusクライアントの開発をする場合は、 `GitHub <https://github.com/jubatus/jubatus-java-skelton>`_ で公開されているスケルトンプロジェクト（Eclipseプロジェクトのテンプレート）を利用すると便利です。
以下の手順に従って、Java開発用スケルトンを利用してください。

#. Eclipseを起動し、［File］－［Import…］を選択します。
#. ［Git］>［Projects from Git］を選択し、［Next］ボタンをクリックします。
#. ［URI］を選択し、［Next］ボタンをクリックします。
#. ［URI］に「https://github.com/jubatus/jubatus-java-skelton.git」と入力し、［Next］ボタンをクリックします。
#. ダイアログに従って操作を進め、[完了]ボタンをクリックします。

一度、インポートが完了すれば、Mavenが自動的にJubatusクライアントライブラリをダウンロードします。
\ ``src/main/java``\ディレクトリの(default package)配下には、Jubatus recommenderを利用した簡単なプログラム「Client.java」が配置してあります。

