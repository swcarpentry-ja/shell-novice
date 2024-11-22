---
title: ループ
teaching: 40
exercises: 10
---

::::::::::::::::::::::::::::::::::::::: objectives

- 複数のファイルセットに対して、1つまたは複数のコマンドを個別に適用するループを書く。
- ループ変数がループの実行中に取る値を追跡する。
- 変数名とその値の違いを説明する。
- ファイル名にスペースや一部の句読点文字を使用してはいけない理由を説明する。
- 最近実行したコマンドを確認する方法を示す。
- 再入力せずに最近実行したコマンドを再実行する。

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- 多くの異なるファイルに対して同じ操作を行うにはどうすればよいですか？

::::::::::::::::::::::::::::::::::::::::::::::::::

**ループ**は、リスト内の各項目に対してコマンドやコマンドのセットを繰り返すことを可能にするプログラミング構造です。
そのため、自動化を通じた生産性向上の鍵となります。
ワイルドカードやタブ補完と同様に、ループを使用することで必要な入力量が減少し（その結果、タイプミスの数も減少します）、効率が向上します。

例えば、数百のゲノムデータファイルが`basilisk.dat`、`minotaur.dat`、`unicorn.dat`という名前で保存されているとします。
この例では、`exercise-data/creatures`ディレクトリを使用しますが、ここには3つの例ファイルしかありません。
ただし、この原則は多数のファイルに適用できます。

これらのファイルの構造は同じです：共通名、分類、更新日が最初の3行に記載され、
その後にDNA配列が続きます。
ファイルの内容を確認してみましょう：

```bash
$ head -n 5 basilisk.dat minotaur.dat unicorn.dat
```

各種の分類を出力したい場合、それは各ファイルの2行目に記載されています。
各ファイルに対して`head -n 2`を実行し、これを`tail -n 1`にパイプで渡す必要があります。
この問題を解決するためにループを使用しますが、まずループの一般的な形式を擬似コードで見てみましょう：

```bash
# "for"という単語は「Forループ」コマンドの開始を示します
for thing in list_of_things 
# "do"という単語は実行すべきジョブリストの開始を示します
do 
    # ループ内のインデントは必須ではありませんが、可読性を高めます
    operation_using/command $thing 
# "done"という単語はループの終了を示します
done  
```

この形式を例に適用すると次のようになります：

```bash
$ for filename in basilisk.dat minotaur.dat unicorn.dat
> do
>     echo $filename
>     head -n 2 $filename | tail -n 1
> done
```

```output
basilisk.dat
CLASSIFICATION: basiliscus vulgaris
minotaur.dat
CLASSIFICATION: bos hominus
unicorn.dat
CLASSIFICATION: equus monoceros
```

:::::::::::::::::::::::::::::::::::::::::  callout

## プロンプトを確認しよう

ループを入力中、シェルプロンプトが`$`から`>`に変わり、再び`$`に戻ることに気づいたかもしれません。
2つ目のプロンプト`>`は、まだ完全なコマンドを入力していないことを思い出させるために異なっています。
セミコロン`;`を使用すれば、1行に複数のコマンドを記述することができます。

::::::::::::::::::::::::::::::::::::::::::::::::::

シェルは`for`というキーワードを見ると、リスト内の各項目に対して1回ずつコマンド（またはコマンドグループ）を繰り返すことを認識します。
ループが実行されるたびに（これを「反復」と呼びます）、リスト内の1つの項目が順番に**変数**に割り当てられ、
ループ内のコマンドが実行され、リストの次の項目に進みます。
ループ内では、変数の値を取得するためにその名前の前に`$`を付けます。
`$`はシェルインタープリタに対して、
その変数を名前として扱い、その値を代入するよう指示します。

