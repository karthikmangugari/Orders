# Orders
                                                                                             
----------                                                                                          
QUEST_TEXT||'*/'                                                                                   
----------------------------------------------------------------------------------------------------
/* 1.                                                                                               
List the name, job, and salary for all employees.*/                                                 
SELECT
    emp_name,
    emp_job,
    emp_salary
FROM
    employee;                                                                                                   
/* 2.                                                                                               
List the unique zip codes found among the customer addresses.*/                                     
SELECT DISTINCT
    cust_zip
FROM
    customer;                                                                                                    
/* 3.                                                                                               
List the order id, order date, and ship date for all of orders                                      
placed by customer 103.*/                                                                           
SELECT
    ord_id,
    ord_orderdate,
    ord_shipdate
FROM
    ord
WHERE
    cust_id = 103;                                                                                                    
/* 4.                                                                                               
List the names and street addresses of all customers in Burlingame who have a credit limit above $50
00.*/                                                                                               
SELECT
    cust_name,
    cust_address
FROM
    customer
WHERE
        cust_city = 'Burlingame'
    AND
        cust_creditlimit > 5000;                                                                                                    
/* 5.                                                                                               
List the total compensation (salary plus commission) for each employee.  If an employee has no commi
ssion, then list just the salary amount.  (Note: in a select clause you may perform arithmetic opera
tions on the values in two or more columns.)*/                                                      
SELECT
    coalesce(
        emp_salary,
        0
    ) + coalesce(
        emp_commission,
        0
    ) AS "total_compensation"
FROM
    employee;
/* 6.                                                                                               
For each customer, list the customer name, the number of orders placed by that customer,  and the to
tal dollar amount of all of that customer's orders.*/                                               
SELECT
    cust_name,
    COUNT(*) AS no_of_orders,
    SUM(ord_total)
FROM
    customer,
    ord
WHERE
    customer.cust_id = ord.cust_id
GROUP BY
    cust_name; 
/* 7.                                                                                               
For each order, list the number of different products ordered, and the minimum and maximum values of
 product actual prices on the order.*/                                                              
SELECT
    ord.ord_id,
    COUNT(*) AS no_of_orders,
    MIN(orditem_actprice),
    MAX(orditem_actprice)
FROM
    ord,
    orderitem
WHERE
    ord.ord_id = orderitem.ord_id
GROUP BY
    ord.ord_id
ORDER BY ord.ord_id;
/* 8.                                                                                               
List the names of customers and the total order volume (Ord_total) for those customers who have purc
hased a total of more than $1000 worth of products on all their orders combined.*/                  
SELECT
    customer.cust_name,
    ord.ord_total AS total_order_volume
FROM
    customer
    JOIN ord ON customer.cust_id = ord.cust_id
WHERE
    ord_total > 1000;                                                                                                   
/* 9.                                                                                               
Create the PRODUCT table.  It has the following column names and data types.  PROD_ID is the primary
 key field.                                                                                         
                                                                                                    
PROD_ID        NUMERIC(6)                                                                           
PROD_DESCRIP   VARCHAR(30)*/                                                                        
CREATE TABLE product (
    prod_id        NUMERIC(6),
    prod_descrip   VARCHAR(30),
    PRIMARY KEY ( prod_id )
);
/* 10.                                                                                              
Insert the data shown at  into y
our tables.*/                                                                                       
INSERT INTO product (
    prod_id,
    prod_descrip
) VALUES (
    '100860',
    'Ace Tennis Racket I'
);

INSERT INTO product (
    prod_id,
    prod_descrip
) VALUES (
    '100861',
    'Ace Tennis Racket II'
);

INSERT INTO product (
    prod_id,
    prod_descrip
) VALUES (
    '100870',
    'Ace Tennis Balls-3 Pack'
);

INSERT INTO product (
    prod_id,
    prod_descrip
) VALUES (
    '100871',
    'Ace Tennis Balls-6 Pack'
);

INSERT INTO product (
    prod_id,
    prod_descrip
) VALUES (
    '100890',
    'Ace Tennis Net'
);

INSERT INTO product (
    prod_id,
    prod_descrip
) VALUES (
    '101860',
    'SP Tennis Racket'
);

INSERT INTO product (
    prod_id,
    prod_descrip
) VALUES (
    '101863',
    'SP Junior Racket'
);

INSERT INTO product (
    prod_id,
    prod_descrip
) VALUES (
    '102130',
    'Guide to Tennis'
);

INSERT INTO product (
    prod_id,
    prod_descrip
) VALUES (
    '200376',
    'SB Energy Bar-6 Pack'
);

