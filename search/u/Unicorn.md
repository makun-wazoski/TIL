# Unicorn
「Unicorn」とは、全世界に公開されるサーバ上で良く利用されるアプリケーションサーバー。rails sコマンドの代わりに「unicorn_railsコマンド」で起動することができる。  
  
EC2サーバにssh接続しUnicornを起動することで全世界からアクセスできるようにする。  

<img width="500" alt="qiita-square" src="https://i.gyazo.com/f714f9429c268099d33149d9d5b28870.png">  
  
# Unicornの設定
### Unicornのインストール
---
Gemfile
``` 
group :production do
  gem 'unicorn','5.4.1'
end
```
  
```
% bundle install
```
  
### 「unicorn.rb」の作成
---
「config/unicorn.rb」作成内容  
```
#サーバ上でのアプリケーションコードが設置されているディレクトリを変数に入れておく
app_path = File.expand_path('../../', __FILE__)

#アプリケーションサーバの性能を決定する
worker_processes 1

#アプリケーションの設置されているディレクトリを指定
working_directory app_path

#Unicornの起動に必要なファイルの設置場所を指定
pid "#{app_path}/tmp/pids/unicorn.pid"

#ポート番号を指定
listen 3000

#エラーのログを記録するファイルを指定
stderr_path "#{app_path}/log/unicorn.stderr.log"

#通常のログを記録するファイルを指定
stdout_path "#{app_path}/log/unicorn.stdout.log"

#Railsアプリケーションの応答を待つ上限時間を設定
timeout 60

#以下は応用的な設定なので説明は割愛

preload_app true
GC.respond_to?(:copy_on_write_friendly=) && GC.copy_on_write_friendly = true

check_client_connection false

run_once = true

before_fork do |server, worker|
  defined?(ActiveRecord::Base) &&
    ActiveRecord::Base.connection.disconnect!

  if run_once
    run_once = false # prevent from firing again
  end

  old_pid = "#{server.config[:pid]}.oldbin"
  if File.exist?(old_pid) && server.pid != old_pid
    begin
      sig = (worker.nr + 1) >= server.worker_processes ? :QUIT : :TTOU
      Process.kill(sig, File.read(old_pid).to_i)
    rescue Errno::ENOENT, Errno::ESRCH => e
      logger.error e
    end
  end
end

after_fork do |_server, _worker|
  defined?(ActiveRecord::Base) && ActiveRecord::Base.establish_connection
end
```

ここまで編集したら、リモートリポジトリに「commit→push」する。
※別にブランチを切っている場合は、masterブランチにmergeする。

### EC2 にローカルの全内容を反映
---
以下のコマンドで権限を付与する。  
```
#mkdirコマンドで新たにディレクトリを作成
[ec2-user@ip-172-xx-xx-xxx ~]$ sudo mkdir /var/www/

#作成したwwwディレクトリの権限をec2-userに変更
[ec2-user@ip-172-xx-xx-xxx ~]$ sudo chown ec2-user /var/www/
```
次に、GitHubから「リポジトリURL」を取得し、クローンする。  
※「HTTPS」でOK、URLをコピーする。  

コードをクローン
```
[ec2-user@ip-172-xx-xx-xxx ~]$ cd /var/www/
[ec2-user@ip-172-xx-xx-xxx www]$ git clone コピーしたURL
```

# 本番環境での設定
### EC2の能力拡張
---
現状動かしているEC2のインスタンスではコンピューターの能力が足りず、Gemのインストール時などにエラーが発生する可能性がある。  
具体的には、コンピューターの処理能力に関するメモリが足らない。  
これは、無料で動かせるインスタンスの限界であるため.  
そこで、今後の設定を行う前に「Swapファイル」と言うメモリを増強する処理を行う。


### Swapファイル
---
「Swapファイル」とは、メモリの要領を一時的に増やす為に準備されるファイルのこと。  
コンピューターが処理を行う際、メモリと呼ばれる場所に処理内容が一時的に記録される。  
メモリは要量が決まっており、要領を超えてしまうとエラーで処理が止まってしまう。  
EC2はデフォルトではSwapファイルを用意していない為、これを準備することでメモリ不足の処理エラーを防ぐ。  

