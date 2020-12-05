## (四) OOP規約 

1. 【強制】避免通過一個類的對象引用訪問此類的靜態變量或靜態方法，無謂增加編譯器解析成本，直接用**類名**來訪問即可。 
2. 【強制】所有的覆寫方法，必須加@Override註解。 
<br><span style="color:orange">說明</span>：getObject()與get0bject()的問題。一個是字母的O，一個是數字的0，加@Override可以準確判斷是否覆蓋成功。另外，如果在抽象類中對方法簽名進行修改，其實現類會馬上編譯報錯。 
3. 【強制】相同參數類型，相同業務含義，纔可以使用Java的可變參數，避免使用Object。 
<br><span style="color:orange">說明</span>：可變參數必須放置在參數列表的最後。（提倡同學們儘量不用可變參數編程） 
<br><span style="color:green">正例</span>：
```
public User getUsers(String type, Integer... ids) {...} 
```
4. 【強制】外部正在調用或者二方庫依賴的介面，不允許修改方法簽名，避免對介面調用方產生影響。介面過時必須加`@Deprecated`註解，並清晰地說明採用的新介面或者新服務是什麼。 
5. 【強制】不能使用過時的類或方法。 
<br><span style="color:orange">說明</span>：java.net.URLDecoder 中的方法decode(String encodeStr) 這個方法已經過時，應該使用雙參數decode(String source, String encode)。介面提供方既然明確是過時介面，那麼有義務同時提供新的介面；作爲調用方來說，有義務去考證過時方法的新實現是什麼。 
6. 【強制】Object的equals方法容易拋空指針異常，應使用常量或確定有值的對象來調用equals。
<br><span style="color:green">正例</span>："test".equals(object);
<br><span style="color:red">反例</span>：object.equals("test"); 
<br><span style="color:orange">說明</span>：推薦使用java.util.Objects#equals（JDK7引入的工具類）
7. 【強制】所有的相同類型的包裝類對象之間值的比較，全部使用equals方法比較。 
<br><span style="color:orange">說明</span>：對於Integer var = ?  在-128至127範圍內的賦值，Integer對象是在IntegerCache.cache產生，會複用已有對象，這個區間內的Integer值可以直接使用==進行判斷，但是這個區間之外的所有數據，都會在堆上產生，並不會複用已有對象，這是一個大坑，推薦使用equals方法進行判斷。 
8. 關於基本數據類型與包裝數據類型的使用標準如下：
<br>1） 【強制】所有的POJO類屬性必須使用包裝數據類型。
<br>2） 【強制】RPC方法的返回值和參數必須使用包裝數據類型。
<br>3） 【推薦】所有的局部變量使用基本數據類型。
<br><span style="color:orange">說明</span>：POJO類屬性沒有初值是提醒使用者在需要使用時，必須自己顯式地進行賦值，任何NPE問題，或者入庫檢查，都由使用者來保證。
<br><span style="color:green">正例</span>：數據庫的查詢結果可能是null，因爲自動拆箱，用基本數據類型接收有NPE風險。
<br><span style="color:red">反例</span>：比如顯示成交總額漲跌情況，即正負x%，x爲基本數據類型，調用的RPC服務，調用不成功時，返回的是預設值，頁面顯示爲0%，這是不合理的，應該顯示成中劃線。所以包裝數據類型的null值，能夠表示額外的信息，如：遠程調用失敗，異常退出。 
9. 【強制】定義DO/DTO/VO等POJO類時，不要設定任何屬性**預設值**。
<br><span style="color:red">反例</span>：POJO類的gmtCreate預設值爲new Date();但是這個屬性在數據提取時並沒有置入具體值，在更新其它字段時又附帶更新了此字段，導致創建時間被修改成當前時間。 
10. 【強制】序列化類新增屬性時，請不要修改serialVersionUID字段，避免反序列失敗；如果完全不兼容升級，避免反序列化混亂，那麼請修改serialVersionUID值。 
<br><span style="color:orange">說明</span>：注意serialVersionUID不一致會拋出序列化運行時異常。 
11. 【強制】構造方法裏面禁止加入任何業務邏輯，如果有初始化邏輯，請放在init方法中。 
12. 【強制】POJO類必須寫function toString() { [native code] }方法。使用IDE中的工具：source> generate function toString() { [native code] }時，如果繼承了另一個POJO類，注意在前面加一下super.function toString() { [native code] }。 <br><span style="color:orange">說明</span>：在方法執行拋出異常時，可以直接調用POJO的function toString() { [native code] }()方法打印其屬性值，便於排查問題。 
13. 【推薦】使用索引訪問用String的split方法得到的陣列時，需做最後一個分隔符後有無內容的檢查，否則會有拋IndexOutOfBoundsException的風險。 
<br><span style="color:orange">說明</span>：
```
String str = "a,b,c,,";  
String[] ary = str.split(",");  
// 預期大於3，結果是3 System.out.println(ary.length);
```
14. 【推薦】當一個類有多個構造方法，或者多個同名方法，這些方法應該按順序放置在一起，便於閱讀，此條規則優先於第15條規則。 
15. 【推薦】 類內方法定義的順序依次是：公有方法或保護方法 > 私有方法 > getter/setter方法。
<br><span style="color:orange">說明</span>：公有方法是類的調用者和維護者最關心的方法，首屏展示最好；保護方法雖然只是子類關心，也可能是“模板設計模式”下的核心方法；而私有方法外部一般不需要特別關心，是一個黑盒實現；因爲承載的信息價值較低，所有Service和DAO的getter/setter方法放在類體最後。 
16. 【推薦】setter方法中，參數名稱與類成員變量名稱一致，this.成員名 = 參數名。在getter/setter方法中，不要增加業務邏輯，增加排查問題的難度。
<br><span style="color:red">反例</span>：
```
  public Integer getData() {      
      if (condition) {  
        return this.data + 100;  
      } else { 
        return this.data - 100; 
      }  
  }
```
17. 【推薦】循環體內，字符串的連接方式，使用StringBuilder的append方法進行擴展。
<br><span style="color:orange">說明</span>：反編譯出的字節碼文件顯示每次循環都會new出一個StringBuilder對象，然後進行append操作，最後通過function toString() { [native code] }方法返回String對象，造成內存資源浪費。  <br><span style="color:red">反例</span>：
```
  String str = "start";
  for (int i = 0; i < 100; i++) {
      str = str + "hello";      
  }
```
18. 【推薦】final可以聲明類、成員變量、方法、以及本地變量，下列情況使用final關鍵字：
<br>1） 不允許被繼承的類，如：String類。
<br>2） 不允許修改引用的域對象，如：POJO類的域變量。
<br>3） 不允許被重寫的方法，如：POJO類的setter方法。
<br>4） 不允許運行過程中重新賦值的局部變量。
<br>5） 避免上下文重複使用一個變量，使用final描述可以強制重新定義一個變量，方便更好地進行重構。 
19. 【推薦】慎用Object的clone方法來拷貝對象。 
<br><span style="color:orange">說明</span>：對象的clone方法預設是淺拷貝，若想實現深拷貝需要重寫clone方法實現屬性對象的拷貝。 
20. 【推薦】類成員與方法訪問控制從嚴：
<br>1） 如果不允許外部直接通過new來創建對象，那麼構造方法必須是private。
<br>2） 工具類不允許有public或default構造方法。
<br>3） 類非static成員變量並且與子類共享，必須是protected。
<br>4） 類非static成員變量並且僅在本類使用，必須是private。
<br>5） 類static成員變量如果僅在本類使用，必須是private。
<br>6） 若是static成員變量，必須考慮是否爲final。
<br>7） 類成員方法只供類內部調用，必須是private。
<br>8） 類成員方法只對繼承類公開，那麼限制爲protected。 
<br><span style="color:orange">說明</span>：任何類、方法、參數、變量，嚴控訪問範圍。過於寬泛的訪問範圍，不利於模塊解耦。思考：如果是一個private的方法，想刪除就刪除，可是一個public的service成員方法或成員變量，刪除一下，不得手心冒點汗嗎？變量像自己的小孩，儘量在自己的視線內，變量作用域太大，無限制的到處跑，那麼你會擔心的。 