# Python Virtual Environment Guide

- Feb 2024
- Simon Kuwahara

Pythonは初心者向け言語だが、仮想環境だけは数多くの初心者を撃退してきた鬼門である。そこで、Pythonの仮想環境について解説する。

想定する読者：

- Python初心者
- 外部ライブラリを`pip install`でインストールして、コード内で`import`して利用したことがある
- 絶対パス、相対パスがわかる

VSCode向けの設定や使い方は別資料にまとめる。

[./vscode_settings.md](./vscode_settings.md)

初心者向けなので他のエディタの説明はしません。
EmacsとかVim/NeoVimとか使っている人は初心者じゃないと思うので。

## 環境

このドキュメントはFeb.2024時点の環境で作成されている。以下の環境で動作確認を行っている。

### Ubuntu 22.04

- Intel x86_64
- Python 3.10

| alias | path | environment |
| --- | --- | --- |
| `python`  | na | na |
| `python3` | `/usr/bin/python3`  | default Python 3.10 |
| `pip`     | `~/.local/bin/pip`  | default Python 3.10 |
| `pip3`    | `~/.local/bin/pip3` | default Python 3.10 |

`python-is-python3`パッケージをインストールしていないデフォルト状態。

`python`のエイリアスはデフォルトではPython2用に温存されている。

### MacOS Sonoma 14.3

- Apple Silicon M1
- defaulp Python
  - Python3.9
- homebrew Python
  - Python3.11
  - Python3.12

| alias | path | environment |
| --- | --- | --- |
| `python`  | `/usr/bin/python3` | default Python3.9 |
| `python3` | `/usr/bin/python3` | default Python3.9 |
| `pip`     | `/opt/homebrew/bin/pip` | homebrew Python3.11 |
| `pip3`    | `/usr/bin/pip3` | default Python3.9 |

デフォルトPythonでは`pip`のエイリアスは無いので、別途エイリアスを定義しない限り、homebrew Pythonの`pip`になる。

### Windows 11 23H2

- Intel x86_64
- Microsoft Store Python
  - Python3.11
  - Python3.10
- Python.org
  - Python3.11
- Chocolatey
  - Python3.12
- PsychoPy
  - Python3.8

| alias | path | environment |
| --- | --- | --- |
| `python`  | `~\AppData\Local\Microsoft\WindowsApps\python.exe`  | Microsoft Store Python 3.11 |
| `python3` | `~\AppData\Local\Microsoft\WindowsApps\python3.exe` | Microsoft Store Python 3.11 |
| `pip`     | `~\AppData\Local\Microsoft\WindowsApps\pip.exe`  | Microsoft Store Python 3.11 |
| `pip3`    | `~\AppData\Local\Microsoft\WindowsApps\pip3.exe` | Microsoft Store Python 3.11 |

## Pythonとは

よくある~~老害~~ベテランの嘆き:

最近の子、コードはめちゃくちゃ書けるのになぜかターミナルから実行してって言うと躓くパターンが多いんよね…

今どきのIDEは優秀で、あの怖い黒い画面を一度も触らずに開発ができちゃいます

しかし、Pythonの仮想環境を扱うときには、IDEを構成する2つの要素、エディタとインタープリターを意識する必要がある

- エディタ: Pythonコードを書くソフト
- インタープリター: Pythonコードを実行してマシンに仕事をさせるソフト

忘れがちだが、Pythonコードは**ただのテキストファイル**にすぎない。
Pythonインタプリタが**Pythonの本体**と言っても良い。

Pythonをインストールすれば、インタープリターというプログラム（＝言ってしまえば**ただのファイル**）が必ずマシン上のどこかにに存在している。

そして、Pythonファイルを実行するとは、マシン上のどこかに存在するPythonインタープリターを実行することである。

ターミナルからPythonインタープリターを起動するには、コマンド`python`または`python3`を使うことが多い。

例：ターミナルからPythonファイル`python_file.py`を実行する

