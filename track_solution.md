The purpose of this file is to track my solutions to the sql exercises found on http://sql-ex.ru/

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
