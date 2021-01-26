# Soru 4)
- Bizim isteğimiz sample.content_category ve sample.content_category_20201222_00_59 tablolarını karşılaştırarak
- insert edilen yeni kayıtları sample.content_category tablosunada eklemek,
- update edilen kayıtlar var ise sample.content_category tablosunda da update etmek ve
- silinmiş olan kayıtların sample.content_category tablosundaki karşılıklarının is_deleted alanını true olarak güncellemek ve silmeden saklamaktır.
- Ve bunu yaparken tek bir create or replace table ya da merge statementı kullanarak yapmak istiyoruz.
- Gereken sorguyu çalıştırdıktan sonra sample.content_category ve sample.content_category_target tablolarının içerikleri birebir aynı olmalıdır!

```SQL

merge `dsmbootcamp.veysel_sekendiz.content_category` t
using `dsmbootcamp.veysel_sekendiz.content_category_20201222_00_59` s
on t.id= s.id
when matched then
update set t.cdc_date= s.cdc_date ,t.category= s.category
when not matched by target then
insert (cdc_date, is_deleted, id, category)
values (s.cdc_date, s.is_deleted, s.id, s.category)
when not matched by source then
update set t.is_deleted= true;

----------------------------------------

create or replace table `dsmbootcamp.veysel_sekendiz.db_hash` as
select farm_fingerprint(to_json_string(t1)) as _hash1
from `dsmbootcamp.veysel_sekendiz.content_category` t1;

------------------------------------------

create or replace table `dsmbootcamp.veysel_sekendiz.target_hash` as
select farm_fingerprint(to_json_string(t1)) as _hash1
from `dsmbootcamp.veysel_sekendiz.content_category_target` t1;

------------------------------------------

create or replace table `dsmbootcamp.veysel_sekendiz.fourth_answer`as
with comparator as
(
 select case when db._hash1= tr._hash1 then 'matched' else 'mismatch' end as cmp
 from `dsmbootcamp.veysel_sekendiz.db_hash` db
 join `dsmbootcamp.veysel_sekendiz.target_hash` tr
 on (db._hash1= tr._hash1)
)
select cmp, count(*)as num_of_correct_row
from comparator
group by cmp

```
- num_of_correct_row eşleşen id sayılarını veriyor. Bu sayının db ve tr tablolarının row sayıları ile aynı olması durumunda, iki tablomuzun eşit olduğu sonucuna varıyoruz.

- references:

merge tables- 
https://www.sqlshack.com/sql-server-merge-statement-overview-and-examples/

comperator- 
https://dwgeek.com/quick-best-way-compare-two-tables-sql.html/
