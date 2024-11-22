---
title: ファイルを見つける
teaching: 25
exercises: 20
---

::::::::::::::::::::::::::::::::::::::: objectives

- `grep` を使って、テキストファイルから特定のパターンに一致する行を選択する。
- `find` を使って、特定のパターンに一致する名前のファイルやディレクトリを見つける。
- 1つのコマンドの出力を別のコマンドのコマンドライン引数として使用する。
- 「テキスト」ファイルと「バイナリ」ファイルの意味、およびなぜ多くの一般的なツールが後者を適切に処理できないかを説明する。

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- ファイルをどのように見つけることができますか？
- ファイル内の内容をどのように見つけることができますか？

::::::::::::::::::::::::::::::::::::::::::::::::::

多くの人が「Google」を「探す」という意味の動詞として使うのと同じように、Unixプログラマーは「grep」という言葉をよく使います。
`grep` は「global/regular expression/print」の略で、初期のUnixテキストエディターで一般的に使用された操作の一連の名前です。
また、非常に便利なコマンドラインプログラムの名前でもあります。

`grep` は、特定のパターンに一致するファイル内の行を検索して表示します。
例として、*Salon* マガジンの[1998年のコンテスト](https://web.archive.org/web/19991201042211/http://salon.com/21st/chal/1998/01/26chal.html)から取った3つの俳句を含むファイルを使用します。
（作者 Bill Torcaso、Howard Korder、Margaret Segall に敬意を表します。俳句エラーメッセージのアーカイブ：
[ページ1](https://web.archive.org/web/20000310061355/http://www.salon.com/21st/chal/1998/02/10chal2.html)、
[ページ2](https://web.archive.org/web/20000229135138/http://www.salon.com/21st/chal/1998/02/10chal3.html)。）
この例では、`writing` サブディレクトリで作業します。

```bash
$ cd
$ cd Desktop/shell-lesson-data/exercise-data/writing
$ cat haiku.txt
```

```output
The Tao that is seen
Is not the true Tao, until
You bring fresh toner.

With searching comes loss
and the presence of absence:
"My Thesis" not found.

Yesterday it worked
Today it is not working
Software is like that.
```

単語「not」を含む行を見つけてみましょう：

```bash
$ grep not haiku.txt
```

```output
Is not the true Tao, until
"My Thesis" not found
Today it is not working
```

ここでは、`not` が検索しているパターンです。
`grep` コマンドは、指定したパターンに一致する行をファイル内で検索します。
使用方法は、`grep` と検索したいパターン、その後に検索対象のファイル名を入力します。

出力は、`not` を含む3行です。

デフォルトでは、`grep` は大文字小文字を区別して検索を行います。
また、選択した検索パターンが完全な単語である必要はありません。
次の例で確認してみましょう。

`The` というパターンを検索してみます。

```bash
$ grep The haiku.txt
```

```output
The Tao that is seen
"My Thesis" not found.
```

今回は、`The` を含む2行が出力され、そのうちの1行には「Thesis」という単語内に検索パターンが含まれています。

`grep` に `-w` オプションを指定すると、単語境界を基準に一致する行を制限できます。

このレッスンの後半では、`grep` の検索動作を大文字小文字の区別に関して変更する方法も説明します。

```bash
$ grep -w The haiku.txt
```

```output
The Tao that is seen
```

単語境界は、行の開始や終了も含むため、スペースに囲まれた文字だけではありません。
場合によっては、単一の単語ではなくフレーズを検索したいこともあります。
`grep` ではフレーズを引用符で囲むことでこれが可能です。

```bash
$ grep -w "is not" haiku.txt
```

```output
Today it is not working
```

単語1つだけの場合は引用符が不要ですが、複数単語を検索する場合は引用符を使用することで、検索対象の用語やフレーズと検索対象のファイルを区別しやすくなります。
残りの例では、引用符を使用します。

便利なオプションとして、`-n` を使用すると、一致する行の行番号を表示できます。

```bash
$ grep -n "it" haiku.txt
```

```output
5:With searching comes loss
9:Yesterday it worked
10:Today it is not working
```

行番号5、9、10に「it」が含まれています。

他のUnixコマンドと同様に、オプション（フラグ）を組み合わせることができます。
例えば、「the」という単語を含む行を検索する場合、
オプション `-w` を使用して単語境界で一致する行を見つけ、`-n` を使用して行番号を表示することができます。

```bash
$ grep -n -w "the" haiku.txt
```

```output
2:Is not the true Tao, until
6:and the presence of absence:
```

次に、`-i` オプションを使って大文字小文字を区別せずに検索します：

```bash
$ grep -n -w -i "the" haiku.txt
```

```output
1:The Tao that is seen
2:Is not the true Tao, until
6:and the presence of absence:
```

さらに、`-v` オプションを使用して検索を反転し、「the」を含まない行を出力します。

```bash
$ grep -n -w -v "the" haiku.txt
```

```output
1:The Tao that is seen
3:You bring fresh toner.
4:
5:With searching comes loss
7:"My Thesis" not found.
8:
9:Yesterday it worked
10:Today it is not working
11:Software is like that.
```

`-r`（再帰）オプションを使用すると、`grep` はサブディレクトリ内の一連のファイルを再帰的にパターン検索できます。

次の例では、`shell-lesson-data/exercise-data/writing` ディレクトリ内で `Yesterday` を再帰的に検索してみます：

```bash
$ grep -r Yesterday .
```

```output
./LittleWomen.txt:"Yesterday, when Aunt was asleep and I was trying to be as still as a
./LittleWomen.txt:Yesterday at dinner, when an Austrian officer stared at us and then
./LittleWomen.txt:Yesterday was a quiet day spent in teaching, sewing, and writing in my
./haiku.txt:Yesterday it worked
```

`grep` には他にも多くのオプションがあります。
これらを確認するには、以下を入力します：

```bash
$ grep --help
```

```output
Usage: grep [OPTION]... PATTERN [FILE]...
Search for PATTERN in each FILE or standard input.
PATTERN is, by default, a basic regular expression (BRE).
Example: grep -i 'hello world' menu.h main.c

Regexp selection and interpretation:
  -E, --extended-regexp     PATTERN is an extended regular expression (ERE)
  -F, --fixed-strings       PATTERN is a set of newline-separated fixed strings
  -G, --basic-regexp        PATTERN is a basic regular expression (BRE)
  -P, --perl-regexp         PATTERN is a Perl regular expression
  -e, --regexp=PATTERN      use PATTERN for matching
  -f, --file=FILE           obtain PATTERN from FILE
  -i, --ignore-case         ignore case distinctions
  -w, --word-regexp         force PATTERN to match only whole words
  -x, --line-regexp         force PATTERN to match only whole lines
  -z, --null-data           a data line ends in 0 byte, not newline

Miscellaneous:
...        ...        ...
```

:::::::::::::::::::::::::::::::::::::::  challenge

## `grep`を使用する

次の出力を得るには、どのコマンドを使用しますか？

```output
and the presence of absence:
```

1. `grep "of" haiku.txt`
2. `grep -E "of" haiku.txt`
3. `grep -w "of" haiku.txt`
4. `grep -i "of" haiku.txt`

:::::::::::::::  solution

## 解答

正解は3です。`-w`オプションは、完全な単語の一致のみを検索します。
他のオプションも「of」が他の単語の一部として現れる場合に一致します。

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::  callout

## ワイルドカード

`grep` の真の力はそのオプションではなく、パターンにワイルドカードを含めることができる点にあります。
（これを技術的には**正規表現**と呼びます。`grep` の名前の中の「re」はこれを指します。）
正規表現は複雑かつ強力であり、複雑な検索を行いたい場合は、
[私たちのウェブサイト](https://librarycarpentry.org/lc-data-intro/01-regular-expressions.html)のレッスンをご覧ください。
ここではその一例を紹介します。次のようにして、2文字目が「o」である行を見つけることができます：

```bash
$ grep -E "^.o" haiku.txt
```

```output
You bring fresh toner.
Today it is not working
Software is like that.
```

`-E`オプションを使用し、パターンを引用符で囲むことで、シェルがそのパターンを解釈しようとするのを防ぎます。
（例えば、パターンに `*` が含まれている場合、シェルは `grep` を実行する前にそれを展開しようとします。）
パターン内の `^` は、行の先頭を基準に一致を固定します。
`.` は1文字を表し（シェルでの `?` と同様）、`o` は実際の「o」を表します。

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## 種の追跡

Leah は、次のような形式で保存された数百のデータファイルを1つのディレクトリに持っています：

```source
2012-11-05,deer,5
2012-11-05,rabbit,22
2012-11-05,raccoon,7
2012-11-06,rabbit,19
2012-11-06,deer,2
2012-11-06,fox,4
2012-11-07,rabbit,16
2012-11-07,bear,1
```

彼女は、種名を最初のコマンドライン引数として受け取り、ディレクトリ名を2番目の引数として受け取るシェルスクリプトを書きたいと考えています。
スクリプトは `<species>.txt` という1つのファイルを出力し、その中に日付とその種が各日に観察された数を記録します。
例えば、上記のデータを使用すると、`rabbit.txt` の内容は次のようになります：

```source
2012-11-05,22
2012-11-06,19
2012-11-07,16
```

以下の各行には、個々のコマンドまたはパイプが含まれています。
Leah の目標を達成するために、それらを1つのコマンドの中で並べ替えましょう：

```bash
cut -d : -f 2
>
|
grep -w $1 -r $2
|
$1.txt
cut -d , -f 1,3
```

ヒント：`man grep` を使用して、ディレクトリ内でテキストを再帰的に検索する方法を調べ、
`man cut` を使用して、1行の複数のフィールドを選択する方法を確認してください。

このようなファイルの例は
`shell-lesson-data/exercise-data/animal-counts/animals.csv`
にあります。

:::::::::::::::  solution

## 解答

```source
grep -w $1 -r $2 | cut -d : -f 2 | cut -d , -f 1,3 > $1.txt
```

実際には、2つの cut コマンドの順序を入れ替えても機能します。
コマンドラインで、cut コマンドの順序を変更して、それぞれのステップの出力を確認してみてください。

上記のスクリプトを次のように呼び出します：

```bash
$ bash count-species.sh bear .
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## 若草物語

Louisa May Alcott の *Little Women* を読み終えたあなたと友人は議論をしています。
4人の姉妹 Jo、Meg、Beth、Amy のうち、友人は Jo が最も多く言及されていると考えています。
しかし、あなたは Amy だと確信しています。
幸い、`LittleWomen.txt` というファイルに小説全体のテキストがあります
（`shell-lesson-data/exercise-data/writing/LittleWomen.txt`）。
`for` ループを使用して、各姉妹が言及された回数を集計するにはどうすればよいでしょうか？

ヒント：解決法の1つは、`grep` と `wc`、そして `|` を利用するかもしれません。
もう1つの解決法は、`grep` のオプションを活用するかもしれません。
プログラミングタスクを解決する方法は1つではないため、特定の解決法は正確な結果、優雅さ、可読性、速度の組み合わせによって選ばれます。

:::::::::::::::  solution

## 解答

```source
for sis in Jo Meg Beth Amy
do
    echo $sis:
    grep -ow $sis LittleWomen.txt | wc -l
done
```

別の、やや劣る解決法：

```source
for sis in Jo Meg Beth Amy
do
    echo $sis:
    grep -ocw $sis LittleWomen.txt
done
```

この解決法が劣る理由は、`grep -c` は一致した行の数のみを報告するためです。
この方法で報告される一致数は、1行に複数の一致がある場合に少なくなります。

洞察力のある観察者は、キャラクター名が章タイトルで全大文字で表示されることがある（例：'MEG GOES TO VANITY FAIR'）ことに気づくかもしれません。
これらもカウントしたい場合は、ケースを区別しない `-i` オプションを追加できます（ただし、この場合、どの姉妹が最も頻繁に言及されているかという答えには影響しません）。

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

`grep` はファイル内の行を検索しますが、`find` コマンドはファイルそのものを検索します。
`find` には多くのオプションがありますが、その最も簡単なものの使い方を説明するために、
以下に示す `shell-lesson-data/exercise-data` ディレクトリツリーを使用します。

```output
.
├── animal-counts/
│   └── animals.csv
├── creatures/
│   ├── basilisk.dat
│   ├── minotaur.dat
│   └── unicorn.dat
├── numbers.txt
├── alkanes/
│   ├── cubane.pdb
│   ├── ethane.pdb
│   ├── methane.pdb
│   ├── octane.pdb
│   ├── pentane.pdb
│   └── propane.pdb
└── writing/
    ├── haiku.txt
    └── LittleWomen.txt
```

`exercise-data` ディレクトリには1つのファイル `numbers.txt` と、4つのディレクトリ：
`animal-counts`、`creatures`、`alkanes`、および `writing` があり、それぞれにさまざまなファイルが含まれています。

まずは `find .` を実行してみましょう（`shell-lesson-data/exercise-data` フォルダ内でコマンドを実行してください）。

```bash
$ find .
```

```output
.
./writing
./writing/LittleWomen.txt
./writing/haiku.txt
./creatures
./creatures/basilisk.dat
./creatures/unicorn.dat
./creatures/minotaur.dat
./animal-counts
./animal-counts/animals.csv
./numbers.txt
./alkanes
./alkanes/ethane.pdb
./alkanes/propane.pdb
./alkanes/octane.pdb
./alkanes/pentane.pdb
./alkanes/methane.pdb
./alkanes/cubane.pdb
```

ここで使われている `.` はカレントディレクトリを意味し、検索をここから開始します。
`find` の出力はカレントディレクトリ以下のすべてのファイル **と** ディレクトリの名前です。
最初は役に立たないように見えるかもしれませんが、`find` には多くのオプションがあり、
その出力をフィルタリングできます。このレッスンでは、それらの一部を紹介します。

最初のオプションは `-type d` で、「ディレクトリであるもの」を意味します。
確かに、`find` の出力は5つのディレクトリ（`.` を含む）の名前です：

```bash
$ find . -type d
```

```output
.
./writing
./creatures
./animal-counts
./alkanes
```

`find` が見つけるオブジェクトは特定の順序でリストされないことに注意してください。
`-type d` を `-type f` に変更すると、すべてのファイルがリストされます：

```bash
$ find . -type f
```

```output
./writing/LittleWomen.txt
./writing/haiku.txt
./creatures/basilisk.dat
./creatures/unicorn.dat
./creatures/minotaur.dat
./animal-counts/animals.csv
./numbers.txt
./alkanes/ethane.pdb
./alkanes/propane.pdb
./alkanes/octane.pdb
./alkanes/pentane.pdb
./alkanes/methane.pdb
./alkanes/cubane.pdb
```

次に、名前で一致するものを検索してみましょう：

```bash
$ find . -name *.txt
```

```output
./numbers.txt
```

すべてのテキストファイルを見つけることを期待しましたが、`./numbers.txt` だけが表示されました。
問題は、シェルが `*` のようなワイルドカードをコマンドが実行される*前に*展開することです。
カレントディレクトリでは `*.txt` は `./numbers.txt` に展開されるため、
実際に実行されたコマンドは以下と同じでした：

```bash
$ find . -name numbers.txt
```

`find` は指定された動作を実行しましたが、私たちが指定したものが誤っていました。

正しい結果を得るには、`grep` で行ったように、`*.txt` を引用符で囲んで
シェルがワイルドカードを展開しないようにしましょう。
これにより、`find` は展開されたファイル名 `numbers.txt` ではなく、パターン `*.txt` を受け取ります。

```bash
$ find . -name "*.txt"
```

```output
./writing/LittleWomen.txt
./writing/haiku.txt
./numbers.txt
```

:::::::::::::::::::::::::::::::::::::::::  callout

## リスト表示と検索

`ls` と `find` は、適切なオプションを指定すれば似たようなことを実行できますが、
通常の状況では `ls` は見つけられるすべてをリストし、
`find` は特定のプロパティを持つものを検索して表示します。

::::::::::::::::::::::::::::::::::::::::::::::::::

コマンドラインの力はツールを組み合わせることにあります。
これまでにパイプを使う方法を見てきましたが、ここでは別の手法を見てみましょう。
先ほど説明したように、`find . -name "*.txt"` は現在のディレクトリ以下のすべてのテキストファイルをリストします。
これを `wc -l` と組み合わせて、これらのファイル内の行数をカウントするにはどうすればよいでしょうか？

最も簡単な方法は、`find` コマンドを `$()` の中に入れることです：

```bash
$ wc -l $(find . -name "*.txt")
```

```output
  21022 ./writing/LittleWomen.txt
     11 ./writing/haiku.txt
      5 ./numbers.txt
  21038 total
```

シェルがこのコマンドを実行するとき、最初に `$()` 内のコマンドを実行します。
次に、`$()` 式をそのコマンドの出力で置き換えます。
`find` の出力が3つのファイル名 `./writing/LittleWomen.txt`、`./writing/haiku.txt`、および `./numbers.txt` であるため、
シェルは次のコマンドを構築します：

```bash
$ wc -l ./writing/LittleWomen.txt ./writing/haiku.txt ./numbers.txt
```

これが意図した通りの結果です。この展開は、`*` や `?` のようなワイルドカードが展開されるときに
シェルが行う処理と正確に同じですが、任意のコマンドを自分の「ワイルドカード」として使用できます。

`find` と `grep` を一緒に使うことも非常に一般的です。
最初にファイル名パターンに一致するファイルを検索し、
次にそのファイル内の別のパターンに一致する行を検索します。
例えば、現在のディレクトリ内の `.txt` ファイルで「searching」という単語を含むものを検索するには、次のようにします：

```bash
$ grep "searching" $(find . -name "*.txt")
```

```output
./writing/LittleWomen.txt:sitting on the top step, affected to be searching for her book, but was
./writing/haiku.txt:With searching comes loss
```

:::::::::::::::::::::::::::::::::::::::  challenge

## マッチングと除外

`grep` の `-v` オプションはパターンマッチングを反転し、
パターンに一致*しない*行のみを表示します。
これを踏まえて、`creatures` ディレクトリ内の `.dat` ファイルをすべて検索し、
`unicorn.dat` を除外するには、次のコマンドのうちどれを使用すればよいでしょうか？
考えた後に、`shell-lesson-data/exercise-data` ディレクトリでコマンドをテストしてみてください。

1. `find creatures -name "*.dat" | grep -v unicorn`
2. `find creatures -name *.dat | grep -v unicorn`
3. `grep -v "unicorn" $(find creatures -name "*.dat")`
4. 上記のいずれでもない。

:::::::::::::::  solution

## 解答

正解は1です。マッチ式を引用符で囲むことで、シェルによる

展開を防ぎ、
`find` コマンドに渡されるようになります。

オプション2もこの場合は機能します。
これは、シェルが `*.dat` を展開しようとしますが、カレントディレクトリに `*.dat` ファイルが存在しないため、
ワイルドカード式がそのまま `find` に渡されるためです。
この動作については[エピソード3](03-create.md)で最初に説明しました。

オプション3は正しくありません。これは、ファイル名ではなく、
ファイル内容の行を検索し、「unicorn」に一致しない行を探してしまいます。

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::  callout

## バイナリファイル

これまで、テキストファイル内のパターン検索に焦点を当ててきましたが、
データが画像、データベース、またはその他の形式で保存されている場合はどうすればよいでしょうか？

`grep` を拡張して一部の非テキスト形式を扱えるようにするツールもありますが、
より一般的なアプローチは、データをテキストに変換するか、
データからテキストに似た要素を抽出することです。
この方法には一長一短があります。
簡単な操作が容易に行える反面、複雑な操作は通常不可能です。
例えば、画像ファイルから X および Y の次元を抽出して `grep` で処理するプログラムを書くことは容易ですが、
セルに数式が含まれるスプレッドシートで値を検索するものを書くのはどうでしょうか？

最後の選択肢として、シェルやテキスト処理には限界があることを認識し、
他のプログラミング言語を使用する方法があります。
この選択肢を取るとき、シェルをあまり厳しく評価しないでください。
多くの現代的なプログラミング言語がシェルから多くのアイデアを借用しており、
模倣は最高の賛辞でもあります。

::::::::::::::::::::::::::::::::::::::::::::::::::

Unix シェルは、それを使用する人々のほとんどよりも古いものです。
これが長く生き延びた理由は、かつて作られた最も生産的なプログラミング環境の1つであり、
おそらく*最も*生産的だからです。
その構文は難解に思えるかもしれませんが、シェルを習得した人々は
さまざまなコマンドを対話的に試しながら学んだことを活かして作業を自動化できます。
グラフィカルユーザーインターフェイスは最初は使いやすいかもしれませんが、
シェルを学んだ後の生産性には匹敵しません。
そして、アルフレッド・ノース・ホワイトヘッドが1911年に書いたように、
「文明は、人間が考えなくても重要な操作を行える数を増やすことで進歩する」のです。

:::::::::::::::::::::::::::::::::::::::  challenge

## `find` パイプラインの読解力

次のシェルスクリプトについて、簡潔な説明コメントを書いてください：

```bash
wc -l $(find . -name "*.dat") | sort -n
```

:::::::::::::::  solution

## 解答

1. カレントディレクトリ以下で `.dat` 拡張子を持つすべてのファイルを再帰的に検索する
2. それらのファイルの各行数をカウントする
3. ステップ2の出力を数値順にソートする

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- `find` は、特定のプロパティを持つファイルを検索するためのコマンドです。
- `grep` は、ファイル内の特定のパターンに一致する行を選択します。
- `--help` は、多くの Bash コマンドや Bash 内から実行可能なプログラムがサポートするオプションで、これらのコマンドやプログラムの使用方法に関する詳細情報を表示します。
- `man [コマンド]` は、指定したコマンドのマニュアルページを表示します。
- `$([コマンド])` は、そのコマンドの出力を挿入します。

::::::::::::::::::::::::::::::::::::::::::::::::::