```bash
# bash/zsh/fish
# python3 任意のPythonファイルのパス
python3 /path/to/python_file.py
```

```powershell
# PowerShell
# python 任意のPythonファイルのパス
python /path/to/python_file.py
```

VSCodeの実行ボタンを押す裏側では上記のようなコマンドが実行されており、実行後にターミナルウィンドウの表示を確認するとよくわかる。

TODO: screenshot

ここでちょっと問題になるのは、Pythonインタープリターが複数インストールされている場合。

油断してると、今どのPythonインタープリターを使っているのかわからなくなる。

- Pythonを実行するとき、どのPythonインタープリターを使っているか
- `pip`でPythonライブラリをインストールするとき、どのPythonインタープリターの`pip`を使っているのか

これらがわからなくなった瞬間、本当にわけ分かんなくなって発狂する。

Python環境を整理する第一歩は、**Pythonインタープリターを意識することである**

## マシンにインストールされているPythonインタープリターを把握する

### `python`コマンドと`pip`コマンド

`python`や`python3`といったコマンドを実行するとき、デフォルトのPythonインタープリターが実行される。

これは環境変数のパス上にある

- Pythonインタープリター

または

- シンボリックリンクが指すPythonインタープリター

である。

これらのコマンドがマシン内で混在していることがよくある。

- Python:
  - `python`
  - `python2`
  - `python3`
- pip:
  - `pip`
  - `pip2`
  - `pip3`

もともとはPython1.x、Python2.x、Python3.xの違いだったが、現在ではPython3系が主流で、Python2系は既にレガシーシステムとなってる。

現在は主に`python`が2系、`python3`が3系が多いが、2系が廃れてきたので、今ではどれが何を指しているのかがぐちゃぐちゃになっている。例えば…

- `python`コマンドがPython3系のインタープリターを向いている。
- `python`というコマンドが存在しない。
- 逆に`python3`というコマンドが存在しない。
- `python`と`python3`がそれぞれマシン内で共存する異なるPython3系インタープリターを指している
- `python`と`python3`は同じPython3系インタープリターを指しているが、`pip`と`pip3`は異なるPython3系インタープリターのpipを指している

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

Ubuntuでは`python-is-python3`パッケージを

Windowsの場合は`gcm`（`Get-Command`）コマンドを使う。

```powershell
# PowerShell
# gcm python | fl だと長ったらしいのでDefinitionプロパティだけ表示
(gcm python).Definition
(gcm python3).Definition
(gcm pip).Definition
(gcm pip3).Definition
```

以降では、ひとつのPythonバージョンについてひとつのPythonインタープリターしか存在しない前提で書いているが、そうでない場合もある。

例えば、何かしらの理由で、同マシン上にPython3.10のインタープリターが複数存在する場合はコマンド`python3.10`がどのインタープリターを指しているのかがパット見ではわからないので、上記同様の注意が必要がある。

### インストールされているPythonインタープリターを探す

マシンにインストールされているPythonインタープリターを100%確実に全て探すには、マシン上の全てのディレクトリを探索するしかない。
しかし、それはめちゃ時間がかかるし非常に面倒である。

そこで、便利はコマンドと一般にPythonインタープリターがインストールされる、またはシンボリックリンクが貼られるディレクトリを紹介する。

しかし、これはあくまでも一般的なPython環境のデフォルト設定を対象とした情報であり、ユーザーが独自にインストールディレクトリを指定した場合は当てはまらない。

加えて、仕様変更による情報の陳腐化、例外、抜けや漏れはのでユーザー自身で確認すること。

特に他のソフトウェアが独自にPython環境を持っているような場合は全て例外である。
例えば、心理学分野だとPsychoPyなんかがこの手のカスタムな例外に該当する。

#### UNIX系の場合

LinuxやMacOSなどのUNIX系OSでは、`whereis`コマンドを使うことでインストールされているPythonインタープリターを探すことができる。

