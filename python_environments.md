# python virtual environment

Feb 2024 Simon Kuwahara

Pythonは初心者向け言語だが、仮想環境だけは鬼門である。そこで、Pythonの仮想環境について解説する。

想定する読者：Python初心者。外部ライブラリを`pip install`でインストールして、コード内で`import`して利用したことがある人。絶対パス、相対パスがわかる人。初心者向けなのでVSCodeを使っていることを前提とする。他のエディタの説明はしません。NeoVimとか使っている人は初心者じゃないと思うので。

TODO

- PEP668
- VSCode extension: [Python Environment Manager](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python-environment-manager)

## 環境

このドキュメントはFeb.2024時点の環境で作成されている。以下の環境で動作確認を行っている。

Linux

- Intel x86_64
- Ubuntu 22.04
- Python 3.10

MacOS

- Apple Silicon M1
- macOS Sonoma 14.3
- defaulp Python
  - Python3.9
- homebrew Python
  - Python3.11
  - Python3.12

| alias | path | environment |
| --- | --- | --- |
| `python` | `/usr/bin/python3` | default Python3.9 |
| `python3` | `/usr/bin/python3` | default Python3.9 |
| `pip` | `/opt/homebrew/bin/pip` | homebrew Python3.11 |
| `pip3` | `/usr/bin/pip3` | default Python3.9 |

Windows

- Intel x86_64
- Windows 11 23H2
- Microsoft Store Python
  - Python3.11
- PsychoPy
  - Python3.8

## Pythonとは

Pythonを書くと言うと、VSCodeやSpyderなどといったエディタでコードを書いて実行するイメージが強い。

最近のエディタは高性能で、実行ボタンを押す（または実行のショートカットを叩く）だけでそのままPythonを実行できる。なので、Pythonを使うことはエディタを使うことと同義と捉えがちである、というか事実上の運用はそうである。他の科学計算用言語のRやMATLABもそれぞれ専用のエディタではあるが、同じようにエディタを開いてコードを書いて実行ボタンを押して実行する。ごく当たり前の風景だ。

その結果、*最近の子、コードはめちゃくちゃ書けるのになぜかターミナルから実行してって言うと躓くパターンが多いんよね*…と~~老害~~ベテランが嘆く。

躓く人はインタープリタとエディタを混同しているのではないだろうか。

- エディタ: 書くソフト
- インタープリター: 動かすソフト

Pythonにしろ、Rにしろ、MATLABにしろ、本体はエディタではなくインタープリタである。（MATLABはJITコンパイラっぽい挙動をするのでもしかしたら中身はインタープリターでないのかも…間違ってたらすいません）

Pythonインタープリタとはなにか？

PythonインタープリターはPythonコード…すなわち**ただの文字列の羅列をPythonの文法に照らし合わせて即時に処理して実行する**プログラムである。

**Pythonインタープリターはただのプログラムである**

だから、Pythonはマシンにインストールするものであり、インストールすれば必ずどこかにインタープリターというプログラム（＝言ってしまえばただのファイル）がマシン上に存在している。

そして、Pythonファイルを実行するとは、マシン上のどこかに存在するPythonインタープリターを実行することである。実行するとき、Pythonファイルのパスを引数としてわたしてやることで任意のPythonファイルを実行できる。

例：ターミナルからPythonファイル`pythonfile.py`を実行する

```bash
# bash/zsh/fish
python3 /path/to/pythonfile.py
```

```powershell
# PowerShell
python /path/to/pythonfile.py
```

コマンド`python`または`python3`はPythonインタープリターを起動するためのコマンドである。`/path/to/pythonfile.py`はPythonファイルのパスである。

また、エディタの実行ボタンを押す裏側では、Pythonインタープリターが実行されている。エディタはPythonインタープリターを起動するためのボタンを提供しているに過ぎない。VSCodeでPythonを実行したあとのターミナルウィンドウの表示を見るとよくわかる。

TODO: screenshot

問題は、マシンによってはPythonインタープリターが複数インストールされていることがある。油断していると、気づいたときには、自分のマシンにどんなPythonインタープリターがいくつインストールされていて、今どのPythonインタープリターを使っているのか、本当にわけ分かんなくなって発狂する。

