## 討論：關於 C 語言的「資料結構函式庫」與「記憶體分配」問題

### 問題 1： C 語言的 HashTable

討論網址： <https://www.facebook.com/groups/programmerMagazine/permalink/707649472585105/>

> 請問大家在寫 C 語言的時候，如果需要一個 HashTable ，那你會怎麼做呢？
> 
> 1. 用 OpenSource .... 哪一個呢？
> 2. 自己寫一個 ..... 
> 3. 其他 .....

心得： 

1. C 語言標準函式庫當中，沒有納入「常用的資料結構」，於是只好選用「非標準函式庫」。
2. 如果用 C++，則有 std:map 可以用。
3. 如果用 GNU glibc 或 POSIX 平台，則可以用 hsearch 函數。
4. 如果用 GTK 的基礎函式庫 glib，則有完整的「常用的資料結構」。
5. 網路上也有很多 C 的資料結構函式庫，請參考下列討論網址：
	* Stackoverflow: [Are there any open source C libraries with common data structures?](http://stackoverflow.com/questions/668501/are-there-any-open-source-c-libraries-with-common-data-structures)


### 問題 2： GTK 裏的 Glib 好用嗎？

討論網址： <https://www.facebook.com/groups/programmerMagazine/permalink/707669262583126/>

> 請問 Glib 好用嗎？在 windows 上呢？
> 
> <http://fred-zone.blogspot.tw/2008/03/glib-programing-io-giochannel.html>

心得：

1. 雖然 GTK 似乎被批評的很慘，但是 Glib 應該是很好用的，風評很不錯！
2. GTK 與 Glib 的用法，可以參考良葛格的網站
	* <http://openhome.cc/Gossip/GTKGossip/index.html>

### 問題 3： malloc 的使用會影響效能嗎？

討論網址： <https://www.facebook.com/groups/programmerMagazine/permalink/707655949251124/>

> 再一個問題是，你在 C 語言裡會盡可能使用靜態宣告，或常常使用 malloc 分配呢？
> 
> 1. 盡可能使用靜態
> 2. 每次都使用動態 malloc 分配，即使是字串複製也會使用 strdup
> 3. malloc 時盡可能一次分配大塊，然後在慢慢使用。(例如用一大塊記憶體當字串表 )

心得：

1. 這個問題引起的爭論特別多，所以有 158 則回應。
2. 似乎只要考慮好區域性的問題 (locality)，那麼用哪一種分配策略應該對效能影向不大 (但嵌入式就不見得了)。
3. 如果採用 「2. 每次 malloc 小塊的方式」，有可能「區域性」會不好，所以要小心。
4. 區域性的問題主要是影響到快取的 Hit Rate (cache hit/cache miss)。
5. 二維以上陣列的存取順序，影響快取非常嚴重，因此必須要注意存取順序問題，例如以下狀況。

我最近讀一本書，書名是 「 [深入理解计算机系统](http://book.douban.com/subject/5333562/) 」，
發現二維以上陣列的存取順序對 locality 影響很深，而 locality 對「cache hit rate 影響很深」
特別是像以下程式：

```CPP
for (i=0; i<n; i++)
  for (j=0; j<m; j++)
    sum += a[i][j];
```
	
如果反過來

```CPP 
for (j=0; j<m; j++)
  for (i=0; i<n; i++)
    sum += a[i][j];
```

這樣 locality 就會很差，執行速度可能因此而慢上數十倍啊 ....

### 結語

雖然 C 語言是很多人都學過的，甚至還是很多人學的第一門程式語言，但其實 C 語言並不好學，
而且有很多「陷阱」。

另外、C 語言的出現很早，但是標準函式庫卻很小，因此常常要使用非標準函式庫，所以更要懂得使用 OpenSource，
而且要避免採用重複功能的函式庫，以免程式膨脹過快。

但是 C 語言的速度快，而且在嵌入式系統與作業系統上有強大的用途，因此很多快速與底層的應用仍然必須仰賴 C 語言。
但是要達到高速，對硬體的很多考慮仍然是必須的，像是 locality 就是其中之一。


