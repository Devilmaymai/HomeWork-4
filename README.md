# HomeWork-4
完整的內容與程式碼將在程式內容(file: HW4)裡說明，這裡僅做重點式的說明與呈現。

Data來自Travel Review Ratings(https://archive.ics.uci.edu/ml/datasets/Tarvel+Review+Ratings)
，檔名為google_review_ratings.csv。

資料內容為不同user對於24種不同類型的景點的平均評分。
這24個類型分別為：

churches、resorts、beaches、parks、theatres、museums、malls、zoo、restaurants、pubs/bars、local services、burger/pizza、shops、hotels/other、lodgings、juice bars、art galleries、dance clubs、swimming pools、gyms、bakeries、beauty & spas、cafes、view points、monuments及gardens。


## Processing

將檔案讀入並大致確人一下資料的情形後，就將這24個類型的名字加回去。

之後確認資料中是否有空值，因為無法確認空值發生的原因，以及是否可以補救。所以，在這裡我將有空值的那筆user資料(row)都當作錯誤資料去除。

接下來，針對資料的類型做檢查，並將評分資料都改為float64的格式。

而由於評分的範圍是一致的，所以就不針對分數rescale。初步處理完的資料如下：

![](.png)

## Analysis

將各類型景點評分做簡單的敘述統計，可以看出標準差都頗大。

![](https://imgur.com/RAR5cTG.png)

因此，就又進一步地看了各類型景點分數的distribution plot，而由下面的distribution plot就不難看出標準差頗大的原因。

此外，仔細檢視分數分布，可以看出幾乎全部的分數分布都有多個peak，並非典型的標準分布(normal distribution)。而這有兩種可能的原因：

1.每個peak都是該項目的一種subtype。這裡的subtype可以源自風格上的不同，也可以代表該類型還可以再被分得更細。

2.評分的user有偏好上的不同。因為偏好的不同，而有不同的評分模式，最後導致有多個peak。

![](https://imgur.com/l2cTdnN.png)

而在比對各個distribution plot後，又可以發現有些項目的分布其實相似度頗高，例如：swimming_pools、gyms、bakeries和beauty&spas。

因此，評分的user有偏好上的不同的可能性會高一點。所以，先藉由correlation的heat map大致看一下類別間分數的關聯。
而由下面結果可以看到，的確有一些項目有不錯的關聯性。因此，接下來會針對評分模式試著做clustering。

![](https://imgur.com/nofy3kp.png)

## Clustering

在這裡，我把每個user當作是多維空間的一個點，而評分的分數就是user在這個多維空間的座標(這裡是24維)。
而評分模式較接近的user在這個多維空間就會較接近彼此。

### Hierarchical Clustering
首先我選用Hierarchical Clustering來進行clustering。而在一開始則先繪製dendrogram，來檢視可能的分群數。由下圖可以看到大致上可能有2~6個分群。

![](https://imgur.com/6epvuct.png)

因此，這裡的cluster number先選用3，而linkage則運用ward's method進行Agglomerative Clustering。
之後再透過PCA將24維座標降成二維，並繪製成scatter plot，搭配前面的分群結果，做視覺化的呈現如下。

![](https://imgur.com/BR0cxHC.png)

可以看到分群的情形還不錯。因此又將cluster number=2~6的結果都做出來並視覺化(linkage = ward's method)，結果如下。

![](https://imgur.com/2vhOAco.png)

由於無法透過二維scatter plot直覺的分辨出哪個結果比較好，因此改降成三維並視覺化。

![](https://imgur.com/7aljbqv.png)

可以看到上圖的結果一樣不容易直觀分辨，所以引入了其他來做評估。
而由於ground truth未知，這裡選用了Silhouette Coefficient及Calinski-Harabasz Index兩種方式，
並針對不同的linkage method及cluster number，進行performance的分析。


#### Summary

![](https://imgur.com/68w8qFI.png)
其中以ward's method所做的分群普遍表現較佳，圖如上，其他linkage method的結果於程式中呈現。
由這裡的clustering嘗試，只能看出有大致上的分群，至於最佳分群數還無法確認，因此接下來嘗試以其他方式來做clustering。

### DBSCAN

猜測可能是由於noise導致前面的分析沒有很明確的結果，所以接下來選用了DBSCAN來做嘗試。

在這裡，使eps由1.1至10.0，每次增加0.1；min_samples則由2到5，進行clustering，並記錄Silhouette Coefficient及Calinski-Harabasz Index的評估結果。
![](https://imgur.com/pAgU7lW.png)

PS.為節省版面這裡就不附上結果的圖片，而結果於程式中有做呈現。

#### Summary

由前面所做出的結果，可以看出DBSCAN在這裡並不適合拿來做這次的clustering。
所做出來的cluser以Silhouette Coefficient和Calinski-Harabasz Index分析， 都可以看出performance基本上都比Hierarchical Clustering差。

因此，最後又轉為嘗試K-Means clistering。

### K-means

#### Summary

## Discussion