**Python環境を整理する第一歩は、Pythonインタープリターを意識することである**

- Pythonを実行するとき、どのPythonインタープリターを使っているか
- `pip`でインストールするとき、どのPythonインタープリターの`pip`を使っているのか

これらがわからなくなった瞬間、わけがわからなくなる。

次の章へ続く。

>Pythonの文法や言語仕様はどこで定義されているだろうか？実質的に定義しているのはPythonインタープリターだ。文法を定義するイコールPythonインタープリタの実装にハードコードされているという意味である。CやC++のように複数のコンパイラがある言語の経験があれば当たり前の感覚なのだが、Pythonの場合はCPython（C言語で書かれたPythonインタープリター）がデファクトスタンダードであるため、あまりこういう意識を持ってない初心者が多い。

## マシンにインストールされているPythonインタープリターを把握する

### `python`コマンドと`pip`コマンド

`python`や`python3`といったコマンドを実行するとき、デフォルトのPythonインタープリターが実行される。これは環境変数のパス上にあるPythonインタープリターまたはシンボリックリンクが指すPythonインタープリターである。

コマンドで`python`と`python3`、`pip`と`pip3`が混在していることがよくある。これは恐らく、Python3系が登場したときにPython2系との互換性があまり無かったので、Python3系を区別するために`python3`及び`pip3`というコマンドが生まれたのだろう。

現在ではPython3系が主流で、Python2系は既にレガシーシステムとなっているので、これらのコマンドが何を指しているのかが余計ぐちゃぐちゃになっている。例えば…

- `python`コマンドがPython3系のインタープリターを向いている。
- `python`というコマンドが存在しない。
- `python`と`python3`が異なるPython3インタープリターを指している
- `python`と`python3`が同じPython3インタープリターを指しているが、`pip`と`pip3`は異なるPython3インタープリターのpipを指している

などのケースが見られる。むちゃくちゃなのだ。

第一歩として、それぞれのコマンドがどこのPythonインタープリターを指しているのか調べる。

UNIX系ならば`which`コマンドを使う。

```bash
# bash/zsh/fish
which python
which python3
which pip
which pip3
```

Windowsの場合は`gcm`（`Get-Command`）コマンドを使う。

```powershell
# PowerShell
# gcm python | fl だと長ったらしいのでDefinitionプロパティだけ表示
(gcm python).Definition
(gcm python3).Definition
(gcm pip).Definition
(gcm pip3).Definition
```

ちなみに、ここでは、ひとつのPythonバージョンについてひとつのPythonインタープリターしか存在しない前提で書いているが、そうでない場合もある。例えば、同マシン上にPython3.10のインタープリターが複数存在する場合は上記に加えて、コマンド`python3.10`がどのインタープリターを指しているのかがパット見ではわからないので、同様に注意する必要がある。

### インストールされているPythonインタープリターを探す

マシンにインストールされているPythonインタープリターを100%確実に全て探すには、マシン上の全てのディレクトリを探索するしかない。しかし、それはめちゃ時間がかかるし非常に面倒である。

そこで、便利はコマンドと一般にPythonインタープリターがインストールされる、またはシンボリックリンクが貼られるディレクトリを紹介する。

これはあくまでも一般的なPython環境を対象とした情報であり、例外や抜け、漏れはある。

特に他のソフトウェアが独自にPython環境を持っているような場合は全て例外である。筆者が属する心理学分野だとPsychoPyなんかがこの手のカスタムな例外に該当する。

#### UNIX系の場合

LinuxやMacOSなどのUNIX系OSでは、`whereis`コマンドを使うことでインストールされているPythonインタープリターを探すことができる。

```bash
# bash/zsh/fish
whereis python
```

Pythonの主なディレクトリは以下の通り。

- `/usr/bin`
- `/usr/local/bin`
- `/opt/homebrew/bin`（MacOSのhomebrewでインストールした場合）
- `$HOME/.local/bin`（ユーザーがインストールした場合）

#### Windowsの場合

Windowsの場合は

TODO

Pythonの主なインストールディレクトリは以下の通り。

