---
title: Referenční dokumentace jazyka SQL pro zrychlení dotazů
titleSuffix: Azure Storage
description: Naučte se používat syntaxi SQL pro zrychlení dotazů.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: affddf7367f58107106ae07a07b8baedac73e251
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659558"
---
# <a name="query-acceleration-sql-language-reference"></a>Referenční dokumentace jazyka SQL pro zrychlení dotazů

Zrychlení dotazů podporuje jazyk podobný standardu ANSI SQL pro vyjádření dotazů přes obsah objektu BLOB.  Dialekt SQL pro zrychlení dotazů je podmnožinou ANSI SQL s omezeným počtem podporovaných datových typů, operátorů atd., ale také rozšiřuje na ANSI SQL pro podporu dotazů v hierarchicky strukturovaných datových formátech, jako je JSON. 

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

## <a name="data-types"></a>Datové typy

|Typ dat|Popis|
|---------|-------------------------------------------|
|INT      |64 – celé číslo se znaménkem.                     |
|Plovák    |64-bit ("dvojitá přesnost") plovoucí desetinná čárka.|
|ŘETEZCE   |Řetězec Unicode s proměnlivou délkou.            |
|ČASOVÉ razítko|Bod v čase.                           |
|DATOVÉHO  |True nebo false                             |

