# DRY（Don't Repeat Yourself)とは
DRYとは**Don't Repeat Yourself**の略で、**「同じ情報を繰り返して定義しない」**という考え方。  
ここでいう情報とは、メソッドであったり、入れうtなどのデータの集合体などを指す。  
**同じコードを繰り返し記述しない**という捉え方で差し支えない。  
DRYを意識するメリットとして、可読性の向上や、コード量を減らすことによりアプリケーションの動作が早くなることが挙げられる。  

<img width="900" alt="image" src="https://i.gyazo.com/4f72f045c67ab8e9da507068335e5b17.png">
<br>

### 【例】

**DRYが意識できていないコード***

```
def select_birthplace
  prefecture = ['北海道', '青森', '...(省略)...', '沖縄']
  num = gets.to_i
  birthplace = prefecture[num]
  puts birthplace
end

def select_address
  prefecture = ['北海道', '青森', '...(省略)...', '沖縄']
  num = gets.to_i
  address = prefecture[num]
  puts address
end

def select_work_address
  prefecture = ['北海道', '青森', '...(省略)...', '沖縄']
  num = gets.to_i
  work_address = prefecture[num]
  puts work_address
end

select_birthplace
select_address
select_work_address
```

上記の例では、`prefecture`という配列の情報を各メソッド内で定義している。  
加えて、`num`という変数に代入する`ges.to_i`や`address`も、代入する値までは同じだが、各メソッドで定義している。  
これでは、「同じ情報を繰り返し定義しなさい」というDRYの考え方に沿っていない。  
<br>

**修正版**  

```
prefecture = ['北海道', '青森', '...(省略)...', '沖縄']

def select_prefecture(prefecture, category)
  num = gets.to_i
  if category == 'birthplace'
    birthplace = prefecture[num]
    puts birthplace
  elsif category == 'address'
    address = prefecture[num]
    puts address
  elsif category == 'work_address'
    work_address = prefecture[num]
    puts work_address
  end
end

select_prefecture(prefecture, 'birthplace')
select_prefecture(prefecture, 'address')
select_prefecture(prefecture, 'work_address')
```
<br>

### 解説
まず、完全に重複しているコードを抜き出す。  

```
 prefecture = ['北海道', '青森', '...(省略)...', '沖縄']
 num = gets.to_i
 ```
 配列である`prefecture`から一つの要素を取り出すという作業を一つにまとめる。  
 
 作業のメソッドを`select_prefecture`とする。  
 そのメソッド内で、`num = gets.to_i`を実行する。  
 
 ```
 prefecture = ['北海道', '青森', '...(省略)...', '沖縄']

def select_prefecture(prefecture)
 num = gets.to_i
end

select_prefecture(prefecture)
```
<br>

最後に、実行結果は条件分岐を設定。第2引数に`category`を設定して条件分岐を行う。  

```
prefecture = ['北海道', '青森', '...(省略)...', '沖縄']

def select_prefecture(prefecture, category)
  num = gets.to_i
  if category == 'birthplace'
    birthplace = prefecture[num]
    puts birthplace
  elsif category == 'address'
    address = prefecture[num]
    puts address
  elsif category == 'work_address'
    work_address = prefecture[num]
    puts work_address
  end
end

puts 


select_prefecture(prefecture, 'birthplace')
select_prefecture(prefecture, 'address')
select_prefecture(prefecture, 'work_address')
```
