# SQLとは
SQLとは、「Structured Query Language」の略で、直訳すると「構造化問い合わせ言語」。　　
コンピュータ言語のひとつだが、プログラミング言語ではない。  
リレーショナルデータベース(RDB)のデータを操作するための言語。  
データベースへ指示を出す言語は「SQL文」と呼び、命令文を組み合わせて処理を実施する。  
またSQL文はANSIやISOが規格化しているため、「Oracle Database」「Microsoft SQL Server」「My SQL」などでもほぼ同じように利用できる。
対話型の操作でコンパイル不要。大量のデータを効率よく操作できるので、使い勝手の良い言語である。  
<br>
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
<br>

## SQLの基礎知識：リレーショナルデータベース
リレーショナルデータベース(RDB)とは、互いに関連付けられた表にデータを格納して扱うデータベースのこと。  
現在、一般的になっているデータベースシステムの一つである。  

正確には「RDB」はデータを管理する手法を指すが、厳密に定義の違いを気にする人はほぼいない。  
また昨今のSQL文を使うデータベースはほとんどRDBであるため、RDBはデータベースのことを指すといっても過言ではないと言える。　　

実際に2018年の時点でRDBのシェアが世界のデータベースシステムの4割を占めるという調査結果も出ている。　　
RDBの上位シェアに位置しているのが「Oracle」「MySQL」「Microsoft SQL Server」「PostgreSQL」である。  
<br>
<br>

## 知っておきたいSQLの3種類の言語と命令文
SQL文の代表的な命令文をみてみる。  
SQLの命令文はDDL・DML・DCLの３種類で構成されている。  

- **DDL(データ定義言語：Data Definition Language)**  

DDLは、主にデータベースの定義や作成に関わる命令文です。

「CREATE(データベースやテーブル作成)」「DROP(データベースやテーブル削除)」「ALTER(データベースやテーブルの変更)」などが挙げられます。

DML(データ操作言語：Data Manipulation Language)
データの取得・登録・更新・削除などのデータ操作に関わる言語をDMLと呼びます。よく使われる言語は「SELECT(データ取得)」「UPDATE(データ更新)」「DELETE(データ削除)」「INSERT(データ挿入)」などです。

DCL(データ制御言語：Data Control Language)
DCLは、主にトランザクションの制御やデータベースへのアクセスを制御するためのコマンドです。代表的なコマンドは「BEGIN(トランザクション開始)」「COMMIT(実行した処理の確定)」「ROLLBACK(データの戻し)」などです。トランザクションについては次項でお伝えします。

参照記事
https://udemy.benesse.co.jp/development/system/intro-sql.html  
<br>
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