- （python.orgからインストールした場合）
- `C:\Users\user\AppData\Local\Programs\Python\Python39`（Microsoft Storeでインストールした場合）
- （Chocolateyでインストールした場合）

### デフォルトのPythonインタープリターを設定する

TODO

### 任意のPythonインタープリターを指定して実行する

特定のPythonインタープリターを指定して実行するときはパスを指定する（フルパス、相対パスなど）。

例：Pythonファイルを実行する

```bash
# bash/zsh/fish
/usr/local/bin/python3 /path/to/pythonfile.py
```

```powershell
# PowerShell
C:\Users\user\AppData\Local\Programs\Python\Python39\python.exe /path/to/pythonfile.py
```

例：`pip`を使ってライブラリをインストールする

`"Pythonインタープリターのパス" -m pip`というコマンドを使う。

```bash
# bash/zsh/fish
/usr/local/bin/python3 -m pip install ipykernel
```

```powershell
# PowerShell
C:\Users\user\AppData\Local\Programs\Python\Python39\python.exe -m pip install ipykernel
```

## 仮想環境とは

仮想環境とは、インストールされているPython環境のうえに作られる独立したPython環境である。

といってもイメージがしにくいと思う。。。

VMやDockerを使ったことがある人は、それらと比較するとわかりやすいかもしれない。

OOP(オブジェクト指向)に慣れている人は、インストール済みのPython環境を親クラス、仮想環境を子クラスと考えるとわかりやすい。

なぜ仮想環境を使うか？

- Pythonのバージョンを指定したい
- 特定のライブラリのバージョンを使いたい
- 一つの環境内で共存できないライブラリがある
- requirements.txt（環境ファイル）を使って環境を再現できる

などなど。特に業務だと、最低限の必要なライブラリだけを入れて、不要なものは一切入れないみたいな使い方は理想的。

ベース環境は綺麗にしておいて、プロジェクトごとに仮想環境を使い分ける運用だと依存関係のトラブルも減る、ということで一般化している（多分）。

仮想環境を提供するツールはいくつかあって混乱のもととなっている。統一しておけば皆ハッピーなのだが、色々歴史的経緯もあったりする。なのでうまく使い分ける。

代表的な仮想環境管理ツール

- venv
- anaconda

なお、`anaconda`は~~ゴミなので~~最初から存在しないものとする。筆者が`anaconda`を忌み嫌う理由は以下の通り。

- 独自のパッケージマネージャ`conda`を使っているので、わかってない人が`pip`と混ぜると環境をぶっ壊す
- サーバーへのインストールがダルい
- 商用利用にはライセンスが必要
- 色々独自なので学習コストが高い

だから、基本`venv`を使う。

