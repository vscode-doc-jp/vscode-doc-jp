---
title: C++ Programming
MetaDescription: Find out how to get the best out of Visual Studio Code and C++.
commitid: 8f449295b321510871e357b12f9aaa6070944db3
---
## C/C++ for VS Code (Preview)

今日 VS Code を使用して Windows, Linux, Mac でクロスプラットホームな C/C++ 開発ができるように、プレビューのサポート環境を提供しています。このプレビュー リリースでは Linux, Mac,  Window 上でのデバッグなど、コード編集とナビゲーションのサポートを中心に取り上げています。

軽量なツールで C++ を編集したいときに  VS Code  は最適です。ですが、既存の Visual C++ プロジェクトや Windows でのデバッグに最適な環境を実現するには [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs) を使用することをお勧めします。

まだ VS Code の C++ はプレビュー状態ですので、今こそ[バグレポート, 機能リクエスト, フィードバック](mailto:c_cpp_support@microsoft.com)するときです。Linux や Mac を開発環境として使用している人のために、 Visual Studio チームと [get engaged](http://landinghub.visualstudio.com/c-nonwin) しませんか。

(編集メモ:get engaged : 婚約, 言い交わす

## Getting Started

**Microsoft C/C++ 拡張機能をインストール:**

* VS Code を開く
* 拡張機能ビューを開く
* `cpptools` を検索
* **Install** して **Reload** する
* C/C++ コードフォルダーを開く

**コード補完とナビゲーションを有効にするには、`c_cpp_properties.json` を作成する必要があります:**

* ソースファイル内の緑破線(例: #include文) にカーソルを合わせます
* カーソル下に表示される電球をクリックします
* **Add include path to settings** をクリックします

これにより `c_cpp_properties.json` が生成されます。このファイルを使用することにより include パスを追加して、コードナビゲーションと自動補完を有効にすることができます。

>**Note:** `c_cpp_properties.json` は **コマンドパレット**`kb(workbench.action.showCommands)` から **C/Cpp: Edit Configurations** を実行しても生成や編集できます。

**VS Code からアプリケーションをビルドするには、`tasks.json` を作成する必要があります:**

* **コマンドパレット**(`kb(workbench.action.showCommands)`) を開きます
* **Tasks: Configure Task Runner** コマンドを実行して、タスクテンプレートのリストを表示します
* 外部コマンドを実行するタスクを作成するので **Others** を選択します
* `command` をアプリケーション構築に使用するコマンドライン式 (例: `g++ -g main.cpp`) に設定します
* 必要な引数を追加します (例: デバッグ用にビルドする場合は `-g`など)
* これにより (`kb(workbench.action.tasks.build)`) でアプリケーションをビルドできるようになります

この時点で `.vscode` フォルダー下の `tasks.json` は、次のように表示されるはずです:

```json
{
    "version": "0.1.0",
    "command": "g++",
    "isShellCommand": true,
    "showOutput": "always",
    "args": ["-g", "main.cpp"]
}
```

タスクの詳細は [Integrate with External Tools via Tasks](/docs/userguide/tasks) を参照してください。

**デバッグを有効にするには、 `launch.json` を作成する必要があります:**

* サイドバーのデバッグアイコンをクリックして、デバッグビューに移動します
* **デバッグビュー**で、**構成** アイコンをクリックします
* **環境の選択**から `C++ (GDB/LLDB)`(GDB, LLDB) または `C++ (Windows)`(Visual Studio Windows Debugger) を選択します。これは、2つの `launch.json` を作成します:
  * **C++ Launch** はデバッグを開始するときにアプリケーションを起動するためのプロパティを定義します。
  * **C++ Attach** はすでに実行中のプロセスにアタッチするためのプロパティを定義します。
* `program プロパティを、デバッグしているプログラムへのパスに更新します。
* デバッグを開始するときにアプリケーションをビルドする場合は、 `tasks.json` で作成したビルドタスクの名前(上記の例で"g++") を含む `preLaunchTask` プロパティを追加します。

詳細については [Configuring launch.json for C/C++ debugging](https://github.com/Microsoft/vscode-cpptools/blob/master/launch.md) を参照してください。

Windows 上で GDB を使ってデバッグする場合は [Windows Debugging on Cygwin/MinGW](#debug_windows_gdb) を参照してください。

## コード編集

### コード整形

今回使用している C/C++ 拡張機能は内蔵の [clang-format](https://clang.llvm.org/docs/ClangFormat.html) を使用した整形をサポートしています。

ファイル全体を **Format Document**(`kb(editor.action.formatDocument)`) で整形したり、コンテキストメニュー(右クリック)から **Format Selection** (`kb(editor.action.formatSelection)`) で選択範囲を整形したりできます。また次の[設定](/docs/getstarted/settings.md)でも、自動整形を構成できます:

* `C_Cpp.clang_format_formatOnSave` -  ファイル保存時にフォーマット
* `editor.formatOnType` - トリガー文字(`kbstyle(;)`) を入力したときにフォーマット

既定で clang-format のスタイルは、ワークスペース内の `.clang-format` を使用する "file" に設定されています。つまり `.clang-format` があるとき、書式設定はファイルで指定した設定に従います。そして `.clang-format` が見つからない場合 `C_Cpp.clang_format_fallbackStyle` [設定](/docs/customization/userandworkspace.md)を代わりとして使用します。現在、既定のフォーマットスタイルは  "Visual Studio" です。"Visual Studio" の書式設定を使用すれば、 VS Code と Visual Studio Community の両方において書式に互換性が(確実に)あるということです。

"Visual Studio" clang-format スタイルは、まだ正式な標準 clang-format スタイルではありませんが、以下のような clang-format 設定になります:

```json
UseTab: (VS Code current setting)
IndentWidth: (VS Code current setting)
BreakBeforeBraces: AllMan
AllowShortIfStatementsOnASingleLine: false
IndentCaseLabels: false
ColumnLimit: 0
```

拡張機能に内包されているバージョンと異なる clang-format を使用する場合は、 `C_Cpp.clang_format_path` [設定](/docs/getstarted/settings.md)を使用して clang-format バイナルのパスを指定します。

Windows の例:

```json
  "C_Cpp.clang_format_path": "C:\\Program Files (x86)\\LLVM\\bin\\clang-format.exe"
```

### Fuzzy Auto-Complete (preview)

(編集メモ: 以後あいまい自動補完

あいまい自動補完は拡張タグパーサーによって機能します。提案はコードの意味解析に基づくものではありませんが、この機能は現在提供されている単一ファイル IntelliSense よりも幅広い候補を提供します。

具体的には、 C 言語開発で優れた機能を提供します。

## ナビゲーションコード

C/C++ 拡張機能が提供するソースコードナビゲーション機能は、コードベースを理解したり回避(?)したりするための強力なツールです。これら機能は、シンボル情報のオフラインデータベース(`browse.VC.db`)に格納されたタグによって提供されています。C/C++ 拡張機能をインストールすると、C++ ソースコードを含むフォルダーを VS Code が読み込むたびにこのデータベースを作成します。タグパーサーがこの情報を生成している間、プラットホームインジゲーター(下図のWin32)が赤色にかわり、火炎アイコンの隣に表示します。

![The platform indicator during tag parsing](images/cpp/parsing.png)

プラットホームインジゲータが通常に戻ったとき、オフラインデータベースにソースコードシンボルがタグ付けされ、ソースコードナビゲーション機能が使用できる状態になります。

### シンボルサポートを最適化する追加の Include ディレクトリーを指定

VS Code の C/C++ 拡張機能は最適なコード編集を提供するために、コード内で参照される各ヘッダファイルがどこにあるのか教える必要があります。既定で拡張機能は、現在のソースディレクトリ、サブディレクトリーおよびプラットホーム固有の場所を検索します。参照されるヘッダファイルが見つからない場合、 VS Code はそれを各 #include ディレクティブの下に緑破線を表示します。

参照する追加インクルード ディレクトリーを指定するには、 緑破線の付いた #include ディレクティブの上にカーソルを置いて、電球をクリックします。これにより `c_cpp_properties.json` を開きます。このファイルで 'includePath' プロパティにディレクトリーを追加することで、各プラットホーム構成に追加の Include ディレクトリーを指定できます。

![Adding an additional include path](images/cpp/includepath.gif)

### シンボル検索

現在のファイルまたはワークスペース内のシンボルを検索してコード内を迅速に移動できます。

現在のファイル内のシンボルを検索するには`kb(workbench.action.gotoSymbol)`を押して検索するシンボルの名前を入力します。入力時にフィルタリングされる一致リストを表示するので、選択して移動します。

![Searching the current file](images/cpp/filesearch.png)

現在のワークスペースでシンボル検索するには、代わりに `kb(workbench.action.showAllSymbols)` を押して、シンボルの名前を入力します。リストは上と同じように表示します。まだ開いていないファイルの一致シンボルへ移動する場合、まずファイルを開きます。

![Searching in your workspace](images/cpp/workspacesearch.png)

あるいは、**コマンドパレット**を介してシンボルを検索することができます。**Quick Open** を使用して現在のファイルを検索するには `@` コマンドを実行し、現在のワークスペースを検索するには `#`コマンドを実行してください。`kb(workbench.action.gotoSymbol)` と `kb(workbench.action.showAllSymbols)` は
それぞれ上と同じ動作をする '@' と '#' のショートカットです。

### 定義をここに表示

定義をここに表示を使用することでシンボルの定義を簡単に確認できます。ピーク ウィンドウ内に定義を表示するので、現在の場所から離れることなく確認できます。

シンボルの定義を見るには、コード内シンボルの上にカーソルを置き `kb(editor.action.previewDeclaration)` を押します。もしくは、コンテキストメニュー(右クリック)から**定義をここに移動**でも表示することができます。

![Peek definition](images/cpp/peekdefn.png)

現在の C/C++ 拡張機能は、シンボルの使用方法にもとづいて競合の定義を見分けるのに役立つようにコード解析しません。これらは、オーバーロード関数,クラスおよびそれらコンストラクタなど、違うコンストラクタで違うシンボル定義をするときに発生します。これらが発生した場合、それぞれの競合する定義をピークウィンドウの右側に表示し、現在選択のソースコードが左側に表示されます。

(編集メモ: 個人的に競合って言葉は悪い意味で使う感じがする

見つけたい定義を見つけるためには、ピークウィンドウを開いて競合する定義リストを参照します。定義に移動するには、目的の定義をダブルクリックするか、ピークウィンドウ左側に表示しているソースコード内の任意の場所をダブルクリックしてください。

### 定義に移動

定義に移動を使用してシンボルを定義している場所に素早く移動することもできます。

シンボルの定義に移動するには、コード内のシンボル上にカーソルを置き `kb(editor.action.goToDeclaration)` を押します。また、コンテキストメニュー(右クリック)から**定義に移動**を選択することもできます。シンボルの定義が1つの場合、その場所に直接移動します。そうでない場合は競合する定義を前のセクションで説明したように、ピークウィンドウで表示し、移動する定義を選択する必要があります。

## デバッグ

[Getting Started](/docs/languages/cpp.md#getting-started) で明示したようにデバッグ環境を設定したらこのセクションで C/C++ のデバッグの詳細に関する情報を確認します。

VS Code は使用しているOSに応じて、 次の C/C++ デバッガをサポートしています:

* **Linux**: GDB
* **OS X**: LLDB or GDB
* **Windows**: Visual Studio Windows Debugger or GDB (Cygwin or MinGW 使用時)

### Cygwin/MinGW 上の GDB による Windows デバッグ <a name="debug_windows_gdb"></a>

VS Code を使用して Cygwin か MinGW を使用して作成した Windows アプリケーションをデバッグできます。Cygwin または MinGW のデバッグ機能を使用するには、デバッガパスを起動構成(`launch.json`)に手動で設定する必要があります。Cygwin か MinGW アプリケーションをデバッグするには、 `miDebuggerPath` プロパティを追加し、その値を Cygwin か MinGW 環境に対応する gdb.exe の場所に設定してください。

例えば:

```json
    "miDebuggerPath": "c:\\mingw\\bin\\gdb.exe"
```

Windows 上での Cygwin/MinGW デバッグは、アタッチとデバッグの両方をサポートします。

### 条件付きブレークポイント

条件付きブレークポイントを使用すると、条件の値が true の場合にのみ特定のコード行で実行を中断することができます。条件付きブレークポイントをセットするには、既存のブレークポイントを右クリックして**ブレークポイントの編集**を選択します。ピークウィンドウが開くので、デバッグ中にヒット式が true と評価されるように条件を入力してください。

![A conditional break](images/cpp/condbreak.png)

エディター内で条件付きブレークポイントは、黒色の等号を内部に表示するブレークポイント記号で表示します。またカーソルを重ねることで、設定されている条件を表示します。

### 関数ブレークポイント

関数ブレークポイントを使用すると、特定のコード行ではなく関数の先頭で実行を中断することができます。設定には、**Debug**ペインで**ブレークポイント**セクションを開き、**関数ブレークポイントの追加**を選択し、実行を中断する関数の名前を入力します。

### 式の評価

VS Code は一部コンテキストで式の評価をサポートします:

* **Debug** パネルの **ウォッチ式**セクションに式を入力でき、ここではブレークポイントがヒットするたびに評価します。
* **デバッグコンソール**に式を入力することができます。この式は1回だけ評価します。
* ブレークポイントで停止している間は、コード内で表示される任意の式を評価できます。

**ウォッチ式**セクションは、デバッグ中のアプリケーションで有効になることに注意してください。変数の値を変更する式は、プログラムの継続時間中にその変数を変更します。

### マルチスレッドのデバッグ

VS Code の C/C++ 拡張機能には、マルチスレッドプログラムをデバッグする機能を用意しています。すべてのスレッドとその呼び出しスタックは、**Call Stack** セクションに表示します。

![Multi-threaded process](images/cpp/threads.png)

### メモリダンプのデバッグ

VS Code の C/C++ 拡張機能には、メモリダンプをデバッグするための機能もあります。メモリダンプをデバッグするには `launch.json` を開いて、**C++ Launch** 設定の `coreDumpPath` (GDB, LLDB) か `dumpPath` (Visual Studio Windows Debugger)  プロパティに、メモリ ダンプのパスを含む文字列を指定します。これはx64上で、x86プログラムをデバッグしている状態でも機能します。

### 追加のシンボル

デバッガが使用できるシンボルファイル(e.g.Visual Studio Windows Debugger: `.pdb`)を、保持した追加のディレクトリーがある場合。`additionalSOLibSearchPath` (GDB, LLDB) もしくは `symbolSearchPath` ( Visual Studio Windows Debugger) 設定して追加することができます。

例えば:

```json
    "additionalSOLibSearchPath": "/path/to/symbols;/another/path/to/symbols"
```

または

```json
    "symbolSearchPath": "C:\\path\\to\\symbols;C:\\another\\path\\to\\symbols"
```

### ソースファイルの場所

ソースファイルがコンパイル場所にない場合ソースファイルの場所を変更することができます。これは `sourceFileMap` セクションに追加した単純なペア置換によって行います。このリストの最初の一致が使用します。

例えば:

```json
"sourceFileMap": {
    "/build/gcc-4.8-fNUjSI/gcc-4.8-4.8.4/build/i686-linux-gnu/libstdc++-v3/include/i686-linux-gnu": "/usr/include/i686-linux-gnu/c++/4.8",
    "/build/gcc-4.8-fNUjSI/gcc-4.8-4.8.4/build/i686-linux-gnu/libstdc++-v3/include": "/usr/include/c++/4.8"
}
```

### GDB, LLDB, MI コマンド (GDB/LLDB)

`C++ (GDB/LLDB)` デバッグ環境では、`-exec` コマンドで直接 GDB, LLDB, MI コマンドをデバッグコンソールから実行できます。しかし、デバッグコンソールで直接コマンドを実行するとテストされず、場合によっては VS Code がクラッシュする可能性があるので注意してください。

### その他のデバッグ機能

* 無条件ブレークポイント
* ウォッチ ウィンドウ
* コール スタック
* ステッピング

VSコードによるデバッグの詳細については [debugging in VS Code](/docs/userguide/debugging.md) の入門を参照してください。

## 既知の制限

### シンボルとコードナビゲーション

All platforms:

* 拡張機能は関数本体を解析しないので関数内で定義したシンボルに対し定義をここに表示と定義に移動は機能しません。

### デバッグ

All platforms:

* コンパイルした場所から現在の場所へのソースファイルマッピングを指定する方法はありません。これは、コンパイルした場所とは別ファイルシステムレイアウトのアプリケーションデバッグを防ぎます。

Windows:

* Cygwin と MinGW の GDB は、実行中のプロセスを中断することはできません。アプリケーション実行中にブレークポイントを設定、またはデバッグ中のアプリケーションを一時停止するには、アプリケーションのターミナルで `kbstyle(Ctrl-C)` を押します。
* Cygwin の GDB はコアダンプを開くことができません。

Linux:

* GDB は、プロセスにアタッチするために高い権限を必要とします。**プロセスにアタッチ(attach to process)**を使用する場合、デバッグセッションを開始する前にパスワードを入力します。

OS X:

* LLDB:
    * LLDB でデバッグする場合、ブレークモード中にターミナルウィンドウを閉じてもデバッグは停止しません。**停止**ボタンを押せば、デバッグを停止できます。
    * デバッグを停止してもターミナルウィンドウは閉じられません。
* GDB:
OS X で GDB を使用するには、追加の手動インストールを完了する必要があります。[README](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) で手動インストール方法を参照してください。
    * GDB でプロセスにアタッチする場合、デバッグ中のアプリケーションを中断できません。GDB は アプリケーションを実行していない間に設定した、ブレークポイントだけをバインドします(アプリケーションにアタッチする前 か アプリケーションが停止している間)。これは、[GDBバグ](https://sourceware.org/bugzilla/show_bug.cgi?id=20035)によるものです。
    * GDB は、[OSXで使用するコアダンプ形式をサポートしていない](https://www.sourceware.org/ml/gdb/2014-01/msg00036.html)ため、コアダンプをロードできません。
    * GDB を持つプロセスにアタッチすると、break-all はプロセスを終了します。

## 次のステップ

次を見てください:

* [Basic Editing](/docs/userguide/codebasics.md) - Learn about the powerful VS Code editor.
* [Code Navigation](/docs/userguide/editingevolved.md) - Move quickly through your source code.
* [Tasks](/docs/userguide/tasks.md) - プロジェクト構築に使用するタスク
* [Debugging](/docs/userguide/debugging.md) - プロジェクトデバッグの方法

## よくある質問

**Q: プロジェクトを読み込みません**

**A:** 現在 VS Code は C++ プロジェクトファイルをサポートしていませんが、選択したディレクトリをプロジェクトのワークスペースとみなします。そのディレクトリー内のソースコードファイルとそのサブディレクトリーは、ワークスペースの一部です。

**Q: IntelliSense が動作しません**

**A:** 現在のリリースで IntelliSense はサポートしていません。今後のリリースで、IntelliSenseや多くの機能を実装する予定です。

**Q: プロジェクトをどうやってビルド/実行しますか?**

**A:** VS Code はアプリケーションビルドを構成するタスクをサポートし、 MSBuid, CSC, XBuild の出力をネイティブに解析します。詳細は [Tasks](/docs/userguide/tasks.md)  を参照してください。

他に質問がある場合や問題が発生した場合は、[GitHub](https://github.com/Microsoft/vscode-cpptools/issues) に問題を発行してください。
