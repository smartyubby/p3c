## (四) ORM映射
1. 【強制】在表查詢中，一律不要使用 * 作爲查詢的字段列表，需要哪些字段必須明確寫明。 
<br><span style="color:orange">說明</span>：1）增加查詢分析器解析成本。2）增減字段容易與resultMap配置不一致。 
2. 【強制】POJO類的布林屬性不能加is，而資料庫字段必須加is_，要求在resultMap中進行字段與屬性之間的映射。 
<br><span style="color:orange">說明</span>：參見定義POJO類以及資料庫字段定義規定，在<resultMap>中增加映射，是必須的。在MyBatis Generator生成的程式碼中，需要進行對應的修改。
3. 【強制】不要用resultClass當返回參數，即使所有類屬性名與資料庫字段一一對應，也需要定義；反過來，每一個表也必然有一個與之對應。 
<br><span style="color:orange">說明</span>：配置映射關係，使字段與DO類解耦，方便維護。 
4. 【強制】sql.xml配置參數使用：#{}，#param# 不要使用${} 此種方式容易出現SQL注入。 
5. 【強制】iBATIS自帶的queryForList(String statementName,int start,int size)不推薦使用。
<br><span style="color:orange">說明</span>：其實現方式是在資料庫取到statementName對應的SQL語句的所有記錄，再通過subList取start,size的子集合。 
<br><span style="color:green">正例</span>：
        
        Map<String, Object> map = new HashMap<String, Object>();    
        map.put("start", start);    
        map.put("size", size);

6. 【強制】不允許直接拿HashMap與Hashtable作爲查詢結果集的輸出。 
<br><span style="color:orange">說明</span>：resultClass=”Hashtable”，會置入字段名和屬性值，但是值的類型不可控。
7. 【強制】更新資料表記錄時，必須同時更新記錄對應的gmt_modified字段值爲當前時間。
8. 【推薦】不要寫一個大而全的資料更新介面。傳入爲POJO類，不管是不是自己的目標更新字段，都進行update table set c1=value1,c2=value2,c3=value3; 這是不對的。執行SQL時，不要更新無改動的字段，一是易出錯；二是效率低；三是增加binlog存儲。 
9. 【參考】`@Transactional`事務不要濫用。事務會影響資料庫的QPS，另外使用事務的地方需要考慮各方面的回滾方案，包括緩存回滾、搜索引擎回滾、消息補償、統計修正等。 
10. 【參考】`<isEqual>`中的compareValue是與屬性值對比的常數，一般是數字，表示相等時帶上此條件；`<isNotEmpty>`表示不爲空且不爲null時執行；`<isNotNull>`表示不爲null值時執行。  