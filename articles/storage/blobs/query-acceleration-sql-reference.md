---
title: Referenční dokumentace jazyka SQL pro zrychlení dotazů (Preview)
titleSuffix: Azure Storage
description: Naučte se používat syntaxi SQL pro zrychlení dotazů.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: 3408970bcf5e34ce9f0f0afe9e723b4877dcd694
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84193410"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>Referenční dokumentace jazyka SQL pro zrychlení dotazů (Preview)

Zrychlení dotazů podporuje jazyk podobný standardu ANSI SQL pro vyjádření dotazů přes obsah objektu BLOB.  Dialekt SQL pro zrychlení dotazů je podmnožinou ANSI SQL s omezeným počtem podporovaných datových typů, operátorů atd., ale také rozšiřuje na ANSI SQL pro podporu dotazů v hierarchicky strukturovaných datových formátech, jako je JSON. 

> [!NOTE]
> Funkce zrychlení dotazů je ve verzi Public Preview a je dostupná v oblastech Kanada – střed a Francie – střed. Chcete-li zkontrolovat omezení, přečtěte si článek [známé problémy](data-lake-storage-known-issues.md) . Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [Tento formulář](https://aka.ms/adls/qa-preview-signup). 

## <a name="select-syntax"></a>VYBRAT syntaxi

Jediným příkazem jazyka SQL podporovaným zrychlením dotazu je příkaz SELECT. Tento příklad vrátí všechny řádky, pro které výraz vrací hodnotu true.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

Pro data ve formátu CSV musí být *tabulka* `BlobStorage` .  To znamená, že dotaz bude spuštěn proti libovolnému objektu blob, který byl zadán ve volání REST.
V případě dat ve formátu JSON je *tabulka* "popisovač tabulky".   Viz část s [popisovači tabulky](#table-descriptors) v tomto článku.

V následujícím příkladu pro každý řádek, pro který *výraz* WHERE vrátí hodnotu true, vrátí tento příkaz Nový řádek, který je vytvořen z vyhodnocení každého výrazu projekce.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

Následující příklad vrátí agregační výpočet (například: průměrnou hodnotu konkrétního sloupce) nad každým řádkem, pro který *výraz* vrací hodnotu true. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

Následující příklad vrátí vhodné posuny pro rozdělení objektu BLOB ve formátu CSV.  Viz část [Sys. Split](#sys-split) tohoto článku.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>Typy dat

|Typ dat|Description|
|---------|-------------------------------------------|
|INT      |64 – celé číslo se znaménkem.                     |
|Plovák    |64-bit ("dvojitá přesnost") plovoucí desetinná čárka.|
|ŘETEZCE   |Řetězec Unicode s proměnlivou délkou.            |
|ČASOVÉ razítko|Bod v čase.                           |
|DATOVÉHO  |True nebo false                             |

Při čtení hodnot z dat ve formátu CSV jsou všechny hodnoty čteny jako řetězce.  Řetězcové hodnoty lze převést na jiné typy pomocí výrazů přetypování.  Hodnoty mohou být implicitně přetypování na jiné typy v závislosti na kontextu. Další informace naleznete v tématu [Priorita datového typu (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## <a name="expressions"></a>Výrazy

### <a name="referencing-fields"></a>Odkazování na pole

Pro data ve formátu JSON nebo data ve formátu CSV s řádkem záhlaví může být na pole odkazováno podle názvu.  Názvy polí můžou být v uvozovkách nebo v uvozovkách. Názvy polí v uvozovkách jsou uzavřeny ve znakech dvojité uvozovky ("), můžou obsahovat mezery a rozlišují velká a malá písmena.  V názvech polí bez uvozovek se nerozlišují malá a velká písmena a nesmí obsahovat žádné speciální znaky.

V datech ve formátu CSV může být pole také odkazována podle pořadového čísla, s předponou podtržítkem (_).  Například první pole může být odkazováno jako _1 nebo pole jedenáctá může být odkazováno jako _11.  Odkazování na pole podle pořadového čísla je užitečné pro data ve formátu CSV, která neobsahují řádek záhlaví. v takovém případě jediný způsob, jak odkazovat na konkrétní pole, je podle pořadového čísla.

### <a name="operators"></a>Operátory

Podporovány jsou následující standardní operátory SQL:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Pokud jsou datové typy nalevo a napravo od operátoru jiné, bude proveden automatický převod podle pravidel, která jsou zde uvedena: [Priorita datového typu (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

Jazyk SQL pro zrychlení dotazů podporuje pouze velmi malou podmnožinu datových typů popsaných v tomto článku.  Další informace najdete v části [typy dat](#data-types) tohoto článku.

### <a name="casts"></a>Přetypování

Jazyk SQL pro zrychlení dotazů podporuje operátor přetypování podle pravidel zde: [Převod datového typu (databázový stroj)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

Jazyk SQL pro zrychlení dotazů podporuje pouze malou podmnožinu datových typů popsaných v tomto článku.  Další informace najdete v části [typy dat](#data-types) tohoto článku.

### <a name="string-functions"></a>Funkce řetězců

Jazyk SQL pro zrychlení dotazů podporuje následující standardní řetězcové funkce jazyka SQL:

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

Tady je několik příkladů:

|Funkce|Příklad|Výsledek|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

Funkce [Like](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) vám pomůže vyhledat vzor. Zde je několik příkladů použití funkce [Like](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) k hledání datového řetězce ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i `` .

|Dotaz|Příklad|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>Datové funkce

Podporují se tyto standardní funkce data SQL:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

V současné době převádíme všechny [formáty data Standard IS08601](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>DATE_ADD funkce

Jazyk SQL pro zrychlení dotazů podporuje pro funkci rok, měsíc, den, hodinu, minutu a sekundu ``DATE_ADD`` .

Příklady:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF funkce

Jazyk SQL pro zrychlení dotazů podporuje pro funkci rok, měsíc, den, hodinu, minutu a sekundu ``DATE_DIFF`` .

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>EXTRAHOVAT funkci

Pro EXTRAKCi jinou než datum podporované pro ``DATE_ADD`` funkci podporuje jazyk SQL Acceleration (Query Acceleration) timezone_hour a timezone_minute jako součást data.

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

Tato tabulka popisuje řetězce, které lze použít k určení výstupního formátu ``TO_STRING`` funkce.

|Řetězec formátu    |Výstup                               |
|-----------------|-------------------------------------|
|RR               |Rok ve formátu 2 číslice – 1999 jako "99"|
|y                |Rok ve formátu 4 číslice               |
|rrrr             |Rok ve formátu 4 číslice               |
|M                |Měsíc roku – 1                    |
|MM               |Nula čalouněný měsíc – 01               |
|MMM              |Abbr. měsíc roku – LED            |
|MMMM             |Celý měsíc – květen                      |
|d                |Den v měsíci (1-31)                  |
|dd               |Nula čalouněného dne v měsíci (01-31)     |
|a                |AM nebo PM                             |
|h                |Hodina dne (1-12)                   |
|hh               |Nula čalouněných hodin od dne (01-12)     |
|H                |Hodina dne (0-23)                   |
|HH               |Nula doplněná hodina dne (00-23)      |
|m                |Minuta v hodinách (0-59)                |
|mm               |Nula čalouněných minut (00-59)           |
|s                |Sekunda z minut (0-59)             |
|ss               |Nula čalouněných sekund (00-59)          |
|S                |Zlomek sekund (0,1 – 0.9)        |
|SS               |Zlomek sekund (0,01 – 0,99)      |
|POVĚŘENÍ              |Zlomek sekund (0,001 – 0.999)    |
|×                |Posun v hodinách                      |
|XX nebo XXXX       |Posun v hodinách a minutách (+ 0430)  |
|XXX nebo XXXXX     |Posun v hodinách a minutách (-07:00) |
|x                |Posun v hodinách (7)                  |
|XX nebo xxxx       |Posun v hodinách a minutách (+ 0530)    |
|XXX nebo xxxxx     |Posun v hodinách a minutách (+ 05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP funkce

Podporují se jenom formáty IS08601.

Příklady:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> ``UTCNOW``K získání systémového času můžete použít také funkci.


## <a name="aggregate-expressions"></a>Agregační výrazy

Příkaz SELECT může obsahovat jeden nebo více výrazů projekce nebo jeden agregační výraz.  Podporovány jsou následující agregační výrazy:

|Výraz|Popis|
|--|--|
|[COUNT ( \* )](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Vrátí počet záznamů, které odpovídají výrazu predikátu.|
|[COUNT (výraz)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Vrátí počet záznamů, pro které výraz má hodnotu null.|
|[AVERAGE (výraz)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Vrátí průměr hodnot výrazu, které nejsou null.|
|[MIN (výraz)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Vrátí minimální hodnotu výrazu, která není null.|
|[Max (výraz](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15))    |Vrátí maximální hodnotu výrazu, který není null.|
|[SUM (výraz)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Vrátí součet všech hodnot, které nejsou null, výrazu.|

### <a name="missing"></a>NENAŠEL

``IS MISSING``Operátor je jediným nestandardním jazykem, který podporuje jazyk SQL pro zrychlení dotazování.  Pokud v případě dat JSON chybí pole v určitém vstupním záznamu, pole výrazu ``IS MISSING`` se vyhodnotí na logickou hodnotu true.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>Deskriptory tabulky

V případě dat CSV je název tabulky vždycky `BlobStorage` .  Příklad:

```sql
SELECT * FROM BlobStorage
```

Pro data JSON jsou k dispozici další možnosti:

```sql
SELECT * FROM BlobStorage[*].path
```

To umožňuje dotazování pro podmnožiny dat JSON.

Pro dotazy JSON můžete uvést cestu v části klauzule FROM. Tyto cesty vám pomůžou analyzovat podmnožinu dat JSON. Tyto cesty mohou odkazovat na hodnoty polí a objektů JSON.

Pojďme si tento příklad porozumět podrobněji.

Tato ukázková data:

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

Může vás zajímat jenom `warehouses` objekt JSON z výše uvedených dat. `warehouses`Objekt je typ pole JSON, takže ho můžete uvést v klauzuli FROM. Vzorový dotaz může vypadat přibližně takto.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

Dotaz získá všechna pole, ale vybere pouze zeměpisnou šířku.

Pokud jste chtěli přístup pouze k `dimensions` hodnotě objektu JSON, můžete použít odkaz na tento objekt v dotazu. Příklad:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Tím také omezíte přístup ke členům `dimensions` objektu. Chcete-li získat přístup k dalším členům polí JSON a vnitřním hodnotám objektů JSON, můžete použít dotazy, jako například v následujícím příkladu:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage a BlobStorage [ \* ] odkazují na celý objekt. Pokud ale máte cestu v klauzuli FROM, budete muset použít BlobStorage [ \* ]. Path.

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys. Split

Jedná se o speciální formu příkazu SELECT, který je k dispozici pouze pro data ve formátu CSV.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Tento příkaz použijte v případech, kdy chcete stáhnout a následně zpracovat záznamy dat CSV v dávkách. Tímto způsobem můžete zpracovávat záznamy paralelně, aniž byste museli stahovat všechny záznamy najednou. Tento příkaz nevrací záznamy ze souboru CSV. Místo toho vrátí kolekci velikostí dávek. Pak můžete použít každou velikost dávky k načtení dávky datových záznamů. 

Pomocí parametru *split_size* určete počet bajtů, které mají jednotlivé dávky obsahovat. Například pokud chcete zpracovávat pouze 10 MB dat najednou, bude příkaz vypadat takto: `SELECT sys.split(10485760)FROM BlobStorage` protože 10 MB je rovno 10 485 760 bajtů. Každá dávka bude obsahovat tolik záznamů, kolik je možné do těchto 10 MB. 

Ve většině případů bude velikost každé dávky mírně vyšší než číslo, které zadáte. To proto, že dávka nemůže obsahovat částečný záznam. Pokud se poslední záznam v dávce spustí před koncem prahové hodnoty, bude dávka větší, aby mohla obsahovat úplný záznam. Velikost poslední dávky bude pravděpodobně menší než zadaná velikost.

>[!NOTE]
> Split_size musí být alespoň 10 MB (10485760).

## <a name="see-also"></a>Viz také

- [Akcelerace dotazů Azure Data Lake Storage (Preview)](data-lake-storage-query-acceleration.md)
- [Filtrování dat pomocí Azure Data Lake Storage akcelerace dotazů (Preview)](data-lake-storage-query-acceleration-how-to.md)

