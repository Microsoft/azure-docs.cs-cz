---
title: Date_Bucket (Transact-SQL) – Azure SQL Edge
description: Další informace o použití Date_Bucket ve službě Azure SQL Edge
keywords: Date_Bucket, Edge SQL
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 896caae2dfd79c4678ffb34c531fb56835e9bd66
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90886842"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (Transact-SQL)

Tato funkce vrací hodnotu DateTime odpovídající začátku každého kontejneru DateTime, z časového razítka definovaného `origin` parametrem nebo výchozí hodnotou, `1900-01-01 00:00:00.000` Pokud není zadán parametr Origin. 

Přehled všech datových typů a dat v jazyce Transact-SQL najdete v tématu [datové typy a funkce data a času &#40;v jazyce Transact-sql&#41;](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/) .

[Konvence syntaxe Transact-SQL](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

## <a name="syntax"></a>Syntaxe

```sql
DATE_BUCKET (datePart, number, date, origin)
```

## <a name="arguments"></a>Argumenty

*datePart*

Část *data* , která se používá s parametrem ' Number '. Například Rok, měsíc, minuta, druhý atd.

> [!NOTE]
> `DATE_BUCKET` nepřijímá uživatelsky definované proměnné ekvivalenty pro argumenty *datepPart* .
  
|*datePart*|Zkratky|  
|---|---|
|**dnu**|**DD**, **d**|  
|**týden**|**týden**, **WW**|  
|**hodiny**|**hh**|  
|**minute**|**mi**, **n**|  
|**second**|**SS**, **s**|  
|**komponentu**|**Arial**|  

*číslo*

Celé číslo, které určuje šířku kontejneru v kombinaci s argumentem *datePart* . To představuje šířku kontejnerů DataParts od počátečního času. **`This argument cannot be a negative integer value`**. 

*Datum*

Výraz, který může být přeložen na jednu z následujících hodnot:

+ **Datum**
+ **hodnotu**
+ **DateTimeOffset**
+ **datetime2**
+ **smalldatetime**
+ **interval**

Pro *Datum* `DATE_BUCKET` bude akceptovat výraz sloupce, výraz nebo uživatelsky definovaná proměnná, pokud se přeloží na některý z výše uvedených typů dat.

**Zdroji** 

Volitelný výraz, který lze přeložit na jednu z následujících hodnot:

+ **Datum**
+ **hodnotu**
+ **DateTimeOffset**
+ **datetime2**
+ **smalldatetime**
+ **interval**

Datový typ `Origin` by měl odpovídat datovému typu `Date` parametru. 

`DATE_BUCKET` používá výchozí hodnotu data původu, `1900-01-01 00:00:00.000` tj. 12:00 dop. ledna 1 1900, pokud pro funkci není zadána žádná hodnota Origin.

## <a name="return-type"></a>Návratový typ

Datový typ vrácené hodnoty pro tuto metodu je dynamický. Návratový typ závisí na argumentu dodaném pro `date` . Pokud je zadán platný datový typ Input pro `date` , `DATE_BUCKET` vrátí stejný datový typ. `DATE_BUCKET` vyvolá chybu, je-li pro parametr zadán řetězcový literál `date` .

## <a name="return-values"></a>Návratové hodnoty

### <a name="understanding-the-output-from-date_bucket"></a>Princip výstupu `DATE_BUCKET`

`Date_Bucket` Vrátí nejnovější hodnotu data nebo času odpovídající parametru datePart a Number. Například ve výrazech níže `Date_Bucket` vrátí výstupní hodnotu `2020-04-13 00:00:00.0000000` , protože výstup se vypočítá na základě jednoho týdenního intervalu z výchozího počátečního času `1900-01-01 00:00:00.000` . Hodnota `2020-04-13 00:00:00.0000000` je 6276 týdnů od počáteční hodnoty `1900-01-01 00:00:00.000` . 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

Pro všechny níže uvedené výrazy se vrátí stejná výstupní hodnota `2020-04-13 00:00:00.0000000` . Důvodem je to `2020-04-13 00:00:00.0000000` , že je 6276 týdnů od data původu a 6276 je dělitelná o 2, 3, 4 a 6.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

Výstup níže uvedeného výrazu je `2020-04-06 00:00:00.0000000` , což je 6275 týdnů z výchozího času původu `1900-01-01 00:00:00.000` .

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

Níže uvedený výstup výrazu je `2020-06-09 00:00:00.0000000` , což je 75 týdnů od zadaného počátečního času `2019-01-01 00:00:00` .

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(wk, 5, @date, @origin)
```

## <a name="datepart-argument"></a>Argument DatePart

**DAYOFYEAR**, **Day**a **Weekday** vrací stejnou hodnotu. Každé *DatePart* a jeho zkratky vrací stejnou hodnotu.
  
## <a name="number-argument"></a>Argument Number

Argument *Number* nemůže být větší než rozsah kladných hodnot typu **int** . V následujících příkazech argument pro *číslo* překračuje rozsah **int** o 1. Následující příkaz vrátí následující chybovou zprávu: "`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

Pokud je do funkce předána záporná hodnota pro číslo `Date_Bucket` , vrátí se následující chyba. 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>Argument data  

`DATE_BUCKET` Vrátí základní hodnotu odpovídající datovému typu `date` argumentu. V následujícím příkladu je vrácena výstupní hodnota s datovým typem datetime2. 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="origin-argument"></a>Argument Origin  

Datový typ `origin` argumentů a v nástroji `date` musí být stejný. Pokud se používají různé datové typy, vygeneruje se chyba.

## <a name="remarks"></a>Poznámky

Použijte `DATE_BUCKET` v následujících klauzulích:

+ GROUP BY
+ HAVING
+ ORDER BY
+ VYBRALI \<list>
+ WHERE

## <a name="examples"></a>Příklady

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>A. Výpočet Date_Bucket se šířkou intervalu 1 od počátku

Každý z těchto příkazů zvyšuje *date_bucket* o šířce intervalu 1 od počátku:

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

Zde je sada výsledků.

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. Použití výrazů jako argumentů pro parametry Number a Date

Tyto příklady používají jako argumenty pro parametry *Number* a *Date* různé typy výrazů. Tyto příklady jsou sestaveny pomocí databáze ' AdventureWorksDW2017 '.
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>Určení uživatelem definovaných proměnných jako číslo a datum  

Tento příklad určuje uživatelsky definované proměnné jako argumenty pro *číslo* a *Datum*:
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

Zde je sada výsledků.

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>Zadání sloupce jako data

V následujícím příkladu počítáme součet OrderQuantity a součtu UnitPrice seskupených v rámci intervalů týdnového data.
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

Zde je sada výsledků.
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>Určení skalární systémové funkce jako data

Tento příklad určuje `SYSDATETIME` pro *Datum*. Přesná hodnota vrácená v závislosti na den a čas provedení příkazu:
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

Zde je sada výsledků.

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>Určení skalárních poddotazů a skalárních funkcí jako čísla a data

V tomto příkladu se `MAX(OrderDate)` jako argumenty pro *číslo* a *Datum*používají skalární poddotazy. `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)` slouží jako umělý argument pro parametr number, který ukazuje, jak vybrat *číselný* argument ze seznamu hodnot.
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>Zadání numerických výrazů a skalárních systémových funkcí jako čísla a datum

V tomto příkladu se používá číselný výraz ((10/2)) a skalární systémové funkce (SYSDATETIME) jako argumenty pro číslo a datum.
  
```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>Určení funkce agregovaného okna jako čísla

V tomto příkladu se používá agregovaná funkce okna jako argument pro *Number*.
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 
### <a name="c-using-a-non-default-origin-value"></a>C. Použití jiné než výchozí hodnoty počátku

Tento příklad používá jinou než výchozí hodnotu orgin k vygenerování datových intervalů. 

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(hh, 2, @date, @origin)
```

## <a name="see-also"></a>Viz také

[Přetypování a převod &#40;Transact-SQL&#41;](/sql/t-sql/functions/cast-and-convert-transact-sql/)
