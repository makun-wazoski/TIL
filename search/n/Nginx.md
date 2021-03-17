# Nginx
「Nginx」とは、Webサーバーの一種。  
ユーザーのリクエストに対して静的コンテンツのみ取り出し処理を行い、動的コンテンツの生成はアプリケーションサーバに依頼する。

# Nginx導入
今回バージョンは「Nginx1」を導入
```
[ec2-user@ip-172-xx-xx-xxx ~]$ sudo amazon-linux-extras install nginx1
```
Is this ok [y/d/N]:と出てきたら、yを選択。

# Nginxの設定ファイルを編集
Nginxの設定は「設定項目X　設定値x；」という形式で入力する。
```
[ec2-user@ip-172-xx-xx-xxx ~]$
sudo vim /etc/nginx/conf.d/rails.conf
```

/etc/nginx/conf.d/rails.conf
```
upstream app_server {
  # Unicornと連携させるための設定
  server unix:/var/www/アプリケーション名/tmp/sockets/unicorn.sock;
}

# {}で囲った部分をブロックと呼ぶ。サーバの設定ができる
server {
  # このプログラムが接続を受け付けるポート番号
  listen 80;
  # 接続を受け付けるリクエストURL ここに書いていないURLではアクセスできない
  server_name Elastic IP;

  # クライアントからアップロードされてくるファイルの容量の上限を2ギガに設定。デフォルトは1メガなので大きめにしておく
  client_max_body_size 2g;

# 接続が来た際のrootディレクトリ
  root /var/www/アプリケーション名/public;

# assetsファイル(CSSやJavaScriptのファイルなど)にアクセスが来た際に適用される設定
  location ^~ /assets/ {
    gzip_static on;
    expires max;
    add_header Cache-Control public;
  }

  try_files $uri/index.html $uri @unicorn;

  location @unicorn {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
    proxy_pass http://app_server;
  }

  error_page 500 502 503 504 /500.html;
}
```
入力を終えたら「escキー」→「:wq」で保存

次に、Nginxの権限を変更
```
[ec2-user@ip-172-xx-xx-xxx ~]$ cd /var/lib
[ec2-user@ip-172-xx-xx-xxx lib]$ sudo chmod -R 775 nginx  
```
設定完了。  
Nginx設定ファイルを再読み込み起動する。
```
[ec2-user@ip-172-xx-xx-xxx lib]$ cd ~
[ec2-user@ip-172-xx-xx-xxx ~]$ sudo systemctl reload nginx
[ec2-user@ip-172-xx-xx-xxx ~]$ sudo systemctl start nginx
```

# Unicornの設定変更
config/unicorn.rb
```
（省略）

listen 3000
↓変更
listen "#{app_path}/tmp/sockets/unicorn.sock"

（省略）
```
「commit→push」  
↓本番環境へ反映
```
# 開発中のアプリケーションに移動
[ec2-user@ip-172-xx-xx-xxx ~]$ cd /var/www/開発中のアプリケーション

# GitHubの内容をEC2に反映させる
[ec2-user@ip-172-xx-xx-xxx <レポジトリ名>]$ git pull origin master
```
次は、Unicornを再起動  
①プロセス確認  
②プロセスkill  
③unicorn_railsコマンドを実行  

①プロセス確認 
```
[ec2-user@ip-172-xx-xx-xxx <レポジトリ名>]$ ps aux | grep unicorn
```

②プロセスkill  
```
[ec2-user@ip-172-xx-xx-xxx <レポジトリ名>]$ kill プロセス番号
```

③unicorn_railsコマンドを実行  
```
[ec2-user@ip-172-xx-xx-xxx <レポジトリ名>]$ RAILS_SERVE_STATIC_FILES=1 unicorn_rails -c config/unicorn.rb -E production -D
```
ブラウザで確認。  
**Elastic IPでアクセス**

# IPアドレスにアクセスしてもエラーが出る時
### 「５０２ but gateway」と出た時
```
[ec2-user@ip-172-xx-xx-xxx <レポジトリ名>]$ sudo less /var/log/nginx/error.log
```
でエラーログを確認

  location @unicorn {:a
