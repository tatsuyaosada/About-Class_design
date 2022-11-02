#### クラス設計について(Web班第三回勉強会)

###### クラスを構成する主な要素
- プロパティ
  - インスタンス変数(クラスを`new`等でインスタンス化した際、実体を持つ変数)
  - クラス変数(`static`修飾子が付いたプロパティ、定義した時点で実体を持つ)
- メソッド
  - インスタンスメソッド(クラスをインスタンス化した際、実体を持つメソッド)
  - クラスメソッド(`static`修飾子が付いたメソッド、定義した時点で実体を持つ)
  - コンストラクタ(クラスをnew等でインスタンス化した際に実行されるメソッド)
  - デストラクタ(インスタンスを破棄する際、実行される。言語によるが、最近はガベージコレクションが有能なため、あまり自前で実装する事は無い)

> 注意しなければならない点は、クラス変数、クラスメソッドはインスタンス変数を使った処理が出来ないと言う事。

###### ざっくりとしたクラスの構造と効能

###### javascript

```javascript
class ClassWithPrivateField {
  #privateField; /* インスタンス変数 */
  
  /* コンストラクタ、newでインスタンス化する際、実行される */
  constructor(params) {
    this.#privateField = params;
  }
  
   /* インスタンスメソッド */
   hogehoge_method() {}
   
   /* インスタンス変数を取得するメソッド */
   getprivateField(){ return this.#privateField; }
}

const instance = new ClassWithPrivateField("コンストラクタに渡す値")
```

```php
class ClassWithPrivateField
{
    private $privateField; /* インスタンス変数 */

    /* コンストラクタ、newでインスタンス化する際、実行される */
    public function __construct($params)
    {
        $this->privateField = $params;
    }
    
    /* インスタンスメソッド */
    protected function hogehoge_method() : bool{}
    
    public function getprivateField() { return $this->privateField; }
}
```

###### ざっくりと、クラスを使うメリットとデメリット

- 相互に関係し合うデータと処理(メソッド/関数)を纏める一つの粒度(固まり)まとめる事が出来る。
- 
