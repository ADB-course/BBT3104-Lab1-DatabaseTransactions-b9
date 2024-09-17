[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/r-tQZu0l)
# BBT3104-Lab1of6-DatabaseTransactions


| **Key**                                                               | Value                                                                                                                                                                              |
|---------------|---------------------------------------------------------|
| **Group Name**                                                               | ? |
| **Semester Duration**                                                 | 19<sup>th</sup> August - 25<sup>th</sup> November 2024                                                                                                                       |

## Flowchart

## Pseudocode

 Step 1: Create the MySQL Docker container
START Docker service
LOAD `Docker-Compose.yaml`
COMPOSE UP - SELECTED SERVICES (mysql)
CHECK if "mysql-8.3.0-bbt3104" container is running

 Step 2: Create a connection to MySQL container in DBeaver
OPEN DBeaver
CREATE new connection using MYSQL driver
SET IP, Port, Username, Password
TEST connection

 Step 3: Turn off auto commit variable
CONNECT to MySQL container using terminal
EXECUTE `SHOW VARIABLES LIKE 'autocommit';`
IF autocommit = ON THEN
    EXECUTE `SET autocommit = OFF`
END

 Step 4: Set the isolation level
CONFIRM isolation level with `SHOW VARIABLES LIKE 'transaction_isolation';`
IF not SERIALIZABLE THEN
    EXECUTE `SET TRANSACTION ISOLATION LEVEL SERIALIZABLE`
END

 Step 5: Start the transaction
USE `classicmodels` database
START TRANSACTION

 Step 6: Retrieve the latest order number
SET @orderNumber = (SELECT MAX(orderNumber) + 1 FROM orders)

Step 7: Create a new order
INSERT INTO `orders` 
  VALUES (@orderNumber, CURDATE(), ADD 3 days to CURDATE(), ADD 2 days to CURDATE(), 'In Process', 145)

Step 8: Create SAVEPOINT before inserting product 1
SAVEPOINT before_product_1

 Step 9: Insert the first product ordered
INSERT INTO `orderdetails` (orderNumber, productCode, quantityOrdered, priceEach, orderLineNumber)
  VALUES (@orderNumber, 'S18_1749', 2724, 136, 1)

Step 10: Update quantity in stock for product 1
SET @quantityInStock = (SELECT quantityInStock FROM `products` WHERE productCode = 'S18_1749')
UPDATE `products` SET `quantityInStock` = @quantityInStock - 2724 WHERE `productCode` = 'S18_1749'

 Step 11: Create SAVEPOINT before inserting product 2
SAVEPOINT before_product_2

 Step 12: Insert the second product ordered
INSERT INTO `orderdetails` (orderNumber, productCode, quantityOrdered, priceEach, orderLineNumber)
  VALUES (@orderNumber, 'S18_2248', 540, 55.09, 2)

 Step 13: Update quantity in stock for product 2
SET @quantityInStock = (SELECT quantityInStock FROM `products` WHERE productCode = 'S18_2248')
UPDATE `products` SET `quantityInStock` = @quantityInStock - 540 WHERE `productCode` = 'S18_2248'

 Step 14: Rollback to savepoint before product 2
ROLLBACK TO SAVEPOINT before_product_2

 Step 15: Create SAVEPOINT before inserting product 3
SAVEPOINT before_product_3

 Step 16: Insert the third product ordered
INSERT INTO `orderdetails` (orderNumber, productCode, quantityOrdered, priceEach, orderLineNumber)
  VALUES (@orderNumber, 'S12_1099', 68, 95.34, 3)

 Step 17: Update quantity in stock for product 3
SET @quantityInStock = (SELECT quantityInStock FROM `products` WHERE productCode = 'S12_1099')
UPDATE `products` SET `quantityInStock` = @quantityInStock - 68 WHERE `productCode` = 'S12_1099'

 Step 18: Receive payment for the order
INSERT INTO `payments` (customerNumber, checkNumber, paymentDate, amount)
  VALUES (145, 'JM555210', CURDATE(), 300000)

Step 19: Commit the transaction
COMMIT TRANSACTION

Step 20: Verify the transaction results
SELECT * FROM `classicmodels`.`orderdetails` WHERE orderNumber = @orderNumber


## Support for the Sales Departments' Report
