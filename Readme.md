### Group by language
```
SELECT 
  language,
  count(*),
  sum(case when activated_at is not null then 1 else null end) as activated,
  sum(case when activated_at is null then 1 else null end) as not_activated,
FROM tutorial.yammer_users
group by 1
ORDER BY 2 DESC
```
### Some of the those in the group had not joined the platform prior to the test
```
with users as (SELECT 
a.user_id,
b.experiment_group
FROM tutorial.yammer_users a
JOIN tutorial.yammer_experiments b
  on a.user_id = b.user_id
where created_at <= '2014-06-30')

SELECT 
experiment_group,
count(distinct user_id) as users
from users 
group by 1
```
### Test
```
with users as (SELECT 
a.user_id,
b.experiment_group
FROM tutorial.yammer_users a
JOIN tutorial.yammer_experiments b
  on a.user_id = b.user_id
where created_at <= '2014-06-30')

SELECT 
a.experiment_group,
case when occurred_at >= '2014-06-01' and occurred_at <= '2014-06-30' then 'June'
  when occurred_at >= '2014-05-01' and occurred_at <= '2014-05-31' then 'May' 
  when occurred_at >= '2014-04-01' and occurred_at <= '2014-04-30' then 'April'
  when occurred_at >= '2014-03-01' and occurred_at <= '2014-03-31' then 'March'
  else null end as month,
count(distinct a.user_id) as users,
count(*) 
FROM users a
JOIN tutorial.yammer_events b 
  on a.user_id = b.user_id 
WHERE event_name = 'send_message'
group by 1,2 
order by 1,2
```

