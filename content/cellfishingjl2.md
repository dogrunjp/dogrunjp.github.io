Title: CellFishing.jlを試してみる(2nd)
Date: 2018-12-14 11:30
Category: Julia

Githubのmasterブランチが修正されたとのことなので、
前回と同様にコマンドラインインターフェースと対話環境でCellFishing.jlにトライしてみました。

### コマンドラインインターフェースでPlass2018.dge.loomをCellFishin.jlする

先ずは[公式のREADME](https://github.com/bicycle1885/CellFishing.jl/blob/master/README.md)と同様に
Plass2018.dge.loomをもう一度CellFishing.jlします。

```
$ ./bin/cellfishing build Plass2018.dge.loom
Build a search database from Plass2018.dge.loom.
  Loading data ―――――――――――― 14 seconds, 149 milliseconds
  Selecting features ―――――― 966 milliseconds
  Creating a database ―――――  ** On entry to SLASCLS parameter number  4 had an illegal value
  ...
  
  Writing the database ―――― 777 milliseconds
The serialized database is in Plass2018.dge.loom.cf.
```
同じサンプルを使うので`On entry to SLASCLS parameter number  4 had an illegal value`は残りますが気にせず続けます。

```
$ ./bin/cellfishing search Plass2018.dge.loom.cf Plass2018.dge.loom >neighbors.tsv
Search Plass2018.dge.loom.cf for 10 neighbors.
  Loading the database ―――― 399 milliseconds
  Loading query data ―――――― 13 seconds, 957 milliseconds
  Searching the database ―― 3 seconds, 15 milliseconds
  Writing neighbors ――――――― 34 milliseconds
  
$ less -S -N neighbors.tsv 

1 all_sgete_4GU75:AAAATTACTTGGCx  all_sgete_4GU75:AAAATTACTTGGCx  all_sgete_4GU75:AAAAGGTTTACTTx  all_sgete_4GU75:AAAAGACTAGTGTx  
      2 all_sgete_4GU75:AAAAGGTTTACTTx  all_sgete_4GU75:AAAATTACTTGGCx  all_sgete_4GU75:AAAAGGTTTACTTx  all_sgete_4GU75:AAAAGACTAGTGTx  
      3 all_sgete_4GU75:AAAAGACTAGTGTx  all_sgete_4GU75:AAAATTACTTGGCx  all_sgete_4GU75:AAAAGGTTTACTTx  all_sgete_4GU75:AAAAGACTAGTGTx  
      4 all_sgete_4GU75:AAACCCTAACAGCx  all_sgete_4GU75:AAAATTACTTGGCx  all_sgete_4GU75:AAAAGGTTTACTTx  all_sgete_4GU75:AAAAGACTAGTGTx  
      5 all_sgete_4GU75:AAACATGATGCGAx  all_sgete_4GU75:AAAATTACTTGGCx  all_sgete_4GU75:AAAAGGTTTACTTx  all_sgete_4GU75:AAAAGACTAGTGTx  
      6 all_sgete_4GU75:AAAGACCACAGTGx  all_sgete_4GU75:AAAATTACTTGGCx  all_sgete_4GU75:AAAAGGTTTACTTx  all_sgete_4GU75:AAAAGACTAGTGTx  
```
Error発生せず。それらしいneighbors.tsvが出力されました。


### テスト用のloomファイルを作り、コマンドラインインターフェースで試す

loompyで作ったテスト用のファイルを同様にコマンドラインインターフェースで試してみます。

先ずはPythonのnumpy.random.randを使って配列を作り、loomとして書き出します。
```python
import numpy as np
import loompy
filename = "test.loom"
matrix = np.random.rand(10, 10)  # 0.0以上、1.0未満、10x10の配列の乱数
row_attrs = { "SomeRowAttr": np.arange(10) }
col_attrs = { "SomeColAttr": np.arange(10) }
loompy.create(filename, matrix, row_attrs, col_attrs)
```
このファイルをJupyter notebookで見てみると、
```
ds = loompy.connect(filename)
ds

          SomeColAttr	0	                1	                  2	                3	                4	                5	                6	                  7	                8	                 9
SomeRowAttr	 	 	 	 	 	 	 	 	 	 	 
0	 	0.2396876204321453	0.7873450487005871	0.37780645241374833	0.8671433367989253	0.6134126218299916	0.6257857211933744	0.2351991261037153	0.05687990825893263	0.9422973537466136	0.4850026172656482
1	 	0.5144405258272866	0.9985185543089808	0.05208261292199956	0.5206197195865041	0.49523984875891747	0.7786804192427867	0.5406144475897734	0.46217822694288824	0.8023488955224481	0.36528632965640706
2	 	0.9419281325841554	0.33141216680528085	0.7249619131535856	0.4822207679952296	0.3740075932444845	0.0028364580008067852	0.04292462230648897	0.6168198766038926	0.31808567902378126	0.05013101551316135
3	 	0.27300380376870936	0.3684618743139705	0.9133050710627907	0.47127482205237126	0.20493957564658427	0.3219545803644297	0.2265000220532043	0.46393198401263536	0.8891700592418956	0.7842988293468391
4	 	0.03956714615110801	0.08015128296788943	0.7469051695357061	0.625519235272402	0.9960447662950902	0.20891999703998143	0.09333740092671794	0.08245896197053637	0.5141247696313814	0.5232399512979921
5	 	0.7561411405454064	0.6671049178984818	0.22151345475650208	0.30925906316109375	0.7425441989162589	0.6181628507666441	0.4422587152654789	0.6619072825701765	0.5221547626875674	0.9088153607121365
6 ....
```
それらしいファイルが出力された気がします。

では、Plass2018.dge.loomと同様にこのファイルをCellFishing.jlします。

```
$ ./bin/cellfishing build test.loom
Build a search database from test.loom.
  Loading data ―――――――――――― HDF5-DIAG: Error detected in HDF5 (1.10.4) thread 0:
  #000: H5O.c line 120 in H5Oopen(): unable to open object
    major: Object header
...
ERROR: LoadError: Error opening object ///row_attrs/Gene
```

numpy.random.rand()で作ったテスト用のデータはLoadErrorとなりました。
データのフォーマットが違いそうな気がします。


### 対話環境でCSVを読み込む

[一回目](https://dogrunjp.github.io/cellfishingjlwoshi-shitemiru.html)と同様の10x10のcsvファイルを使って
対話環境でCellFishing.jlを試します。

```
julia> using CellFishing
julia> using CSV
julia> data = CSV.read("test.csv", delim=',')
10×10 DataFrames.DataFrame
│ Row │ A        │ B        │ C        │ D        │ E         │ F        │ G        │ H         │ I         │ J         │
│     │ Float64⍰ │ Float64⍰ │ Float64⍰ │ Float64⍰ │ Float64⍰  │ Float64⍰ │ Float64⍰ │ Float64⍰  │ Float64⍰  │ Float64⍰  │
├─────┼──────────┼──────────┼──────────┼──────────┼───────────┼──────────┼──────────┼───────────┼───────────┼───────────┤
│ 1   │ 0.66881  │ 0.684548 │ 0.543321 │ 0.395865 │ 0.580571  │ 0.181782 │ 0.463489 │ 0.114986  │ 0.45077   │ 0.273915  │
│ 2   │ 0.628929 │ 0.655457 │ 0.568615 │ 0.510255 │ 0.772066  │ 0.824264 │ 0.59687  │ 0.628348  │ 0.418789  │ 0.983976  │
│ 3   │ 0.723614 │ 0.418452 │ 0.249667 │ 0.139474 │ 0.334926  │ 0.376858 │ 0.192299 │ 0.0670013 │ 0.93907   │ 0.140252  │
│ 4   │ 0.963905 │ 0.147077 │ 0.231303 │ 0.70938  │ 0.497994  │ 0.30241  │ 0.314685 │ 0.0725281 │ 0.0401966 │ 0.569654  │
│ 5   │ 0.319602 │ 0.112272 │ 0.982957 │ 0.7491   │ 0.830083  │ 0.393083 │ 0.687088 │ 0.343796  │ 0.0315826 │ 0.323356  │
│ 6   │ 0.42922  │ 0.166654 │ 0.162221 │ 0.864021 │ 0.015853  │ 0.20722  │ 0.687266 │ 0.44684   │ 0.0539362 │ 0.172383  │
│ 7   │ 0.379178 │ 0.154495 │ 0.485539 │ 0.824225 │ 0.429369  │ 0.674071 │ 0.184348 │ 0.647457  │ 0.0908948 │ 0.729107  │
│ 8   │ 0.816236 │ 0.825931 │ 0.801143 │ 0.211432 │ 0.671175  │ 0.678665 │ 0.141858 │ 0.713245  │ 0.0540037 │ 0.0674242 │
│ 9   │ 0.737016 │ 0.995226 │ 0.847743 │ 0.914302 │ 0.484938  │ 0.467824 │ 0.271309 │ 0.58344   │ 0.244783  │ 0.280495  │
│ 10  │ 0.707984 │ 0.715595 │ 0.524505 │ 0.453797 │ 0.0954121 │ 0.942033 │ 0.5295   │ 0.228389  │ 0.551513  │ 0.747924  │

julia> cellnames = string.(names(data))
julia> featurenames = ["1","2", "3","4","5","6","7","8","9","10"]
julia> counts = Matrix(data[:,:])
10×10 Array{Union{Missing, Float64},2}:
 0.66881   0.684548  0.543321  0.395865  0.580571   0.181782  0.463489  0.114986   0.45077    0.273915 
 0.628929  0.655457  0.568615  0.510255  0.772066   0.824264  0.59687   0.628348   0.418789   0.983976 
 ...
 
julia> counts = Matrix{Float64}(counts)
10×10 Array{Float64,2}:
 0.66881   0.684548  0.543321  0.395865  0.580571   0.181782  0.463489  0.114986   0.45077    0.273915 
 0.628929  0.655457  0.568615  0.510255  0.772066   0.824264  0.59687   0.628348   0.418789   0.983976 
 ...
 
julia> features = CellFishing.selectfeatures(counts, featurenames)
CellFishing.Features(<#features=10,#selected=1>)

julia> database = CellFishing.CellIndex(counts, features, metadata=cellnames)
ERROR: ArgumentError: invalid n_dims

```
CellFishing.CellIndex()実行時に、前回試した時に発生したMethodErrorは無くなりましたが、
でArgumentErrorはまだ残ります。。。

コマンドラインインターフェースでも対話型実行環境でも、自作のサンプルデータ（loom or csv）を読み込んでの
CellFishing.jlは失敗していますが、なんとなく読み込んだファイルのフォーマットの不具合がErrorの原因かも、、とい気はしています。
CellFishin.jlを実際に利用する際は各種データをloomに変換して使うことになると思うので、
そのあたりの設定等の情報をもう少しよく読んでまたトライしてみたいと思います。

