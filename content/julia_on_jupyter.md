Title: Juliaをjupyter notebookで使う
Date: 2018-12-14 21:52
Category: Julia

Juliaはjupyter notebookでも使うことができます。

```
julia> ]
(v1.0) pkg> add Conda
(v1.o) pkg> add IJulia
julia > using IJulia
```

Juliaからnotebookを起動するには、

```
julia> using IJulia
julia> IJulia.notebook()
```

ですが、jupyter notebookを起動すると、
Juliaカーネルを選択できるように追加されるので普通にjupyter notebookを開いてJuliaを選んでも良いかも。

自分の環境（macOS Sierra、Jupyter notebookはインストール済み）では非常に簡単にJulia kernelをインストールできました。

[参考](https://ystt.hatenablog.com/entry/2018/08/11/141132)

