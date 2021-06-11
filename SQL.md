# SQLとは
SQLとは、「Structured Query Language」の略で、直訳すると「構造化問い合わせ言語」。　　
コンピュータ言語のひとつだが、プログラミング言語ではない。  
リレーショナルデータベース(RDB)のデータを操作するための言語。  
データベースへ指示を出す言語は「SQL文」と呼び、命令文を組み合わせて処理を実施する。  
またSQL文はANSIやISOが規格化しているため、「Oracle Database」「Microsoft SQL Server」「My SQL」などでもほぼ同じように利用できる。
対話型の操作でコンパイル不要。大量のデータを効率よく操作できるので、使い勝手の良い言語である。  
<br>

## SQLで何ができる？
SQLでは次のような処理ができる。  

- データの条件検索  
検索の際に詳細な条件を指定できる。効率よくデータを検索できるため便利。　　  

- データの取得・登録・更新・削除  
データの条件検索と組み合わせると、特定の行や列を指定してデータの取得が容易に実施できる。また登録や更新、削除も同様に指定の行にのみ実施していける。  
  
- テーブルの作成・削除・変更  
テーブルとはデータを格納する表のこと。必要に応じてテーブルの作成・変更・削除ができるので、思い通りにテーブルを構成できまる。  
  
膨大な量のデータがRDBによって整理されているため、これらの処理も効率よく実施可能である。  
<br>

参照記事
https://udemy.benesse.co.jp/development/system/intro-sql.html　　
<br>

## コマンド
#### MySQLバージョン確認コマンド
```
接続前
% mysql —version
接続後
mysql> select version();
```
<br>

#### mysqladminやstatusを使えばより詳細に色々わかる
```
接続前
% mysqladmin version
接続後
mysql> status
```
<br>

#### 文字コードに関するシステム変数の確認コマンド
```
mysql> SHOW VARIABLES like ‘char%’;
一部確認（例）
mysql> show variables like “character_set_database”
```
<br>

#### 文字コード変更コマンド(uft8mb4に対応）
```
mysql> SET character_set_xxxxxxxx=utf8mb4;
（もともとutf8のものをutf8mb4に変更）
```
<br>

#### 作成済みデータベースの文字コード変更
```
mysql> ALTER DATABASE <データベース名> DEFAULT CHARACTER SET utf8mb4;
```
<br>

#### 新規作成データベースの文字コード変更
```
mysql> CLEATE DATABASE <データベース名> DEFAULT CHARACTER SET utf8mb4;
```
<br>

#### 作成済みテーブルの文字コード変更
```
mysql> ALTER TABLE <テーブル名> DEFAULT CHARACTER SET utf8mb4;
```
<br>

#### 新規作成テーブルの文字コード変更
```
mysql> CLEATE TABLE <テーブル名> DEFAULT CHARACTER SET utf8mb4;
```
<br>

#### 作成済みカラムの文字コード変更
```
mysql> ALTER TABLE <テーブル名> MODIFY <カラム名> <型>  CHARACTER SET utf8mb4 […];
```
<br>

#### テーブル削除コマンド
```
mysql> drop table <テーブル名>;
```
