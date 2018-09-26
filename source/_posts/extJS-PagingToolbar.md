---
title: extJS 3.4：PagingToolbar 搭配 MemoryProxy
date: 2018-09-24 10:21:01
tags: extJS
---

在使用 PagingToolbar 不論你使用哪種 Proxy 得到資料，後端的資料結構都應該是一個 JSON 格式  

JSON 裡面包含目前這個分頁要呈現的資料 `$returnResult['rows']` 對應到 reader 的 root  

及全部的資料數量 `$returnResult['results']` 對應到 reader 的 totalProperty  

讓 PagingToolbar 知道該如何分頁

<!--more-->

```php
// GetDBdata.php

// ...
// some code to process data
// ...

$returnResult['rows'] = $resultArray;
$returnResult['results'] = $totalCount;

$rtn = json_encode($returnResult);
return $rtn;
```

當使用 Ext.data.MemoryProxy 作為 store 的 proxy 時，要更新資料可以直接把資料寫入 store.proxy.data  

再藉由 store.load() 刷新資料

```javascript
// main.js

var limitRow = 25; // server script should only send back 25 items at a time
var startRow = 0;

var drawColumns = function() {
    Ext.QuickTips.init();
    Ext.QuickTips.getQuickTip().dismissDelay = 0;

    store = new Ext.data.GroupingStore({
        proxy: new Ext.data.MemoryProxy(),
        pageSize: limitRow,
        reader: getReader(),
        sortInfo: { field: 'date', direction: 'DESC' },
        enablePaging: true
    });

     grid = new Ext.grid.GridPanel({
            tbar: new Ext.PagingToolbar({
            store: store,       // grid and PagingToolbar using same store
            displayInfo: true,
            pageSize: limitRow,
            prependButtons: true,
            listeners: {
                beforechange: function(pagingtoolbar,pageData){ // 點擊換頁時再去要資料
                    startRow = pageData.start;
                    limitRow = pageData.limit;
                    doGetData();
                },
                // change: function(pagingtoolbar,pageData){
                // },
            },
        }),
    });
};

var getReader = function() {
    var r = ['id','name','date'];
    var reader = new Ext.data.JsonReader({
        root: 'rows',
        totalProperty:'results',
        fields: r,
    });
    return reader;
};

var doGetData = function(jdata) {
    var p = {
        "uid"       : uid,
        "startRow"  : startRow,
        "limitRow"  : limitRow,
    };
    MyFunction.Send("GetDBdata", p, LoadData, this); // 到 GetDBdata.php 取得資料，交給 LoadData 載入資料
};

var LoadData = function(jdata) {
    var ds = jdata || [];
    store.proxy.data = ds;
    store.load({
        params: {
            // specify params for the first page load if using paging
            start: startRow,
            limit: limitRow,
        }
    });
};
```
