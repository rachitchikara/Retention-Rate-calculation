# Retenetion-Rate-calculation


create table kolo_users
(id int, user_id int, login_date date);


insert into kolo_users values(1,    10,    '2022-01-01')
insert into kolo_users values(2,    11,    '2022-01-01')
insert into kolo_users values(3,    23,    '2022-01-03')
insert into kolo_users values(4,    11,    '2022-01-04')
insert into kolo_users values(5,    11,    '2022-01-06')
insert into kolo_users values(6,    22,    '2022-01-07') 
insert into kolo_users values(7,    10,    '2022-01-07')
insert into kolo_users values(8,    23,    '2022-01-08')
insert into kolo_users values(9,    10,    '2022-01-08')
insert into kolo_users values(10,   11,    '2022-01-12')
insert into kolo_users values(11,   10,    '2022-01-17')
insert into kolo_users values(12,   11,    '2022-01-23')
insert into kolo_users values(13,   23,    '2022-01-26')
insert into kolo_users values(14,   23,    '2022-01-26')
insert into kolo_users values(15,   10,    '2022-01-28')

insert into kolo_users values(16,    23,    '2022-02-01')
insert into kolo_users values(17,    11,    '2022-02-03')
insert into kolo_users values(18,    11,    '2022-02-03')
insert into kolo_users values(19,    10,    '2022-02-07')
insert into kolo_users values(20,    23,    '2022-02-09')
insert into kolo_users values(21,    23,    '2022-02-09') 
insert into kolo_users values(22,    23,    '2022-02-09')
insert into kolo_users values(23,    24,    '2022-02-10')
insert into kolo_users values(24,    25,    '2022-02-11')
insert into kolo_users values(25,    25,    '2022-02-12')
insert into kolo_users values(26,    10,    '2022-02-13')



insert into kolo_users values(27,   10,    '2022-03-14')
insert into kolo_users values(28,   23,    '2022-03-15')
insert into kolo_users values(29,   11,    '2022-03-15')
insert into kolo_users values(30,   10,    '2022-03-17')




with t as (
select a.*, b.total_count from

(select month(l_month.login_date) as month_no,
count( distinct (t_month.user_id)) as rep_cus
from kolo_users as l_month
left join kolo_users as t_month
on l_month.user_id = t_month.user_id
and DATEDIFF(month, t_month.login_date, l_month.login_date) = 1
group by month(l_month.login_date)) as a

left join

(select month(login_date) as month_no, count( distinct user_id) as total_count
from kolo_users 
group by month(login_date))  as b

on a.month_no = b.month_no)

select Month_No, (rep_cus*100/total_count) as Retenetion_Rate from t;



with t as 
(select *, 
lag(login_date, 1) over(partition by user_id order by login_date) as ret_date
from kolo_users)

select a.login_date, count(distinct b.user_id) as daily_retention_count
from 

( select * from
kolo_users) as a

left join

(select login_date, user_id, count(user_id) as rep_cus  from
t
group by login_date, ret_date, user_id
having datediff(day, ret_date, login_date) = 1) as b

on a.login_date = b.login_date

group by a.login_date;