### Swapファイルの領域を広げる
---
```
[ec2-user@ip-172-xx-xx-xxx ~]$ cd
```
↓
```
# 処理に時間がかかる可能性があるコマンドです

[ec2-user@ip-172-xx-xx-xxx ~]$ sudo dd if=/dev/zero of=/swapfile1 bs=1M count=512

# しばらく待って、以下のように表示されれば成功です
512+0 レコード入力
512+0 レコード出力
536870912 バイト (537 MB) コピーされました、 7.35077 秒、 73.0 MB/秒
```
↓
```
[ec2-user@ip-172-xx-xx-xxx ~]$ sudo chmod 600 /swapfile1
```
↓
```
[ec2-user@ip-172-xx-xx-xxx ~]$ sudo mkswap /swapfile1

# 以下のように表示されれば成功
スワップ空間バージョン1を設定します、サイズ = 524284 KiB
ラベルはありません, UUID=74a961ba-7a33-4c18-b1cd-9779bcda8ab1
```
↓
```
[ec2-user@ip-172-31-25-189 ~]$ sudo swapon /swapfile1
```
↓
```
[ec2-user@ip-172-xx-xx-xxx ~]$ sudo sh -c 'echo "/swapfile1  none        swap    sw              0   0" >> /etc/fstab'
```
これで、Swapファイルの領域を確保できた。

# EC2内でgemをインストール
### 必要なgemのインストール
```
# クローンしたディレクトリに移動
[ec2-user@ip-172-xx-xx-xxx www]$ cd  /var/www/開発中のアプリケーション

# rubyのバージョンを確認
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ ruby -v
ruby 2.6.5p114 (2019-10-01 revision 67812) [x86_64-linux]
```
次に、本番環境でgemを管理するためのbundlerをインストール。  
まず、ローカルで開発してきたアプリケーションでどのバージョンのbundlerが使われていたのか確認する。  

ローカル
```
# 開発中のアプリケーションのディレクトリで実行

% bundler -v
Bundler version 2.1.4
```
ローカルと同じバージョンのbundlerをEC2側に導入。
```
# 「2.1.4」の箇所は、ローカルで確認したbundlerのバージョンを導入します
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ gem install bundler -v 2.1.4

# 以下のコマンドは、処理に数分以上かかる場合があります
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ bundle install
```

# 環境変数の設定
〜省略〜

# Railsを起動する（本番環境）
本番環境でRailsを起動するには「unicorn_railsコマンド」を使う。  
VSCodeで「databese.yml」の本番環境設定を編集。  
本番環境のmysqlの設定に合わせるため、ローカルのdatabase.ymlを編集。

config/database.yml（ローカル）
```
production:
  <<: *default
  database:（※こちらは編集しないでください）
  username: root
  password: <%= ENV['DATABASE_PASSWORD'] %>
  socket: /var/lib/mysql/mysql.sock
```
編集を「commit→push」
リモートリポジトリが更新されたため、サーバ上のアプリケーションにも反映。
GitHubの内容をEC2に反映
```
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>] git pull origin master
```

### RAILS_ENV=production
---
「RAILS_ENV=production」とは、本番環境でコマンド実行するときに付くオプションのこと。
実行しようといているコマンドは、「RAILSのENV（環境）がproduction（本番環境）ですよ」と言う意味。
EC2内のデータベースを作成
```
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ rails db:create RAILS_ENV=production
Created database '<データベース名>'
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ rails db:migrate RAILS_ENV=production
```
 ※もしここで「Mysql2::Error: Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock'」というエラーが起こった場合、データベースが起動していない可能性あり。  
 「sudo systemctl start mariadb」というコマンドをターミナルから打ち込み、mysqlの起動を試してみる。

ここまできたら、Railsを起動してみる。
```
[ec2-user@ip-172-xx-xx-xxx ~]$ cd /var/www/[リポジトリ]
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ bundle exec unicorn_rails -c config/unicorn.rb -E production -D
```

### アセットファイルをコンパイルする
---
「アセットファイル」とは、画像・CSS・JavaScript等を管理しているファイルのこと。  
このアセットファイルを圧縮し、そのデータを転送する処理を「コンパイル」という。
この作業を行わないと、本番間環境でCSSが反映されずにビューが崩れていまったり、エラーでブラウザが表示されない、などの問題が生じる。  
開発中はアクセスごとにアセットファイルを自動的にコンパイルする仕組みが備わっているが、本番モードの時にはパフォーマンスを重視するため、アクセス毎にコンパイルが実行されないようになっている。

```
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ rails assets:precompile RAILS_ENV=production
```

# Railsを再起動させる
**本番環境での作業**
①
  - プロセスを確認する
  - プロセスを止める
②
  - 「unicorn_railsコマンド」を実行する

Unicornプロセスの確認をする。

