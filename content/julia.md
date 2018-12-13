Title: CellFishing.jlを試してみる
Date: 2018-12-12 21:54
Category: Julia

# CellFishing.jlを試してみる

高速に大量の細胞の類似度を解析するツールの[CellFishing.jl](https://github.com/bicycle1885/CellFishing.jl)を試してみました。


CellFishing.jlはJuliaで動くツールなのですが、Juliaを使うのも初めてのため、実行環境からたどたどしく設定していきます。


## MacにJuliaの環境を作る

macOS Sierraだとbrew installでjuliaをインストールできる

```
brew cask install Caskroom/cask/julia
```

`$ julia`で対話環境がスタートします。

対話環境は

```julia
               _
   _       _ _(_)_     |  Documentation: https://docs.julialang.org
  (_)     | (_) (_)    |
   _ _   _| |_  __ _   |  Type "?" for help, "]?" for Pkg help.
  | | | | | | |/ _` |  |
  | | |_| | | | (_| |  |  Version 1.0.2 (2018-11-08)
 _/ |\__'_|_|_|\__'_|  |  Official https://julialang.org/ release
|__/                   |

julia> 
```
こんな感じです。

CellFishingはJulia 0.7.0以降をサポートしているようです。
brewでインストールされるJuliaのバージョンは1.0.2（これを書いている時点で）なので、JuliaのバージョンはOKと思われます。

## CellFishingをインストールする

公式のREADMEを見ながらCellFishing.jlをインストールしてみます。

Juliaをインストールする前に対話環境で"HDF5"と"DocOpt"を対話環境でインストールします。

※ juliaでHDF5をインストールする前に`brew install hdf5`する必要があるかもしれません。
```
$ julia
julia > using Pkg
julia > Pkg.build("HDF5")
julia > Pkg.build("DocOpt")
```

次にCellFishing.jlのインストール。
これも対話環境でインストールできます。

```
julia > ]
(v1.0) pkg> add git@github.com:bicycle1885/CellFishing.jl.git
```
エラーメッセージがでなければインストール成功とのこと。

```
$ julia -e 'using Pkg; Pkg.test("CellFishing")'
```
で、ユニットテストできます。

### コマンドラインインターフェースでCellFishing.jlを試す


実際に解析を試すにはデータが必要ですが、[こちらのブログ](https://bonohu.github.io/cellfishing.html)を参考に
loomフォーマットのデータを取得します。

```
$ curl -O http://bimsbstatic.mdc-berlin.de/rajewsky/PSCA/all_sgete_4GU75.loom.gz 
$ gzip -dc all_sgete_4GU75.loom.gz > Plass2018.dge.loom
```

公式のREADMEにあるように、bin/cellfishingスクリプトをこのファイルを使ってコマンドラインインターフェースで呼び出します。

```
$ ./bin/cellfishing build Plass2018.dge.loom

Build a search database from Plass2018.dge.loom.
  Loading data ―――――――――――― 17 seconds, 22 milliseconds
  Selecting features ―――――― 1 second, 46 milliseconds
  Creating a database ―――――  ** On entry to SLASCLS parameter number  4 had an illegal value
 ** On entry to SLASCLS parameter number  4 had an illegal value
 ** On entry to SLASCLS parameter number  5 had an illegal value
6 seconds, 637 milliseconds
  Writing the database ―――― 779 milliseconds
The serialized database is in Plass2018.dge.loom.cf.
```
`Plass2018.dge.loom.cf`ファイルが生成されるのですが、
"Creating a database ――――― "の行が怪しいような。。

処理を続けます。
```
$ ./bin/cellfishing search Plass2018.dge.loom.cf Plass2018.dge.loom >neighbors.tsv

  Loading the database ―――― 409 milliseconds
  Loading query data ―――――― 14 seconds, 822 milliseconds
  Searching the database ―― ERROR: LoadError: MethodError: no method matching findneighbors(::Int64, ::Array{Float32,2}, ::Array{String,1}, ::CellFishing.CellIndex; inferparams=false)
```
`no method matching findneighbors()`のように、LoadErrorとMethodErrorが発生し、は実行後に空の`neighbors.tsv`生成されます。


### Juliaの対話環境でCellFishing.jlを試す

先ずは小さなテスト用のデータ（"test.csv"）を作成して
Juliaの対話環境からCellFishing.jlを試してみます。

テスト用のデータはPythonで作ることにしました。

```python
import numpy as np
data = np.random.rand(10, 10)
np.savetxt('test.csv', data, delimiter=',', header="A,B,C,D,E,F,G,H,I,J", comments='')
```
これをJuliaで読み込みます。

```
$ julia

julia> using CellFishing
julia> using CSV
julia> data = CSV.read("test.csv", delim=',')

10×10 DataFrames.DataFrame
│ Row │ A         │ B         │ C        │ D        │ E         │ F         │ G        │ H         │ I        │ J        │
│     │ Float64⍰  │ Float64⍰  │ Float64⍰ │ Float64⍰ │ Float64⍰  │ Float64⍰  │ Float64⍰ │ Float64⍰  │ Float64⍰ │ Float64⍰ │
├─────┼───────────┼───────────┼──────────┼──────────┼───────────┼───────────┼──────────┼───────────┼──────────┼──────────┤
│ 1   │ 0.534841  │ 0.639309  │ 0.951326 │ 0.51084  │ 0.322408  │ 0.523939  │ 0.27006  │ 0.163017  │ 0.434338 │ 0.901502 │
│ 2   │ 0.0446897 │ 0.304389  │ 0.75983  │ 0.300121 │ 0.219402  │ 0.56748   │ 0.347568 │ 0.456145  │ 0.227494 │ 0.170003 │
│ 3   │ 0.91494   │ 0.631714  │ 0.938954 │ 0.580154 │ 0.329751  │ 0.277192  │ 0.749877 │ 0.770334  │ 0.219486 │ 0.290027 │
│ 4   │ 0.517408  │ 0.764021  │ 0.131445 │ 0.711081 │ 0.0529305 │ 0.626505  │ 0.522473 │ 0.405844  │ 0.526721 │ 0.298303 │
│ 5   │ 0.105178  │ 0.981811  │ 0.650602 │ 0.245692 │ 0.59703   │ 0.0556564 │ 0.659795 │ 0.963262  │ 0.644763 │ 0.880459 │
│ 6   │ 0.404249  │ 0.240975  │ 0.983174 │ 0.502272 │ 0.606391  │ 0.367596  │ 0.452079 │ 0.257366  │ 0.973874 │ 0.838456 │
│ 7   │ 0.576502  │ 0.714612  │ 0.414675 │ 0.267715 │ 0.493334  │ 0.400844  │ 0.70549  │ 0.377676  │ 0.299713 │ 0.739577 │
│ 8   │ 0.370031  │ 0.0245976 │ 0.629839 │ 0.7359   │ 0.158023  │ 0.27708   │ 0.319453 │ 0.0474918 │ 0.923331 │ 0.605994 │
│ 9   │ 0.196867  │ 0.65604   │ 0.821253 │ 0.260594 │ 0.221491  │ 0.737735  │ 0.453375 │ 0.634916  │ 0.477798 │ 0.387817 │
│ 10  │ 0.304881  │ 0.864026  │ 0.293561 │ 0.210086 │ 0.543985  │ 0.796436  │ 0.222279 │ 0.729611  │ 0.403505 │ 0.678046 │

julia> typeof(data)
DataFrames.DataFrame
```
CSV.read()で生成したデータはJuliaのDataFrame.DataFrameになる模様
（テーブルの上にも表示されていますが）

CellFishing.jlの処理に続きます。

```
julia> cellnames = string.(names(data))
julia> featurenames = ["1","2", "3","4","5","6","7","8","9","10"]
julia> counts = Matrix(data[:,:])

```

生成したJuliaのDataFrameにはindex nameが含まれません（行番号のみ）。
とりあえずfeaturenamesには行番号的数字を渡してみます。
このDataFrameは属性的な文字列を含まないのでcountsにはdata全体を渡して、処理を続けます。

```
julia> features = CellFishing.selectfeatures(counts, featurenames)
julia> database = CellFishing.CellIndex(counts, features, metadata=cellnames)
ERROR: MethodError: no method matching CellFishing.CellIndex(::Array{Union{Missing, Float64},2}, ::CellFishing.Features; metadata=["A", "B", "C", "D", "E", "F", "G", "H", "I", "J"])
Closest candidates are:
  CellFishing.CellIndex(::Any, ::Any, ::Any, ::Any, ::Any) at /Users/oec/.julia/packages/CellFishing/xKKa0/src/index.jl:103 got unsupported keyword argument "metadata"
  CellFishing.CellIndex(::AbstractArray{#s17,2} where #s17<:Real, ::CellFishing.Features; metadata, index, keep_counts, normalize, scalefactor, transformer, n_dims, randomize, standardize, n_bits, n_lshashes, superbit) at /Users/oec/.julia/packages/CellFishing/xKKa0/src/index.jl:178
Stacktrace:
 [1] top-level scope at none:0

```

MethodErrorに。。no method matching CellFishin.CellIndex() とのこと。

この処理に渡すcsv・arrayが妥当かどうかがまず妖しいところなので、
テスト用のデータをCellFishing.jlgithubに置いていただけたりすると
ありがたいかもしれません。