この例では、リストは3つのファイル名`basilisk.dat`、`minotaur.dat`、`unicorn.dat`です。
ループが反復されるたびに、まず`echo`を使用して現在の`$filename`変数の値を出力します。
これは結果には必要ありませんが、操作の流れを追いやすくするために役立ちます。
次に、現在の`$filename`によって参照されるファイルに対して`head`コマンドを実行します。
ループの最初の実行時、`$filename`は`basilisk.dat`です。
インタープリタはこのファイルに対して`head`を実行し、
最初の2行を`tail`コマンドにパイプで渡します。
その結果、`basilisk.dat`の2行目が出力されます。
2回目の実行時、`$filename`は`minotaur.dat`になります。
今回は`head`が`minotaur.dat`に対して実行され、同様の操作が行われます。
3回目の実行時、`$filename`は`unicorn.dat`になり、
`head`がそのファイルに対して実行されます。
リストが3項目だけなので、シェルは`for`ループを終了します。

:::::::::::::::::::::::::::::::::::::::::  callout

## 同じ記号、異なる意味

ここでは、`>`がシェルプロンプトとして使用されていますが、
`>`は出力をリダイレクトする際にも使用されます。
同様に、`$`はシェルプロンプトとして表示されますが、変数の値を取得する際にも使用されます。

*シェル*が`>`または`$`を表示している場合、それは入力を待っているプロンプトです。

*自分*で`>`または`$`を入力する場合、それは出力をリダイレクトしたり、
変数の値を取得するための命令です。

::::::::::::::::::::::::::::::::::::::::::::::::::

変数を使用する際には、変数名を明確に区切るために名前を波括弧で囲むこともできます：
`$filename`は`${filename}`と同等ですが、
`${file}name`とは異なります。
他人のプログラムでこの記法を見かけるかもしれません。

このループでは、変数を`filename`と名付けてその目的を人間の読者にとって分かりやすくしています。
シェル自体は変数が何と呼ばれるかは気にしません。
例えば、このループを以下のように書いても同じように動作します：

```bash
$ for x in basilisk.dat minotaur.dat unicorn.dat
> do
>     head -n 2 $x | tail -n 1
> done
```

または：

```bash
$ for temperature in basilisk.dat minotaur.dat unicorn.dat
> do
>     head -n 2 $temperature | tail -n 1
> done
```

ただし、これは*やめましょう*。
プログラムは人間が理解できて初めて役に立ちます。
意味のない名前（例えば`x`）や誤解を招く名前（例えば`temperature`）は、
プログラムが読者の意図通りに動作しない可能性を高めます。

上記の例では、変数（`thing`、`filename`、`x`、`temperature`）には、
コードを書く人や読む人にとって意味が分かるものであれば、
他の名前を使用しても問題ありません。

また、ループはファイル名だけでなく、
例えば数字のリストやデータのサブセットなど、他の用途にも使用できます。

:::::::::::::::::::::::::::::::::::::::  challenge

## 自分でループを書いてみよう

0から9までの10個の数字をエコー出力するループを書くにはどうすればよいでしょうか？

:::::::::::::::  solution

## 解

答

```bash
$ for loop_variable in 0 1 2 3 4 5 6 7 8 9
> do
>     echo $loop_variable
> done
```

