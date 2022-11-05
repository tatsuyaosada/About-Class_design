### クラス設計について(Web班第三回勉強会)

#### テーマのコンセプト
ウェブ言語も含めプログラミング言語において、クラスと言う構造は必須と言っても良い程仕様に含まれている。ただ、未経験の段階から独学でプログラミングを学んでいると、そう言うものがある、と言う事は分かっても、じゃあ実際にどういう使い方をし、具体的にそれを用いる事でどう言ったメリットがあるか、と言うのは中々に掴み辛い様に思える、実際自分がそうであった。今回このテーマを掲げたのはそう言った部分に起因する。

##### クラスの実装
- 基本的にクラスとして書かれたコードはそれだけでは何も実行されない、定義するだけである。インスタンス化(主に`new`と言うキーワードを用いる)と言う過程を経て、初めてその実体を持つ事になる。
> ベースとなる考え方だけなら関数に近いイメージと捉えても差し支えないだろう(関数も定義しただけでは意味を持たず、実行処理を記述する事で初めて意味を成す)。

```php
class SomeClass{
  // 内には主に値(プロパティ)、処理(メソッド)と言ったものが定義される。
}

/* インスタンス化、この`$instance`がクラスの実体となる */
$instance = new SomeClass();
```

##### クラスを構成する主な要素
- プロパティ
  - インスタンス変数(クラスを`new`等でインスタンス化した際、実体を持つ変数)
  - クラス変数(`static`修飾子が付いたプロパティ、定義した時点で実体を持つ)
- メソッド
  - インスタンスメソッド(クラスをインスタンス化した際、実体を持つメソッド)
  - クラスメソッド(`static`修飾子が付いたメソッド、定義した時点で実体を持つ)
  - コンストラクタ(クラスをnew等でインスタンス化した際に実行されるメソッド)
  - デストラクタ(インスタンスを破棄する際、実行される。言語によるが、最近はガベージコレクションが有能なため、あまり自前で実装する事は無い)

###### クラスの構造と効能(javascriptとPHPを例として)
<details>
<summary>javascript</summary>

```javascript
class ClassWithPrivateField {
  #privateField; /* インスタンス変数 */
  
  /* コンストラクタ、newでインスタンス化する際にコレが実行される */
  constructor(params) {
    this.#privateField = params;
  }
  
   /* インスタンスメソッド */
   hogehoge_method() {}
   
   /* インスタンス変数を取得するメソッド(getterと呼ばれる) */
   getprivateField(){ return this.#privateField; }
}

const instance = new ClassWithPrivateField("コンストラクタに渡す値");
/* インスタンスメソッドの実行 */
instance.hogehoge_method();
instance.getprivateField(); /* コンストラクタで代入された「コンストラクタに渡す値」が取得される */
```
</details>

<details>
<summary>PHP</summary>

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
    
    /* インスタンス変数を取得するメソッド */
    public function getprivateField() { return $this->privateField; }
}

$instance = new ClassWithPrivateField("コンストラクタに渡す値");
/* インスタンスメソッドの実行 */
$instance.hogehoge_method();
instance.getprivateField(); /* コンストラクタで代入された「コンストラクタに渡す値」が取得される */
```
</>
  
> それぞれ違う言語なので仕様による多少の違いはあるが、大体こんな感じに似通った感じになる。と同時に、ここでクラスと言う粒度を用いる最も重要な仕様の一つが言語化されている。上記のコードのインスタンス変数は両者とも`private`と言う修飾子が与えられている。このインスタンス変数は、このクラスの外部からは全く触れる事の出来ない状態になっていると言う事になり、要はクラス外部(正確にはインスタンスの外部)からは一切触る事が出来ないため、一般的な手続き型の変数と比べ、堅牢度が高い。

> 続いて、`getprivateField`を見て欲しい。これは`public`と言うアクセス修飾子が与えられ、上述したインスタンス変数をそのまま返している。`public`は、クラス外部からもアクセス出来る事を意味する。つまり、`privateField`は、クラス外部からは値を取得する事のみ可能で、代入や削除等が出来ない、と言う事になる。極論言ってしまうと、クラスを用いる理由の半分以上は、この仕組みを用いるため、と言っても良いかもしれない。この`getprivateField`は一般的に`getter`と呼ばれる。

###### アクセス修飾子の種類

- `public`::どこからでもアクセスする事が可能
- `protected`::設定したクラス内部、またはそのクラスを継承したクラスでのみアクセスする事が可能
- `private`::クラス内部のみでアクセス可能
- `static`についてはここでは詳細は省く

###### ざっくりと、クラスを使うメリット

- 上述した様に、変数の値を"ある程度"想定しない値から守る事が出来る。
- 相互に関係し合うデータ(インスタンス変数)と処理(メソッド/関数)を纏める一つの粒度(固まり)まとめる事が出来る。


##### (例)クライアントから受け取ったリクエスト(GETとかPOSTとか)を処理して適切なレスポンスヘッダを返すクラス

```php
class Response
{

