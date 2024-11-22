---
title: セットアップ
---

## ファイルのダウンロード

このレッスンを進めるには、いくつかのファイルをダウンロードする必要があります。

1. [shell-lesson-data.zip][zip-file] をダウンロードし、デスクトップに移動させます。
2. ファイルを解凍/展開します。
   **この手順で問題があれば、インストラクターに知らせてください**。
   デスクトップに **`shell-lesson-data`** という新しいフォルダが作成されるはずです。

## ソフトウェアのインストール

シェルソフトウェアがまだインストールされていない場合は、
[こちら][install_shell] からダウンロードしてインストールしてください。

## 新しいシェルを開く

ソフトウェアをインストールした後、

3. ターミナルを開きます。
   ご使用のオペレーティングシステムでターミナルを開く方法がわからない場合は、
   以下の説明をご覧ください。
4. ターミナルで `cd` と入力し、<kbd>Return</kbd> キーを押します。
   この手順により、ホームフォルダを作業ディレクトリとして開始できます。

レッスンでは、このフォルダ内のデータファイルにアクセスする方法を学びます。

:::::::::::::::::::::::::::::::::::::::::  callout

## コマンドを入力する場所：新しいシェルを開く方法

シェルは、コンピュータにコマンドを送信し、出力を受け取ることを可能にするプログラムです。
ターミナルやコマンドラインとも呼ばれます。

多くのコンピュータには、デフォルトで Unix シェルプログラムが含まれています。
以下の手順は、インストール済みの Unix シェルプログラムを特定して開く方法を説明しています。
また、Unix シェルプログラム、Linux/UNIX エミュレーター、またはサーバー上の Unix シェルにアクセスするプログラムを特定して
ダウンロードする方法も紹介します。

以下のオプションがいずれも該当しない場合は、
「Unix shell [コンピュータモデル] [オペレーティングシステム]」でオンライン検索を試してください。

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::: solution

### Windows {#windows}

Windows オペレーティングシステムを搭載したコンピュータには、デフォルトで Unix シェルプログラムがインストールされていません。
このレッスンでは、[Git for Windows][install_shell] に含まれるエミュレーターの使用を推奨します。
これにより、Bash シェルコマンドと Git にアクセスできます。

インストール後、Windows のスタートメニューから「Git Bash」を実行することでターミナルを開けます。

**上級者向け：**

Git for Windows の代替として、[Windows Subsystem for Linux（WSL）][wsl] をインストールすることで、
Windows 10 以降で Bash シェルコマンドラインツールを使用することもできます。

ただし、WSL のコマンドはレッスンやワークショップで提示される内容と若干異なる場合があります。

::::::::::::

:::::::::::: solution

### MacOS {#macos}

macOS Mojave またはそれ以前のリリースを実行している Mac コンピュータのデフォルト Unix シェルは Bash です。
macOS Catalina またはそれ以降を実行している場合、デフォルト Unix シェルは Zsh です。
デフォルトのシェルは、ユーティリティフォルダ内のターミナルプログラムを介して利用できます。

ターミナルを開くには、以下のいずれかを試してください：

- Finder で「移動」メニューを選択し、「ユーティリティ」を選択します。
  ユーティリティフォルダ内の「ターミナル」を見つけて開きます。
- Mac の「Spotlight」検索機能を使用します。
  `Terminal` と検索し、<kbd>Return</kbd> を押します。

お使いのマシンが Bash 以外を使用するように設定されているかどうかを確認するには、
ターミナルウィンドウで `echo $SHELL` と入力します。

もし Bash 以外が設定されている場合、ターミナルを開き、`bash` と入力することで Bash を使用できます。

[Macでターミナルを使用する方法][mac-terminal]

::::::::::::

:::::::::::: solution

### Linux {#linux}

Linux オペレーティングシステムのデフォルト Unix シェルは通常 Bash です。
ほとんどの Linux バージョンでは、
[Gnome Terminal][gnome-terminal]、[KDE Konsole][kde-konsole]、または [xterm] を実行することでアクセスできます。
これらはアプリケーションメニューや検索バーから見つけることができます。
もし Bash 以外を使用するように設定されている場合、ターミナルを開き、`bash` と入力することで Bash を使用できます。

::::::::::::

[zip-file]: data/shell-lesson-data.zip
[install_shell]: https://carpentries.github.io/workshop-template/install_instructions/#shell
[wsl]: https://learn.microsoft.com/en-us/windows/wsl/install
[mac-terminal]: https://www.macworld.co.uk/feature/mac-software/how-use-terminal-on-mac-3608274/
[gnome-terminal]: https://help.gnome.org/users/gnome-terminal/stable/
[kde-konsole]: https://konsole.kde.org/
[xterm]: https://en.wikipedia.org/wiki/Xterm