`venv`は[PEP405](https://peps.python.org/pep-0405/)で公式推奨の手法となっているので、[公式ライブラリに含まれている](https://docs.python.org/3/library/venv.html)。即ち、どこでも使えるし、使える人も多いので話が通じやすい。

## venv

本当は`venv`を直接使うのではなく、`venv`+`virtualenv`+`virtualenvwrapper`を使うのがオススメだが、一番基本だし、なんだかんだで使う機会が多いので紹介しておく。

`"Pythonインタープリターのパス" -m venv`というコマンドを使う。仮想環境のバージョンはインタープリターのバージョンに依存する。例えば、Python3.10の仮想環境を使いたければ、Python3.10のインタープリターをインストールして仮想環境を作る。Python2系は使えない。

使い方は[公式ドキュメント](https://docs.python.org/3/library/venv.html)を見るのがわかりやすい。

例：プロジェクトディレクトリ内で`env3`という名前のの仮想環境を作る。

```bash
# pythonインタープリター -m venv 仮想環境名
python3 -m venv env3
```

カレントディレクトリ内で`env3`という名前のフォルダーが作成される。このフォルダーが仮想環境である。`pip`でインストールするライブラリもこのフォルダー内にインストールされる。仮想環境はプラットフォーム間の互換性は無い。

仮想環境を有効化するには仮想環境内の`activate`スクリプトを実行する。

UNIX系では`env3/bin/acitvate`を実行する。

```bash
# bash(Ubuntu) and zsh(MacOS)
source env3/bin/activate

# fish on bash(Ubuntu) and zsh(MacOS)
source env3/bin/activate.fish
```
Windowsでは`env3\Scripts\Activate.ps1`を実行する。

```powershell
# PowerShell(Windows)
env3\Scripts\Activate.ps1

# 実行ポリシーが`Restricted`になっているとエラーを吐く。
# その場合は`Set-ExecutionPolicy`コマンドで実行ポリシーを変更する必要がある。
# Microsoft公式: https://learn.microsoft.com/ja-jp/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.4
# 参考ブログ: https://qiita.com/kikuchi/items/59f219eae2a172880ba6
Set-ExecutionPolicy RemoteSigned
```

```cmd
# command prompt(Windows)
env3\Scripts\activate.bat
```

仮想環境が有効化された状態で実行するPythonコードや`pip`コマンドは、仮想環境内のPythonインタープリターと`pip`を使う。

仮想環境に入るとターミナルの表示が変わる（一番左にカッコで環境名が表示される）ので、どの環境で作業しているかを常に意識すること。

仮想環境を無効化するには`deactivate`コマンドを実行する。

```bash
# all shells
deactivate
```

### VSCodeで仮想環境を使う

VSCodeでPythonファイルなら右下、Jupyter Notebookなら右上のボタンからPython環境を選択できるので、任意の仮想環境を選択する。環境変数のパス上かプロジェクトディレクトリ内にあるPython環境しか選択肢に表示されないので、プロジェクトディレクトリ外のPython環境を使いたい場合は、vscodeの設定ファイルを作る。

VSCodeではプロジェクトディレクトリ内の`.vscode/settings.json`に以下を追加することで、プロジェクトディレクトリ外のPython環境を選択できる。このファイルのレポジトリ内に例を用意しているので参照。

```json
{
    "python.pythonPath": "/path/to/python"
}
```

Python環境のパスをプロジェクトディレクトリから見た相対パスで指定することもできる。

しかし、あまり効率が良くないので、`virtualenv`を使うことをオススメする。と言いつつ、ターミナルでの使い勝手を捨てるならVSCodeの設定で`python.venvPath`を設定すれば`venv`だけでも良いかもしれない。[下記参照](#vscodeで設定)。

## virtualenv + virtualenvwrapper

仮想環境を管理するツール`venv`以外にも存在する。`virtualenv`はそれらを一元的に管理できるユニバーサルなツールである。

- `virtualenv`: 仮想環境管理ツール
- `virtualenvwrapper`: マシン上の`virtualenv`仮想環境をひとつのフォルダー`~/.virtualenvs`にまとめるツール

一回環境構築してしまえば使いやすいが、環境構築がめんどい。

**重要**: `fish`は使えないのでコマンドの実行は`bash`または`zsh`を使うこと。もしくは[`VirtualFish`](https://github.com/justinmayer/virtualfish)を使う。

### virtualenvwrapper導入手順

まず、メインとなるPythonインタープリターを決める。以下の例ではデフォルトを使っているが、必要に応じて適切なインタープリターに変える。

`virtualenv`と`virtualenvwrapper`はPythonライブラリなのでpipでインストールする。そのあと、`virtualenvwrapper.sh`を`.bashrc`等に追加する。

補足

- `.bashrc`、`.zshrc`：シェルが起動するときに読み込まれる設定ファイル
- POSIX環境向けに作られているのでWindowsの場合は工夫が必要になる（後述）

homebrewやaptでインストールすることもできるが、パスが違うので各自の環境にあわせて直すこと。同様に、`virtualenvwrapper`をインストールするときは`sudo`を使わない場合はパスが異なるので注意。自分でパスを確認して、わかってやっている限りは大丈夫です。

[公式ドキュメント](https://virtualenvwrapper.readthedocs.io/en/latest/install.html)

#### Linux

TODO

#### MacOS

インテルとアップルシリコンでディレクトリ構造が違うらしい。筆者はM1ユーザーでよくわからないので、Intel Macユーザーは適宜読み替えてほしい。

[下記のコマンドを使ったマシンの環境は上記参照。](#環境)

`virtualenv`と`virtualenvwrapper`をインストールする。

```zsh
pip3 install virtualenv
sudo pip3 install virtualenvwrapper # sudoを使わない場合はスクリプトのパスが違うので注意
```

`pip3`(sudoなし)で`virtualenvwrapper`をインストールしたあと、やっぱり`pip3`(sudoあり)でインストールしたい場合は一回アンインストールしてから`sudo`で再インストールすること。

Macはデフォルトで`.zshrc`がないのでもし無ければ作る。

```zsh
nano ~/.zshrc # コマンドをつかわずに手動で作っても良い
```

以下を`.zshrc`に追加する。

```zsh
# ~/.zshrc

# 環境変数（参考：https://zenn.dev/sprout2000/articles/bd1fac2f3f83bc）
# 上から優先したい順に書く
# 注意：筆者はりんご初心者なのでテキトーです、りんご教の人は教えてください
typeset -U path PATH
path=(
	/usr/bin
	/usr/sbin
	/bin
	/sbin
	/opt/homebrew/bin(N-/)
	/opt/homebrew/sbin(N-/)
	/usr/local/bin(N-/)
	/usr/local/sbin(N-/)
	# $HOME/.local/bin(N-/)
	/Library/Apple/usr/bin
	$HOME/Library/Python/3.9/bin
)

# python virtualenvwrapper
export WORKON_HOME=$HOME/.virtualenvs # 仮想環境のディレクトリ
export PROJECT_HOME=$HOME/Devel
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3

# homebrew: /opt/homebrew/bin/virtualenvwrapper.sh
# non-sudo pip3: $HOME/Library/Python/3.9/bin/virtualenvwrapper.sh
source /usr/local/bin/virtualenvwrapper.sh
```

#### Windows

`virtualenvwrapper`はPOSIX環境向けに作られているので、Windowsで使うには工夫が必要である。

1. MSYS等のPOSIX環境で使う。（git bash的な感じ）
2. Windows用にポートした`virtualenvwrapper-win`を使う。

TODO

### VSCodeで設定

VSCodeの設定で`python.venvPath`を設定することで、VSCodeのPython環境選択ボタンで`virtualenv`の仮想環境を選択できる。

設定画面のGUIから設定可能。`Python: Venv Path`を検索して以下の値を設定する。

`"~/.virtualenvs"`

このパスは`virtualenvwrapper`で定義されている。

>補足：virtualenvwrapperを使わない場合（例えばvenvしか使わないとしても）、この設定は便利。どこかフォルダーを一つ作って、そのフォルダーの中だけに全ての仮想環境を作るという運用にすることもできる。そのフォルダーを`python.venvPath`に設定することで、VSCodeのPython環境選択ボタンでそのフォルダー内の仮想環境を選択できる。ターミナルで使う時はプロジェクトディレクトリから遠く離れた場所のactivateスクリプトを実行することになるので色々工夫しないと面倒だが、VSCodeだけなら使い勝手は良い。

### 使用例

詳細は公式ドキュメントを参照。

FIXME:コマンドは多分正しいけど、ディレクトリに関してやオプション、フラグは間違いあるかも。

```bash
# 使える仮想環境を表示
workon

# 仮想環境env310をactivate
workon env310

# プロジェクトディレクトリ内で仮想環境env310を作成
cd /path/to/project # プロジェクトディレクトリに移動
mkvirtualenv env310

# Pythonバージョンを3.10に指定して仮想環境env311を作成
# python3.10がインストールされていて、コマンドpython3.10が適切なインタープリターに向いていること
mkvirtualenv -p python3.11 env311

# Pythonインタープリターを指定して仮想環境spenv38を作成
# Pythonインタープリターのパス/usr/bin/python3.8は適宜書き換えること
mkvirtualenv -p /usr/bin/python3.8 spenv38

# 仮想環境をdeactivate
deactivate

# 仮想環境一覧を表示
lsvirtualenv

# 仮想環境env310を削除
rmvirtualenv env310
```

## 環境ファイル

TODO

- venv    : "requirements.txt"
- Anaconda: "(環境名).yml"
