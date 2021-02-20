# grepコマンド
「grepコマンド」は、環境変数などを検索するときに使う。  
使い方は「grep （検索対象）」となる。
  
```
[ec2-user@ip-172-31-23-189 ~]$ env | grep SECRET_KEY_BASE
SECRET_KEY_BASE='secret_key_base'
```
