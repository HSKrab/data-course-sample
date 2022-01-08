# 專案目的
利用協同過濾 (collaborative filter) 的方法來產生推薦。

# 方法
因為本次的推薦法主要受到 user(reviewerID column) 及 rating(overall column) 的影響，因此在前兩週 EDA的基礎上，多花許多時間在這兩個數據的分析及前置作業。

# 資料整理
1. ratings 按時間排序、移除 metadata 中較少資訊欄位、加入 ranktype 欄位、統一 metadata 的格式。
2. 將各商品的平均評分以及被購買數量加入 metadata 欄位中。確認 metadata 中每個商品都有被購買、評分過。
3. 有鑒於 ranktype 欄位中 97% 以上 均為同一 type，爲探討是否將 ratings 中非屬此類的評分資料刪除，先探討各 ranktype 的商品平均購買數。
   - 發現第二大類的商品平均被購買數呈現壓倒性的勝利，判斷若因因為佔比少就直接刪除，可能在統計人氣商品時會失準。
   - 若欲刪除較小眾的 ranktype 商品，可刪除商品數量為 10 以下的 ranktype 應該比較適當。

# EDA 
1. 將每個月「新進用戶評論數」和「所有用戶評論數」作圖發現每個月新進用戶佔比極高。
2. 僅有 3.6% 的商品只被購買一次，大部分商品都被重複購買過。但僅被購買過一次的商品########
3. 統計每個 reviewerID 共留下過幾筆評論資料。發現 77.5% 左右的用戶只留下過一筆資料。同一用戶最多留下 27 筆評論資料。
4. 統計過濾掉留下筆數為 n 筆以上時，會剩下多少用戶，發現若取 n = 3 時，用戶只會剩下 6.7% 左右。
5. 統計同用戶的購買資訊，重複買家購入相同物品且給同評分/重複買家 的 評價筆數 為 18,837/83,561。其中有將近 400 筆資料是同買家對於同商品有不同評分。
6. 將留下最多比資訊的買家購買紀錄調出來看，發現有給同商品不同評分的紀錄。
7. 查看其他買家給予評分的狀況，有分布較廣的，也有幾乎都給將近滿分的。

# Normalize 探討
上週觀摩同學的作業，有發現 Davy 助教給其他同學的建議中有將評分 normalize 的內容(Ref 連結1)；另外我在在 U49 的頁面有提問(Ref 連結2)，雖說接受了助教的解釋，但仍希望能自己觀察 data 後再做決定，因此在此結合自己的 EDA 分析來決定是否做 normalize 恰當。
1. 滿分 (5分) 的佔所有評分的 62% 左右。且有找到給過大量評分的買家給分偏甜，沒有鑑別度的情況。
2. 有將近 400 筆資料是同買家對於同商品有不同評分。
3. 結合自己做的「轉置特性研究」，除了利用簡單的數據來確認概念外，也認為有以下的情況：
   - 我起評分都是4，沒有特別瑕疵一律給5。
   - 因為我喜好分明，所以習慣只給1 or 3 or 5。
   - 我習慣以3為基準，特別好才會給到4，爛到爆就扣到2。
4. 因此決定將給過多次評分的用戶中，所有評分做 normalize 較恰當。
5. Normalize 的方法，請見 Normalize 方法研究。本處使用 scipy.stats 的 zscore 套件，將只給過單一評分 (例如全部都給 5 分) 的用戶，分數轉換至 3 分；給過不只一種分數的用戶，分數轉換至平均 3 分、標準差 1 分。

# 推薦邏輯
1. 手刻 cf-user-based
2. 手刻 cf-item-based
3. 套件 cf-surprise
4. 加入 rule-based (因為上週做出的結論為完全無法發揮 content-based 的功用，因此本次不考慮使用 content-based 來迭代)
5. 將 normalized 的評分資料取代原先資料進行分析。

# 評估分數
#### 無 normalize
1. cf-user-based:
2. cf-item-based:
3. cf-surprise:
4. cf-user + rule -based:
5. cf-item + rule -based:
6. cf-surprise + rule-based:

#### normalize
1. cf-user-based:
2. cf-item-based:
3. cf-user + rule -based:
4. cf-item + rule -based:
** normalize 不使用 surprise 原因為，還沒有完全通透 surprise 的 演算法，也來不及轉化為套件的輸入格式。

# 討論
1. 原則上協同過濾的推薦法和上週內容推薦的結論雷同，因為新用戶的比例極大，即便推薦演算法能發揮功效，也僅限在比例極低的舊用戶，對於比例極高的新用戶無法發揮任何作用。
2. 用戶購入次數越多越有參考價值，然而過濾門檻設太高，用戶會所剩無幾，門檻設太低運算量又太龐大。
3. 評論分數 normalize 的函式，也許是寫法的關係，效率極慢。

# Ref
1. https://lighthouse.alphacamp.co/courses/125/assignments/3734/submissions/180508?assignment_id=3734&batch_id=269&choice_cohort=all_cohort&course_id=125&from=other-submission&page=3&question_id=6429
2. https://lighthouse.alphacamp.co/courses/125/units/26722?comment_id=154772
