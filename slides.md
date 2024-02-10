---
theme: ./theme
class: text-center
highlighter: prism
lineNumbers: false
info: |
 # phpカンファレンス関西2024
drawings:
  persist: true
transition: none
title: autoloadのはなし
colorSchema: light
aspectRatio: '16/9'
canvasWidth: 960
mdc: true
layout: center
---

<h1 id="title" style="text-align: left;font-size: 2.5rem; line-height: 3rem">
なんで、ファイル名とクラス名を揃えるの？
<br>知っておきたいautoloadのはなし
</h1>
<div>
  <ul style="list-style: none; text-align: left;">
      <li>PHPカンファレンス関西2024</li>
      <li>2024/2/11</li>
      <li>赤塚啓紀</li>
  </ul>
</div>

---

# 自己紹介

<div>
  <table>
    <tr>
      <td>名前</td>
      <td>赤塚啓紀</td>
    </tr>
    <tr>
      <td>所属</td>
      <td>株式会社オフショア</td>
    </tr>
    <tr>
      <td>仕事</td>
      <td>神戸の会社で医療機関向けの業務支援システムを作っています</td>
    </tr>
    <tr>
      <td>X</td>
      <td>
          <a href="https://twitter.com/aki_artisan">
            <div style="display: flex; align-items: flex-end;">
              <img src="/images/icon.jpg" width=50px></img>
              あかつか(@aki_artisan)
            </div>
          </a>
      </td>
    </tr>
    <tr>
      <td>趣味</td>
      <td>散歩、ハイキング、甘いもの</td>
    </tr>
    <!-- <tr>
      <td></td>
      <td>昨年関西に引っ越してきてきました<br>システム開発歴はだいたい3年です</td>
    </tr> -->
  </table>
</div>


---

# `Target class [...] does not exist.`

- このようなエラーを見たことはないでしょうか？

- Laravelで開発していた時の私が遭遇したエラーです。

---

# 解決方法

- ファイル名とクラス名を揃えるようにしたらうまく動くようになった

- なんでファイル名とクラス名を揃えると動くようになったのか、よくわかっていませんでした

- 次に同じエラーが出たときにちゃんと治せるか不安

ちゃんと理解しておけば、怖くありません


---
layout: center
---

# というわけで本題です。

---
layout: cover
---

# 知っておきたい<br>autoloadのはなし

---

# 最初に結論から
- なんでファイル名とクラス名を揃えるの？
  - → autoloadのルール(<a href="https://www.php-fig.org/psr/psr-4/">PSR-4</a>)がそうなっているから

---
layout: center
---

<span text-8xl>🤔</span>

---
layout: center
---

# ひとつひとつ説明します。

---
layout: cover
---

# 用語の説明

---

# 1. autoloadとは

未定義のクラス（インターフェース、トレイトも含む）を呼び出したときに、PHPが自動的にクラスの定義を書いたファイルを読み込んでくれるしくみ

---

# 2. PSR-4ってなに？

autoloadのルール

PHP-FIGという団体が決めていて、デファクトスタンダード（事実上の標準）

依存関係を管理するcomposerというツールがこのルールを満たすようにautoloadを実装してくれています

ということは、使う時はこのルールを守れば良いということ！

---

# 2. PSR-4ってなに？

<div>
具体的には、クラス名（名前空間とクラス名）とファイルパス（ディレクトリ構成とファイル名）を揃えるということです。

→今はわからなくても大丈夫です！後ほどコードを見ながら説明します。

<div style="text-align: center;">
  <a class="image-link" href="https://zenn.dev/aki_artisan/articles/psr4-translation-ja" style="display:inline-block;">
    <img src="https://res.cloudinary.com/zenn/image/upload/s--PukdpG2p--/c_fit%2Cg_north_west%2Cl_text:notosansjp-medium.otf_55:PSR-4%2520%25E3%2582%25AA%25E3%2583%25BC%25E3%2583%2588%25E3%2583%25AD%25E3%2583%25BC%25E3%2583%2580%25E3%2583%25BC%25EF%25BC%2588%25E6%2597%25A5%25E6%259C%25AC%25E8%25AA%259E%25E8%25A8%25B3%25EF%25BC%2589%2Cw_1010%2Cx_90%2Cy_100/g_south_west%2Cl_text:notosansjp-medium.otf_37:%25E3%2581%2582%25E3%2581%258B%25E3%2581%25A4%25E3%2581%258B%2Cx_203%2Cy_121/g_south_west%2Ch_90%2Cl_fetch:aHR0cHM6Ly9zdG9yYWdlLmdvb2dsZWFwaXMuY29tL3plbm4tdXNlci11cGxvYWQvYXZhdGFyL2I5NDMyMGUwMTEuanBlZw==%2Cr_max%2Cw_90%2Cx_87%2Cy_95/v1627283836/default/og-base-w1200-v2.png" alt="PSR-4 オートローダー（日本語訳）" width="400px">
  </a>
