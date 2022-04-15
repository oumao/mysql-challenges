**Question 1**\
You are given a table flats with the following structure:
```
CREATE TABLE `flats` (
  `id` int NOT NULL,
  `city` varchar(40) NOT NULL,
  `price` int NOT NULL,
  PRIMARY KEY (`id`)
);
```
Each row of table flats represents a flat located in city available for sale for a given price. Write an SQL query that, for each city finds the three cheapest flats located in that city. In case of tie, the query may return any three flats wih the cheapest prices.

The result table should contain three columns: id, city and price and should be sorted by id column. If the city has less than three flats for sale, the result table should contain all of them. Please use and OVER clause to simplify your solution.

Example:
```
Given
+------+-----------+---------+
| id   | city      | price   |
+------+-----------+---------+
| 25   | London    |  200000 |
| 5    | Cairo     |  90000  |
| 7    | London    |  200000 |
| 18   | Warsaw    |  150000 |
| 2    | London    |  178000 |
| 3    | Cairo     |  300000 |
| 21   | London    |  500000 |
| 9    | London    |  200000 |
+------+-----------+---------+
```
one of the possible output is:
```
+------+-----------+---------+
| id   | city      | price   |
+------+-----------+---------+
| 2    | London    |  178000 |
| 3    | Cairo     |  300000 |
| 5    | Cairo     |  90000  |
| 7    | London    |  200000 |
| 9    | London    |  200000 |
| 18   | Warsaw    |  150000 |
+------+-----------+---------+
```
There are only two flats available in Warsaw and one in Cairo, so they are all selected. The cheapest flat in London has ID 2, then there are three flats with the same price (with IDs: 7,9,25). Any two of them may be selected.

Assume that:\
 * the flats table has at most 100 rows;\ 
 * price column can only contain integers from 1000 to 10000000\

**Solution**
```
select id, city, price, price_rank
from (
   select id, city, price, 
   ROW_NUMBER() OVER (PARTITION BY city ORDER BY price asc) AS n,
   rank() OVER (
   	partition by city
   	ORDER BY price ASC 
   ) price_rank
   from flats
) t
where n<=3
order by id;
```
