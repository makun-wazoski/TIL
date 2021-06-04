# DNS
DNSDNSとは「Domain Name System(ドメインネームシステム）」の略であり、ドメインをIPアドレスに変換する仕組みのことを指す。  
DNSの役割は電話帳に似ている。  
電話を掛けるためには、電話番号が必要だが、無機質な数字の羅列を大量に記憶するのは大変。　　
そのため、私たちは電話帳で名前と番号を紐付けて管理している。  
DNSも同様である。IPアドレスのまま管理するのは大変だが、「google.com」のように意味のある文字列に変換すると人間にとって管理がしやすくなる。  
そしてDNSのサービスを提供するサーバーを「DNSサーバー」と呼ぶ。  
DNSサーバーは、ブラウザから入力されたドメインに対して、紐付けられたIPアドレスを返す。  
<br>

<img width="700" alt="qiita-square" src=https://i.gyazo.com/68ee868cc7685bf84fdfa135a828757e.png>
<br>

以上をまとめると、ブラウザからドメインでアクセスする際には、以下のような処理が裏側で行われている。

- ブラウザからドメインでアクセス
- DNSサーバーにドメインを問い合わせる
- ドメインからIPアドレスを検索
- IPアドレスをブラウザに渡す
- ブラウザからIPアドレスでアクセス
- 該当サイトの表示
<br>

<img width="700" alt="qiita-square" src=https://i.gyazo.com/e0b6c312889c6aa22ae18f2414a283f3.png>
<br>
<br>

# DNSレコード
ドメインとIPアドレスを紐づけるゾーンファイルに書かれた一行ごとの情報をDNSレコードと呼び、これには複数の種類がある。

参照記事
https://www.weblab.co.jp/staff/other/10116.html  
https://www.onamae.com/option/dnsrecord/  
https://help.onamae.com/answer/7883  
https://qiita.com/leomaro7/items/7d717077dd9165357f16 → 簡易的（わかりやすい）  
https://wa3.i-3-i.info/word12284.html　　→ ピヨ太