INSERT INTO product (
    prod_id,
    prod_descrip
) VALUES (
    '200380',
    'SB Vita Snack-6 Pack'
);
/* 11.                                                                                              
List the name and job of all employees working in Dallas.  (the use IN + subquery construct.)*/     
SELECT
    emp_name,
    emp_job
FROM
    employee
WHERE
    dept_no IN (
        SELECT
            dept_no
        FROM
            department
        WHERE
            dept_loc = 'Dallas'
    );
/* 12.                                                                                              
List the names of all employees who are the customer representative for a customer who has purchase 
at least one unit of product 100860.   Use a series of nested IN + subquery clauses.*/              
SELECT
    emp_name
FROM
    employee
WHERE
    emp_id IN (
        SELECT
            emp_id
        FROM
            customer
        WHERE
            cust_id IN (
                SELECT
                    cust_id
                FROM
                    ord
                WHERE
                    ord_id IN (
                        SELECT
                            ord_id
                        FROM
                            orderitem
                        WHERE
                            prod_id = '100860'
                    )
            )
    );
/* 13.                                                                                              
List the employee id all employees who are salesmen but who have no customers in Redwood City.  Use 
some sort of set difference.*/                                                                      
SELECT
    emp_id
FROM
    employee
WHERE
    emp_job = 'Salesman'
MINUS ( SELECT
    emp_id
FROM
    customer
WHERE
    cust_city = 'Redwood City'
);
/* 14.                                                                                              
List the employees who earn an above-average salary.*/                                              
SELECT
    emp_name
FROM
    employee
WHERE
    emp_salary > (
        SELECT
            AVG(emp_salary)
        FROM
            employee
    );
/* 15.                                                                                              
List the names and telephone numbers of all customers whose sales representative is Ward (don't type
 in a value for the Emp_ID).*/                                                                      
select cust_name, cust_phone 
from customer where emp_id in (
    select emp_id 
    from employee where emp_name = 'Ward');
/* 16.                                                                                              
List the employee name and name of each customer the employee serves.  Include ALL employees, even 
those not serving specific customers.*/                                                              
SELECT
    employee.emp_name,
    customer.cust_name
FROM
    customer
    LEFT JOIN employee ON employee.emp_id = customer.emp_id
ORDER BY employee.emp_name;
/* 17.                                                                                             
List the names of all employees, together with the name of their manager.  (If an employee has no manager, the manager column 
should be NULL).*/                                                        
SELECT DISTINCT
    emp.emp_name AS employee_name,
    mngr.emp_name AS manager_name
FROM
    employee emp
    INNER JOIN employee mngr ON emp.emp_mgr = mngr.emp_id;
/* 18.                                                                                              
For each product, list the product id, the order id of the order on which the greatest quantity of 
that product was purchased.  List also the quantity.*/                                               
WITH great_quantity AS (
    SELECT
        prod_id,
        MAX(orditem_qty) AS max_orditem_qty
    FROM
        orderitem
    GROUP BY
        prod_id
) SELECT
    great_quantity.prod_id,
    orderitem.ord_id,
    orderitem.orditem_qty
FROM
    orderitem,
    great_quantity
WHERE
        great_quantity.prod_id = orderitem.prod_id
    AND
        great_quantity.max_orditem_qty = orderitem.orditem_qty;   
/* 19.                                                                                              
List the employees who are associated with a customer who has placed no orders.  (Use NOT EXISTS)*/ 
SELECT DISTINCT
    employee.emp_name
FROM
    employee
    JOIN customer ON employee.emp_id = customer.emp_id
WHERE
    NOT
        EXISTS (
            SELECT
                cust_id
            FROM
                ord
            WHERE
                ord.cust_id = customer.cust_id
        );
/* 20.                                                                                              
List the names of customers who have purchased at least one of each product.*/                      
SELECT DISTINCT
    customer.cust_name
FROM
    customer
    JOIN ord ON customer.cust_id = ord.cust_id
WHERE
    ord_id IN (
        SELECT
            orderitem.ord_id
        FROM
            orderitem
            JOIN ord ON ord.ord_id = orderitem.ord_id
        WHERE
            prod_id IN (
                SELECT
                    product.prod_id
                FROM
                    product
                    JOIN orderitem ON product.prod_id = orderitem.prod_id
            )
    );
/* 21.                                                                                              
List all the employees and the number of customers they service.  For employees who service no custo
mer, the value should be 0.*/                                                                       
SELECT
    employee.emp_name,
    COUNT(customer.cust_id) AS no_of_customers
FROM
    employee
    FULL OUTER JOIN customer ON customer.emp_id = employee.emp_id
GROUP BY
    emp_name;
/* 22.                                                                                              
Increase the credit limit for all customers by 10%.*/                                               
UPDATE customer
    SET
        cust_creditlimit = cust_creditlimit *1.10;
