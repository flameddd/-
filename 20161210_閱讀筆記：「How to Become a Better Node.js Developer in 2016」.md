#閱讀筆記：「How to Become a Better Node.js Developer in 2016」

##1.Callback convention - with Promise support
  利用 callback interface來拋錯、非同步的function就用callback interface with Promise 來處理．
##2.採用 Javascript的coding Style  
近年已經有很多工具可以使用來幫助coding style的檢查與規範，如：`JSHint`、`JSCS`、`ESLint`．
##3.新建專案  
永遠使用`npm init`來新建專案，這樣才會產生`package.json`檔案．  
##4.監控你的服務  
監控服務來竟早知道系統線上問題，這樣才能拯救你的business <== means save $$$
open-source方面有可以試試看這些工具`Zabbix`、`Collectd`、`ElasticSearch` or `Logstash`或者可以使用`Trace`<https://trace.risingstack.com/?utm_source=how-to-become-a-better-node-js-developer-in-2016&utm_medium=rsblog&utm_campaign=trace/> Node.js 小服務  
##5.自動化所有事情吧
`Grunt`、`glup` ＝>當下查了一下，我應該會選用glup    
現在大多的前端開發專案都採用`Webpack`，然後用`gulp`來完成其他自動化任務
`Webpack`的學習曲線比較高，筆者高度推薦讀讀`Webpack Cookbooks`． 
##6.利用最新的Node.js版本（LTS, long-term support）  
##7.選 適合的 資料庫  
談到Node.js，現在大家最常推薦`MongoDB`，這當然也ＯＫ，但系統開發沒有一定的對錯，所以...問問自己這些問題
```
*你有資料結構嗎？
*你需要handle transactions嗎？
*你的資料要存多久？
```   

你可能會需要用到`Redis`、如果有資料結構，你能考慮`PostgreSQL`、如果你使用SQL來配合Node.js開發那`knex`也是個方向．  

##8.採用語意化版本(Semantic Versioning)    

>版本格式：主版號.次版號.修訂號，版號遞增規則如下：
>>主版號：當你做了不相容的 API 修改，  
>>次版號：當你做了向下相容的功能性新增，  
>>修訂號：當你做了向下相容的問題修正。  
>>>先行版號及版本編譯資訊可以加到「主版號.次版號.修訂號」的後面，作為延伸。  


可以利用`semantic-release`來發佈你的JavaScript modules．
##9.Keep up    
這些是很好的JavaScript資訊來源，訂閱吧！
```
*Node.js Weekly Newsletter
*Microservice Weekly Newsletter
*Changelog Weekly - for Open-Source new
```
