The purpose of this file is to track my solutions to the sql exercises found on sql-ex

1. Find the model number, speed and hard drive capacity for all the PCs with prices below $500. Result set: model, speed, hd. 
```sql
  SELECT model,speed, hd FROM PC WHERE price <500
  ```
2. List all printer makers. Result set: maker. 
```sql
  SELECT distinct Maker From Product Where type ='Printer' 
  ```
3. Find the model number, RAM and screen size of the laptops with prices over $1000. 
```sql 
  SELECT model,ram,screen FROM Laptop WHERE price >1000 
   ```
4. Find all records from the Printer table containing data about color printers.
```sql
  SELECT * FROM Printer WHERE color ='y'
  ```
5. Find the model number, speed and hard drive capacity of PCs cheaper than $600 having a 12x or a 24x CD drive.
```sql
  SELECT model,speed,hd FROM PC WHERE price <600 AND (CD = '12x' OR CD = '24x')
  ```
6. For each maker producing laptops with a hard drive capacity of 10 Gb or higher, find the speed of such laptops. Result set: maker, speed.
```sql
  SELECT DISTINCT maker, speed FROM 
    Product INNER JOIN Laptop ON Product.model=Laptop.model 
    WHERE hd >= 10 ORDER BY speed,maker
  ```
7. Get the models and prices for all commercially available products (of any type) produced by maker B.
```sql
  SELECT pc.model,  price FROM 
    product INNER JOIN pc on product.model=pc.model WHERE product.maker='B'
  UNION
  SELECT laptop.model,price FROM 
    product INNER JOIN laptop on product.model=laptop.model WHERE product.maker='B'
  UNION 
  SELECT printer.model,price FROM
    product INNER JOIN printer ON product.model=printer.model WHERE product.maker = 'B'
  ```
8. Find the makers producing PCs but not laptops.
```sql
  SELECT product.maker FROM product WHERE type ='pc' 
  EXCEPT
  SELECT maker FROM product WHERE 
  maker IN (SELECT maker FROM product WHERE type ='laptop')
 ```
 9. Find the makers of PCs with a processor speed of 450 MHz or more. Result set: maker.
 ```sql
  SELECT DISTINCT product.maker FROM 
    product INNER JOIN pc ON product.model=pc.model
    WHERE pc.speed >=450
```
10. Find the printer models having the highest price. Result set: model, price.
```sql
  SELECT model,price FROM printer WHERE price = (SELECT MAX(price) FROM printer)
  ```
11. Find out the average speed of PCs.
```sql
  SELECT AVG(speed) AS Avg_speed FROM pc
  ```
12. Find out the average speed of the laptops priced over $1000.
```sql
  SELECT AVG(speed) AS avg_speed FROM laptop WHERE price >1000
  ```
13. Find out the average speed of the PCs produced by maker A.
```sql
  SELECT AVG(speed) AS avg_speed FROM pc INNER JOIN product ON pc.model=product.model WHERE maker = 'A'
  ```
14. For the ships in the Ships table that have at least 10 guns, get the class, name, and country.
```sql
  SELECT classes.class,name,country FROM ships 
    INNER JOIN classes ON ships.class=classes.class WHERE numGuns >=10
  ```
15. Get hard drive capacities that are identical for two or more PCs.
Result set: hd.
```sql
  SELECT hd FROM pc GROUP BY hd HAVING COUNT(hd) >=2
  ```
16. Get pairs of PC models with identical speeds and the same RAM capacity. Each resulting pair should be displayed only once, i.e. (i, j) but not (j, i).
Result set: model with the bigger number, model with the smaller number, speed, and RAM.
```sql
  SELECT DISTINCT a.model, b.model,a.speed,a.ram FROM 
    pc AS a, pc b WHERE a.speed =b.speed AND a.ram=b.ram and a.model>b.model
  ```
