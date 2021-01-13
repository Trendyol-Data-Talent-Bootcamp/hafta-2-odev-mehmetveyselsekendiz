# Soru 2) 1980’den itibaren herhangi bir spor grubunda üst üste 3 veya daha fazla madalya almış atletleri bulalım.

```SQL

create or replace table dsmbootcamp.veysel_sekendiz.second_answer as
with all_athlete as
(
 select athlete,
    lag(year,1) over (partition by athlete order by year) as prev,
    year,
    lead(year,1) over(partition by athlete order by year) as forw
 from `dsmbootcamp.veysel_sekendiz.summer_medals`
 where year >= 1980
 group by athlete, year
 order by athlete
)
select distinct athlete
from all_athlete
where prev is not null and forw is not null
order by athlete

```

- Düzeltme

```SQL
create or replace table `dsmbootcamp.veysel_sekendiz.second_answer` as
with all_athlete as
(
 select athlete,
    lag(year,1) over (partition by athlete order by year) as prev,
    year,
    lead(year,1) over(partition by athlete order by year) as forw
 from `dsmbootcamp.veysel_sekendiz.summer_medals`
 where year >= 1980
 group by athlete, year
 order by athlete
)
select distinct athlete
from all_athlete
where year-prev=4  and forw-year=4 // 4 yıl öncesi ve 4 yıl sonrasını belirttik
order by athlete
```