```bash
# bash/zsh/fish (Linux/MacOS)
whereis python
```

Pythonの主なディレクトリは以下の通り。

- `/usr/bin`
- `/usr/local/bin`
- `/opt/homebrew/bin`（MacOSのhomebrew）
- `$HOME/.local/bin`（ユーザーがインストールした場合）

#### Windowsの場合

Pythonの主なインストールディレクトリは以下の通り。

- `~\AppData\Local\Programs\Python\`（python.org）
- `~\AppData\Local\Microsoft\WindowsApps\`（Microsoft Store）
- `C:\ProgramData\chocolatey\bin\`（Chocolatey）

### デフォルトのPythonインタープリターを設定する

TODO

### 任意のPythonインタープリターを指定して実行する

特定のPythonインタープリターを指定して実行するときはパスを指定する（フルパス、相対パスなど）。

例：Pythonファイルを実行する

```bash
# bash/zsh/fish (Linux/MacOS)
/usr/local/bin/python3 /path/to/pythonfile.py
```

```powershell
# PowerShell (Windows)
~\AppData\Local\Programs\Python\Python39\python.exe /path/to/pythonfile.py
```

例：`pip`を使ってライブラリをインストールする

`"Pythonインタープリターのパス" -m pip`というコマンドを使う。

```bash
# bash/zsh/fish (Linux/MacOS)
/usr/local/bin/python3 -m pip install ipykernel
```

```powershell
# PowerShell (Windows)
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
- PEP668 -> PyPA specifications: "[Externally Managed Environments](https://packaging.python.org/en/latest/specifications/externally-managed-environments/#externally-managed-environments)"

ベース環境は綺麗にしておいて、プロジェクトごとに仮想環境を使い分けるのが理想的。

特に業務だと、不要なものは一切排除する運用は依存関係トラブルのリスクを大幅に減らせる。

仮想環境を提供するツールは統一しておけば皆ハッピーなのだが、色々な経緯があって何種類か存在していて混乱のもととなっている。

2024年現在の代表的な仮想環境管理ツール：

- venv
- anaconda

なお、`anaconda`は~~ゴミなので~~最初から存在しないものとする。筆者が`anaconda`を忌み嫌う理由は以下の通り。

- 独自のパッケージマネージャ`conda`を使っているので、わかってない人が`pip`と混ぜると環境をぶっ壊す
- サーバーへのインストールがダルい
- 商用利用にはライセンスが必要
- 色々独自なので結果的に学習コストが高い

だから、基本`venv`を使う。

