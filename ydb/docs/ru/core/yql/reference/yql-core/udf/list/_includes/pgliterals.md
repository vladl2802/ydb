### Целочисленные литералы {#intliterals}

Суффикс | Тип | Комментарий
----- | ----- | -----
`p` | `PgInt4` | 32-битное знаковое целое (в PostgreSQL нет беззнаковых типов)
`ps`| `PgInt2` | 16-битное знаковое целое
`pi`| `PgInt4` |
`pb`| `PgInt8` | 64-битное знаковое цело
`pn`| `PgNumeric` | знаковое целое произвольной точности (до 131072 цифр)

### Литералы с плавающей точкой {#floatliterals}

Суффикс | Тип | Комментарий
----- | ----- | -----
`p` | `PgFloat8` | число с плавающей точкой (64 бит double)
`pf4`| `PgFloat4` | число с плавающей точкой (32 бит float)
`pf8`| `PgFloat8` |
`pn` | `PgNumeric` | число с плавающей точкой произвольной точности (до 131072 цифр перед запятой, до 16383 цифр после запятой)

### Строковые литералы {#stringliterals}

Суффикс | Тип | Комментарий
----- | ----- | -----
`p` | `PgText` | текстовая строка
`pt`| `PgText` |
`pv`| `PgVarchar` | текстовая строка
`pb`| `PgBytea` | бинарная строка

{% note warning "Внимание" %}

Значения строковых/числовых литералов (т.е. то что идет перед суффиксом) должны быть валидной строкой/числом с точки зрения YQL.
В частности, должны соблюдаться правила эскейпинга YQL, а не PostgreSQL.

{% endnote %}

Пример:

```yql
SELECT
    1234p,             -- pgint4
    0x123pb,         -- pgint8
    "тест"pt,         -- pgtext
    123e-1000pn; -- pgnumeric
;
```

### Литерал массива

Для построения литерала массива используется функция `PgArray`:

```yql
SELECT
    PgArray(1p, NULL ,2p) -- {1,NULL,2}, тип _int4
;
```


### Конструктор литералов произвольного типа {#literals_constructor}

Литералы всех типов (в том числе и Pg типов) могут создаваться с помощью конструктора литералов со следующей сигнатурой:
`Имя_типа(<строковая константа>)`.

Напрмер:
```yql
DECLARE $foo AS String;
SELECT
    PgInt4("1234"), -- то же что и 1234p
    PgInt4(1234),   -- в качестве аргумента можно использовать литеральные константы
    PgInt4($foo),   -- и declare параметры
    PgBool(true),
    PgInt8(1234),
    PgDate("1932-01-07"),
;
```

Также поддерживается встроенная функция `PgConst` со следующей сигнатурой: `PgConst(<строковое значение>, <тип>)`.
Такой способ более удобен для кодогенерации.

Например:

```yql
SELECT
    PgConst("1234", PgInt4), -- то же что и 1234p
    PgConst("true", PgBool)
;
```

Для некоторых типов в функции `PgConst` можно указать дополнительные модификаторы. Возможные модификаторы для типа `pginterval` перечислены в [документации PostgreSQL](https://www.postgresql.org/docs/16/datatype-datetime.html).

```yql
SELECT
    PgConst(90, pginterval, "day"), -- 90 days
    PgConst(13.45, pgnumeric, 10, 1); -- 13.5
;
```
