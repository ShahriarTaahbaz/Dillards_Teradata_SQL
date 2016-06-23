# Dillards_Teradata_SQL

SELECT sub.Store,
       s.city,
       s.state, 
       sub.aug_sales/sub.aug_nd AS Avg_Aug_Sales,
       sub.sep_sales/sub.sep_nd AS Avg_Sep_Sales,
       ((Avg_Aug_Sales-Avg_Sep_Sales)/Avg_Sep_Sales)*100 AS Sep_Sales_Percent_Decrease

FROM  strinfo s,
      (SELECT SUM(CASE
                     WHEN EXTRACT(MONTH FROM t.saledate)=8 
                     THEN t.sprice
                 END) AS aug_sales,
             SUM(CASE
                     WHEN EXTRACT(MONTH FROM t.saledate)=9
                     THEN t.sprice
                 END) AS sep_sales,
             t.store AS Store,
             COUNT(DISTINCT CASE
                                WHEN EXTRACT(MONTH FROM t.saledate)=8
                                THEN t.saledate
                            END) AS aug_nd,
             COUNT(DISTINCT CASE
                                WHEN EXTRACT(MONTH FROM t.saledate)=9
                                THEN t.saledate
                            END) AS sep_nd
      FROM trnsact t
      WHERE t.stype='P'
      HAVING COUNT(DISTINCT saledate) > 20
      GROUP BY store) AS sub
WHERE sub.Store=s.store
ORDER BY 6 DESC
;