`venv`は[PEP405](https://peps.python.org/pep-0405/)で公式推奨の手法となっているので、[公式ライブラリに含まれている](https://docs.python.org/3/library/venv.html)。即ち、どこでも使えるし、使える人も多いので話が通じやすい。

## venv

本当は`venv`を直接使うのではなく、`venv`+`virtualenv`+`virtualenvwrapper`を使うのがオススメなのだが…

PEP準拠の観点からも`venv`が一番の基本だし、なんだかんだで使う機会が多いので紹介しておく。

`"Pythonインタープリターのパス" -m venv`というコマンドを使う。仮想環境のバージョンはインタープリターのバージョンに依存する。例えば、Python3.10の仮想環境を使いたければ、Python3.10のインタープリターをインストールして仮想環境を作る。Python2系は使えない。

使い方は[公式ドキュメント](https://docs.python.org/3/library/venv.html)を見るのがわかりやすい。

例：プロジェクトディレクトリ内で`env3`という名前のの仮想環境を作る。

```bash
# pythonインタープリター -m venv 仮想環境名
python3 -m venv env3
```

カレントディレクトリ内で`env3`という名前のフォルダーが作成される。このフォルダーが仮想環境である。

`pip`でインストールするライブラリもこのフォルダー内にインストールされる。

仮想環境はプラットフォーム間の互換性は無いので注意。
同じOSかつ同じアーキテクチャであれば結構動くので、仮想環境をクラウドストレージ上に載せても結構使えちゃうが、外部ファイルに依存するライブラリもあるので、あまり信用しすぎないように。

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
Set-ExecutionPolicy RemoteSigned
```

```cmd
# 参考として紹介: command prompt
env3\Scripts\activate.bat
```

仮想環境が有効化された状態で実行するPythonコードや`pip`コマンドは、仮想環境内のPythonインタープリターと`pip`を使う。

仮想環境に入るとターミナルの表示が変わる（**一番左にカッコで環境名が表示される**）ので、どの環境で作業しているかを常に意識すること。

仮想環境を無効化するには全てのシェル共通で`deactivate`コマンドを実行する。

```bash
deactivate
```

### VSCodeで仮想環境を使う

VSCodeでPythonファイルなら右下、Jupyter Notebookなら右上のボタンからPython環境を選択できるので、任意の仮想環境を選択する。

環境変数のパス上かプロジェクトディレクトリ内にあるPython環境しか選択肢に表示されないので、プロジェクトディレクトリ外のPython環境を使いたい場合は、vscodeの設定ファイルを作る。

設定の詳細は[別資料](./vscode_settings.md)を参照。

## virtualenv + virtualenvwrapper

仮想環境を管理するツール`venv`以外にも存在する。`virtualenv`それらを一元的に管理できるユニバーサルなツールである。venv登場前から存在していて人気も根強い。

- `virtualenv`: 仮想環境管理ツール
- `virtualenvwrapper`: マシン上の`virtualenv`仮想環境をひとつのフォルダー`~/.virtualenvs`にまとめるツール

一回環境構築してしまえば使いやすいが、環境構築がめんどい。

**重要**: POSIX環境以外は動かない

- `fish`は使えないのでコマンドの実行は`bash`または`zsh`を使うこと。もしくは[`VirtualFish`](https://github.com/justinmayer/virtualfish)を使う。
- POSIX環境向けに作られているのでWindowsの場合は工夫が必要になる（後述）

おおまかな手順は

1. メインとなるPythonインタープリターを決める
2. `pip install virtualenv`
3. `sudo pip install vitualenvwrapper`
4. `~/.bashrc`または`~/.zshrc`に設定を追加する

詳細は[virtualenvwrapper公式ドキュメント](https://virtualenvwrapper.readthedocs.io/en/latest/install.html)参照。

各OSごとの導入手順は以下の通り。

- [Linuxに`virtualenvwrapper`をインストールする](#linux導入手順)
- [MacOSに`virtualenvwrapper`をインストールする](#macos導入手順)
- [Windowsに`virtualenvwrapper`をインストールする](#windows導入手順)

### Linux導入手順

[下記のコマンドを使ったマシンの環境は上記参照。](#環境)

`virtualenv`と`virtualenvwrapper`をpipでインストールする。

```bash
# Linux bash
pip install virtualenv
sudo pip install virtualenvwrapper # sudoを使わない場合はスクリプトのパスが違うので注意
```

`.bashrc`(シェルが起動するときに読み込まれる設定ファイル)に設定を追加する。

```bash
vim ~/.bashrc
```

```bash
# ~/.bashrc
# 末尾に追加

# virtualenvwrapper
export WORKON_HOME=$HOME/.virtualenvs # 仮想環境のディレクトリ
export PROJECT_HOME=$HOME/Devel
# export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
source /usr/local/bin/virtualenvwrapper.sh # 下記参照
```

補足：アクティベーションスクリプトの例

- non-sudo pip: `~/.local/bin/virtualenvwrapper.sh`
- sudo pip: `/usr/local/bin/virtualenvwrapper.sh`

[次へ(インストール後)](#vscodeの設定)

### MacOS導入手順

インテルとアップルシリコンでディレクトリ構造が違うらしい。
筆者はM1ユーザーでよくわからないので、Intel Macユーザーは適宜読み替えてほしい。
[下記のコマンドを使ったマシンの環境は上記参照。](#環境)

`virtualenv`と`virtualenvwrapper`をpipでインストールする。

```zsh
pip3 install virtualenv
sudo pip3 install virtualenvwrapper # sudoを使わない場合はスクリプトのパスが違うので注意
```

`pip3`(sudoなし)で`virtualenvwrapper`をインストールしたあと、やっぱり`pip3`(sudoあり)でインストールしたい場合は一回アンインストールしてから`sudo`で再インストールすること。

`.zshrc`(シェルが起動するときに読み込まれる設定ファイル)に設定を追加する。
Macはデフォルトで`.zshrc`がないのでもし無ければ新しく作る。

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
source /usr/local/bin/virtualenvwrapper.sh # 下記参照
```

補足：アクティベーションスクリプトの例

- homebrew: `/opt/homebrew/bin/virtualenvwrapper.sh`
- non-sudo pip3: `$HOME/Library/Python/3.9/bin/virtualenvwrapper.sh`
- sudo pip3: `/usr/local/bin/virtualenvwrapper.sh`

[次へ(インストール後)](#vscodeの設定)

### Windows導入手順

#### Windowsではそのまま動かない理由

歴史的にはUNIX系の系譜のOSが系統としてメジャーとされており、日常的に触れるほぼ全てのOSがUNIX系である。

- BSD
  - MacOS -> iOS
  - pfSense
  - trueNAS
- Linux
  - Android
  - Gentoo -> ChromeOS
  - Arch -> Manjaro/SteamOS
  - Debian -> Ubuntu/Mint/Kali
  - RedHat -> Fedora/~~CentOS~~
  - Slackware -> SUSE

Windowsは独自の系譜でかなり特殊なOSである。

UNIX系の基本構造がPOSIX環境としてIEEEで規格化されているのに対して、Windowsはほぼ独自仕様である。

これが開発ではLinuxやMacOSを使う人が多い理由の一つでもある。

`virtualenvwrapper`はPOSIX環境向けに作られているので、Windowsで使うには工夫が必要である。

1. MSYS等のPOSIX環境で使う。
2. Windows用にポートした`[virtualenvwrapper-win](https://pypi.org/project/virtualenvwrapper-win/)`などを使う。

#### ここではMSYSを使う方法を紹介する

MSYSはbashエミュレーター…
要は、Windows上でLinuxコマンドが使えるようになるソフト。

MSYS2を[公式サイト](https://www.msys2.org/)からダウンロードしてインストールする。

公式ドキュメントの[インストールガイド](https://www.msys2.org/wiki/MSYS2-installation/)を読む。

インストールガイドにある通り、MSYS2はArch Linuxで使われている`pacman`というパッケージマネージャを使っている。

TODO

### VSCodeの設定

VSCodeの設定で`python.venvPath`を設定することで、VSCodeのPython環境選択ボタンで`virtualenv`の仮想環境を選択できる。

設定の詳細は[別資料](./vscode_settings.md)参照。

### 使用例

詳細は[virtualenvwrapper公式ドキュメント](https://virtualenvwrapper.readthedocs.io/en/latest/install.html)を参照。

> 再掲: 通常のvirtualenvwrapperでは **`fish`/`powershell`/`cmd`は使えない**のでコマンドの実行は`bash`/`zsh`を使うこと。

```bash
# 使える仮想環境を表示
workon

# 仮想環境env310をactivate
workon env310

# プロジェクトディレクトリ内で仮想環境env310を作成
cd /path/to/project # プロジェクトディレクトリに移動
mkvirtualenv env310

# Pythonインタープリターを指定して仮想環境spenv38を作成
mkvirtualenv -p /usr/bin/python3.8 spenv38

# Pythonバージョンを3.11に指定して仮想環境env311を作成
# コマンドpython3.11が適切なインタープリターに向いていること
mkvirtualenv -p python3.11 env311

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
