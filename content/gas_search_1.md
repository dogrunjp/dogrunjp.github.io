Title: Google Apps Script, search with url parameter.
Date: 2018-07-14 17:30
Category: JS

# Google Apps Script(GAS)でスプレッドシートを検索する

Googleスプレッドシートの特定のカラムを、URLパラメータを引数に検索して
マッチしたデータのみJSONで返す様なAPIのサンプル。

```javascript
function getData(id, sheetName, v) {
  var val = v || " "
  var sheet = SpreadsheetApp.openById(id).getSheetByName(sheetName);
  var rows = sheet.getDataRange().getValues();
  //var keys = rows.splice(0, 1)[0];
  rows.shift()
  var keys = ["id", "title", "date"]
  var l = rows.map(function(row) {
      
    //必要なカラムの値だけオブジェクトに渡します。
    var item = [row[2].split('/').slice(-1)[0], row[3], row[9]] 
    
    // title要素を引数で検索し、matchした場合だけobjを返す
    if(item[1].match(val) ){
      var obj = {}
      item.map(function(x, i) {
        obj[keys[i]] = x;
      });
      return obj;
    }
  });
  l = l.filter(Boolean)
  return l
}

function doGet(e) {
  var val = e.parameter.v  //URLパラメータを取得
  var data = getData(スプレッドシートID, ワークシート名, val);
  return ContentService.createTextOutput(JSON.stringify(data, null, 2))
  .setMimeType(ContentService.MimeType.JSON);
}


```