```output
0
1
2
3
4
5
6
7
8
9
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## ループ内での変数

この演習は、`shell-lesson-data/exercise-data/alkanes`ディレクトリを参照します。
`ls *.pdb`を実行すると、次の出力が得られます：

```output
cubane.pdb  ethane.pdb  methane.pdb  octane.pdb  pentane.pdb  propane.pdb
```

次のコードの出力はどうなりますか？

```bash
$ for datafile in *.pdb
> do
>     ls *.pdb
> done
```

次に、以下のコードの出力はどうなりますか？

```bash
$ for datafile in *.pdb
> do
>     ls $datafile
> done
```

なぜこれら2つのループは異なる出力を生成するのでしょうか？

:::::::::::::::  solution

## 解答

最初のコードブロックでは、ループを通じて各反復で同じ出力が得られます。
Bashはループ本体内でワイルドカード`*.pdb`を展開します
（ループが開始する前にも展開されます）、
そしてそれを`ls`でリストします。
展開されたループは以下のようになります：

```bash
$ for datafile in cubane.pdb  ethane.pdb  methane.pdb  octane.pdb  pentane.pdb  propane.pdb
> do
>     ls cubane.pdb  ethane.pdb  methane.pdb  octane.pdb  pentane.pdb  propane.pdb
> done
```

```output
cubane.pdb  ethane.pdb  methane.pdb  octane.pdb  pentane.pdb  propane.pdb
cubane.pdb  ethane.pdb  methane.pdb  octane.pdb  pentane.pdb  propane.pdb
cubane.pdb  ethane.pdb  methane.pdb  octane.pdb  pentane.pdb  propane.pdb
cubane.pdb  ethane.pdb  methane.pdb  octane.pdb  pentane.pdb  propane.pdb
cubane.pdb  ethane.pdb  methane.pdb  octane.pdb  pentane.pdb  propane.pdb
cubane.pdb  ethane.pdb  methane.pdb  octane.pdb  pentane.pdb  propane.pdb
```

2つ目のコードブロックでは、各ループの反復で異なるファイルがリストされます。
変数`datafile`の値が`$datafile`を使用して評価され、
その値が`ls`コマンドでリストされます。

```output
cubane.pdb
ethane.pdb
methane.pdb
octane.pdb
pentane.pdb
propane.pdb
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## ファイルセットの制限

次のループを`shell-lesson-data/exercise-data/alkanes`ディレクトリで実行すると、
出力はどうなりますか？

```bash
$ for filename in c*
> do
>     ls $filename
> done
```

1. ファイルは何もリストされない。
2. すべてのファイルがリストされる。
3. `cubane.pdb`、`octane.pdb`、`pentane.pdb`のみがリストされる。
4. `cubane.pdb`のみがリストされる。

:::::::::::::::  solution

## 解答

4が正解です。`*`はゼロ個以上の文字に一致するため、文字`c`で始まり、その後にゼロ個以上の文字が続くファイル名が一致します。

:::::::::::::::::::::::::

次のコマンドを使用した場合、出力はどのように変わりますか？

```bash
$ for filename in *c*
> do
>     ls $filename
> done
```

1. 同じファイルがリストされる。
2. 今回はすべてのファイルがリストされる。
3. 今回はファイルが何もリストされない。
4. ファイル`cubane.pdb`と`octane.pdb`がリストされる。
5. ファイル`octane.pdb`のみがリストされる。

:::::::::::::::  solution

## 解答

4が正解です。`*`はゼロ個以上の文字に一致するため、文字`c`の前にゼロ個以上の文字があり、
文字`c`の後にもゼロ個以上の文字があるファイル名が一致します。

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## ループでファイルに保存する - パート1

`shell-lesson-data/exercise-data/alkanes`ディレクトリ内で、このループの効果は何ですか？

```bash
for alkanes in *.pdb
do
    echo $alkanes
    cat $alkanes > alkanes.pdb
done
```

1. `cubane.pdb`、`ethane.pdb`、`methane.pdb`、`octane.pdb`、`pentane.pdb`、
  `propane.pdb`が出力され、`propane.pdb`のテキストが`alkanes.pdb`というファイルに保存される。
2. `cubane.pdb`、`ethane.pdb`、`methane.pdb`が出力され、3つのファイルのテキストが結合されて
  `alkanes.pdb`というファイルに保存される。
3. `cubane.pdb`、`ethane.pdb`、`methane.pdb`、`octane.pdb`、`pentane.pdb`が出力され、
  `propane.pdb`のテキストが`alkanes.pdb`というファイルに保存される。
4. 上記のどれでもない。

:::::::::::::::  solution

## 解答

1. 各ファイルのテキストが順に`alkanes.pdb`に書き込まれます。
  ただし、ループの各反復でファイルが上書きされるため、
  最終的な`alkanes.pdb`の内容は`propane.pdb`のテキストになります。

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## ループでファイルに保存する - パート2

