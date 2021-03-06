# Limitations
Current implementation of Cycle ORM includes multiple limitations.

## Compound Primary Keys
ORM relies on unique primary key to create proper entity map, using complex primary keys is currently not supported.

## Filter by relations from external databases
It is currently not possible to automatically filter the selection based on values of related entities located in external source. 
You must manually filter the selected result after loading all the data. Though, it is possible to use `fetchData` of
`Cycle\ORM\Select` to avoid entity instantion before the filtering.

Since you can provide any iterable source to the `Cycle\ORM\Iterator` you can create nested generator:

```php
use Cycle\ORM;

function filterByExternal(ORM\Select $select, $value) 
{
    foreach($select->load('external')->fetchData() as $item) {
        if ($line['external']['value'] == $item) {
            yield $item;
        }
    }
}

// ...

foreach (new ORM\Iterator($orm, User::class, filterByExternal($select, $value)) as $user) {
    print_r($user);
}
```

> Given approach will minimize the amount of allocated memory.

## MyISAM
It is not reliable to use Cycle with MySQL MyISAM engine as it does not support transaction which can guarantee the recovery from persist errors. Use InnoDB engine instead.
