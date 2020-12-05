## (三) SQL語句 
1. 【強制】不要使用count(列名)或count(常數)來替代count(*)，count(*)是SQL92定義的標準統計行數的語法，跟資料庫無關，跟NULL和非NULL無關。 
<br><span style="color:orange">說明</span>：count(*)會統計值爲NULL的行，而count(列名)不會統計此列爲NULL值的行。 
2. 【強制】count(distinct col) 計算該列除NULL之外的不重複行數，注意 count(distinct col1, col2) 如果其中一列全爲NULL，那麼即使另一列有不同的值，也返回爲0。 
3. 【強制】當某一列的值全是NULL時，count(col)的返回結果爲0，但sum(col)的返回結果爲NULL，因此使用sum()時需注意NPE問題。 
<br><span style="color:green">正例</span>：可以使用如下方式來避免sum的NPE問題：
<pre>SELECT IF(ISNULL(SUM(g)),0,SUM(g)) FROM table; </pre>
4. 【強制】使用`ISNULL()`來判斷是否爲NULL值。 說明：NULL與任何值的直接比較都爲NULL。  
1） `NULL<>NULL`的返回結果是NULL，而不是`false`。  
2） `NULL=NULL`的返回結果是NULL，而不是`true`。  
3） `NULL<>1`的返回結果是NULL，而不是`true`。 
5. 【強制】 在程式碼中寫分頁查詢邏輯時，若count爲0應直接返回，避免執行後面的分頁語句。 
6. 【強制】不得使用外鍵與級聯，一切外鍵概念必須在應用層解決。 
<br><span style="color:orange">說明</span>：以學生和成績的關係爲例，學生表中的student_id是主鍵，那麼成績表中的student_id則爲外鍵。如果更新學生表中的student_id，同時觸發成績表中的student_id更新，即爲級聯更新。外鍵與級聯更新適用於單機低併發，不適合分佈式、高併發集羣；級聯更新是強阻塞，存在資料庫更新風暴的風險；外鍵影響資料庫的插入速度。 
7. 【強制】禁止使用存儲過程，存儲過程難以調試和擴展，更沒有移植性。 
8. 【強制】資料訂正（特別是刪除、修改記錄操作）時，要先select，避免出現誤刪除，確認無誤才能執行更新語句。 
9. 【推薦】in操作能避免則避免，若實在避免不了，需要仔細評估in後邊的集合元素數量，控制在1000個之內。 
10. 【參考】如果有全球化需要，所有的字元存儲與表示，均以utf-8編碼，注意字元統計函數的區別。 
<br><span style="color:orange">說明</span>：
<pre>SELECT LENGTH("輕鬆工作")； 返回爲12
SELECT CHARACTER_LENGTH("輕鬆工作")； 返回爲4</pre>
如果需要存儲表情，那麼選擇utf8mb4來進行存儲，注意它與utf-8編碼的區別。 
11. 【參考】 TRUNCATE TABLE 比 DELETE 速度快，且使用的系統和事務日誌資源少，但TRUNCATE無事務且不觸發trigger，有可能造成事故，故不建議在開發程式碼中使用此語句。 
<br><span style="color:orange">說明</span>：TRUNCATE TABLE 在功能上與不帶 WHERE 子句的 DELETE 語句相同。 