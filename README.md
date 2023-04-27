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

-> Table scan on <temporary>  (cost=2.5..2.5 rows=0) (actual time=6745..6745 rows=391 loops=1)
    -> Temporary table with deduplication  (cost=0..0 rows=0) (actual time=6745..6745 rows=391 loops=1)
        -> Window aggregate with buffering: sum(payment.amount) OVER (PARTITION BY c.customer_id,f.title )   (actual time=2778..6514 rows=642000 loops=1)
            -> Sort: c.customer_id, f.title  (actual time=2777..2868 rows=642000 loops=1)
                -> Stream results  (cost=10.9e+6 rows=17.1e+6) (actual time=2.07..2190 rows=642000 loops=1)
                    -> Nested loop inner join  (cost=10.9e+6 rows=17.1e+6) (actual time=2.06..1934 rows=642000 loops=1)
                        -> Nested loop inner join  (cost=9.21e+6 rows=17.1e+6) (actual time=0.382..1657 rows=642000 loops=1)
                            -> Nested loop inner join  (cost=7.49e+6 rows=17.1e+6) (actual time=0.377..1361 rows=642000 loops=1)
                                -> Inner hash join (no condition)  (cost=1.65e+6 rows=16.5e+6) (actual time=0.367..69.1 rows=634000 loops=1)
                                    -> Filter: (cast(p.payment_date as date) = '2005-07-30')  (cost=1.72 rows=16500) (actual time=0.0303..6.49 rows=634 loops=1)
                                        -> Table scan on p  (cost=1.72 rows=16500) (actual time=0.0197..4.43 rows=16044 loops=1)
                                    -> Hash
                                        -> Covering index scan on f using idx_title  (cost=111 rows=1000) (actual time=0.0407..0.243 rows=1000 loops=1)
                                -> Covering index lookup on r using rental_date (rental_date=p.payment_date)  (cost=0.25 rows=1.04) (actual time=0.00119..0.00181 rows=1.01 loops=634000)
                            -> Single-row index lookup on c using PRIMARY (customer_id=r.customer_id)  (cost=250e-6 rows=1) (actual time=191e-6..235e-6 rows=1 loops=642000)
                        -> Single-row covering index lookup on i using PRIMARY (inventory_id=r.inventory_id)  (cost=925e-6 rows=1) (actual time=156e-6..200e-6 rows=1 loops=642000)
```


- перечислите узкие места;
```
1. Судя по анализу, много производительности жрет оконная функция.
2. Обращение к таблице inventory можно убрать, оно никак не влияет на результат.
```
- оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.
![image](https://user-images.githubusercontent.com/106932460/234908867-29b52265-dc1a-49fb-8970-6b0f103bfa03.png)

## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 3*

Самостоятельно изучите, какие типы индексов используются в PostgreSQL. Перечислите те индексы, которые используются в PostgreSQL, а в MySQL — нет.

*Приведите ответ в свободной форме.*
