The purpose of this file is to track my solutions to the sql exercises found on http://sql-ex.ru/
1.Find the model number, speed and hard drive capacity for all the PCs with prices below $500.
Result set: model, speed, hd.
     ```sql
     SELECT model,speed, hd FROM PC WHERE price <500
     ```
2.List all printer makers. Result set: maker.
    ```sql
    SELECT distinct Maker From Product Where type ='Printer'
    ```
3.Find the model number, RAM and screen size of the laptops with prices over $1000.
	  ```sql
    SELECT model,ram,screen FROM Laptop WHERE price >1000
    ```