同じく`shell-lesson-data/exercise-data/alkanes`ディレクトリ内で、
以下のループの出力はどうなりますか？

```bash
for datafile in *.pdb
do
    cat $datafile >> all.pdb
done
```

1. `cubane.pdb`、`ethane.pdb`、`methane.pdb`、`octane.pdb`、`pentane.pdb`のすべてのテキストが結合され、
  `all.pdb`というファイルに保存される。
2. `ethane.pdb`のテキストが`all.pdb`というファイルに保存される。
3. `cubane.pdb`、`ethane.pdb`、`methane.pdb`、`octane.pdb`、`pentane.pdb`、
  `propane.pdb`のすべてのテキストが結合され、`all.pdb`というファイルに保存される。
4. `cubane.pdb`、`ethane.pdb`、`methane.pdb`、`octane.pdb`、`pentane.pdb`、
  `propane.pdb`のすべてのテキストが画面に出力され、`all.pdb`というファイルにも保存される。

:::::::::::::::  solution

## 解答

3が正解です。`>>`はファイルを上書きせずに追記するため、各ファイルのテキストが`all.pdb`に追加されます。
`cat`コマンドの出力はリダイレクトされているため、画面には何も表示されません。

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

次に、`shell-lesson-data/exercise-data/creatures`ディレクトリでの例を続けます。
ここに少し複雑なループがあります：

```bash
$ for filename in *.dat
> do
>     echo $filename
>     head -n 100 $filename | tail -n 20
> done
```

シェルは`*.dat`を展開して処理するファイルのリストを作成します。
**ループ本体**は、これらのファイルごとに2つのコマンドを実行します。
最初のコマンド`echo`はコマンドライン引数を標準出力に表示します。
例えば：

```bash
$ echo hello there
```

出力：

```output
hello there
```

この

場合、シェルは`$filename`を現在のファイル名に展開するため、
`echo $filename`はそのファイル名を出力します。
以下のように書くことはできません：

```bash
$ for filename in *.dat
> do
>     $filename
>     head -n 100 $filename | tail -n 20
> done
```

この場合、ループの最初の反復で`$filename`が`basilisk.dat`に展開されると、
シェルは`basilisk.dat`をプログラムとして実行しようとするからです。
最後に、`head`と`tail`の組み合わせは、
処理対象のファイルから81行目から100行目を選択します
（ファイルに少なくとも100行があると仮定します）。

:::::::::::::::::::::::::::::::::::::::::  callout

## 名前にスペースを含める

スペースは、ループで繰り返すリスト要素を区切るために使用されます。
もしこれらの要素の中にスペースが含まれている場合は、引用符で囲み、
ループ変数も同様に引用符で囲む必要があります。
データファイルが以下のように名前付けされているとします：

```source
red dragon.dat
purple unicorn.dat
```

これらのファイルをループで処理するには、次のようにダブルクォートを追加する必要があります：

```bash
$ for filename in "red dragon.dat" "purple unicorn.dat"
> do
>     head -n 100 "$filename" | tail -n 20
> done
```

スペース（やその他の特殊文字）をファイル名に使用しない方が簡単です。

上記のファイルは存在しないため、コードを実行すると`head`コマンドがファイルを見つけられず、
次のようなエラーメッセージが返されます。ただし、エラーには期待されるファイル名が表示されます：

```error
head: cannot open ‘red dragon.dat' for reading: No such file or directory
head: cannot open ‘purple unicorn.dat' for reading: No such file or directory
```

上記のループで`$filename`から引用符を削除すると、スペースの影響がどのように出るか確認できます。
`creatures`ディレクトリでこのコードを実行すると、`unicorn.dat`に関する結果が得られることに注意してください：

