```sql
select vend_id ,Min(prod_price) cheapet_item from products group by vend_id  order by cheapet_item
-- 
select order_num from OrderItems group by order_num having sum(quantity*item_price)>=1000 order by order_num 
-- 
select cust_id from Orders where order_num in (select order_num from OrderItems group by order_num having Sum(item_price)>=10 )
-- 
select c.cust_email from orders a 
left jion OrderItems b on a.order_num=b.order_num 
left jion Cuntomers  c on b.cust_id=c.cust_id
where a.prod_id='cust_id'
-- 
select cust_id ,sum(item_price*quantity) from OrderItems oi,Orders o where oi.order_num=o.order_num 
group by cust_id 
-- 
select p.prod_name ,sum(o.quantity) quant_sold from products p left jion orderItems o on p.prod_id=o.prod_id 
group by p.prod_name

SELECT p.prod_name, tb.quant_sold
FROM (SELECT prod_id, Sum(quantity) AS quant_sold
    FROM OrderItems
    GROUP BY prod_id) AS tb,
  Products p
WHERE tb.prod_id = p.prod_id
-- 
select cust_name ,order_num from Customers c left jion Orders o on c.cust_id=o.cust_id
-- 
select c.cust_name,o.order_num,sum(oi.quantity,oi.item_price) as OrderToal from 
Customers c,Orders o,Orderitem oi where c.cust_name=o.cust_name
and o.order_num=oi.order_num
group by c.cust_name,o.order_num
order by c.cust_name,o.order_num
```

```sql
select o.cust_id,t.order_date from orders o where order_num in (select * from OrderItems where prod_id='BRo1') as t order by t.order_date
-- 
select c.cust_email from Customers c ,Orders o,OrderItems oi where oi.order_num=o.order_num and o.cust_id=c.cust_id 
and oi.prod_id='BRo1';
/或者
select c.cust_email from Customers c 
 left jion Order o on o.cust_id=c.cust_id 
 left jion orderItems oi on o.order_num=oi.order_num
 where oi.prod_id='BRo1';
```

```sql
select cust_name,sum(item*quantity) as total from Customers  c
left jion orders o on o.cust_id=c.cust_id
left jion OrderItems oi on o.order_num=oi.order_num
group by c.cust_id 
having total>=1000
```

```sql
select cust_name,order_num
from orders o inner jion Customer c on c.cust_id=o.cust_id 
```

```sql
select count(*) as total_pv,count(submit_time) as complete_pv,count(distinct exam_id) as complete_exam_cnt
from exam_record
```

```sql
select score from exam_record er where score>(select round(avg(score),1) as avg_score from exam_record er1 where exam_id=(select exam_id from examination_info where tag='SQL') group by exam_id having submit_time is not null and score is not null)
order by score limit 1;
```

```sql
select Date_Format(submit_time,'%Y%m') month,
 round(count(DISTINCT UID, DATE_FORMAT(submit_time, '%Y%m%d')) / count(DISTINCT UID), 2) avg_active_days,
 count(Distinct uid)
 from exam_record
 -- Date_Fromat(submit,'%Y%m')
 select round(avg(count(distinct uid),2) from score where time=2021 group by Month(submit)
```

```sql
select DateFormat(submit_time,'%Y%m') submit_month,count(question_id) month_q_cnt,Round(moth./Day(LastDay(DateFormat(submit_time,'%Y%m%d')))) as avg_day from practice_record 
group by submit_month
union all 
select '2021汇总',count(question) as month_q_cnt ,round(count(question)/yearDays,3) from record 
where DATE_FORMAT(submit_time, '%Y') = '2021'
```

```sql
select uid,
 count(submit) as complete,
 count(submit,submit is null) unComplete,
 contact(',',此处拼接信息)
 from record r left jion info i on r.id=i.id
 group by r.uid
 ...
```

```sql
select tag,count(submit_time) as times
from exam_record e left jion info i on e.exam_id=i.exam_id
where  uid in
(select uid from exam_record group by uid having count(submit_time)>3)
group by tag
order by times DESC
```

```sql
select e.exam_id,count(distinct uid) uv,
round(sum(score)/count(e.uid),1)from user_info u
left jion exam_record e on u.uid=e.uid
left jion exam_info ei on ei.exam_id=e.exam_id
where u.level>5 and ei.tag='SQL'
group by exam_id having ei.releaseTime =e.releaseTime
having submit is not null
order by uv ,exam DESC


```

```sql
select level,count(uid) level_uv
from userInfo u
left jion exam_record e on u.examid=e.examid 
left jion exam_info ei on 
ei.exam_id=e.exam_id
where e.score>80 
group by u.level
order by level_uv DESC
```

```sql
select exam_id as tid
,count(Distinct uid) uv
,count(submit_time) pv
from exam_record
where submit_time is not null
and score is not null
group by exam_id

```

