# git add .
git add .　コマンドでコミットしたいファイルを「インデックス」あるいは「ステージングエリア」と呼ばれる領域に追加できる。  
インデックスにはファイルの変更箇所などが記録せれる。  

### インデックスとは
変更修正が、一時的に保存される場所。  
インデックスに保存されている内容がcommitの対象になる。  

### addとは
追加するファイルや変更修正をインデックスに登録して、commitの対象にすることを意味する。  

GitHubDesktopは、変更修正した項目は全てインデックスへ追加されるようになっている。  
下記のようにチェックを外すことで、インデックスへ追加されないようにできる。
  
  
<img width="500" alt="qiita-square" src="https://i.gyazo.com/0e84ceba3d5276e3ba7964cf17c8aeb0.png"> 
  
  
  
ターミナルでcommitする際は以下の流れ
```
% git add .
% git commit -m "コミット名"
```

# 空のコミットの生成
ファイルの変更履歴が存在しない場合。  
設定した環境変数だけ本番環境に反映させたい場合など。
```
% git commit --allow-empty -m "空のcommit"
```