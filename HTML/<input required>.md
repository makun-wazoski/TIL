# <input required>タグ
<input>要素のrequired属性を指定すると、 その入力項目が入力必須であることをブラウザに知らせることができる。  

- 属性  
```
required
入力必須を指定する
```
<br>

```
HTMLソース

<form action="xxx.php" method="post">
<fieldset>
名前:
<input type="text" name="yourname" required> ※入力必須<br>
<input type="submit" value="送信">
</fieldset>
</form>
```
<br>

<img height="200" width="400" alt="Gyazo-image" src="https://i.gyazo.com/80147988b00a8f3e4c1190dbc3b95ccb.png">  
↓  
<img height="200" width="400" alt="Gyazo-image" src="https://i.gyazo.com/471b0c67dd96bcc3586756e98b68af67.png">  
<br>

参照  
http://www.htmq.com/html5/input_required.shtml