Při čtení hodnot z dat ve formátu CSV jsou všechny hodnoty čteny jako řetězce.  Řetězcové hodnoty lze převést na jiné typy pomocí výrazů přetypování.  Hodnoty mohou být implicitně přetypování na jiné typy v závislosti na kontextu. Další informace naleznete v tématu [Priorita datového typu (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql).

## <a name="expressions"></a>Výrazy

### <a name="referencing-fields"></a>Odkazování na pole

Pro data ve formátu JSON nebo data ve formátu CSV s řádkem záhlaví může být na pole odkazováno podle názvu.  Názvy polí můžou být v uvozovkách nebo v uvozovkách. Názvy polí v uvozovkách jsou uzavřeny ve znakech dvojité uvozovky ("), můžou obsahovat mezery a rozlišují velká a malá písmena.  V názvech polí bez uvozovek se nerozlišují malá a velká písmena a nesmí obsahovat žádné speciální znaky.

V datech ve formátu CSV může být pole také odkazována podle pořadového čísla, s předponou podtržítkem (_).  Například první pole může být odkazováno jako _1 nebo pole jedenáctá může být odkazováno jako _11.  Odkazování na pole podle pořadového čísla je užitečné pro data ve formátu CSV, která neobsahují řádek záhlaví. v takovém případě jediný způsob, jak odkazovat na konkrétní pole, je podle pořadového čísla.

### <a name="operators"></a>Operátory

Podporovány jsou následující standardní operátory SQL:

|Operátor|Popis|
|--|--|
|[=](https://docs.microsoft.com/sql/t-sql/language-elements/equals-transact-sql)    |Porovná rovnost dvou výrazů (relační operátor).|
|[!=](https://docs.microsoft.com/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |Testuje, zda jeden výraz není roven jinému výrazu (operátor porovnání).|
|[<>](https://docs.microsoft.com/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |Porovná dva výrazy pro nerovnost (operátor porovnání).|
|[<](https://docs.microsoft.com/sql/t-sql/language-elements/less-than-transact-sql)    |Porovná dva výrazy pro menší než (operátor porovnání).|
|[<=](https://docs.microsoft.com/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |Porovná dva výrazy menší než nebo rovno (operátor porovnání).|
|[>](https://docs.microsoft.com/sql/t-sql/language-elements/greater-than-transact-sql)    |Porovná dva výrazy pro větší než (operátor porovnání). |
|[>=](https://docs.microsoft.com/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |Porovná dva výrazy o větší nebo rovno (operátor porovnání).|
|[+](https://docs.microsoft.com/sql/t-sql/language-elements/add-transact-sql)    |Přidá dvě čísla. Tento operátor sčítání může také do data přidat číslo (ve dnech).|
|[-](https://docs.microsoft.com/sql/t-sql/language-elements/subtract-transact-sql)    |Odečte dvě čísla (operátor aritmetického odčítání). |
|[/](https://docs.microsoft.com/sql/t-sql/language-elements/divide-transact-sql)    |Vydělí jedno číslo jiným (operátor aritmetického dělení).|
|[*](https://docs.microsoft.com/sql/t-sql/language-elements/multiply-transact-sql)    |Vynásobí dva výrazy (aritmetický operátor násobení).|
|[%](https://docs.microsoft.com/sql/t-sql/language-elements/modulo-transact-sql)    |Vrátí zbytek jednoho čísla vydělený jinou.|
|[ANI](https://docs.microsoft.com/sql/t-sql/language-elements/bitwise-and-transact-sql)    |Provede bitovou logickou a operaci mezi dvěma celočíselnými hodnotami.|
|[ANI](https://docs.microsoft.com/sql/t-sql/language-elements/bitwise-or-transact-sql)    |Provede bitovou logickou hodnotu nebo operaci mezi dvěma zadanými celočíselnými hodnotami, které jsou přeloženy na binární výrazy v příkazech jazyka Transact-SQL.|
|[MĚNÍ](https://docs.microsoft.com/sql/t-sql/language-elements/not-transact-sql)    |Negace logického vstupu.|
|[CAST](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)    |Převede výraz jednoho datového typu na jiný.|
|[JEDNOTLIVÝCH](https://docs.microsoft.com/sql/t-sql/language-elements/between-transact-sql)    |Určuje rozsah, který se má testovat.|
|[PRO](https://docs.microsoft.com/sql/t-sql/language-elements/in-transact-sql)    |Určuje, zda zadaná hodnota odpovídá jakékoli hodnotě v poddotazu nebo seznamu.|
|[NULLIF](https://docs.microsoft.com/sql/t-sql/language-elements/nullif-transact-sql)    |Vrací hodnotu null, pokud jsou dva zadané výrazy stejné.|
|[COALESCE](https://docs.microsoft.com/sql/t-sql/language-elements/coalesce-transact-sql)    |Vyhodnotí argumenty v pořadí a vrátí aktuální hodnotu prvního výrazu, který zpočátku není vyhodnocen jako NULL.|

Pokud jsou datové typy nalevo a napravo od operátoru jiné, bude proveden automatický převod podle pravidel, která jsou zde uvedena: [Priorita datového typu (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql).

Jazyk SQL pro zrychlení dotazů podporuje pouze velmi malou podmnožinu datových typů popsaných v tomto článku.  Další informace najdete v části [typy dat](#data-types) tohoto článku.

### <a name="casts"></a>Přetypování

Jazyk SQL pro zrychlení dotazů podporuje operátor přetypování podle pravidel zde: [Převod datového typu (databázový stroj)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine).  

Jazyk SQL pro zrychlení dotazů podporuje pouze malou podmnožinu datových typů popsaných v tomto článku.  Další informace najdete v části [typy dat](#data-types) tohoto článku.

### <a name="string-functions"></a>Funkce řetězců

Jazyk SQL pro zrychlení dotazů podporuje následující standardní řetězcové funkce jazyka SQL:

|Funkce|Popis|
|--|--|
|CHAR_LENGTH    | Vrátí délku ve znacích řetězcového výrazu, pokud je řetězcový výraz znakový datový typ. v opačném případě vrátí délku v bajtech řetězcového výrazu (nejmenší celé číslo, které je menší než počet bitů dělený hodnotou 8). (Tato funkce je stejná jako funkce CHARACTER_LENGTH.)|
|CHARACTER_LENGTH    |Vrátí délku ve znacích řetězcového výrazu, pokud je řetězcový výraz znakový datový typ. v opačném případě vrátí délku v bajtech řetězcového výrazu (nejmenší celé číslo, které je menší než počet bitů dělený hodnotou 8). (Tato funkce je stejná jako funkce CHAR_LENGTH|
|[LOWER](https://docs.microsoft.com/sql/t-sql/functions/lower-transact-sql)    |Vrátí výraz znaků po převedení velkých znakových dat na malá písmena.|
|[UPPER](https://docs.microsoft.com/sql/t-sql/functions/upper-transact-sql)    |Vrátí znakový výraz s malými znakovými daty převedenými na velká písmena.|
|[PODŘETĚZEC](https://docs.microsoft.com/sql/t-sql/functions/substring-transact-sql)    |Vrátí část znakového, binárního, textového nebo obrázkového výrazu v SQL Server.|
|[SKLON](https://docs.microsoft.com/sql/t-sql/functions/trim-transact-sql)    |Odstraní znak znaku mezery (32) nebo jiné zadané znaky od začátku a konce řetězce.|
|NULA    |Popis|
|KONCOVÉ    |Popis|

Tady je několik příkladů:

|Funkce|Příklad|Výsledek|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>Datové funkce

Podporují se tyto standardní funkce data SQL:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

V současné době převádíme všechny [formáty data Standard IS08601](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>DATE_ADD funkce

Jazyk SQL pro zrychlení dotazů podporuje pro funkci rok, měsíc, den, hodinu, minutu a sekundu ``DATE_ADD`` .

Příklady:

' ' DATE_ADD SQL (DatePart, množství, časové razítko) DATE_ADD (Minute, 1, CAST (' 2017-01-02T03:04:05.006 Z ' jako časové RAZÍTKo)
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

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
|pro                |AM nebo PM                             |
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
|X                |Posun v hodinách                      |
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
|[COUNT ( \* )](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Vrátí počet záznamů, které odpovídají výrazu predikátu.|
|[COUNT (výraz)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Vrátí počet záznamů, pro které výraz má hodnotu null.|
|[AVERAGE (výraz)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql)    |Vrátí průměr hodnot výrazu, které nejsou null.|
|[MIN (výraz)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql)    |Vrátí minimální hodnotu výrazu, která není null.|
|[MAX (výraz](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql)    |Vrátí maximální hodnotu výrazu, který není null.|
|[SUM (výraz)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql)    |Vrátí součet všech hodnot, které nejsou null, výrazu.|

### <a name="missing"></a>NENAŠEL

``IS MISSING``Operátor je jediným nestandardním jazykem, který podporuje jazyk SQL pro zrychlení dotazování.  Pokud v případě dat JSON chybí pole v určitém vstupním záznamu, pole výrazu ``IS MISSING`` se vyhodnotí na logickou hodnotu true.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>Deskriptory tabulky

V případě dat CSV je název tabulky vždycky `BlobStorage` .  Například:

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

Pokud jste chtěli přístup pouze k `dimensions` hodnotě objektu JSON, můžete použít odkaz na tento objekt v dotazu. Například:

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

- [Akcelerace dotazů Azure Data Lake Storage](data-lake-storage-query-acceleration.md)
- [Filtrování dat pomocí Azure Data Lake Storage akcelerace dotazů](data-lake-storage-query-acceleration-how-to.md)