```output
head: cannot open ‘red' for reading: No such file or directory
head: cannot open ‘dragon.dat' for reading: No such file or directory
head: cannot open ‘purple' for reading: No such file or directory
CGGTACCGAA
AAGGGTCGCG
CAAGTGTTCC
...
```

::::::::::::::::::::::::::::::::::::::::::::::::::

`shell-lesson-data/exercise-data/creatures`ディレクトリの各ファイルを修正したいですが、
オリジナルのファイルも保存したいと考えています。
たとえば、オリジナルファイルを`original-basilisk.dat`や`original-unicorn.dat`のように
名前を付けてコピーします。しかし次のコマンドは使えません：

```bash
$ cp *.dat original-*.dat
```

なぜなら、これを展開すると次のようになるからです：

```bash
$ cp basilisk.dat minotaur.dat unicorn.dat original-*.dat
```

これではファイルがバックアップされず、エラーが発生します：

```error
cp: target `original-*.dat' is not a directory
```

この問題は、`cp`が2つ以上の入力を受け取る場合に発生します。この場合、
最後の入力をディレクトリと解釈してすべてのファイルをそこにコピーしようとします。
`creatures`ディレクトリに`original-*.dat`というディレクトリが存在しないため、
エラーが出ます。

その代わりに、ループを使用します：

```bash
$ for filename in *.dat
> do
>     cp $filename original-$filename
> done
```

このループは各ファイル名に対して`cp`コマンドを1回ずつ実行します。
最初の反復では、`$filename`が`basilisk.dat`に展開され、次のコマンドが実行されます：

```bash
cp basilisk.dat original-basilisk.dat
```

2回目は次のコマンドになります：

```bash
cp minotaur.dat original-minotaur.dat
```

3回目、最後の反復では次のコマンドになります：

```bash
cp unicorn.dat original-unicorn.dat
```

通常、`cp`コマンドは何も出力を表示しないため、ループが正しく動作しているかどうかを確認するのは難しいです。
ただし、以前学んだ`echo`を使用して文字列を出力する方法を使えば、
実際にループ内で実行されるコマンドを確認できます。
次の図は、修正されたループを実行した際の動作を示しており、
`echo`を使ったデバッグ技術の重要性を説明しています。

![](fig/shell_script_for_loop_flow_chart.svg){alt='ループ "for filename in .dat; do echo cp $filename original-$filename;done" は、現在のディレクトリ内のすべての".dat"ファイルの名前を$filename変数に代入し、それぞれのファイルについてechoコマンドを実行します。"basilisk.dat"、"minotaur.dat"、"unicorn.dat" の場合、それぞれ "cp basilisk.dat original-basilisk.dat"、"cp minotaur.dat original-minotaur.dat"、"cp unicorn.dat original-unicorn.dat" と表示されます。'}

## Nelleのパイプライン：ファイルの処理

Nelleは`goostats.sh`というシェルスクリプトを使ってデータファイルを処理する準備ができました。
このスクリプトは、タンパク質サンプルファイルから統計情報を計算し、
2つの引数を受け取ります：

1. 入力ファイル（生データを含む）
2. 出力ファイル（計算結果を保存する）

Nelleはシェルの使い方をまだ学んでいる最中なので、
必要なコマンドを段階的に構築していきます。
まず、適切な入力ファイルを選択できることを確認します。
これらのファイル名は`A`または`B`で終わり、`Z`では終わらないことを覚えておいてください。
`north-pacific-gyre`ディレクトリに移動し、次のコマンドを入力します：

```bash
$ cd
$ cd Desktop/shell-lesson-data/north-pacific-gyre
$ for datafile in NENE*A.txt NENE*B.txt
> do
>     echo $datafile
> done
```

```output
NENE01729A.txt
NENE01736A.txt
NENE01751A.txt

