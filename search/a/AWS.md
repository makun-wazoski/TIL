# Amazon Route53
Amazon Route53はDNS(ドメインネームサービス）のこと。（以下R53)  
（※補足：DNSとは、Webブラウザに入力した「http://wwww.xxxxxxxxx.com/」のようなURLを。「IPアドレス」に変換する仕組みのこと）  
R53では、アクセスしてもらいたいアドレスを、実際に使用しているEC2やS３などのAWSサービスのエンドポイント（接続点）に結び付ける。  
これを名前解決という。  
R53ではドメインの取得できる。  
ドメインの取得とは、「masa.jp]や「uhouho.com」などのドメインの使用権を買って、レジストラ（ドメインを担当する組織）に申請すること。
R53は、トラフィックが一つのエンドポイントに集中しないようにしたり、一箇所のサービスに障害が生じたとき速やかに他の場所に切り替える機能もあり、ルーティングを柔軟に管理できる。