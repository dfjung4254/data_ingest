#### 20210427 Hive, Impala 시험 - 정근화


```sql
// 1
select * from products where brand='Gigabux' and price < 1000;
```
![answer_1.png](answer_1.png)


```sql
// 2 
alter table vendors rename to suppliers;
```
![answer_2.png](answer_2.png)


```sql
// 3
select p.brand, p.name, count(*) as cnt
from orders o
join order_details od on (o.order_id = od.order_id)
join products p on (od.prod_id = p.prod_id)
group by p.brand, p.name
order by cnt desc
limit 3;
```
![answer_3.png](answer_3.png)
