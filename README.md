# python memo

# Jupyter Notebookの起動
`$ jupyter-notebook`

## pythonでの代入
大抵は値渡しではなく参照渡し→気をつけよう!
変更不可（immutable）な型をもつオブジェクトが渡されたときは値渡しのようにふるまいます
https://crimnut.hateblo.jp/entry/2018/09/05/070000

## コレクション

||要素の順番|変更|重複|書き方|
|:-:|:-:|:-:|:-:|:-:|
|リスト|あり|可能|許容|[] or list()|
|タプル|あり|不可能|許容|() or tuple()|
|セット|なし|可能|認めない|{} or set()|
|リスト|なし|可能(keyは不変)|key:認めない 値:許容|{:} or dict()|

### sliceによるlistの反転

スライスは`[start:stop:end]`の形で範囲や増分を指定する。start, stopを省略すると全体を選択し、stepを-1とすると後ろから一つずつ要素を取得することになるので`[::-1]`とすると逆順に並べ替えられたオブジェクトが取得できる。

```
org_list = [1, 2, 3, 4, 5]

new_list = org_list[::-1]
print(org_list)
print(new_list)
# [1, 2, 3, 4, 5]
# [5, 4, 3, 2, 1]
```

https://qiita.com/rsakamot/items/2277e26e3716e8f8f5a2

### タプルのメリット
- 戻り値や不変にしておきたいデータ群を設定することに役立つ
- リストよりも高速に処理できる
- ハッシュ化できる

### セットのメリット
- 順番を持たずユニークな値しか保持しないため、厳格。これを使って集合処理ができる

### 要素をランダムで取り出す
https://note.nkmk.me/python-random-choice-sample-choices/

## ファイル操作
ファイルを開いたら、最後に`file.close()`が必要となるが、仮につけていなかった場合、ガベージコレクタがファイルオブジェクトを自動で削除する。
でも、それまでの間にファイルオブジェクトが生成されたままとなっている可能性がある。

`with open()`構文でファイルを開くと、ファイル入出力はインデントの間だけ実行される。

```sample.py
# file = open("file_ope/data.txt")
# data = file.read()
# print(data)
# file.close()

# やってることは一緒
with open("file_ope/data.txt") as file:
    print(file.read())
```

### importするモジュール名とファイル名の重複に気をつけること!!!

# 仮想環境の作成

```bash
# 特定のプロジェクト内で
% python3 -m venv [仮想環境名(venvにしとくといいよ)] # use: $ python3 -m venv venv
% source venv/bin/activate
# これでpipをするとvenv内の実行環境にライブラリがインストールされるから環境が汚染されない!

# 仮想環境から抜けるには
% deactivate
```

# numpyのチュートリアル
https://deepage.net/features/numpy/



## Sort

```py
>>> import numpy as np
>>> ary = np.random.randn(3)
>>> ary = np.random.randn(100, 3)
>>> ary.shape
(100, 3)
>>> ary[:5]
array([[-2.55202485, -0.22806646, -0.05205104],
       [-0.55630503, -1.47442745, -0.49591525],
       [-0.80667429,  0.06196743,  0.29047129],
       [ 0.33160321,  0.34339444, -1.64164379],
       [ 0.06125807, -0.51736907, -0.26786316]])
>>> ary[:, 1][:5]
array([-0.22806646, -1.47442745,  0.06196743,  0.34339444, -0.51736907])
>>> ary[:, 1][:5].argsort()
array([1, 4, 0, 2, 3])
>>> srt = ary[:, 1][:5].argsort()
>>> ary[:5][srt]
array([[-0.55630503, -1.47442745, -0.49591525],
       [ 0.06125807, -0.51736907, -0.26786316],
       [-2.55202485, -0.22806646, -0.05205104],
       [-0.80667429,  0.06196743,  0.29047129],
       [ 0.33160321,  0.34339444, -1.64164379]])
>>> ary[:5][srt][::-1]
array([[ 0.33160321,  0.34339444, -1.64164379],
       [-0.80667429,  0.06196743,  0.29047129],
       [-2.55202485, -0.22806646, -0.05205104],
       [ 0.06125807, -0.51736907, -0.26786316],
       [-0.55630503, -1.47442745, -0.49591525]])
>>> ary[:5][srt][::-1][:3]
array([[ 0.33160321,  0.34339444, -1.64164379],
       [-0.80667429,  0.06196743,  0.29047129],
       [-2.55202485, -0.22806646, -0.05205104]])
```

# 開発環境周りメモ

## Big Surでpyenvでinstallできない

### 症状
pyenvを使ってPythonをインストールしようとしたら失敗した(他バージョンでも同じ症状)

```
-> % pyenv install 3.7.6
python-build: use openssl@1.1 from homebrew
python-build: use readline from homebrew
Downloading Python-3.7.6.tar.xz...
-> https://www.python.org/ftp/python/3.7.6/Python-3.7.6.tar.xz
Installing Python-3.7.6...
python-build: use readline from homebrew
python-build: use zlib from xcode sdk

BUILD FAILED (OS X 11.2.1 using python-build 1.2.23-54-gabcbf6e1)
.
.
.
```

### 解決策

3.9.0に対しては、これで解決した
環境変数を追加して再度インストールしてみる

```
-> % LDFLAGS="-L$(xcrun --show-sdk-path)/usr/lib" pyenv install 3.9.0
```