17. Get the laptop models that have a speed smaller than the speed of any PC.
```sql
  SELECT DISTINCT product.type, laptop.model,speed FROM laptop, product
  WHERE speed < ALL(SELECT speed FROM pc) AND type='laptop'
```
18. Find the makers of the cheapest color printers.
```sql
  SELECT DISTINCT product.maker, price FROM product 
    INNER JOIN (SELECT model,color,price FROM printer WHERE color ='y') AS a 
    ON product.model=a.model 
    WHERE price IN (SELECT MIN(price) FROM printer WHERE color='y')
  ```
19. For each maker having models in the Laptop table, find out the average screen size of the laptops he produces.
```sql
  SELECT maker, AVG(screen) FROM product INNER JOIN laptop ON product.model=laptop.model GROUP BY maker
  ```
20. Find the makers producing at least three distinct models of PCs.
```sql
  SELECT DISTINCT a.maker, COUNT(a.model) AS count_model FROM 
    (SELECT maker, model FROM product WHERE type ='pc') AS a  
    GROUP BY a.maker HAVING COUNT(a.model) >= 3
  ```
21. Find out the maximum PC price for each maker having models in the PC table.
```sql
  SELECT product.maker, MAX(price) FROM product INNER JOIN pc ON product.model=pc.model GROUP BY maker
  ```
22. For each value of PC speed that exceeds 600 MHz, find out the average price of PCs with identical speeds.
```sql
  SELECT speed, AVG(price) FROM pc GROUP BY speed HAVING speed >600
  ```
23. Get the makers producing both PCs having a speed of 750 MHz or higher and laptops with a speed of 750 MHz or higher.
```sql
  SELECT DISTINCT maker FROM product 
      INNER JOIN (SELECT speed,model FROM laptop) AS a ON product.model=a.model WHERE speed >=750
  INTERSECT
  SELECT DISTINCT maker FROM product 
      INNER JOIN (SELECT speed,model FROM pc) AS b ON product.model=b.model WHERE speed >=750
```
24. List the models of any type having the highest price of all products present in the database.
```sql
  WITH db AS (
    SELECT model, price FROM laptop UNION ALL
    SELECT model, price FROM pc UNION ALL
    SELECT model, price FROM printer)
  SELECT DISTINCT model FROM db 
   WHERE price = (SELECT MAX(price) FROM db)
```
25. Find the printer makers also producing PCs with the lowest RAM capacity and the highest processor speed of all PCs having the lowest RAM capacity.
```sql
  SELECT distinct maker FROM product
  WHERE type = 'printer' AND 
      maker IN (SELECT maker FROM Product 
                 WHERE model IN (SELECT distinct model FROM PC
                                  WHERE speed = (SELECT MAX(speed) FROM PC 
                                                   WHERE ram=(SELECT MIN(ram) FROM PC)    
                                                  )
				                          AND ram = (SELECT MIN(ram) FROM pc)
	                                )
                 )
```
26. Find out the average price of PCs and laptops produced by maker A.
```sql
  SELECT AVG(x.price) AS avg_price FROM 
 (
    SELECT price FROM pc WHERE model IN (SELECT model FROM product WHERE 	maker ='A')
UNION ALL
	  SELECT price FROM laptop WHERE model IN (SELECT model FROM product WHERE maker ='A') 
	) AS x
  ```
