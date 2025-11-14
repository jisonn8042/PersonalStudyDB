# iOS開発環境管理

開発をしながら２つの同じプログラムでバージョンが違うものを設置する場合があります。

*例）パソコンには既にpython 3.9が設置されているのに、使おうと思ったライブラリがpython 3.15を必要となっている場合*

その時WindowsではPATHというGUIで管理することが出来ますが。
MacOSではと疑問を持ちました。

ちょうど触る機会があって触りながら学んだことをまとめて公有したいと思います。

バージョン作成やセットアップのやり方を説明しながら、なんで必要かを説明していきたいと思います。

</br>
</br>

開発環境をチーム同士に同じくセットするには以下の要素のバージョンを管理する必要があります。

**管理項目**

1. Ruby言語バージョン

2. Rubyツール別のバージョン（cocoapods）

3. bundlerのバージョン（特定したRubyバージョン実行することができるRubyツール）

4. iOSライブラリバージョン

5. README.mdファイル作成（OSバージョンなど他の要素を作成）

**選択事項**

1. xcodeprojファイル管理（また、力が足りないためこの内容に関しては今後作成予定）

</br>
</br>

目次です、以下の手順に説明します。

**Ⅰ. バージョンを作成するために必要なこと（セットアップする側はbundlerの設置まで進めても🆗）**

1. Homebrewの設置

2. rbenv,ruby-buildの設置

    2.1. Rubyの設置（特定バージョンを使う場合）

3. bundlerの設置

4. cocoapodsの設置

    4.1. cocoapodsの設置（特定バージョンを使う場合）

</br>

**Ⅱ. バージョン作成**

1. .ruby-versionファイル作成

2. GemfileやGemfile.lockファイル作成

3. PodfileやPodfile.lockファイル作成

4. README.md作成

</br>

**Ⅲ. バージョンセットアップ**

1. rbenv install [version] (ruby-versionファイルを確認し同じバージョンを設置)

2. bundle install（特定されているRubyツールを設置（cocoapodsなど））

3. bundle exec pod intall（特定されているcocoapodsバージョンでiOSライブラリ設置）

4. README.mdファイル参考（他に気にすべき項目があるか確認）

</br>
</br>

## Ⅰ. バージョンを作成するために必要なこと

**1. Homebrewの設置**

Homebrewはシステムレベルのパッケージ管理ツールで運営体制に必要なパッケージを管理します。
例）Node.js,pythonなど

Macでは最初からRubyが設置されていますが、、、

特定バージョンのRubyが必要で設置すると、パソコンはどのバージョンのRubyを使えば良いのかわからなくなるのでバージョンんを管理してくれるツールが必要となります。

バージョンを管理するツールとしてrbenvを良く使われていますが、そのツールを設置するにはHomebrewが必要となるので設置します。

</br>

**設置する前に設置有無確認**

下のコマンドをターミナルに貼り付けてEnterを押してください。

結果が、Homebrew 4.6.20 の形式で出たら2. rbenv,ruby-buildの設置に進めてください。

```cmd
//設置されているHomebrewバージョン確認コマンド
brew -v
```

</br>

**設置方法**

Homebrew設置の流れ

1. Homebrewを設置（ターミナルにコマンド入力で実施）

2. パスワード入力

3. 設置確認（ターミナルにコマンド入力で実施）

</br>

1. Homebrewを設置コマンド入力

Homebrewサイトに入ると、見えるインストールコマンドをターミナルターミナルに入力しEnterを押します。

```cmd
// Homebrewでコピしたコマンド（下のコマンドを貼り付けてもOK）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

</br>

2. パスワード入力
その後パスワード入力がが出た時パスワードを入力し待つと設置が完了されます。

</br>

3. 設置確認
下のコマンドをターミナルに貼り付けてEnterを押して結果が、Homebrew 4.6.20 の形式で出たら成功

```cmd
//設置されているHomebrewバージョン確認コマンド
brew -v
```

</br>

+ 自分の場合（M1使用中）設置した後にコマンド入力が必要となっていて入力しました。
（何でこの過程が必要となるのかは長くなる為、最後に記述します。）

```cmd
// /Users/ユーザ名/.zprofile ファイルに eval "$(/opt/homebrew/bin/brew shellenv)"を作成します。
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/ユーザ名/.zprofile

// 実際に実行
eval "$(/opt/homebrew/bin/brew shellenv)"
```

</br>
</br>

**2. rbenv,ruby-buildの設置**

rbenvはRubyバージョンを管理するツールとして良く使われています。

プロジェクト別に使用するRubyのバージョンを制御することができます。

まずは、rbenv,ruby-buildをインストールしてからどう制御できるか述します。

</br>

*rbenvとruby-build２つを設置する理由*

*rbenvはバージョンを切り替えるツールで他のバージョンのRubyを設置できないのですがruby-buildを設置することで設置することが出来ます。*

</br>

**設置方法**

rbenv、ruby-version設置の流れ

1. rbenv,ruby-versionを設置する（ターミナルにコマンド入力で実施）

2. ~/.zshr ファイルにrbenv設定技術する（ターミナルにコマンド入力で実施）

3. 2で技術した設定を現在のターミナルに適用（ターミナルにコマンド入力で実施）

4. 設置確認

</br>

1. rbenv,ruby-versionを設置する（ターミナルにコマンド入力で実施）

```cmd
//rbenvを設置するコマンドです。ターミナルに貼り付けてEnterを押すと設置されます。
brew install rbenv ruby-build
```



**2.1. Rubyの設置（特定バージョンを使う場合）**

3. bundlerの設置

bundlerはRubyプロジェクトでライブラリを管理する為使われているツールとなります。
運営体制に必要なパッケージを管理します。 例）Node.js,pythonなど

Macでは最初からRubyが設置されていますが、、、

特定バージョンのRubyが必要で設置すると、パソコンはどのバージョンのRubyを使えば良いのかわからなくなるのでバージョンんを管理してくれるツールが必要となります。

バージョンを管理するツールとしてrbenvを良く使われていますが、そのツールを設置するにはHomebrewが必要となるので設置します。

4. cocoapodsの設置

    4.1. cocoapodsの設置（特定バージョンを使う場合）



ターミナルで ```ruby -v``` を入力することでセットされているRubyのバージョンを確認できます。

```cmd
example@mexamplenoMacBook-Air ~ % ruby -v
ruby 2.6.10p210 (2022-04-12 revision 67958) [universal.arm64e-darwin25]
```