</div>

</div>

---

# 3. 名前空間ってなに？

- クラス名の前につけることができる名前のこと

- クラスの集まり同士を分けるために使う

---

# 3. 名前空間ってなに？

```php
namespace App\Models;

class Person
{
    // ...
}
```

<div grid="~ cols-2 gap-4">
  <div>

  同じ名前空間の時

  ```php
  namespace App\Models;

  $person = new Person();
  ```
  </div>
  <div>

  別の名前空間の時

  ```php
  $person = new \App\Models\Person();
  ```
  </div>
</div>

---
layout: cover
---

# autoloadの使い方をコードで<br>理解する

---

# フォルダ構成

<div>
以下のようなフォルダ構成とします。

```txt
.
├── public
│   └── index.php
├── src
│   └── Models
│        └── Person.php
├── vendor
├── composer.json
└── composer.lock
```
</div>

---

# 1. 同じファイルにクラスを定義している場合（autoloadを使わない場合）
```php
<?php
// クラスを使うファイルと同じファイルにクラスを定義している

class Person
{
    public function greet(string $name) : void
    {
        echo 'Hello ' . $name . '!';
    }
}

$person = new Person();
$person->greet('Taro'); // Hello Taro!
```

---

# 1. 同じファイルにクラスを定義している場合（autoloadを使わない場合）

- 実行結果

  ```shell
  $ php public/index.php
  Hello Taro!
  ```

---

# 2. requireでクラス定義ファイルを読み込む場合（autoloadを使わない場合）

- `src/Models/Person.php`

  ```php
  <?php
  namespace App\Models;

  class Person
  {
      public function greet(string $name) : void
      {
          echo 'Hello ' . $name . '!';
      }
  }
  ```

---

# 2. requireでクラス定義ファイルを読み込む場合（autoloadを使わない場合）

- `public/index.php`
  ```php
  <?php
  require __DIR__ . '/../src/Models/Person.php';
  // 使う側のファイルからクラスの定義が書いてあるファイルを読み込む

  $person = new App\Models\Person();
  $person->greet('Taro'); // Hello Taro!
  ```
- 使うクラスが増えると、<br>requireするファイルが増えてしまいます。

---

# 3. autoloadを使う場合（composer）

- `src/Models/Person.php`は同じ
  ```php
  <?php
  namespace App\Models;

  class Person
  {
      public function greet(string $name) : void
      {
          echo 'Hello ' . $name . '!';
      }
  }
  ```

---

# 3. autoloadを使う場合（composer）

- `public/index.php`

  ```php
  <?php
  require __DIR__ . '/../vendor/autoload.php';

  $person = new App\Models\Person();
  $person->greet('Taro');
  ```

---

# 3. autoloadを使う場合（composer）

- `composer.json`に設定を追加します。<br>（新しい名前空間にオートロードを追加する時のみ）
  ```json
  {
      "autoload": {
          "psr-4": {
              "App\\": "src/"
          }
      }
  }
  ```
- `App`という名前空間を`src`ディレクトリに紐づける

---

# 3. autoloadを使う場合（composer）

- この記述を追加した後は、以下のコマンドを実行する必要があります
  ```shell
  $ composer dump-autoload
  ```

※同じディレクトリにある他のクラスが読み込めていたら、`composer.json`の設定や`composer dump-autoload`は不要です。

（この辺りはAsk the speaker で聞いてね）

---

# 3. autoloadを使う場合（composer）

- 実行結果
  ```shell
  $ php public/index.php
  Hello Taro!
  ```

---

# 3. autoloadを使う場合（composer）

- ファイルを直接指定していなくても`Person`クラスが読み込めています。
- クラス名で読み込むファイル名が決まるので、ファイル名とクラス名を揃える必要があります。

ここまでが、autoloadの動きの部分です。

---

# 4. autoloadを実現する仕組み

- 次はこの仕組みをどうやって実現しているのかを見ていきます。

---

# 4. autoloadを実現する仕組み

- autoloadを使うには`spl_autoload_register`という関数を使って、<br>「**クラスが未定義だったらこれをしてね**」という処理を登録しておく

- これをしておかないとPHPは何をして良いかわからず、結果として「クラスが見つからない」というエラーが出る

---

# 4. autoloadを実現する仕組み

- composerを使う場合は、`vender/autoload.php`や、`vendor/composer/autoload_real.php`にこの処理が書かれている

ので、気になったら見てみてください。（私にはちょっと難しかったです。）

---
layout: cover
---

# autoloadを自作する

---

# autoloadを自作する

- せっかくなので、今回はcomposerに頼らず、`spl_autoload_register`を使って動きを確かめてみましょう！

---

# spl_autoload_register

```php
spl_autoload_register(function ($class) {
    // requireなどでクラスの定義が書いてあるファイルを読み込む処理
});
```

- `spl_autoload_register`は関数を引数に取る関数

