3.3.4.4. Python
=================

ここではPython版のGraphサンプルプログラムの解説をします。

--------------------------------
3.3.4.4.1. ソースコード
--------------------------------

このサンプルプログラムでは、学習アルゴリズム等の設定をするtrain_route.json、グラフ作成を行うcreate_graph.py、最短経路を計算するsearch_route.pyを利用します。以下にソースコードを記載します。

**train_route.json**

.. code-block:: python

 1 : {
 2 :   "method": "graph_wo_index",
 3 :   "parameter": {
 4 :     "damping_factor" : 0.9,
 5 :     "landmark_num" : 256
 6 :   }
 7 : }
 

**create_graph.py**

.. code-block:: python

 01 : #!/usr/bin/env python
 02 : # -*- coding: utf-8 -*-
 03 : 
 04 : import urllib
 05 : from xml.dom import minidom, Node
 06 : 
 07 : from jubatus.graph import client, types
 08 : 
 09 : host = '127.0.0.1'
 10 : port = 9199
 11 : instance_name = ''
 12 : 
 13 : stations = {}
 14 : 
 15 : class station_join():
 16 :     def __init__(self, station1, station2):
 17 :         self.station1 = station1
 18 :         self.station2 = station2
 19 : 
 20 : def get_station_join(line_cd):
 21 :     '''
 22 :     Create array of `station_join` that represents which stations are connected.
 23 : 
 24 :     In this example, we are connecting stations BY NAME, i.e., two stations
 25 :     with the same name are regarded as connected and can be transferred to
 26 :     each other.
 27 :     '''
 28 :     join_list = []
 29 :     url = 'http://www.ekidata.jp/api/n/' + str(line_cd) + '.xml'
 30 :     dom = minidom.parse(urllib.urlopen(url))
 31 :     for join_node in dom.getElementsByTagName('station_join'):
 32 :         for join_node_child in join_node.childNodes:
 33 :             if join_node_child.nodeType == Node.TEXT_NODE:
 34 :                 continue
 35 :             name = join_node_child.nodeName
 36 :             value = join_node_child.childNodes.item(0).nodeValue
 37 :             if name == 'station_name1':
 38 :                 station_name1 = value
 39 :             elif name == 'station_name2':
 40 :                 station_name2 = value
 41 :         join_list += [ station_join(station_name1, station_name2) ]
 42 :     return join_list
 43 : 
 44 : def create_graph(c, join_list):
 45 :     for join in join_list:
 46 :         # Create nodes for stations.
 47 :         s1_node_id = add_station(c, join.station1)
 48 :         s2_node_id = add_station(c, join.station2)
 49 : 
 50 :         # Create bi-directional edge between two nodes.
 51 :         edge_1 = types.edge({}, s1_node_id, s2_node_id)
 52 :         edge_2 = types.edge({}, s2_node_id, s1_node_id)
 53 :         c.create_edge(instance_name, s1_node_id, edge_1)
 54 :         c.create_edge(instance_name, s2_node_id, edge_2)
 55 : 
 56 :         # Comment-out this line if you're running in distributed mode.
 57 :         c.update_index(instance_name)
 58 : 
 59 : def add_station(c, name):
 60 :     if name in stations:
 61 :         node_id = stations[name]
 62 :     else:
 63 :         node_id = c.create_node(instance_name)
 64 :         c.update_node(instance_name, node_id, {'name': name})
 65 :         stations[name] = node_id
 66 :     return node_id
 67 : 
 68 : def print_stations():
 69 :     for station in sorted(stations.keys(), key=lambda k: int(stations[k])):
 70 :         print "%s\t%s" % (stations[station], station)
 71 : 
 72 : if __name__ == '__main__':
 73 :     # ①Jubatus Server への接続設定
 74 :     c = client.graph(host, port)
 75 : 
 76 :     # ②プリセットクエリーを登録
 77 :     pq = types.preset_query([], [])
 78 :     c.add_shortest_path_query(instance_name, pq)
 79 : 
 80 :     # ③グラフの作成
 81 :     create_graph(c, get_station_join(11302)) # 山手線
 82 :     create_graph(c, get_station_join(11312)) # 中央線
 83 : 
 84 :     # ④駅IDの表示
 85 :     print "=== Station IDs ==="
 86 :     print_stations()


 
 
**search_route.py**