### psコマンド
「psコマンド」とは、現在動いているプロセスを確認するためのコマンド。
```
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ ps aux | grep unicorn
```
すると、以下のようにプロセスが表示される。
```
ec2-user 17877  0.4 18.1 588472 182840 ?       Sl   01:55   0:02 unicorn_rails master -c config/unicorn.rb -E production -D
ec2-user 17881  0.0 17.3 589088 175164 ?       Sl   01:55   0:00 unicorn_rails worker[0] -c config/unicorn.rb -E production -D
ec2-user 17911  0.0  0.2 110532  2180 pts/0    S+   02:05   0:00 grep --color=auto unicorn
```
大事なのは左から２番目の列。  
ここに表示されるのがプロセスのid(PIDと言う）になる。  
「unicorn_rails master」と表示されているプロセスがUnicornのプロセス本体。  

# killコマンド
killコマンドは、現在動いているプロセスを停止させるためのコマンド。
```
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ kill <確認したunicorn rails masterのプロセスid>
```
実行したプロセスを再度表示させ、終了できていることを確認。
```
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ ps aux | grep unicorn
...
ec2-user 17918  0.0  0.2 110532  2180 pts/0    S+   02:05   0:00 grep --color=auto unicorn
```
上記状態なら停止が完了。  
※プロセスが1つだけ残っているのは、「ps aux | grep unicorn」コマンドのプロセス。  
このコマンドが「unicornに関するすべてのプロセスを表示する」という役割なので、「unicornのプロセスを検索ために使った ”ps aux | grep unicorn” もカウントされている」ということになる。  

※「master」のプロセスをkillすると、「worker」のプロセスも一緒にkillさる。

最後に、Railsを再起動するコマンドを実行。  
今回はコマンドの先頭に「RAILS_SERVE_STATIC_FILES=1」というオプションをつける。

### RAILS_SERVE_STATIC_FILES=1
「RAILS_SERVE_STATIC_FILES=1」は、Railsがコンパイルされたアセットを見つけられるように指定する役割がある。
```
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ RAILS_SERVE_STATIC_FILES=1 unicorn_rails -c config/unicorn.rb -E production -D
```
ブラウザで http://＜Elastic IP＞:3000/ にアクセスして、サイトが正常に表示されているか確認する。

# Railsの起動がうまくできなかった時
エラーログを確認する。

### lessコマンド
---
「lessコマンド」を使うとファイルの中身を確認できる。  
「catコマンド」も同様の役割をする。
```
[ec2-user@ip-172-31-23-189 <リポジトリ名>]$ less log/unicorn.stderr.log
I, [2016-12-21T04:01:19.135154 #18813]  INFO -- : Refreshing Gem list
I, [2016-12-21T04:01:20.732521 #18813]  INFO -- : listening on addr=0.0.0.0:3000 fd=10
E, [2016-12-21T04:01:20.734067 #18813] Mysql2::Error::ConnectionError: Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)
/var/www/furima/shared/bundle/ruby/2.6.0/gems/mysql2-0.5.3/lib/mysql2/client.rb:90:in `connect'
/var/www/furima/shared/bundle/ruby/2.6.0/gems/mysql2-0.5.3/lib/mysql2/client.rb:90:in `initialize'
/var/www/furima/shared/bundle/ruby/2.6.0/gems/activerecord-6.0.2.1/lib/active_record/connection_adapters/mysql2_adapter.rb:24:in `new'
/var/www/furima/shared/bundle/ruby/2.6.0/gems/activerecord-6.0.2.1/lib/active_record/connection_adapters/mysql2_adapter.rb:24:in `mysql2_connection'
/var/www/furima/shared/bundle/ruby/2.6.0/gems/activerecord-6.0.2.1/lib/active_record/connection_adapters/abstract/connection_pool.rb:889:in `new_connection'
```
※ログファイルは「一番下から最新のログ」が表示される。  
”「shiftキー」＋「G」”を実行すると、一番下まで一瞬で移動できる。

### ブラウザに「We’re sorry, but 〜」と表示されている時の対処法
---

<img width="500" alt="qiita-square" src="https://i.gyazo.com/9e611717604b7d8d7b1595ce83ddd3a7.png">  

このような表示が出ている場合、まず「production.log」を確認。

### production.log
「production.log」とは、サーバーログを記録する場所で、いわば「EC2内での出来事を記録している場所」のことを指す。  
ローカルにおいてrails sコマンドでアプリケーションを実行したときも、さまざまなログが表示される。  
それと同様の役割を果たす。
```
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ less log/production.log

（production.logの表示）
```
また、もう少しログを見やすく確認できるツールとして「tail -fコマンドというものもある。

### tail -fコマンド
最新のログを10行分だけ表示してくれる。
```
[ec2-user@ip-172-xx-xx-xxx <リポジトリ名>]$ tail -f production.log
```









