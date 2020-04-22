---
title: Práce s daty JSON
description: Azure SQL Database umožňuje analyzovat, dotazovat a formátovat data v zápisu javascriptového objektu (JSON).
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 992c981d49e7c6fbf8b6156570f6554a05caab5d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687761"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Začínáme s funkcemi JSON v Azure SQL Database
Azure SQL Database umožňuje analyzovat data a dotazy reprezentovaná ve formátu [JSON (JavaScript](https://www.json.org/) Object Notation) a exportovat relační data jako text JSON. V Azure SQL Database jsou k dispozici následující scénáře JSON:
- [Formátování relačních dat ve formátu JSON](#formatting-relational-data-in-json-format) pomocí `FOR JSON` klauzule.
- [Práce s daty JSON](#working-with-json-data)
- [Dotazování na data JSON](#querying-json-data) pomocí skalárních funkcí JSON.
- [Transformace JSON do tabulkového formátu](#transforming-json-into-tabular-format) pomocí `OPENJSON` funkce.

## <a name="formatting-relational-data-in-json-format"></a>Formátování relačních dat ve formátu JSON
Pokud máte webovou službu, která přebírá data z databázové vrstvy a poskytuje odpověď ve formátu JSON nebo javascriptové architektury nebo knihovny na straně klienta, které přijímají data formátovaná jako JSON, můžete formátovat obsah databáze jako JSON přímo v dotazu SQL. Už nemusíte psát kód aplikace, který formátuje výsledky z Azure SQL Database jako JSON, ani zahrnout některé knihovny serializace JSON pro převod výsledků tabulkového dotazu a serializaci objektů do formátu JSON. Místo toho můžete použít for JSON klauzule k formátování výsledků dotazu SQL jako JSON v Azure SQL Database a použít přímo ve vaší aplikaci.

V následujícím příkladu jsou řádky z tabulky Sales.Customer formátovány jako JSON pomocí klauzule FOR JSON:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

For JSON PATH klauzule formátuje výsledky dotazu jako text JSON. Názvy sloupců se používají jako klíče, zatímco hodnoty buněk jsou generovány jako hodnoty JSON:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Sada výsledků je formátována jako pole JSON, kde je každý řádek formátován jako samostatný objekt JSON.

CESTA označuje, že můžete přizpůsobit výstupní formát výsledku JSON pomocí dot notace v aliasy sloupců. Následující dotaz změní název klíče "CustomerName" ve výstupním formátu JSON a vloží telefonní a faxová čísla do podobjektu "Kontakt":

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

V tomto příkladu jsme vrátili jeden objekt JSON namísto pole zadáním [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) možnost. Tuto možnost můžete použít, pokud víte, že vracíte jeden objekt v důsledku dotazu.

Hlavní hodnota for JSON klauzule je, že umožňuje vrátit komplexní hierarchická data z databáze formátované jako vnořené JSON objekty nebo pole. Následující příklad ukazuje, jak zahrnout `Orders` řádky z tabulky, které patří do `Customer` vnořeného pole `Orders`:

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

Namísto odesílání samostatných dotazů k získání dat zákazníka a následnému načtení seznamu souvisejících objednávek můžete získat všechna potřebná data s jedním dotazem, jak je znázorněno v následujícím ukázkovém výstupu:

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
Pokud nemáte přísně strukturovaná data, pokud máte složité dílčí objekty, pole nebo hierarchická data nebo pokud se datové struktury v průběhu času vyvíjejí, může vám formát JSON pomoci reprezentovat libovolnou složitou datovou strukturu.

JSON je textový formát, který lze použít jako jakýkoli jiný typ řetězce v Azure SQL Database. JSON můžete odesílat nebo ukládat jako standardní NVARCHAR:

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

Data JSON použitá v tomto příkladu jsou reprezentována pomocí typu NVARCHAR(MAX). JSON lze vložit do této tabulky nebo poskytnout jako argument uložené procedury pomocí standardní syntaxe Transact-SQL, jak je znázorněno v následujícím příkladu:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Jakýkoli jazyk nebo knihovna na straně klienta, která pracuje s řetězcovými daty v Azure SQL Database, bude také pracovat s daty JSON. JSON lze uložit v libovolné tabulce, která podporuje typ NVARCHAR, jako je například tabulka optimalizovaná pro paměť nebo tabulka s verzí systému. JSON nezavádí žádné omezení v kódu na straně klienta nebo v databázové vrstvě.

## <a name="querying-json-data"></a>Dotazování na data JSON
Pokud máte data formátovaná jako JSON uložená v tabulkách Azure SQL, funkce JSON umožňují používat tato data v libovolném dotazu SQL.

Funkce JSON, které jsou dostupné v databázi Azure SQL, umožňují považovat data formátovaná jako JSON za jakýkoli jiný datový typ SQL. Můžete snadno extrahovat hodnoty z textu JSON a použít data JSON v libovolném dotazu:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funkce JSON_VALUE extrahuje hodnotu z textu JSON uloženého ve sloupci Data. Tato funkce používá cestu jako JavaScript k odkazování na hodnotu v textu JSON k extrahování. Extrahovaná hodnota lze použít v libovolné části dotazu SQL.

Funkce JSON_QUERY je podobná funkci JSON_VALUE. Na rozdíl od JSON_VALUE tato funkce extrahuje složitý podobjekt, jako jsou pole nebo objekty, které jsou umístěny v textu JSON.

Funkce JSON_MODIFY umožňuje určit cestu hodnoty v textu JSON, která by měla být aktualizována, stejně jako novou hodnotu, která přepíše starou. Tímto způsobem můžete snadno aktualizovat text JSON bez úpravy celé struktury.

Vzhledem k tomu, že JSON je uložen ve standardním textu, neexistují žádné záruky, že hodnoty uložené v textových sloupcích jsou správně formátovány. Můžete ověřit, že text uložený ve sloupci JSON je správně formátován pomocí standardních omezení kontroly azure SQL database a funkce ISJSON:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Pokud je vstupní text správně formátován JSON, vrátí funkce ISJSON hodnotu 1. Při každém vložení nebo aktualizaci sloupce JSON toto omezení ověří, že nová textová hodnota není poškozená JSON.

## <a name="transforming-json-into-tabular-format"></a>Transformace JSON do tabulkového formátu
Azure SQL Database také umožňuje transformovat kolekce JSON do tabulkového formátu a načíst nebo dotaz ovat data JSON.

OPENJSON je funkce hodnoty tabulky, která analyzuje text JSON, vyhledá pole objektů JSON, iterace přes prvky pole a vrátí jeden řádek ve výstupním výsledku pro každý prvek pole.

![JSON tabulkové](./media/sql-database-json-features/image_2.png)

Ve výše uvedeném příkladu můžeme určit, kde umístit pole JSON, které by mělo být otevřeno (v $. Objednávky cesta), jaké sloupce by měly být vráceny jako výsledek a kde najít hodnoty JSON, které budou vráceny jako buňky.

Můžeme transformovat pole JSON @orders v proměnné do sady řádků, analyzovat tuto sadu výsledků nebo vložit řádky do standardní tabulky:

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

Kolekce objednávek formátován jako pole JSON a za předpokladu, jako parametr uložené procedury lze analyzovat a vložit do objednávky tabulky.

## <a name="next-steps"></a>Další kroky
Chcete-li se dozvědět, jak integrovat JSON do aplikace, podívejte se na tyto prostředky:

* [Blog technetu](https://blogs.technet.microsoft.com/dataplatforminsider/20../../json-in-sql-server-2016-part-1-of-4/)
* [Dokumentace msdn](https://msdn.microsoft.com/library/dn921897.aspx)
* [Kanál 9 video](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Chcete-li se dozvědět o různých scénářích integrace JSON do vaší aplikace, podívejte se na ukázky v tomto [kanálu 9 video](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) nebo najít scénář, který odpovídá vašemu případu použití v [JSON blogu](https://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

