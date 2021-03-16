# Unicorn
全世界に公開されるサーバ上でよく利用されるアプリケーションサーバー。  
rails s コマンドの代わりに「unicorn_rails コマンド」で起動することができる。  
EC2サーバにssh接続しUnicornを起動することで全世界からアクセスできるようになる。  
  
# Unicornの設定
### Unicornのインストール
```Gemfile
group :production do
  gem 'unicorn','5.4.1'
end
```
  
```
bundle install
```
