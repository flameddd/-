

#1.執行模式有兩種  
 
 一般模式跟test模式，test模式能調用一些function
 
 安裝方式：`npm install casper -g`
 
 像下面這樣並存成`xxx.js`，然後在命令列上輸入`casper xxx.js` 就能執行了
 ```js
 var casper = require('casper').create();

casper.start('http://casperjs.org/', function() {
    this.echo(this.getTitle());
});

casper.thenOpen('http://phantomjs.org', function() {
    this.echo(this.getTitle());
});

casper.run();
 ```
 
 測試模式的話，它會不準你custom casper，所以如果有`require('casper').create()`執行會出錯
 而使用casper時，要用`.test`當中介
 假如此檔案檔名為`xxx2.js'，接著就在命令列執行`casper test xxx2.js`
 ```js
 casper.test.begin('The heading exists', 1, function suite(test) {
    casper.start('http://domain.tld/page.html', function() {
        test.assertExists('h1.page-title');
    }).run(function() {
        test.done();
    });
});
 ```
 
#2.顯示錯誤訊息與Log

 一般模式時，在create中加入相關參數．並且可以設定相關訊息的顏色
 ```js
 var casper = require('casper').create({ 
	verbose : true , 
	logLevel: 'debug'});
var testCount = 1;

casper.log('plop' , 'debug');
casper.log('plip' , 'warning');

casper.options.viewportSize = {width: 1024 , height:768};
 ```