.. code-block:: python

 01 : #!/usr/bin/env python
 02 : # -*- coding: utf-8 -*-
 03 : 
 04 : import sys
 05 : from jubatus.graph import client, types
 06 : 
 07 : host = '127.0.0.1'
 08 : port = 9199
 09 : instance_name = ''
 10 : 
 11 : def search_route(from_id, to_id):
 12 :     # ①Jubatus Server への接続設定
 13 :     c = client.graph(host, port)
 14 : 
 15 :     # ②クエリーの準備
 16 :     pq = types.preset_query([], [])
 17 :     spreq = types.shortest_path_query(from_id, to_id, 100, pq)
 18 :     
 19 :     # ③最短経路を計算
 20 :     stations = c.get_shortest_path(instance_name, spreq)
 21 : 
 22 :     # ④結果の表示
 23 :     print "Pseudo-Shortest Path (hops) from %s to %s:" % (from_id, to_id)
 24 :     for station in stations:
 25 :         node = c.get_node(instance_name, station)
 26 :         station_name = ''
 27 :         if 'name' in node.property:
 28 :             station_name = node.property['name']
 29 :         print "  %s\t%s" % (station, station_name)
 30 : 
 31 : if __name__ == '__main__':
 32 :     if len(sys.argv) < 2:
 33 :         print "Usage: %s from_station_id to_station_id" % sys.argv[0]
 34 :         sys.exit(1)
 35 :     search_route(str(sys.argv[1]), str(sys.argv[2]))



--------------------------------
3.3.4.4.2. 解説
--------------------------------

**train_route.json**

設定は単体のJSONで与えられます。JSONの各フィールドは以下のとおりです。

 * method
 
  グラフ解析に使用するアルゴリズムを指定します。
  ここでは、インデックスのないグラフを利用するための "graph_wo_index" を指定します。
  
  
 * parameter
 
  アルゴリズムに渡すパラメータを指定します。
  ここでは2つのパラメータ、"damping_factor" と "landmark_num" を指定しています。
  "damping_factor" は、PageRank の計算におけるdamping factorで、次数の異なるノードのスコアを調整します。大きくすると構造をよく反映したスコアを出す代わりに、スコアに極端な偏りが発生します。
  "landmark_num" は最短パスにおいてランドマークの総数を指定します。大きくすると正確な最短パスに近づく代わりに、多くのメモリを消費します。


