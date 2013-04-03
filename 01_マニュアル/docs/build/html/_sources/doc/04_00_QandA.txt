===================================================
4. Q&A
===================================================

本項では、JubatusシステムにおけるQ&Aを記載します。


| 

*Q : Jubatusサーバを複数台で分散させた場合、Mixが正常動作したか確認する方法はありますか？*

A : Mixの動作については、Jubatusサーバにおいて出力されるログにより確認することができます。以下のようなログが出力されます。

  ::
  
    I0218 06:01:49.587540  3845 linear_mixer.cpp:173] starting mix:
    I0218 06:01:49.703693  3845 linear_mixer.cpp:231] mixed with 3 servers in 0.112371 secs, 8 bytes (serialized data) has been put.
    I0218 06:01:49.705159  3845 linear_mixer.cpp:185] .... 22th mix done.
    I0218 06:03:15.502995  3845 linear_mixer.cpp:173] starting mix:
    I0218 06:03:15.642297  3845 linear_mixer.cpp:231] mixed with 3 servers in 0.137258 secs, 8 bytes (serialized data) has been put.
    I0218 06:03:15.644685  3845 linear_mixer.cpp:185] .... 23th mix done.



| 

*Q : 分散構成のJubatusを準備する場合、jubaclassifier、jubaclassifier_keeper/Client、ZooKeeperを1台のサーバにインストールし、その構成のサーバを複数用意しても問題ありませんか？*

A : 問題ありません。但し、各プロセスを単独のサーバで動作させた場合と比べ、処理性能が低下する可能性があります。またZooKeeperは奇数台でアンサンブルを構成することを推奨いたします。


| 

*Q : Jubatusに学習をさせる場合、以下のような学習のさせ方の違いにより、学習モデルに差異は発生しますか？*
    ・ 学習データを一括してJubatusに渡し学習させる（trainメソッドを1度だけ呼び出す）
    
    ・ 学習データの数だけtrainメソッドを呼び出し、学習させる

A : いいえ、学習モデルに差異は発生しません。


| 

*Q ： Classifier（shogun）のpython版サンプルにおいて、学習データをshuffleする処理を実行しておりますが、shuffleは必須ですか？*

A : はい、shuffleは必須です。

