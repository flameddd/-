#測試的指標有四個  
```
1.行       覆蓋率：是否每一行都執行了？
2.function 覆蓋率：是否每一個function都有被調用了？
3.分支      覆蓋率：是否每一個if都有被調用了？
4.statement覆蓋率：是否每一個statement都有被調用了？
```

#執行方式  
命令列上輸入`istanbul cover simple.js`就可以了
然後去`coverage/lcov-report/`目錄底下打開`index.html`查看結果