27. Find out the average hard disk drive capacity of PCs produced by makers who also manufacture printers.
```sql
  SELECT product.maker, AVG(hd) FROM pc
  INNER JOIN product ON pc.model=product.model GROUP by product.maker
  HAVING maker IN (SELECT maker FROM product WHERE type = 'printer')
```
28. Using Product table, find out the number of makers who produce only one model.
```sql
  SELECT count(a.maker) FROM 
    (SELECT maker FROM product GROUP BY maker HAVING count(maker)=1) as a
```
29. Under the assumption that receipts of money (inc) and payouts (out) are registered not more than once a day for each collection point [i.e. the primary key consists of (point, date)], write a query displaying cash flow data (point, date, income, expense).
Use Income_o and Outcome_o tables.
```sql
  SELECT a.point, a.date,inc,out FROM
    (SELECT income_o.point, income_o.date FROM income_o
      UNION 
    SELECT outcome_o.point,outcome_o.date FROM outcome_o 
    ) AS a
  LEFT JOIN income_o ON a.point=income_o.point AND a.date=income_o.date
  LEFT JOIN outcome_o ON a.point=outcome_o.point AND a.date=outcome_o.date
```
30. Under the assumption that receipts of money (inc) and payouts (out) can be registered any number of times a day for each collection point [i.e. the code column is the primary key], display a table with one corresponding row for each operating date of each collection point.
Result set: point, date, total payout per day (out), total money intake per day (inc).
Missing values are considered to be NULL.
```sql
  SELECT distinct y.point, y.date, y.Outcome, sum(inc) AS income FROM 
  (SELECT distinct x.point,x.date, sum(out) Outcome FROM
      ( SELECT distinct point, date FROM income
      UNION 
        SELECT distinct point, date FROM outcome
        ) AS x
    LEFT JOIN outcome ON x.point=outcome.point AND x.date=outcome.date
    GROUP BY x.point,x.date
   ) AS y
LEFT JOIN  income ON y.point=income.point AND y.date=income.date
GROUP BY y.point, y.date,y.outcome
```
31. For ship classes with a gun caliber of 16 in. or more, display the class and the country.
```sql
  SELECT class, country FROM classes WHERE bore >= 16
  ```
32. One of the characteristics of a ship is one-half the cube of the calibre of its main guns (mw).
Determine the average ship mw with an accuracy of two decimal places for each country having ships in the database.
```sql
 SELECT countRy, CAST(AVG(power(bore,3)*0.5) AS NUMERIC(6,2)) FROM
  (  SELECT country,bore,name FROM classes,ships WHERE classes.class=ships.class
    UNION
    SELECT country,bore,ship FROM classes, outcomes WHERE classes.class=outcomes.ship
   ) as x
    GROUP BY country
```
33. Get the ships sunk in the North Atlantic battle
```sql
  SELECT ship FROM outcomes WHERE battle = 'North Atlantic' AND result = 'sunk'
  ```
34. In accordance with the Washington Naval Treaty concluded in the beginning of 1922, it was prohibited to build battle ships with a displacement of more than 35 thousand tons.
Get the ships violating this treaty (only consider ships for which the year of launch is known).
List the names of the ships.
```sql
 SELECT distinct name FROM ships,classes
    WHERE ships.class=classes.class 
    AND launched >=1922 AND displacement>35000 AND type='bb'
```
35. Find models in the Product table consisting either of digits only or Latin letters (A-Z, case insensitive) only.
```sql
  SELECT model,type FROM product WHERE model NOT LIKE '%[^0-9]%' OR model NOT LIKE '%[^a-z]%'
  ```
