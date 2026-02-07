# Домашнее задание к занятию "`SQL. Часть 2`" - `Рыбянцев Павел`

---

Задание можно выполнить как в любом IDE, так и в командной строке.

### Задание 1

Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию: 
- фамилия и имя сотрудника из этого магазина;
- город нахождения магазина;
- количество пользователей, закреплённых в этом магазине.

```sql
select CONCAT(s.last_name, ' ', s.first_name) "employee", c2.city,  count(*) "count_customers"
from customer c
join staff s on c.store_id = s.store_id
join store s2 on c.store_id = s2.store_id 
join address a on s2.address_id = a.address_id 
join city c2 on a.city_id = c2.city_id 
group by c.store_id, 1, 2
HAVING count(c.store_id) > 300;
```
```
employee    |city      |count_customers|
------------+----------+---------------+
Hillyer Mike|Lethbridge|            326|
```
### Задание 2

Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

```sql
select count(*) 
from film f 
where f.`length` > (
	select AVG(f.`length`) from film f
);
```
```
count(*)|
--------+
     489|
```

### Задание 3

Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

```sql
select DATE_FORMAT(p.payment_date, '%Y-%m') "year_month", 
SUM(p.amount) "sum_payment", 
count(p.rental_id) "count_rental"
from payment p
group by 1
order by 2 DESC
LIMIT 1;
```
```
year_month|sum_payment|count_rental|
----------+-----------+------------+
2005-07   |   28368.91|        6709|
```

## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 4*

Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».

```sql
with p as (
	select 
	CONCAT(s.last_name, ' ', s.first_name) "employee", count(*) "cnt"
	from payment p
	join staff s on p.staff_id  = s.staff_id 
	group by 1
)
select p.*,
case 
	when p.cnt > 8000 then 'Да'
	else 'Нет'
end "Премия"
from p
```
```
employee    |cnt |Премия|
------------+----+------+
Hillyer Mike|8054|Да    |
Stephens Jon|7990|Нет   |
```


### Задание 5*

Найдите фильмы, которые ни разу не брали в аренду.

```sql
/*
 * Похоже что такого запроса будет достаточно,
 * так как если фильма нет в таблице inventory,
 * значит его нет в таблице rental
 */
select f.film_id, f.title  
from film f 
where 
f.film_id not in (
	select i.film_id from inventory i 
);
```
```
film_id|title                 |
-------+----------------------+
     14|ALICE FANTASIA        |
     33|APOLLO TEEN           |
     36|ARGONAUTS TOWN        |
     38|ARK RIDGEMONT         |
     41|ARSENIC INDEPENDENCE  |
     87|BOONDOCK BALLROOM     |
    108|BUTCH PANTHER         |
    128|CATCH AMISTAD         |
    144|CHINATOWN GLADIATOR   |
    148|CHOCOLATE DUCK        |
    171|COMMANDMENTS EXPRESS  |
    192|CROSSING DIVORCE      |
    195|CROWDS TELEMARK       |
    198|CRYSTAL BREAKING      |
    217|DAZED PUNK            |
    221|DELIVERANCE MULHOLLAND|
    318|FIREHOUSE VIETNAM     |
    325|FLOATS GARDEN         |
    332|FRANKENSTEIN STRANGER |
    359|GLADIATOR WESTWARD    |
    386|GUMP DATE             |
    404|HATE HANDICAP         |
    419|HOCUS FRIDA           |
    495|KENTUCKIAN GIANT      |
    497|KILL BROTHERHOOD      |
    607|MUPPET MILE           |
    642|ORDER BETRAYED        |
    669|PEARL DESTINY         |
    671|PERDITION FARGO       |
    701|PSYCHO SHRUNK         |
    712|RAIDERS ANTITRUST     |
    713|RAINBOW SHOCK         |
    742|ROOF CHAMPION         |
    801|SISTER FREDDY         |
    802|SKY MIRACLE           |
    860|SUICIDES SILENCE      |
    874|TADPOLE PARK          |
    909|TREASURE COMMAND      |
    943|VILLAIN DESPERATE     |
    950|VOLUME HOUSE          |
    954|WAKE JAWS             |
    955|WALLS ARTIST          |
```