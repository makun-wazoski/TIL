# コマンド色々

ターミナルにてMySQLというRDBMSを使用してSQLを実行。　　

MySQLを使用するには、ターミナルからログイン。下記のコマンドを実行。　　
<br>

```
# ホームディレクトリに変更
% cd

# MySQLに接続
% mysql -u root
```
<br>

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
