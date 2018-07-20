Title: Show HTML as innerHTML in Riot.js each loop.
Date: 2018-07-20 14:38
Category: JS

# Riot.jsのeachループの中でデータをHTMLとして出力する方法。

Riot.jsの{}の値は常にエスケープされるため、例えば数式などで`<sub>`タグを
使った場合、タグはHTMLとして解釈されずそのまま文字列として表示されてしまいます。

Riotタグの中でHTMLを使いたい場合、色々ググってみた感じ、
一派的には[innerHTMLとして出力する](https://qiita.com/zrelyydereva/items/b468e36e03885044c128)ようです。

タグの中で一箇所出力する場合はinnerHTMLをそのまま使えば良いのですが、
Riotのeachループの中でHTMLを出力しようとした場合やや面倒だったので解決方法をメモしておきます。


```javascript
<summary>
    <ul>
       <virtual each="{list}">
          <div id={uid} ></div>
       </virtual>
    </ul>
    
    <script>
        fetch(何かのAPI)
            .then(function (data) {
                return data.json()
            })
            .then(function (json) {
                this.list = json;
                this.update();
                for (item of self.list){
                    var elm = document.getElementById(item["uid"])
                    elm.innerHTML = item["text"]
                }
            }
            .bind(this));
    </script>
</summary>
```
updateの後、動的にidを振られたDOMにinnerHTMLを渡してHTMLとしてタグを表示することができました。