36. List the names of lead ships in the database (including the Outcomes table).
```sql
 SELECT name FROM ships INNER JOIN classes ON ships.name=classes.class
UNION 
SELECT ship FROM outcomes INNER JOIN classes ON outcomes.ship=classes.class
```
37. Find classes for which only one ship exists in the database (including the Outcomes table).
```sql
 SELECT class FROM
    (SELECT c.class, name FROM classes c,ships s WHERE c.class=s.class
      UNION
    SELECT c.class,ship AS name FROM classes c, outcomes o WHERE c.class=o.ship
    ) AS full_ship
GROUP BY class
HAVING COUNT(class)=1
```
38. Find countries that ever had classes of both battleships (‘bb’) and cruisers (‘bc’).
```sql
SELECT country FROM classes WHERE type='bb'
INTERSECT
SELECT country FROM classes WHERE type='bc'
```
39. Find the ships that `survived for future battles`; that is, after being damaged in a battle, they participated in another one, which occurred later.
```sql
 SELECT distinct y.ship FROM
    (SELECT distinct ship,battle,result,date FROM outcomes o, battles b WHERE o.battle=b.name
        AND result='damaged'
      ) AS x,
    ( SELECT distinct ship,battle,result,date FROM outcomes o,battles b WHERE o.battle=b.name
      ) AS y
WHERE x.ship=y.ship AND x.date<y.date
```
40. Get the makers who produce only one product type and more than one model.
```sql
 SELECT distinct maker, type FROM product WHERE maker IN
    (SELECT maker FROM product GROUP BY maker 
      HAVING  COUNT(DISTINCT model)>1 AND COUNT(DISTINCT type)=1
     )
```
41. For each maker who has models at least in one of the tables PC, Laptop, or Printer, determine the maximum price for his products.
Output: maker; if there are NULL values among the prices for the products of a given maker, display NULL for this maker, otherwise, the maximum price.
```sql
  SELECT DISTINCT  maker, 
    CASE
        	WHEN sum(CASE
			          WHEN x.price IS NULL THEN 1
			          ELSE 0 END)>0 THEN NULL
	        ELSE MAX(x.price)
     END mx_price
FROM product INNER JOIN
    (SELECT DISTINCT model,price  FROM pc 
      UNION
     SELECT DISTINCT model,price  FROM laptop 
      UNION 
     SELECT DISTINCT model,price FROM printer
     ) AS x
     ON product.model=x.model
     GROUP BY maker
```
42. Find the names of ships sunk at battles, along with the names of the corresponding battles.
```sql
  SELECT distinct ship,battle FROM outcomes WHERE result='sunk'
  ```
43. Get the battles that occurred in years when no ships were launched into water.
```sql
SELECT name FROM battles
    WHERE DATEPART(yy,date)
    NOT IN 
    (SELECT launched FROM ships WHERE launched IS NOT NULL)
```
44. Find all ship names beginning with the letter R.
```sql
  SELECT name FROM ships WHERE name LIKE 'R%'
UNION 
  SELECT ship FROM outcomes WHERE ship LIKE 'R%'
```
45. Find all ship names consisting of three or more words (e.g., King George V).
Consider the words in ship names to be separated by single spaces, and the ship names to have no leading or trailing spaces.
```sql
  SELECT name FROM ships WHERE name LIKE '% % %'
UNION 
  SELECT ship FROM outcomes WHERE ship LIKE '% % %'
```
46. For each ship that participated in the Battle of Guadalcanal, get its name, displacement, and the number of guns.
```sql
 SELECT s.name, displacement,numGuns FROM ships s, 
    (SELECT ship FROM outcomes WHERE battle='Guadalcanal') AS  o, classes c 
        WHERE s.name=o.ship AND c.class=s.class
  UNION
SELECT ship, displacement, numGuns FROM
    (SELECT ship FROM outcomes WHERE battle ='Guadalcanal') AS o LEFT JOIN classes c 
        ON o.ship = c.class WHERE o.ship NOT IN (SELECT name FROM ships)
```
47. Find the countries that have lost all their ships in battles.
```sql
  SELECT ship_tbl_count.country FROM  
	  (SELECT country, COUNT(name) AS ship_count FROM 
		    (SELECT s.name AS name, c.country AS country FROM classes c 
            INNER JOIN ships s ON c.class = s.class 
		      UNION 
		      SELECT o.ship AS name, c.country AS country FROM classes c 
            INNER JOIN outcomes o ON o.ship = c.class 
          ) x
 		  GROUP BY country
	  ) AS ship_tbl_count
INNER JOIN
  (SELECT country, COUNT(ship) AS sunk_count FROM 
		(SELECT s.name AS name, c.country AS country 
			  FROM classes c INNER JOIN ships s ON c.class = s.class 
		 UNION 
		 SELECT o.ship AS name, c.country AS country 
			  FROM classes c INNER JOIN outcomes o ON o.ship = c.class 
		  ) y 
	INNER JOIN outcomes o ON o.ship = y.name WHERE result ='sunk' GROUP BY country 
   ) AS sunk_tbl_count
ON ship_tbl_count.country=sunk_tbl_count.country AND ship_tbl_count.ship_count=sunk_tbl_count.sunk_count
```
