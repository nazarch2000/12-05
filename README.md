# Домашнее задание к занятию «Индексы»

### Задание 1

Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

![image](https://user-images.githubusercontent.com/106932460/234861882-d5027ffb-b089-4cbc-83da-74ce66f26e14.png)

### Задание 2

Выполните explain analyze следующего запроса:
```sql
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id
-> Table scan on <temporary>  (cost=2.5..2.5 rows=0) (actual time=58.1..58.1 rows=391 loops=1)
    -> Temporary table with deduplication  (cost=0..0 rows=0) (actual time=58.1..58.1 rows=391 loops=1)
        -> Window aggregate with buffering: sum(payment.amount) OVER (PARTITION BY c.customer_id )   (actual time=56.8..57.9 rows=634 loops=1)
            -> Sort: c.customer_id  (actual time=56.8..56.8 rows=634 loops=1)
                -> Stream results  (cost=8168 rows=1689) (actual time=30.5..56.6 rows=634 loops=1)
                    -> Nested loop inner join  (cost=8168 rows=1689) (actual time=30.5..56.4 rows=634 loops=1)
                        -> Nested loop inner join  (cost=7577 rows=1689) (actual time=30.5..55.5 rows=634 loops=1)
                            -> Covering index scan on r using rental_date  (cost=1666 rows=16421) (actual time=0.0259..3.65 rows=16044 loops=1)
                            -> Filter: ((p.payment_date = r.rental_date) and (cast(p.payment_date as date) = '2005-07-30'))  (cost=0.257 rows=0.103) (actual time=0.00305..0.00308 rows=0.0395 loops=16044)
                                -> Index lookup on p using fk_payment_rental (rental_id=r.rental_id)  (cost=0.257 rows=1.03) (actual time=0.0021..0.00268 rows=1 loops=16044)
                        -> Single-row index lookup on c using PRIMARY (customer_id=p.customer_id)  (cost=0.25 rows=1) (actual time=0.00115..0.00119 rows=1 loops=634)
```


- перечислите узкие места;
- оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.
![image](https://user-images.githubusercontent.com/106932460/234863274-fb4ecd88-7343-4ca7-a400-7a3b00d042f5.png)


## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 3*

Самостоятельно изучите, какие типы индексов используются в PostgreSQL. Перечислите те индексы, которые используются в PostgreSQL, а в MySQL — нет.

*Приведите ответ в свободной форме.*