    protected
        $_headers,
        $_headers_sent,
        $_protocol,
        $_status_code,
        $_status_text;

    /**
     * 
     */
    public function __construct()
    {
        $this->_status_code = 200;
        $this->_status_text = "OK";
        $this->_protocol = isset($_SERVER["SERVER_PROTOCOL"]) ? $_SERVER["SERVER_PROTOCOL"] : "HTTP/1.1";
        $this->_headers = (array) function_exists("apache_response_headers") ? apache_response_headers() : [];
        /* $this->_headers_sent = (bool) function_exists("headers_sent") && headers_sent(); */
    }

    /**
     * 設定されたステータスコードとヘッダを送信する
     * @param array $headers
     * @param type $status_code
     * @param type $status_text
     * @throws HeaderAlreadySentException
     */
    public function send(array $headers = [], $status_code = null, $status_text = null)
    {
        if ($this->_headers_sent)
            throw new HeaderAlreadySentException("Headers already sent,cant send headers");
        $this->_headers = array_merge($this->_headers, $headers);
        $this->_status_code = !is_null($status_code) ? $status_code : $this->_status_code;
        $this->_status_text = !is_null($status_text) ? $status_text : $this->_status_text;

        function_exists("http_response_code") ?
            http_response_code($this->_status_code) :
            header($this->_protocol . " " . $this->_status_code . " " . $this->_status_text);
        function_exists("header_register_callback") ? header_register_callback(function () {
            array_map(function ($name, $value) {
                header($name . ": " . $value);
            }, array_keys($this->_headers), array_values($this->_headers));
        }) : array_map(function ($name, $value) {
            header($name . ": " . $value);
        }, array_keys($this->_headers), array_values($this->_headers));
        $this->_headers_sent = true;
    }

    /**
     * 
     * @return bool
     */
    public function isHeadersSent()
    {
        return (bool) $this->_headers_sent;
    }

    /**
     * 
     * @param type $name
     * @param type $value
     * @throws HeaderAlreadySentException
     */
    public function setHeader($name, $value)
    {
        if ($this->isHeadersSent())
            throw new Exception("Headers already sent,can not set headers");
        $this->_headers[$name] = $value;
    }

    /**
     * 
     * @param array $headers
     * @return array
     */
    public function setHeaders(array $headers)
    {
        return array_map([$this, rtrim(__FUNCTION__, "s")], array_keys($headers), array_values($headers));
    }

    /**
     * 
     * @param type $code
     * @param type $text
     */
    public function setStatusCode($code = 200, $text = "")
    {
        $this->_status_code = (int) $code;
        $this->_status_text = (string) $text;
    }

    /**
     * 
     * @param type $name
     * @throws Exception
     */
    public function removeHeader($name)
    {
        if ($this->isHeadersSent())
            throw new Exception("Headers already sent,can not set headers");
        if (array_key_exists($name, $this->_headers))
            unset($this->_headers[$name]);
        if (function_exists("header_remove"))
            header_remove($name);
    }

    /**
     * 
     * @return type
     */
    public function getHeaders()
    {
        return (array) $this->_headers;
    }

    /**
     * 
     * @param type $name
     * @return type
     */
    public function getHeader($name)
    {
        return array_key_exists($name, $this->_headers) ? $this->_headers[$name] : null;
    }
}

```

> コードの中身は理解出来なくとも問題無い。要はResponseと言うクラスがあって、中身はそれに関連する処理を丸め込んでいる、と言う事だけ理解出来ていれば良い。
こうする事で、少なくともレスポンスヘッダに関する処理と値はこのクラスにある、と言う事が分かり、コードの整理整頓に一役買っている。
> 極論を言えばほとんどの構造、例えば`namespace`や、もうちょいミクロな粒度で言えば関数や配列なんかも、<em>コードを整理する</em>、と言うのが主目的である。逆に言えば、プログラミングに置いて何がどこにあるか、と言うのは極めて重要なファクターと言えるワケだ。
