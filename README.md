## Example queries

List classes with instance counts and total sizes.

```sql
with class_instance as (
    select
        class.obj_id,
        count(*) count,
        count(*) * class.instance_size size,
        name.text
    from instance
        inner join class on instance.class_obj_id = class.obj_id
        inner join load_class on class.obj_id = load_class.obj_id
        inner join name on load_class.name_id = name.name_id
        group by class.obj_id
        order by size desc
)
select
    count,
    count * 1.0 / (select sum(count) from class_instance) count_frac,
    size,
    size * 1.0 / (select sum(size) from class_instance) size_frac,
    text
from class_instance
;
```

Find dupes in `load_class`:

```sql
with dupe as (
    select obj_id, name_id, count(*) from load_class
    group by obj_id having count(*) > 1
)
select * from class
    inner join dupe on class.obj_id = dupe.obj_id
    inner join name on dupe.name_id = name.name_id
;
```