**create_graph.py**

 create_graph.pyでは、山手線と中央線の接続を表すグラフを作成します。Graphのクライアントプログラムは、jubatus.graphクラス内で定義されているGraphClientクラスを利用して作成します。サンプルで使用するメソッドは、以下の5つです。

 ① Jubatus Serverへの接続設定
  Jubatus Serverへの接続を行います（74行目）。
  Jubatus ServerのIPアドレス，Jubatus ServerのRPCポート番号を設定します。
  
 ② プリセットクエリーを登録
  最短経路を計算するために、クエリーをあらかじめadd_shortest_path_queryメソッドで登録しておく必要があります。
  そのためのクエリーを作成します(77行目)。
  add_shortest_path_queryメソッドで作成したクエリーを登録します(78行目)。
  
 ③ グラフの作成
  山手線と中央線の接続を表すグラフを作成します。
  ここでは、create_graphメソッドを呼び出します(81, 82行目)。
  create_graphメソッドの第1引数は①で作成したクライアントです。
  第2引数には get_station_joinメソッドの戻り値を指定します。
  
  get_station_joinメソッドでは接続する2駅を組み合わせたリストを作成します。
  駅情報をWEB上にあるXMLファイルから取得します(29, 30行目)
  取得したXMLファイルの構造は下記のようになっています。
  今回のプログラムでは駅間の距離などは考慮せず、駅の接続情報のみ用いるため、下記XMLファイルの<station_name1>、<station_name2>の値しかプログラム中では扱いません。
  
  ::
  
   <ekidata version="ekidata.jp station_join api 1.0">
   <station_join>
    <station_cd1>1131231</station_cd1>
    <station_cd2>1131232</station_cd2>
    <station_name1>西八王子</station_name1>
    <station_name2>高尾</station_name2>
    <lat1>35.656621</lat1>
    <lon1>139.31264</lon1>
    <lat2>35.642026</lat2>
    <lon2>139.282288</lon2>
   </station_join>
   <station_join>
    <station_cd1>1131230</station_cd1>
    <station_cd2>1131231</station_cd2>
    <station_name1>八王子</station_name1>
    <station_name2>西八王子</station_name2>
    <lat1>35.655555</lat1>
    <lon1>139.338998</lon1>
    <lat2>35.656621</lat2>
    <lon2>139.31264</lon2>
   </station_join>
   <station_join>
    <station_cd1>1131229</station_cd1>
    <station_cd2>1131230</station_cd2>
    <station_name1>豊田</station_name1>
    <station_name2>八王子</station_name2>
    <lat1>35.659502</lat1>
    <lon1>139.381495</lon1>
    <lat2>35.655555</lat2>
    <lon2>139.338998</lon2>
   </station_join>
   -以下略-
   

  次に取得した駅情報のXMLファイルの<station_cd1>の値をstation_joinクラスのインスタンス変数station1に、<station_cd2>の値をstation2に格納します。
  タグ<station_join>の数だけstation_joinクラスのインスタンスを作成し、28行目で作成したリストに格納していきます（31-41行目）。
  
  上記で作成したリストを用いて、グラフを作成します(44-57行目)。
  create_graphメソッドでは、以下の作業を行います。
  
   ③－1.駅情報の追加と駅IDの取得
    グラフ内にノードを追加します。ここでのノードは駅に相当します。（例. 品川駅、御茶ノ水駅、東京駅など）
    
   ③－2.追加した2駅の相互にエッジを張る
    登録した駅から隣接する駅へエッジを張ります。ここでのエッジは線路に相当します。（例.原宿⇒渋谷など）
    
  ③－1.駅情報の追加と駅IDの取得
   取得したリストの1要素から隣接する2駅station1とstation2をそれぞれノードとしてグラフ内に追加するため、add_stationメソッドを呼び出します（47, 48行目）。
   add_stationメソッドではマップstationsに、引数に指定した駅が含まれているかを確認し、含まれている場合はその駅のID nodeIdを返却し、含まれない場合は新たにノードを登録して駅名とnodeIdをstationsに格納した後にnodeIdを返却します（59-66行目）。
   ノードの登録はcreate_nodeメソッドとupdate_nodeメソッドで行います。
   まず、create_nodeメソッドを、引数にタスクを識別するZooKeeperクラスタ内でユニークな名前nameを指定して呼び出し、その戻り値をnodeIdとします(63行目)。
   そしてupdate_nodeメソッドで、63行目で作成したノードの属性を更新します(64行目)。
   
  ③－2.追加した2駅の相互にエッジを張る
   add_stationメソッドで隣接する2駅station1とstation2を追加した後に、station1からstation2へ向けたエッジとstation2からstation1へ向けたエッジを張ります（51-54行目）。
   エッジを張るためにはcreate_edgeメソッドを利用します。
   第2引数に接続元のnodeIDを指定し、第3引数には接続元と接続先のnodeIDを格納したエッジを指定します。
   
  57行目のupdate_indexメソッドはmixをローカルで実行するものです。分散環境では利用しないでください。
  
 ④駅IDの表示
  ③-1で駅名と駅ID(nodeID)をstationsに格納しました。ここでは駅名を駅IDの昇順に並び替えて表示しています(68-70行目)。
  
 **search_route.py**
 
 search_route.pyでは、create_graph.pyで作成したグラフから2駅間の最短経路を計算します。
 使用するメソッドは、最短経路を計算するためのget_shortest_pathメソッドです。
  
  ① Jubatus Serverへの接続設定
   Jubatus Serverへの接続を行います（13行目）。
   Jubatus ServerのIPアドレス，Jubatus ServerのRPCポート番号を設定します。
   
  ②クエリーの準備
   最短経路を計算するためのクエリーを準備します(16, 17行目)。
   最短経路を計算するためのget_shortest_pathメソッドに必要なshortest_path_queryを作成します(17行目)。
   types.shortest_path_queryの第1引数に接続元の駅ID、第2引数に接続先の駅IDを設定します。第3引数で指定したホップ以内に発見できなかった場合、結果は切り詰められます。
   またクエリーはあらかじめadd_shortest_path_queryで登録しておく必要があります。
   
  ③最短経路の計算
   ②で作成したshortest_path_queryを指定して、get_shortest_pathを呼び出し、最短経路の計算をします(20行目)。
   
  ④結果の表示
   ③で取得した最短経路で通過する駅を駅IDと関連付けて表示しています(23-29行目)。


------------------------------------
3.3.4.4.3. サンプルプログラムの実行
------------------------------------

［Jubatus Serverでの作業］

**サーバの起動**

jubagraphを起動します。

::

 $ jubagraph --configpath train_route.json 


［Jubatus Clientでの作業］

Jubatus 0.4.0 + Pythonクライアントをインストールしてください。

**グラフの作成**

鉄道の接続を表すグラフを作成します。

::

 $ ./create_graph.py
 === Station IDs ===
 0       品川
 1       大崎
 4       田町
 ...
 139     中野
 144     四ツ谷
 147     御茶ノ水
 ```

 駅名に対応する駅ID(グラフ上のnode ID) が出力されます。

**経路の探索**


2つの駅IDから最短経路を検索します。

::

 $ ./search_route.py 0 144
 Pseudo-Shortest Path (hops) from 0 to 144:
   0     品川
   4     田町
   7     浜松町
   10    新橋
   13    有楽町
   16    東京
   19    神田
   147   御茶ノ水
   144   四ツ谷