...
NENE02040B.txt
NENE02043B.txt
```

次に、`goostats.sh`解析プログラムが作成するファイルの名前を決定します。
各入力ファイル名に`stats`をプレフィックスとして追加するのが簡単そうです。
そこで、ループを次のように変更します：

```bash
$ for datafile in NENE*A.txt NENE*B.txt
> do
>     echo $datafile stats-$datafile
> done
```

```output
NENE01729A.txt stats-NENE01729A.txt
NENE01736A.txt stats-NENE01736A.txt
NENE01751A.txt stats-NENE01751A.txt
...
NENE02040B.txt stats-NENE02040B.txt
NENE02043B.txt stats-NENE02043B.txt
```

`goostats.sh`を実際にはまだ実行していませんが、
これで正しいファイルを選択し、正しい出力ファイル名を生成できることを確認しました。

同じコマンドを何度も入力するのが面倒になってきたNelleは、
ミスを防ぐためにループを再入力する代わりに、
<kbd>↑</kbd>を押します。
すると、シェルはループ全体を1行に再表示します
（セミコロンで各部分を区切ります）：

```bash
$ for datafile in NENE*A.txt NENE*B.txt; do echo $datafile stats-$datafile; done
```

<kbd>←</kbd>を使用して`echo`コマンドに移動し、それを`bash goostats.sh`に変更します：

```bash
$ for datafile in NENE*A.txt NENE*B.txt; do bash goostats.sh $datafile stats-$datafile; done
```

<kbd>Enter</kbd>を押すと、シェルが修正されたコマンドを実行します。
ただし、何も起こらないように見えます。
これは、スクリプトが画面に何も表示しなくなったため、
実行中かどうか、どれくらいの速さで進んでいるのかが分からないからです。
Nelleは<kbd>Ctrl</kbd>\+<kbd>C</kbd>を入力してコマンドを中断し、
<kbd>↑</kbd>を押してコマンドを再実行し、
次のように

修正します：

```bash
$ for datafile in NENE*A.txt NENE*B.txt; do echo $datafile;
bash goostats.sh $datafile stats-$datafile; done
```

```markdown
:::::::::::::::::::::::::::::::::::::::::  callout

## 開始位置と終了位置の移動

シェルで行の先頭に移動するには<kbd>Ctrl</kbd>\+<kbd>A</kbd>を、行の末尾に移動するには<kbd>Ctrl</kbd>\+<kbd>E</kbd>を使用します。

::::::::::::::::::::::::::::::::::::::::::::::::::

彼女がプログラムを実行すると、約5秒ごとに1行の出力が生成されるようになりました：

```output
NENE01729A.txt
NENE01736A.txt
NENE01751A.txt
...
```

1518ファイル × 5秒 ÷ 60秒を計算すると、スクリプトの実行には約2時間かかることがわかります。  
最終チェックとして、彼女は別のターミナルウィンドウを開き、`north-pacific-gyre`ディレクトリに移動し、次のコマンドを使用して出力ファイルの1つを確認します：

```bash
$ cat stats-NENE01729B.txt
```

内容が良好であることを確認したNelleは、コーヒーを飲みながら読書をすることにしました。

:::::::::::::::::::::::::::::::::::::::::  callout

## 履歴を活用する

以前の作業を繰り返すもう1つの方法は、`history`コマンドを使用して過去数百件の実行コマンドをリスト表示し、  
その中から番号を指定して`!123`のように入力することです（ここで`123`はコマンド番号です）。  
例えば、Nelleが次のように入力した場合：

```bash
$ history | tail -n 5
```

```output
456  for datafile in NENE*A.txt NENE*B.txt; do echo $datafile stats-$datafile; done
457  for datafile in NENE*A.txt NENE*B.txt; do echo $datafile stats-$datafile; done
458  for datafile in NENE*A.txt NENE*B.txt; do bash goostats.sh $datafile stats-$datafile; done
459  for datafile in NENE*A.txt NENE*B.txt; do echo $datafile; bash goostats.sh $datafile
stats-$datafile; done
460  history | tail -n 5
```

彼女は`!459`と入力するだけで、ファイルに対して再度`goostats.sh`を実行できます。

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::  callout

