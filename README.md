# Python初心者向け勉強会資料

This repo is written in Japanese.

## 対象者

Pythonを数十時間触った初心者

- Pythonの基本的な文法を理解している
- pipでパッケージをインストールして使ったことがある
- vscodeで以下の操作ができる
  - フォルダーを開く
  - エクステンションをインストールする
  - Pythonファイルを作成する
  - インタープリターを選択する
  - Pythonファイルを実行する
  - jupyter notebookでも同じことができる
- 相対パス、絶対パスがわかる
- マークダウンを使える
- ターミナルで以下の操作を行える
  - ls
  - cd

## code map

- python_conventions.py: コーディング規約とベストプラクティス
- python_environment.py: 開発環境構築
- func: 関数、クラスディレクトリ
  - helper_functions.py: helper関数
- .vscode/settings.json: 参考用のVSCodeの設定ファイル

## マークダウン基本

**重要！**：このファイルに限らず、`README.md`または`README`という名前のファイルがあったらまず最初に開いて読む癖をつけましょう。
`README`にはユーザーに知ってほしいことを書くことになっています。これは世界共通ですので覚えておきましょう。

このファイルはマークダウンという形式のファイルです（拡張子`.md`）。
マークダウンファイルは簡単な文体修飾機能をつけたテキストファイルの様なものです。
めちゃ簡単で、メモアプリからブログまで広く使われているので初めましての方は勉強だと思ってまずは以下のウェブサイトを開いて読んでみましょう。

[マークダウンの基本](https://tracpath.com/works/development/markdown_basics/)

チートシート：

# h1
## h2
### h3
#### h4
##### h5

---

**bold**

*italic*

~~strike~~

`code`

---

- item1
- item2
  - item2-1
  - item2-2
- item3

---

1. item1
2. item2
   1. item2-1
   2. item2-2
3. item3

---

| col1 | col2 | col3 |
|------|------|------|
| 1    | 2    | 3    |
| 4    | 5    | 6    |

---

| col1 | col2 | col3 |
|:-----|:----:|-----:|
| 1    | 2    | 3    |
| 4    | 5    | 6    |

---

link: [Google](https://www.google.com)

---

```python
# python code block
print('Hello, world!')
```