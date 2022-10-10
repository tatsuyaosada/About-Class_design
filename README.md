#### クラス設計について(Web班第三回勉強会/長田)

###### クラスを構成する要素
- プロパティ
  - インスタンス変数(クラスを`new`等でインスタンス化した際、実体を持つ変数)
  - クラス変数(`static`修飾子が付いたプロパティ、定義した時点で実体を持つ)
- メソッド
  - インスタンスメソッド(クラスをインスタンス化した際、実体を持つメソッド)
  - クラスメソッド(`static`修飾子が付いたメソッド、定義した時点で実体を持つ)

> 注意しなければならない点は、クラス変数、クラスメソッドはインスタンス変数を使った処理が出来ないと言う事。

###### ざっくりと、クラスを使うメリットとデメリット

- 相互に関係し合うデータと処理(メソッド/関数)をまとめる事が出来る
- 
