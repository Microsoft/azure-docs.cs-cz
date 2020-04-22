---
title: Referenční příručka jazyka SQL akcelerace dotazu (náhled)
titleSuffix: Azure Storage
description: Přečtěte si, jak používat syntaxi sql akcelerace dotazu.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772116"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>Referenční příručka jazyka SQL akcelerace dotazu (náhled)

Akcelerace dotazu podporuje jazyk podobný ANSI SQL pro vyjádření dotazů nad obsahem objektu blob.  Akcelerace dotazu SQL dialekt je podmnožinou ANSI SQL, s omezenou sadou podporovaných datových typů, operátorů atd., ale také rozšiřuje NA ANSI SQL pro podporu dotazů přes hierarchické polostrukturované datové formáty, jako je Například JSON. 

> [!NOTE]
> Funkce akcelerace dotazu je ve verzi Public Preview a je k dispozici v oblastech Kanada – střed a Francie – střed. Omezení najdete v článku [Známé problémy.](data-lake-storage-known-issues.md) Chcete-li se zaregistrovat do náhledu, přečtěte [si tento formulář](https://aka.ms/adls/qa-preview-signup). 

## <a name="select-syntax"></a>Syntaxe SELECT

Jediným příkazem SQL podporovaným akcelerací dotazu je příkaz SELECT. Tento příklad vrátí každý řádek, pro který výraz vrátí true.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

U dat ve formátu *table* CSV `BlobStorage`musí být tabulka .  To znamená, že dotaz bude spuštěn proti podle toho, blob byl zadán ve volání REST.
Pro data ve formátu JSON je *tabulka* "popisovač tabulky".   V tomto článku najdete v části [Popisy tabulek.](#table-descriptors)

V následujícím příkladu pro každý řádek, pro který *výraz* WHERE vrátí hodnotu true, vrátí tento příkaz nový řádek, který je vyroben z vyhodnocení každého výrazu projekce.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

Následující příklad vrátí agregační výpočet (například průměrná hodnota určitého sloupce) přes každý z řádků, pro které *výraz* vrátí hodnotu true. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

Následující příklad vrátí vhodné posuny pro rozdělení objektu blob ve formátu CSV.  V tomto článku najdete v části [Sys.Split.](#sys-split)

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>Typy dat

|Typ dat|Popis|
|---------|-------------------------------------------|
|INT      |64bitové podepsané celé číslo.                     |
|Float    |64bitová ("dvojitá přesnost") s plovoucí desetinnou tázkou.|
|Řetězec   |Řetězec Unicode s proměnnou délkou.            |
|Časové razítko|Bod v čase.                           |
|Boolean  |True nebo false                             |

Při čtení hodnot z dat ve formátu CSV jsou všechny hodnoty čteny jako řetězce.  Řetězcové hodnoty mohou být převedeny na jiné typy pomocí výrazů CAST.  Hodnoty mohou být implicitně přetypována do jiných typů v závislosti na kontextu. Další informace naleznete v [tématu Priorita datového typu (Transact-SQL).](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)

## <a name="expressions"></a>Výrazy

### <a name="referencing-fields"></a>Odkazování na pole

Pro data ve formátu JSON nebo data ve formátu CSV s řádkem záhlaví mohou být pole odkazována podle názvu.  Názvy polí mohou být citovány nebo nekotovány. Názvy polí v uvozovkách jsou uzavřeny ve dvoucionálových znacích ("), mohou obsahovat mezery a rozlišují malá a velká písmena.  Názvy nekotovaných polí nerozlišují malá a velká písmena a nesmí obsahovat žádné speciální znaky.

V datech ve formátu CSV mohou být pole také odkazována ordinálním znakem s předponou (_).  První pole může být například odkazováno jako _1 nebo jedenácté pole může být odkazováno jako _11.  Odkazování na pole podle řadového čísla je užitečné pro data ve formátu CSV, která neobsahují řádek záhlaví, v takovém případě je jediným způsobem, jak odkazovat na určité pole, řadové.

### <a name="operators"></a>Operátory

Podporovány jsou následující standardní operátory SQL:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Pokud se datové typy na levé a pravé straně operátoru liší, bude automatický převod proveden podle zde uvedených pravidel: [Priorita datového typu (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

Dotaz akcelerace jazyka SQL podporuje pouze velmi malou podmnožinu datových typů popsaných v tomto článku.  V tomto článku najdete část [Datové typy.](#data-types)

### <a name="casts"></a>Vrhá

Dotaz akcelerace SQL jazyk podporuje operátor CAST, podle pravidel zde: [Převod datového typu (Database Engine)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

Dotaz akcelerace jazyka SQL podporuje pouze malou podmnožinu datových typů popsaných v tomto článku.  V tomto článku najdete část [Datové typy.](#data-types)

### <a name="string-functions"></a>Funkce řetězců

Jazyk SQL akcelerace dotazu podporuje následující standardní funkce řetězce SQL:

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

Zde je několik příkladů:

|Funkce|Příklad|Výsledek|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

Funkce [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) vám pomůže vyhledat vzorek. Zde je několik příkladů, které používají funkci [LIKE](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``k prohledání datového řetězce .

|Dotaz|Příklad|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>Datové funkce

Podporovány jsou následující standardní funkce data SQL:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

V současné době převádíme všechny [formáty dat standardního IS08601](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>DATE_ADD funkce

Akceležádost dotazu SQL jazyk podporuje rok, měsíc, ``DATE_ADD`` den, hodina, minuta, sekunda pro funkci.

Příklady:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF funkce

Akceležádost dotazu SQL jazyk podporuje rok, měsíc, ``DATE_DIFF`` den, hodina, minuta, sekunda pro funkci.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>FUNKCE EXTRACT

Pro extract jiné než datum ``DATE_ADD`` část podporovaná pro funkci, akcelerace dotazu JAZYK SQL podporuje timezone_hour a timezone_minute jako část data.

Příklady:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING funkce

Příklady:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

Tato tabulka popisuje řetězce, které můžete použít k ``TO_STRING`` určení výstupního formátu funkce.

|Formátovací řetězec    |Výstup                               |
|-----------------|-------------------------------------|
|RR               |Rok ve dvoumístném formátu – 1999 jako '99'|
|y                |Rok ve čtyřmístném formátu               |
|rrrr             |Rok ve čtyřmístném formátu               |
|M                |Měsíc v roce – 1                    |
|MM               |Nula polstrovaný měsíc – 01               |
|MMM              |Abbr. měsíc roku -JAN            |
|MMMM             |Celý měsíc – květen                      |
|d                |Den v měsíci (1-31)                  |
|dd               |Nula polstrovaný den v měsíci (01-31)     |
|a                |Dopoledne nebo odpoledne                             |
|h                |Hodina dne (1-12)                   |
|hh               |Nula polstrované hodiny od den (01-12)     |
|H                |Hodina dne (0-23)                   |
|HH               |Nula Polstrovaná hodina dne (00-23)      |
|m                |Minuta (0-59)                |
|mm               |Nulová polstrovaná minuta (00-59)           |
|s                |Druhá z minut (0-59)             |
|ss               |Nula polstrované sekundy (00-59)          |
|S                |Zlomek sekund (0.1-0.9)        |
|SS               |Zlomek sekund (0.01-0.99)      |
|Sss              |Zlomek sekund (0.001-0.999)    |
|×                |Posun v hodinách                      |
|XX nebo XXXX       |Posun v hodinách a minutách (+0430)  |
|XXX nebo XXXXX     |Posun v hodinách a minutách (-07:00) |
|x                |Posun v hodinách (7)                  |
|xx nebo xxxx       |Posun v hodině a minutě (+0530)    |
|Xxx nebo xxxxx     |Posun v hodině a minutě (+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP funkce

Podporovány jsou pouze formáty IS08601.

Příklady:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> Můžete také použít ``UTCNOW`` funkci získat systémový čas.


## <a name="aggregate-expressions"></a>Agregační výrazy

Příkaz SELECT může obsahovat jeden nebo více výrazů projekce nebo jeden agregační výraz.  Podporovány jsou následující agregační výrazy:

|Expression|Popis|
|--|--|
|[POČET(\*)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Vrátí počet záznamů, které odpovídaly predikátu výraz.|
|[COUNT(výraz)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Vrátí počet záznamů, pro které výraz není null.|
|[PRŮMĚR(výraz)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Vrátí průměr hodnot výrazu, které nejsou null.|
|[MIN(výraz)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Vrátí minimální hodnotu výrazu, která není null.|
|[MAX(výraz)](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15)    |Vrátí maximální hodnotu výrazu, která není null.|
|[SUMA(výraz)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Vrátí součet všech hodnot výrazu, které nejsou nulové.|

### <a name="missing"></a>Chybějící

Operátor ``IS MISSING`` je jediný nestandardní, který podporuje dotaz akcelerace jazyka SQL.  Pokud u dat JSON chybí pole v určitém vstupním ``IS MISSING`` záznamu, pole výrazu vyhodnotí logickou hodnotu true.

<a id="table-descriptors" />

## <a name="table-descriptors"></a>Popisovače tabulky

U dat CSV je název `BlobStorage`tabulky vždy .  Příklad:

```sql
SELECT * FROM BlobStorage
```

Pro data JSON jsou k dispozici další možnosti:

```sql
SELECT * FROM BlobStorage[*].path
```

To umožňuje dotazy přes podmnožiny dat JSON.

Pro dotazy JSON můžete uvést cestu v části FROM klauzule. Tyto cesty pomohou analyzovat podmnožinu dat JSON. Tyto cesty mohou odkazovat na hodnoty Pole JSON a Object.

Vezměme si příklad, abychom to pochopili podrobněji.

Toto jsou naše ukázková data:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Možná vás bude zajímat `warehouses` pouze objekt JSON z výše uvedených dat. Objekt `warehouses` je typ pole JSON, takže to můžete zmínit v klauzuli FROM. Ukázkový dotaz může vypadat nějak takto.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

Dotaz získá všechna pole, ale vybere pouze zeměpisnou šířku.

Pokud jste chtěli získat `dimensions` přístup pouze k hodnotě objektu JSON, můžete použít odkazovat na tento objekt v dotazu. Příklad:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

To také omezuje váš přístup `dimensions` k členům objektu. Pokud chcete získat přístup k ostatním členům polí JSON a vnitřních hodnot objektů JSON, můžete použít dotazy, které jsou uvedeny v následujícím příkladu:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage a BlobStorage[\*] oba odkazují na celý objekt. Pokud však máte cestu v klauzuli FROM, budete muset použít\*BlobStorage[ .cesta

<a id="sys-split" />

## <a name="syssplit"></a>Sys.Split

Jedná se o zvláštní formu příkazu SELECT, který je k dispozici pouze pro data ve formátu CSV.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Toto prohlášení použijte v případech, kdy chcete stáhnout a zpracovat datové záznamy CSV v dávkách. Tímto způsobem můžete zpracovávat záznamy paralelně namísto nutnosti stahovat všechny záznamy najednou. Tento příkaz nevrací záznamy ze souboru CSV. Místo toho vrátí kolekci velikostí dávek. Potom můžete použít každou velikost dávky k načtení dávky datových záznamů. 

Pomocí *parametru split_size* určete počet bajtů, které mají každá dávka obsahovat. Například pokud chcete zpracovat pouze 10 MB dat najednou, můžete příkaz vypadat `SELECT sys.split(10485760)FROM BlobStorage` takto: protože 10 MB se rovná 10,485,760 bajtů. Každá dávka bude obsahovat tolik záznamů, kolik se vejde do těchto 10 MB. 

Ve většině případů bude velikost každé dávky mírně vyšší než zadané číslo. To proto, že dávka nemůže obsahovat částečný záznam. Pokud poslední záznam v dávce začíná před koncem prahové hodnoty, dávka bude větší, aby mohla obsahovat celý záznam. Velikost poslední dávky bude pravděpodobně menší než zadaná velikost.

>[!NOTE]
> Split_size musí být alespoň 10 MB (10485760).

## <a name="see-also"></a>Viz také

- [Akcelerace dotazu Azure Data Lake Storage (preview)](data-lake-storage-query-acceleration.md)
- [Filtrování dat pomocí akcelerace dotazu Azure Data Lake Storage (preview)](data-lake-storage-query-acceleration-how-to.md)

