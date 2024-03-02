# VSCode Settings Guide


- VSCode extension: [Python Environment Manager](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python-environment-manager)

VSCodeではプロジェクトディレクトリ内の`.vscode/settings.json`に以下を追加することで、プロジェクトディレクトリ外のPython環境を選択できる。このファイルのレポジトリ内に例を用意しているので参照。

```json
{
    "python.pythonPath": "/path/to/python"
}
```

Python環境のパスをプロジェクトディレクトリから見た相対パスで指定することもできる。

しかし、あまり効率が良くないので、`virtualenv`を使うことをオススメする。と言いつつ、ターミナルでの使い勝手を捨てるならVSCodeの設定で`python.venvPath`を設定すれば`venv`だけでも良いかもしれない。

設定画面のGUIから設定可能。`Python: Venv Path`を検索して以下の値を設定する。

`"~/.virtualenvs"`

このパスは`virtualenvwrapper`で定義されている。

補足：virtualenvwrapperを使わない場合（例えばvenvしか使わないとしても）、この設定は便利。どこかフォルダーを一つ作って、そのフォルダーの中だけに全ての仮想環境を作るという運用にすることもできる。そのフォルダーを`python.venvPath`に設定することで、VSCodeのPython環境選択ボタンでそのフォルダー内の仮想環境を選択できる。ターミナルで使う時はプロジェクトディレクトリから遠く離れた場所のactivateスクリプトを実行することになるので色々工夫しないと面倒だが、VSCodeだけなら使い勝手は良い。