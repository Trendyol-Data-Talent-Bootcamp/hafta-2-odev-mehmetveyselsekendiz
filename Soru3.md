# Soru 3) "sample.pageview": tablosunda 1 gün içerisinde trendyol.com a gelen tüm ziyaretlerin logu var. Bu çalışmada çıkarmak istediğimiz bilgi, günün her bir dakikası için aktif kullanıcı sayısının hesaplanması.

```SQL

create or replace table `dsmbootcamp.veysel_sekendiz.third_answer` as
with user_per_min as
(
 select timestamp_trunc(view_ts, minute) as view_ts_minute, hll_count.init(deviceid,24) as user      
 from `dsmbootcamp.veysel_sekendiz.pageview`
 group by 1
 order by 1
),
large as
(
 select view_ts_minute, array_agg(user) over (order by view_ts_minute rows between 4 preceding and current row) as active_user_raw
 from user_per_min
)
select view_ts_minute, (select hll_count.merge(regular) from unnest(active_user_raw) as regular) as active_user
from large
order by 1

```

- reference:
https://pex.com/blog/counting-distinct-values-rolling-windows-in-bigquery-hyperloglog-sketches/
