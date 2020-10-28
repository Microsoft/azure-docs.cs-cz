---
title: Práce s daty JSON
description: Azure SQL Database a Azure SQL Managed instance vám umožní analyzovat, dotazovat a formátovat data v zápisu JavaScript Object Notation (JSON).
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 4258c60335a13a6c0f27588aac1c6cdb185fcf6a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782795"
---
# <a name="getting-started-with-json-features-in-azure-sql-database-and-azure-sql-managed-instance"></a>Začínáme s funkcemi JSON v Azure SQL Database a Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database a Azure SQL Managed instance umožňují analyzovat a dotazovat data reprezentovaná ve formátu JavaScript Object Notation [(JSON)](https://www.json.org/) a exportovat relační data jako text JSON. K dispozici jsou následující scénáře JSON:

- [Formátování relačních dat ve formátu JSON](#formatting-relational-data-in-json-format) pomocí `FOR JSON` klauzule.
- [Práce s daty JSON](#working-with-json-data)
- [Dotazování na data JSON](#querying-json-data) pomocí skalárních funkcí JSON.
- [Transformuje JSON na tabulkový formát](#transforming-json-into-tabular-format) pomocí `OPENJSON` funkce.

## <a name="formatting-relational-data-in-json-format"></a>Formátování relačních dat ve formátu JSON

Máte-li webovou službu, která přebírá data z databázové vrstvy a poskytuje odpověď ve formátu JSON, nebo knihovny JavaScript na straně klienta, které přijímají data formátovaná jako JSON, můžete obsah databáze naformátovat jako JSON přímo v dotazu SQL. Už nemusíte psát kód aplikace, který formátuje výsledky z Azure SQL Database nebo Azure SQL Managed instance jako JSON, nebo může obsahovat nějaké knihovny serializace JSON pro převod výsledků tabulkových dotazů a pak serializovat objekty ve formátu JSON. Místo toho můžete použít klauzuli FOR JSON pro formátování výsledků dotazu SQL jako JSON a použít ho přímo v aplikaci.

V následujícím příkladu jsou řádky z tabulky Sales. Customers formátovány jako JSON pomocí klauzule FOR JSON:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Klauzule FOR JSON PATH formátuje výsledky dotazu jako text JSON. Názvy sloupců se používají jako klíče, zatímco hodnoty buněk jsou vygenerované jako hodnoty JSON:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Sada výsledků je formátována jako pole JSON, kde je každý řádek formátován jako samostatný objekt JSON.

CESTA indikuje, že můžete přizpůsobit výstupní formát výsledku JSON pomocí zápisu tečky v aliasech sloupců. Následující dotaz změní název klíče "Customer" ve formátu JSON výstupu a vloží čísla telefonů a faxů do podobjektu "Contact":

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Výstup tohoto dotazu vypadá takto:

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

V tomto příkladu jsme vrátili jeden objekt JSON namísto pole zadáním možnosti [WITHOUT_ARRAY_WRAPPER](/sql/relational-databases/json/remove-square-brackets-from-json-without-array-wrapper-option) . Tuto možnost můžete použít, pokud víte, že vracíte jeden objekt jako výsledek dotazu.

Hlavní hodnotou klauzule FOR JSON je, že umožňuje vracet složitá hierarchická data z databáze formátovaná jako vnořené objekty JSON nebo pole. Následující příklad ukazuje, jak zahrnout řádky z `Orders` tabulky, které patří do `Customer` vnořeného pole jako `Orders` :

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

Místo posílání samostatných dotazů pro získání zákaznických dat a následné načtení seznamu souvisejících objednávek můžete získat veškerá potřebná data pomocí jediného dotazu, jak je znázorněno v následujícím ukázkovém výstupu:

```json
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
  ]
}
```

## <a name="working-with-json-data"></a>Práce s daty JSON

Pokud nemáte výhradně strukturovaná data, pokud máte komplexní dílčí objekty, pole nebo hierarchická data nebo pokud vaše datové struktury vyvíjíte v průběhu času, může vám formát JSON poznamenat, že je možné znázornit jakoukoli složitou datovou strukturu.

JSON je textový formát, který se dá použít jako libovolný jiný typ řetězce v Azure SQL Database a Azure SQL Managed instance. Data JSON můžete posílat nebo ukládat jako standardní NVARCHAR:

```sql
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Data JSON použitá v tomto příkladu jsou reprezentována pomocí typu NVARCHAR (MAX). JSON lze vložit do této tabulky nebo zadat jako argument uložené procedury pomocí standardní syntaxe jazyka Transact-SQL, jak je znázorněno v následujícím příkladu:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Všechny jazyky nebo knihovny na straně klienta, které pracují s řetězcovými daty v Azure SQL Database a Azure SQL Managed instance, budou také fungovat s daty JSON. JSON může být uložený v libovolné tabulce, která podporuje typ NVARCHAR, jako je paměťově optimalizovaná tabulka nebo tabulka se systémovou správou verzí. JSON nezavádí žádné omezení buď v kódu na straně klienta, nebo ve vrstvě databáze.

## <a name="querying-json-data"></a>Dotazování na data JSON

Pokud máte data formátovaná jako JSON uložená v tabulkách Azure SQL, funkce JSON je umožňují používat v jakémkoli dotazu SQL.

Funkce JSON, které jsou k dispozici v Azure SQL Database a Azure SQL Managed instance, umožňují zpracovávat data formátovaná jako JSON jako jakýkoli jiný datový typ SQL. Můžete snadno extrahovat hodnoty z textu JSON a použít data JSON v jakémkoli dotazu:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funkce JSON_VALUE extrahuje hodnotu z textu JSON uloženého ve sloupci data. Tato funkce používá cestu podobnou JavaScriptu k odkazování na hodnotu v textu JSON k extrakci. Extrahovaná hodnota se dá použít v jakékoli části dotazu SQL.

Funkce JSON_QUERY je podobná JSON_VALUE. Na rozdíl od JSON_VALUE Tato funkce extrahuje složitý dílčí objekt, například pole nebo objekty, které jsou umístěny v textu JSON.

Funkce JSON_MODIFY umožňuje zadat cestu k hodnotě v textu JSON, která se má aktualizovat, a také novou hodnotu, která přepíše starou. Tímto způsobem můžete snadno aktualizovat text JSON bez nutnosti přeanalyzovat celou strukturu.

Vzhledem k tomu, že JSON je uložený ve standardním textu, nezaručujeme, že hodnoty uložené v textových sloupcích budou správně naformátované. Můžete ověřit, že text uložený ve sloupci JSON je správně formátovaný pomocí standardních Azure SQL Database omezení CHECK a funkce-JSON:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Pokud je vstupní text ve správném formátu JSON, vrátí funkce-JSON hodnotu 1. Při každém vložení nebo aktualizaci sloupce JSON toto omezení ověří, že nová textová hodnota není poškozená JSON.

## <a name="transforming-json-into-tabular-format"></a>Transformace JSON do tabulkového formátu

Azure SQL Database a Azure SQL Managed instance vám také umožní transformovat kolekce JSON do tabulkového formátu a načíst data nebo dotazovat data JSON.

OPENJSON je funkce hodnot tabulky, která analyzuje text JSON, vyhledává pole objektů JSON, Iterovat prvky pole a vrátí jeden řádek výsledku výstupu pro každý prvek pole.

![Tabulka JSON](./media/json-features/image_2.png)

V příkladu výše můžeme určit, kde se má najít pole JSON, které by se mělo otevřít (v $. Cesta objednávek), které sloupce by měly být vráceny jako výsledek a kde najít hodnoty JSON, které budou vráceny jako buňky.

Pole JSON v proměnné můžeme transformovat @orders do sady řádků, analyzovat tuto sadu výsledků nebo vkládat řádky do standardní tabulky:

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

Kolekce objednávek naformátovaných jako pole JSON a poskytované jako parametr pro uloženou proceduru lze analyzovat a vložit do tabulky Orders.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak do své aplikace integrovat JSON, najdete v těchto zdrojích:

Další informace o různých scénářích pro integraci formátu JSON do vaší aplikace najdete v ukázkách v tomto [videu kanálu 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) nebo v článku Najděte scénář, který odpovídá případu použití v [příspěvcích blogu JSON](/archive/blogs/sqlserverstorageengine/json-in-sql-server-use-cases).