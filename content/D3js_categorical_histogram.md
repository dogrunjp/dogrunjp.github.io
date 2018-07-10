Title: D3v4 use categorical scale  
Date: 2018-07-10 18:26
Category: D3

# D3v4でカテゴリカルなスケールのヒストグラムを書くベストプラクティスはを考えてみた 

最近、[Riotx](https://qiita.com/elastic/items/dfadc8da4cc22bddfb15)をある程度使えるようになった（気がする）
ので、Riotxを使って複数のコンポーネントに跨ったチャートの状態を同期した拡張版crossfilterのようなものが書けないか検討しています。

試しに簡単なサンプルを作って見ようと、d3.histogramを使って書いて見ようとしたのですが、
d3.scaleOrdinalなデータをhistogramに変換できない。。
ということで、D3.jsでカテゴリー的スケールのヒストグラムを書く良い方法を検討してみました。

## オブジェクトの配列を度数に変換する

ヒストグラム用のデータを作るなら当たり前の処理ですが、この処理をモジュール的に作っておこうと思います。

__d3category.js__
```javascript
const d3category = {
  count: function (data, k) {
    const nestedData = d3.nest()
      .key(function(d) {return d[k]})
      .entries(data);

    const l = [];
    nestedData.forEach((d) => {
      const item = {}
      l.push({key: d.key, count: d.values.length})
    })

    return l;
  },
  filter: function (data, s) {
    // crossfitlter用に準備
    const res = data.filter(d => {return d[s[0]] === s[1]});
    return res
  }

};
```

## 変換した度数のデータで普通にbarcharを描画する。

データはこんな感じ。普通はd3.requestsを使って読み込むと思います。
```javascript
const data = [
    {age: 1, breed: "Chihuahua"},
    {age: 2, breed: "Chihuahua"},
    {age: 2, breed: "Chihuahua"},
    {age: 2, breed: "Poodle"},
    {age: 3, breed: "Chihuahua"},
    {age: 3, breed: "Shiba"},,,
]
```

これを上記の関数で度数に変換し、そのデータで
```javascript
const  width = 600,
    height = 300;

const cats = d3category.count(data, "breed");

const svg = d3.select('svg'),
    xScale = d3.scaleBand()
      .range([0, width])
      .domain(cats.map(d => d.key))
      .padding(0.05),
    yScale = d3.scaleLinear()
      .domain([0, d3.max(cats.map(x => x.count))])
        .range([0, height]);

svg.selectAll('rect')
    .data(cats)
    .enter()
    .append('rect')
    .attr('width', xScale.bandwidth())
    .attr('height', d => yScale(d.count))
    .attr('x',  d => xScale(d.key))
    .attr('y', d => height - yScale(d.count));

svg .append("g")
  .attr("transform", "translate(0, " + height + ")")
  .call(d3.axisBottom(xScale));

svg.append("g")
  .call(d3.axisLeft(yScale));

```
結果、極々普通の書き方に。

実行結果は[こちら](https://codepen.io/dogrunjp/pen/ajbdjO)。