/* 23.                                                                                              
Fire all of the clerks.*/                                                                           
DELETE FROM employee WHERE
    emp_job = 'Clerk';
/* 24.                                                                                              
Add a check constraint to the ORD table that will enforce the constraint that the ship date must be 
greater than or equal to the order date.*/                                                          
ALTER TABLE ord ADD CONSTRAINT chk_ship_date CHECK (
    ord_shipdate >= ord_orderdate
);
/* 25.                                                                                              
Increase the maximum size of the CUST_NAME column to 75 characters.*/                               
ALTER TABLE customer MODIFY
    cust_name VARCHAR(75);                                                                                                   
/* 26.                                                                                              
For each customer, list that customer's customer id, name, and orders in chronological-order by orde
r date.  For each order, show the order total along with the cumulative amount of that order plus th
e order totals of any preceding orders.*/                                                           
SELECT
    ord.cust_id,
    cust_name,
    ord_orderdate,
    ord_id,
    ord_total,
    SUM(
        ord_total
    ) OVER(PARTITION BY
        ord.cust_id
        ORDER BY
            ord_orderdate
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS ord_cum_amount
FROM
    customer,
    ord
WHERE
    customer.cust_id = ord.cust_id;
/* 27.                                                                                              
Create a view that displays the customer ID, name, and number of orders placed by the customer.*/   
CREATE VIEW customer_ord AS
    ( SELECT
        ord.cust_id,
        customer.cust_name,
        COUNT(*) AS abc
    FROM
        ord,
        customer
    WHERE
        customer.cust_id = ord.cust_id
    GROUP BY
        ord.cust_id,
        customer.cust_name
    );

SELECT
    *
FROM
    customer_orders;

/* 28.                                                                                              
Create an Oracle sequence on the Ord_ID column, starting with a value greater than current Ord_ID va
lues.  Add a new order for Customer 106 using the sequence you just created.   Leave the Ord_Total N
ULL.  Otherwise, make up your own data.*/                                                           

CREATE SEQUENCE ordseq START WITH 622 INCREMENT BY 1;

INSERT INTO ord (
    ord_id,
    ord_orderdate,
    ord_commplan,
    ord_shipdate,
    ord_total,
    cust_id
) VALUES (
    ordseq.NEXTVAL,
    '25-MAY-15',
    'B',
    '31-MAY-15',
    1000,
    106
);
/* 29.                                                                                              
List the order number, customer name, and order dollar amount for all sales attributed to Ward.*/   
SELECT
    ord.ord_id,
    ord.ord_total,
    customer.cust_name
FROM
    ord
    JOIN customer ON ord.cust_id = customer.cust_id
WHERE
    emp_id IN (
        SELECT
            employee.emp_id
        FROM
            employee
            JOIN customer ON employee.emp_id = customer.emp_id
        WHERE
            emp_name = 'Ward'
    );
/* 30.                                                                                              
Alter the OrderItem table to add a foreign key constraint on the Prod_ID column.*/                  
ALTER TABLE orderitem ADD FOREIGN KEY ( prod_id )
    REFERENCES product ( prod_id );
/* 31.                                                                                              
For each customer, list the product of which the customer has purchased the most unts, together with
 the product description, customer name, and the number of units of the product purchased by the cus
tomer.*/                                                                                            
WITH most_units AS (
    SELECT
        product.prod_id,
        product.prod_descrip,
        customer.cust_name,
        orderitem.orditem_qty
    FROM
        customer
        JOIN ord ON customer.cust_id = ord.cust_id
        JOIN orderitem ON ord.ord_id = orderitem.ord_id
        JOIN product ON orderitem.prod_id = product.prod_id
) SELECT
    *
FROM
    most_units
WHERE
    orditem_qty = (
        SELECT
            MAX(orditem_qty)
        FROM
            most_units mu
        WHERE
            most_units.prod_id = mu.prod_id
    );
/* 32.                                                                                              
List each product together with the number of units of that product sold.  Also list the product cat
egory:   "High volume" if the total qty > 1000; "Moderate volume" if total aty > 500; "Low volume" i
f total qty <=500.*/                                                                                
SELECT
    product.prod_id,
    SUM(orderitem.orditem_qty) AS prod_cat,
        CASE
            WHEN SUM(orderitem.orditem_qty) <= 500             THEN 'Low Volume'
            WHEN SUM(orderitem.orditem_qty) BETWEEN 500 AND 1000THEN 'Moderate Volume'
            ELSE 'High Volume'
        END
    prod_category
FROM
    product,
    orderitem
WHERE
    product.prod_id = orderitem.prod_id
GROUP BY
    product.prod_id
--order by product.prod_descrip;
