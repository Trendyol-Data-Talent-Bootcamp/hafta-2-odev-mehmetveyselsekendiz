# Soru 1) 1980’den itibaren spor grubu bazında en çok madalya alan 1. 3. 5. ülkeyi bulalım.

```SQL

create or replace table dsmbootcamp.veysel_sekendiz.first_answer_1 as
with all_countries as
(
 select country, count(sport) as total_medals_each_country
 from `dsmbootcamp.veysel_sekendiz.summer_medals`
 where year >= 1980
 group by country
 order by count(sport) desc
)
select row_number() over (order by total_medals_each_country desc) as row_id, country, total_medals_each_country
from all_countries
order by row_id;

create or replace table dsmbootcamp.veysel_sekendiz.first_answer as
select *
from `dsmbootcamp.veysel_sekendiz.first_answer_1`
where row_id=1 or row_id=3 or row_id=5
order by row_id


-------------------------------------------------------------------------

create or replace table dsmbootcamp.veysel_sekendiz.first_answer as
select *
from 
(
 with all_countries as
 (
  select country, count(sport) as total_medals_each_country
  from `dsmbootcamp.veysel_sekendiz.summer_medals`
  where year >= 1980
  group by country
  order by count(sport) desc
 )
 select row_number() over (order by total_medals_each_country desc) as row_id, country, total_medals_each_country
 from all_countries
 order by row_id
)
where row_id=1 or row_id=3 or row_id=5
order by row_id


```
