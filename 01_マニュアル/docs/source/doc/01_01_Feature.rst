===================================================
1.1. Jubatusの特徴
===================================================


1.1.1 ビッグデータとその分析手法
===================================================

ビッグデータとその分析手法は、大きく2つのタイプに分けることができます。

 ・ 蓄積されたビッグデータ（ストック型）に対する一括高速分析（バッチ処理）
 
 ・ 連像的に発生しているデータの流れ（フロー型）に対する逐次高速分析（リアルタイム処理）

Jubatusは、後者のフロー型ビッグデータのリアルタイム分析を対象とし、オンライン機械学習による深い分析という付加価値を提供するフレームワークです。

機械学習とは、自然言語など人間向けの非構造情報を自動的にカテゴライズする分析や、情報の推薦、予測、新たな関係性の発見など、明確には定式化されていない処理を、人間に代わりコンピュータ（機械）に行わせる処理を意味します。


1.1.2 Jubatusの特徴
==================================================

Jubatusは「大量データ」を「常に素早く」「深く分析」することを狙った分散基盤技術です。

大量のデータを複数のサーバに振り分け並列かつ逐次的に処理させ、複数のサーバ間で緩やかに途中処理結果を共有することにより、サーバ間の通信オーバーヘッドの削減や安定性の向上を実現し、高いリアルタイム性と解析精度を確保しています。

「大量」「速い」「深い」という3つの要件は基本的にトレードオフの関係にあります。

Jubatusは、不確実で曖昧さが残る中で迅速な判断を下すニーズにこたえること、常に判断材料を集め続けて、遅滞なく判断するための設計思想を徹底的に検討しました。

具体的には、深い分析（機能）とスケーラビリティ（非機能）を分離します。深い解析の設計はオンライン機械学習のロジックを「エンジン・CPU」に例えて、着脱可能な分析モジュールとして持続的に拡充可能とします。

スケーラビリティの設計は、共通的な基盤「シャーシ・マザーボード」とみて、共通の枠組みで分析モジュールを搭載しスケールさせる設計とします。

 ・ 深い分析
  Jubatusは、分類、回帰、統計、推薦、グラフマイニングなど多くの深い分析を実現しています。またバッチ型機械学習と同程度の学習精度を実現しています。

 ・ スケーラビリティ
  Jubatusは、廉価なコモディティサーバを多数並べた分散処理（スケールアウト）が可能です。理論的には100台程度までスケールアウトが可能です。Jubatusでは、コモディティサーバで10万qpsを超えるスケーラブルな機械学習を実現しています。
    
 ・ リアルタイム
  Jubatusは、データをためることなく瞬時に学習を行います。全世界のTwitter書込み（最高8,000TPS）を2台のサーバで分析することが可能です。


従来の機械学習エンジン単体では、正確な分析は行えるものの、扱うデータサイズは小～中規模で、バッチ処理（レイテンシ大）、個別の開発が必要なケースが一般的です。

これに対してJubatusでは、許容範囲内で誤差を容認しつつ、ビッグデータを高速処理する仕組みを共通仕様で開発することができます。

ユーザ・プロセスは、JubatusクライアントAPIを用いてスクリプト言語や汎用プログラミング言語で記述することができます。オープンソースコミュニティに対する外部の貢献により、Python、Ruby、Javaなど多様な言語バインディングが利用可能です。


1.1.3 Hadoopとの比較
=============================================

Hadoop/MahoutとJubatusの間には多くの共通点があります。これらはスケーラブルで、コモディティサーバ上で動作します。

ストック型ビッグデータに対する一括高速分析（バッチ処理）を得意とするHadoopは、機械学習がMapReduceパラダイムにあまり適合していないこともあり、洗練された機械学習機能を備えていません。Apache Mahout も Hadoop-based な機械学習プラットフォームですが、オンライン処理はスコープ外です。

Jubatusは、データ分析に特化しています。 Jubatusは、すべてのデータをメモリ上で処理し、オンラインで処理し、高いスループットと低いレイテンシを実現します。
この機能を実現するために、Jubatusは分散環境におけるゆるいモデルの共有と同期というアーキテクチャを採用しているのです。
