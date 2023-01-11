## クラス設計について(Web班第三回勉強会)

#### テーマのコンセプト
ウェブ言語も含めプログラミング言語において、クラスと言う構造は必須と言っても良い程仕様に含まれている。ただ、未経験の段階から独学でプログラミングを学んでいると、そう言うものがある、と言う事は分かっても、じゃあ実際にどういう使い方をし、具体的にそれを用いる事でどう言ったメリットがあるか、と言うのは中々に掴み辛い様に思える、実際自分がそうであった。今回このテーマを掲げたのはそう言った部分に起因する。

#### OOP(オブジェクト指向プログラミング)
クラスを語る上でどうしても外せないのがオブジェクト指向と言う考え方。
- 実物なんかに例えるから分かり辛くなる。
- 続く...

#### クラスの実装
- 基本的にクラスとして書かれたコードはそれだけでは何も実行されない、定義するだけである。インスタンス化(主に`new`と言うキーワードを用いる)と言う過程を経て、初めてその実体を持つ事になる。

<details>
<summary>クラスの定義とインスタンス化</summary>
  
```php
class SomeClass{
  // 内には主に値(プロパティ)、処理(メソッド)と言ったものが定義される。
}

/* インスタンス化、この`$instance`がクラスの実体となる */
$instance = new SomeClass();
```

</details>

#### クラスを構成する主な要素
クラスは、主にプロパティとメソッドと言う二つの要素によって構成される。

- プロパティ
  - インスタンス変数(クラスを`new`等でインスタンス化した際、実体を持つ変数)
  - クラス変数(`static`修飾子が付いたプロパティ、定義した時点で実体を持つ)
- メソッド
  - インスタンスメソッド(クラスをインスタンス化した際、実体を持つメソッド)
  - クラスメソッド(`static`修飾子が付いたメソッド、定義した時点で実体を持つ)
  - コンストラクタ(クラスをnew等でインスタンス化した際に実行されるメソッド)
  - デストラクタ(インスタンスを破棄する際、実行されるメソッド。今回は詳細については割愛する)


#### クラスの構造と効能
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
$instance.getprivateField(); /* コンストラクタで代入された「コンストラクタに渡す値」が取得される */
```
</details>

> それぞれthisと言うキーワードが頻出するが、これはそのクラスそのもの(実際にはインスタンス)を示す。
  
それぞれ違う言語なので仕様や記述方法による多少の違いはあるが、大体こんな似通った感じになる。と同時に、ここでクラスと言う構造を用いる大きなメリットの一つが既に示されている。まず、上記のコードのインスタンス変数は両者とも`private`と言う修飾子が与えられている。この修飾子が与えられたインスタンス変数は、このクラスの外部からは一切触れる事の出来ない状態になっていると言う事になり、要はクラス外部からは直接的に操作する事が出来ない。

```php
$instance.privateField = "何かしらの値";
```

こう言う事は出来ない。これがどういう事かと言うと、一言で言えば値のスコープ(有効範囲)がクラス内部に限定される、言い方を変えるとクラス内部の処理にのみに依存する、と言う事になる。

続いて、`getprivateField`を見て欲しい。これは`public`と言うアクセス修飾子が与えられ、上述したインスタンス変数をそのまま返している。`public`は、クラス外部からもアクセス出来る事を意味する。つまり、`privateField`は、クラス外部からは値を取得する事のみ可能で、代入や削除等が出来ない、と言う事になる。この仕組みは`カプセル化`と呼ばれ、`getprivateField`は一般的に`getter`と呼ばれる。これにより、`privateField`は一般的な手続き型の変数と比べて影響範囲が分かり易く限定されるため、不正な値や想定外の値になる事が極端に少なくなり、バグの温床となり辛くなる(もちろんクラス内でその値を扱う処理が適切に設計されている事が前提となるが)。

##### アクセス修飾子の種類

- `public`::クラス外部からアクセスする事が可能
- `protected`::設定したクラス内部、またはそのクラスを継承したクラスでのみアクセスする事が可能
- `private`::クラス内部のみでアクセス可能

<details>
<summary>(作例)クライアントから受け取ったリクエスト(GETとかPOSTとか)を処理して適切なレスポンスヘッダを返すクラス</summary>

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
  
</details>

コードの中身は理解出来なくとも問題無い。要はResponseと言うクラスがあって、中身はそれに関連する値と処理を丸め込んでいる、と言う事だけ理解出来ていれば良い。こうする事で、少なくともレスポンスヘッダに関する処理と値はこのクラスにある、と言う事が分かり、コードの整理整頓に一役買っている。クラスと言う構造のもう一つの利点として、関連する処理とその値をひとまとめに出来る、と言う点が挙げられる(関心の分離)。上記の例では、例えばブラウザから要求された処理に対して返す必要がある値、レスポンスヘッダ`$_headers`やステータスコード`$_status_code`をプロパティとして定義し、それを設定する処理や、最終的にブラウザに返す処理をメソッドとして定義してある。つまりレスポンスヘッダを返す機能を持つパーツは、全てこのクラスに集約されている事になる。繰り返しになるが、この様に関連する値と処理を一つの構造体として定義出来るのが、`カプセル化`に続く、もう一つのクラスのメリットであると言えよう。

極論を言えばほとんどの構造、例えば`namespace`等、他の構造体、もうちょいミクロな粒度で言えば関数や配列なんかも、<em>コードを整理整頓して何がどこにあるか分かり易くする</em>、と言うのが主目的である。逆に言えば、プログラミングに置いて、それは極めて重要なファクターと言えるワケだ。


#### ここまでのまとめ
- クラスは、主として値(プロパティ)と処理(メソッド)によって構成される。
- 構成された要素はそれぞれスコープ(有効範囲)を自分で定義する事が出来る。
- 上記の機能を利用し、クラス内で定義したプロパティ(インスタンス変数)の値を外部から守る事が出来る(カプセル化)。
- 関係し合うデータ(インスタンス変数)と処理(メソッド)を纏める一つの粒度(固まり)まとめる事が出来る(関心の分離)。

#### クラスは関数ライブラリではない！
- 

#### どこでインスタンスを生成すべきか
要はどこで`new`するか、と言う事。これは開発するアプリケーションやその設計にもよるので、一概にコレ、と言う正解は無い。そのインスタンスを使うクラスのコンストラクタで生成し、そのクラスのプロパティに格納する、と言うのが最もシンプルなセオリーであろう。

<details>
<summary>他のクラス(インスタンス)を使うクラスのセオリー</summary>
  
```php
class SampleWithUseInstance
{
    private $_someInstance; /* インスタンスを格納するプロパティ */

    /* コンストラクタでこのクラスで使うクラスのインスタンスを生成し、プロパティに格納している */
    public function __construct($params)
    {
        $this->__someInstance = new SomeClass();
    }
    
```
  
</details>