- クラスが未定義だったときに実行して欲しい処理を登録できる

- 登録する関数の引数(`$class`)には、読み込もうとしているクラスの名前空間付きクラス名が入る（`App\Models\Person`）

---

# app/Models/Person.php

```php
<?php
namespace App\Models;

class Person
{
    public function greet(string $name) : void
    {
        echo 'Hello ' . $name . '!' . PHP_EOL;
    }
}

```

---

# public/index.php

```php
<?php
require __DIR__ . '../lib/autoload.php';

$person = new App\Models\Person();
$person->greet('Taro');
```

---

# lib/autoload.php

```php
<?php
spl_autoload_register(function ($class) {
    $prefix = 'App\\'; // トップレベル名前空間
    $base_dir = __DIR__ . '/../src/'; // Appに紐づけるディレクトリ
    $len = strlen($prefix); // トップレベル名前空間の長さ
    $relative_class = substr($class, $len);
    // トップレベル名前空間を除いたクラス名
    $file = $base_dir
        . str_replace('\\', '/', $relative_class)
        . '.php';
    require_once $file;
});

```

---

# 実行結果

```shell
$ php public/index.php
Hello Taro!
```

---

# 例：`$class = 'App\Models\Person'`のとき

```php
<?php
spl_autoload_register(function ($class) {
    $prefix = 'App\\';
    $base_dir = __DIR__ . '/../src/';
    $len = strlen($prefix); // 4
    $relative_class = substr($class, $len); // Models\Person
    $file = $base_dir 
        . str_replace('\\', '/', $relative_class)
        . '.php'; // ../src/Models/Person.php
    require_once $file;
});

```

なので、ファイル名とクラス名が不一致だと読み込めない

---

# autoloadを自作する

<div>
このようにして<code>spl_autoload_register</code> を使ってautoloadを実装できました。

※実用ではcomposerに頼った方が良いです。
</div>

---
layout: cover
---

# 知っておくと嬉しいこと

---

# 1. 業務で役立つ

<ul>
<li>エラーが出ても、ちゃんとどうすれば良いかわかった上で対応できるので、問題解決が早くなる</li>
<li>フレームワークのソースコードがどこにあるかわかる</li>
</ul>

---

# 2. 勉強しているときにサクッとautoloadをかける

<ul>
<li>本で勉強してみたいときなど、autoloadを正しく設定できると、すぐに本題に入れるようになる！</li>
</ul>


---

# まとめ

- **なんでファイル名とクラス名を揃えるの？**
  - autoloadのルール（PSR-4）がそうなっているから

- **autoloadとは**
  - 未定義のクラスを呼び出したときに、PHPが自動的にクラスが定義されているファイルを読み込んでくれるしくみ

- **autoloadを使うにはcomposerを使うのが無難**


---
layout: center
---

# 今日の話はブログにまとめてあるので、<br>文字で読みたい方はそちらもどうぞ！

<div style="text-align: center;">
  <a class="image-link" href="https://zenn.dev/aki_artisan/articles/php-autoloading" style="display:inline-block; width:400px">
    <img src="https://res.cloudinary.com/zenn/image/upload/s--y-V4ZV4a--/c_fit%2Cg_north_west%2Cl_text:notosansjp-medium.otf_55:php%25E3%2581%25AEautoload%25E3%2582%2592%25E7%2590%2586%25E8%25A7%25A3%25E3%2581%2599%25E3%2582%258B%2Cw_1010%2Cx_90%2Cy_100/g_south_west%2Cl_text:notosansjp-medium.otf_37:%25E3%2581%2582%25E3%2581%258B%25E3%2581%25A4%25E3%2581%258B%2Cx_203%2Cy_121/g_south_west%2Ch_90%2Cl_fetch:aHR0cHM6Ly9zdG9yYWdlLmdvb2dsZWFwaXMuY29tL3plbm4tdXNlci11cGxvYWQvYXZhdGFyL2I5NDMyMGUwMTEuanBlZw==%2Cr_max%2Cw_90%2Cx_87%2Cy_95/v1627283836/default/og-base-w1200-v2.png" alt="phpのautoloadを理解する" width="400px">
  </a>
</div>



---
layout: image-right
image: ./public/images/note-thanun-WOo1qkKIHiI-unsplash.jpg
---

# Contact

<div style="position: relative; height: calc(552px - 80px - 56px)">
<div style="position: absolute; top: 50%; left: 0%; transform: translateY(-50%); -webkit- transform: translateY(-50%);">
  <p>Twitter: <a href="https://twitter.com/aki_artisan">@aki_artisan</a></p>
  <p>GitHub: <a href="https://github.com/akinoriakatsuka">akinoriakatsuka</a></p>
  <p>Ask the speaker きてね！</p>
</div>
</div>

---
layout: cover
background: ./public/images/note-thanun-WOo1qkKIHiI-unsplash.jpg
---

# ご清聴ありがとうございました
