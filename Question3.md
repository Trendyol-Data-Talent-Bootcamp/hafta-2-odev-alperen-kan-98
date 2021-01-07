* Bu sorguda hem gerçek aktif kullanıcı sayısı hem de yaklaşık aktif kullanıcı sayısı hesaplanıyor.
* Ayrıca bu iki değer arasındaki farklar alınıp yüzdesel hata oranı da gösteriliyor.(Hatalar %2 den küçük)
* Bu yüzden sorgu yaklaşık 1.5 dakika sürmektedir
```
WITH RESULT AS(
SELECT TIMESTAMP_TRUNC(VIEW_TS,MINUTE) AS DateMinute, APPROX_COUNT_DISTINCT(DEVICEID) AS ActiveUserCount, COUNT(DISTINCT DEVICEID) AS ExactValue
FROM(
    SELECT VIEW_TS, DEVICEID FROM alperen_kan.pageview
    UNION ALL 
    SELECT TIMESTAMP_ADD(VIEW_TS, INTERVAL 1 MINUTE), DEVICEID FROM alperen_kan.pageview
    UNION ALL 
    SELECT TIMESTAMP_ADD(VIEW_TS, INTERVAL 2 MINUTE), DEVICEID FROM alperen_kan.pageview
    UNION ALL 
    SELECT TIMESTAMP_ADD(VIEW_TS, INTERVAL 3 MINUTE), DEVICEID FROM alperen_kan.pageview
    UNION ALL 
    SELECT TIMESTAMP_ADD(VIEW_TS, INTERVAL 4 MINUTE), DEVICEID FROM alperen_kan.pageview) 
GROUP BY DateMinute
ORDER BY 1) SELECT *,ROUND(((ExactValue - ActiveUserCount)/ActiveUserCount) *-100,3) AS ErrorPercentage FROM RESULT
```

* Aşağıdaki sorgu istenilen cevabı 40 saniyede getirmektedir.
```
SELECT TIMESTAMP_TRUNC(VIEW_TS,MINUTE) AS DateMinute, APPROX_COUNT_DISTINCT(DEVICEID) AS ActiveUserCount
FROM(
    SELECT VIEW_TS, DEVICEID FROM alperen_kan.pageview
    UNION ALL 
    SELECT TIMESTAMP_ADD(VIEW_TS, INTERVAL 1 MINUTE), DEVICEID FROM alperen_kan.pageview
    UNION ALL 
    SELECT TIMESTAMP_ADD(VIEW_TS, INTERVAL 2 MINUTE), DEVICEID FROM alperen_kan.pageview
    UNION ALL 
    SELECT TIMESTAMP_ADD(VIEW_TS, INTERVAL 3 MINUTE), DEVICEID FROM alperen_kan.pageview
    UNION ALL 
    SELECT TIMESTAMP_ADD(VIEW_TS, INTERVAL 4 MINUTE), DEVICEID FROM alperen_kan.pageview) 
GROUP BY DateMinute
ORDER BY 1
```
