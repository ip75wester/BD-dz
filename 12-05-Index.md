# Домашнее задание к занятию "`Индексы`" - `Илья Попов`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
Задание можно выполнить как в любом IDE, так и в командной строке.

### Задание 1. 

Напишите запрос к учебной базе данных, который вернет процентное отношение общего размера всех индексов к общему размеру всех таблиц.

SELECT COUNT(INDEX_LENGTH) / (SELECT SUM(DATA_LENGTH) FROM INFORMATION_SCHEMA.TABLES) *100

FROM information_schema.TABLES t




![Скриншот](https://github.com/ip75wester/BD-dz/blob/main/za1.PNG)





### Задание 2. 

Выполните explain analyze следующего запроса:

select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)

from payment p, rental r, customer c, inventory i, film f

where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id

![Скриншот](https://github.com/ip75wester/BD-dz/blob/main/za2.PNG)

- перечислите узкие места;
- оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.

### Ответ:
- большое время обработки, использованы лишние таблицы

Необходимо:

- Убрать из запроса лишнее поле f.title и лишнюю таблицу film f
- Оконную функцию переписать на group by
- Убрать distinct
- Добавить условие соединения таблиц JOIN 
- Создать индекс по полю p.payment_date


![Скриншот](https://github.com/ip75wester/BD-dz/blob/main/za5.PNG)



explain analyze

select concat(c.last_name, ' ', c.first_name), sum(p.amount)

from payment p

JOIN rental r ON p.payment_date = r.rental_date

JOIN customer c ON r.customer_id = c.customer_id

JOIN inventory i ON i.inventory_id = r.inventory_id

where payment_date >= '2005-07-30' and payment_date < DATE_ADD('2005-07-30', INTERVAL 1 DAY) 

group by c.customer_id


![Скриншот](https://github.com/ip75wester/BD-dz/blob/main/za6.PNG)