## その他の履歴ショートカット

履歴を操作するための他の便利なショートカットコマンドもあります。

- <kbd>Ctrl</kbd>\+<kbd>R</kbd>: 履歴検索モード「reverse-i-search」に入り、次に入力する文字列と一致する最新のコマンドを検索します。
  <kbd>Ctrl</kbd>\+<kbd>R</kbd>をさらに押すことで、以前の一致項目を順に検索できます。その後、矢印キーで選択して編集し、<kbd>Return</kbd>を押してコマンドを実行できます。
- `!!`: 直前のコマンドを取得して再実行します（<kbd>↑</kbd>キーを使用するより便利と感じる場合があります）。
- `!$`: 直前のコマンドの最後の単語を取得します。
  これは意外と便利です。例えば、`bash goostats.sh NENE01729B.txt stats-NENE01729B.txt`の後に`less !$`と入力すれば、`stats-NENE01729B.txt`ファイルをすばやく確認できます。
  

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## ドライラン（Dry Run）を行う

ループは、一度に多くの操作を行う手段ですが、誤っている場合は多くのミスを一度に引き起こします。  
ループが実行するコマンドを実際に実行せずにプレビューする方法は、コマンドを`echo`することです。

以下のループが実行するコマンドをプレビューしたい場合、次の2つのバージョンのどちらを使用するべきでしょうか？

```bash
# バージョン1
$ for datafile in *.pdb
> do
>     echo cat $datafile >> all.pdb
> done
```

```bash
# バージョン2
$ for datafile in *.pdb
> do
>     echo "cat $datafile >> all.pdb"
> done
```

:::::::::::::::  solution

## 解答

バージョン2を実行するべきです。  
このバージョンでは、引用符で囲まれたすべての内容を画面に表示し、変数名（$付き）は展開されます。  
さらに、`>>`は文字列の一部として扱われるため、リダイレクト命令としては解釈されず、  
`all.pdb`ファイルは変更も作成もされません。

一方、バージョン1は`echo cat $datafile`コマンドの出力を`all.pdb`に追加します。  
このファイルには、`cat cubane.pdb`、`cat ethane.pdb`、`cat methane.pdb`などのリストが保存されるだけです。

両方のバージョンを試してみて、結果を確認してください！`all.pdb`ファイルの内容も確認してみましょう。

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::  challenge

## ネストされたループ

複数の化合物や異なる温度で反応速度定数を測定する実験を整理するために、ディレクトリ構造を設定したいとします。  
次のコードを実行した結果はどうなりますか？

```bash
$ for species in cubane ethane methane
> do
>     for temperature in 25 30 37 40
>     do
>         mkdir $species-$temperature
>     done
> done
```

:::::::::::::::  solution

## 解答

これはネストされたループであり、外側のループで指定された各化合物について、  
内側のループ（ネストされたループ）は温度のリストを反復処理します。そして、各組み合わせのディレクトリが作成されます。

実際にコードを実行して、どのディレクトリが作成されるか確認してみてください！

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- `for`ループは、リスト内の各要素に対してコマンドを繰り返します。
- 各`for`ループには、現在処理中の項目を参照する変数が必要です。
- 変数を展開（値を取得）するには`$name`または`${name}`を使用します。
- ファイル名にはスペース、引用符、ワイルドカード（`*`や`?`など）を使用しないでください。変数展開が複雑になります。
- ファイルに一貫した名前を付け、ワイルドカードパターンで簡単に選択できるようにすることで、ループ処理が容易になります。
- <kbd>↑</kbd>キーを使用して以前のコマンドをスクロール表示し、編集して再実行できます。
- <kbd>Ctrl</kbd>\+<kbd>R</kbd>で、以前に入力したコマンドを検索できます。
- `history`を使用して最近のコマンドを表示し、`![番号]`を使用して特定のコマンドを再実行できます。

::::::::::::::::::::::::::::::::::::::::::::::::::


