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
ーーー
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


















[ec2-user@ip-172-xx-xx-xxx www]$ git clone コピーしたURL
