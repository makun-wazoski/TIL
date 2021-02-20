# envコマンド
「envコマンド」とは、設定されている環境変数を表示するためのコマンド。  
envは「environment（環境）」の略称。  
このコマンドで全環境変数を表示できる。
  
```
[ec2-user@ip-172-31-23-189 ~]$ env | grep SECRET_KEY_BASE
SECRET_KEY_BASE='secret_key_base'
```
