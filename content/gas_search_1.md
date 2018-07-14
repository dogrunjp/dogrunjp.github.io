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
  //URLパラメータを取得
  var val = e.parameter.v  
  var data = getData(スプレッドシートID, ワークシート名, val);
  return ContentService.createTextOutput(JSON.stringify(data, null, 2))
  .setMimeType(ContentService.MimeType.JSON);
}

```

次は、取得する行をレンジ指定するAPIを考えてみた。
pager的に取得するページとページのサイズを引数として渡すと以下のようになる。

```javascript
function getData(id, sheetName, p, m) {
  var p = p || 1
  var retmax = m || 10
  
  // columnのstart,rangeはとりあえず固定とする
  var col_start = 3
  var col_range = 3
  
  // 引数のpageは1スタートとする
  // getRange()に渡すrow:starting row を計算。header行があるため+2。
  var start = (p - 1) * retmax + 2
  
  var sheet = SpreadsheetApp.openById(id).getSheetByName(sheetName);
  // start row, 
  var rows = sheet.getRange(start, col_start, retmax, col_range).getValues();
  var keys = ["col1", "col2", "col3"]
  
  //行の範囲を選択
  return rows.map(function(row) {
    var obj = {}
    row.map(function(item, index) {
      obj[keys[index]] = item;
    });
    return obj;
  });
}

function doGet(e) {
  var page = e.parameter.page
  var max = e.parameter.max
  var data = getData('スプレッドシートID', 'ワークシート名', page, max);
  return ContentService.createTextOutput(JSON.stringify(data, null, 2))
  .setMimeType(ContentService.MimeType.JSON);
}

```

