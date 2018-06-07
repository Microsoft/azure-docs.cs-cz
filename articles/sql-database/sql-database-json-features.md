---
title: Funkce Azure SQL databáze JSON | Microsoft Docs
description: Databáze SQL Azure umožňuje analýzu, dotazů a formátování dat v notaci JavaScript Object Notation (JSON).
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.date: 04/01/2018
ms.author: jovanpop
ms.topic: conceptual
ms.openlocfilehash: 687986d108a15973c19ff06f5ca98a468439f30d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648995"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Začínáme s funkcí JSON v Azure SQL Database
Azure SQL Database umožňuje analyzovat a zadávat dotazy na data v JavaScript Object Notation [(JSON)](http://www.json.org/) formátu a exportovat relačních dat jako JSON text.

JSON je Oblíbené data formát používaný pro výměnu dat v moderní webové a mobilní aplikace. JSON je také používá k ukládání částečně strukturovaných dat v souborech protokolů nebo ve databáze NoSQL, jako [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). Mnoho webových služeb REST vráceny výsledky formátovat jako JSON text nebo přijímat data formátu JSON. Většina Azure služeb, jako [Azure Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/), a [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) mít koncové body REST, které vrátí nebo využívat JSON.

Databáze SQL Azure vám umožní snadno pracovat s daty JSON a integrovat moderní služeb vaší databáze.

## <a name="overview"></a>Přehled
Azure SQL Database poskytuje následující funkce pro práci s daty JSON:

![Funkce JSON](./media/sql-database-json-features/image_1.png)

Pokud máte JSON text, můžete extrahovat data z JSON nebo ověřte, že JSON je ve správném formátu pomocí integrované funkce [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), a [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). [Příkazu JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) funkce vám umožní aktualizovat hodnotu uvnitř JSON text. Pro další pokročilé dotazy a analýzy, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) funkce můžete převést pole objektů JSON do sady řádků. Jakýkoli dotaz SQL, mohou být provedeny u vrácené výsledné sady. Nakonec je [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) klauzuli, která vám umožní zformátovat data uložená v tabulkách relační jako JSON text.

## <a name="formatting-relational-data-in-json-format"></a>Formátování relační data ve formátu JSON
Pokud máte webová služba přijímá data z databáze vrstvy a poskytuje odpověď ve formátu JSON formátu, nebo rozhraní JavaScript na straně klienta nebo knihovny, které přijímá data formátu JSON, je databáze obsahu formátovat jako JSON přímo v příkazu jazyka SQL. Už máte k zápisu kódu aplikace, který zformátuje výsledky z Azure SQL Database jako JSON, nebo zahrnout některé knihovny serializace JSON a převést výsledky tabulkový dotaz a pak serializovat objekty do formátu JSON. Místo toho můžete v klauzuli FOR JSON výsledky dotazu SQL formátovat jako JSON v Azure SQL Database a jeho použití přímo v aplikaci.

V následujícím příkladu jsou pomocí klauzule FOR JSON řádky z tabulky Sales.Customer formátu JSON:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

V klauzuli FOR JSON PATH naformátuje výsledky dotazu jako JSON text. Názvy sloupců se používají jako klíče, hodnoty buněk se sice generuje jako hodnoty JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Sada výsledků je naformátován jako pole JSON, kde každý řádek je formátován jako samostatný objekt JSON.

CESTA označuje, že výstupní formát vaší výsledku JSON můžete přizpůsobit pomocí zápisu s tečkou v aliasy sloupců. Následující dotaz změní název "JménoZákazníka" klíč ve formátu JSON výstupu a vloží číslo telefonu a faxu v objektu dílčí "Kontakt":

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Výstup tohoto dotazu vypadá takto:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

V tomto příkladu jsme vrátil jeden objekt JSON místo pole tak, že zadáte [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) možnost. Tuto možnost můžete použít, pokud víte, že jsou vrácení jednoho objektu. v důsledku dotazu.

Hlavní hodnota klauzule FOR JSON je, že umožňuje vrátit komplexní hierarchické data v databázi formátován jako vnořených objektů JSON nebo pole. Následující příklad ukazuje, jak zahrnout objednávky, které patří do zákazníka jako vnořená pole objednávek:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Místo abyste odesílali oddělené dotazy k získání dat zákazníků a pak se načíst seznam související objednávky, můžete získat všechna potřebná data pomocí jednoho dotazu, jak je znázorněno v následující ukázkový výstup:

```
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
Pokud nemáte výhradně strukturovaných dat, pokud máte komplexní dílčí objekty, pole nebo hierarchické data nebo datové struktury v průběhu času vyvíjejí, formát JSON můžete k reprezentaci všech komplexní datová struktura.

JSON je textový formát, který lze použít jako libovolný jiný typ řetězce ve službě Azure SQL Database. Můžete odeslat nebo jako standardní NVARCHAR úložiště dat JSON:

```
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

Data JSON použité v tomto příkladu je reprezentována pomocí typu NVARCHAR(MAX). JSON můžete vložit do této tabulky nebo zadaný jako argument uloženou proceduru pomocí standardní syntaxe Transact-SQL, jak je znázorněno v následujícím příkladu:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Jazyk klienta nebo knihovny, která funguje s daty řetězec v databázi SQL Azure bude také pracovat s daty JSON. JSON může být uložen v žádné tabulky, která podporuje typu NVARCHAR, jako je například paměťově optimalizované tabulky nebo tabulky se systémovou správou verzí. JSON nezavádí žádné omezení v kódu na straně klienta nebo v databázové vrstvě.

## <a name="querying-json-data"></a>Dotazování na JSON data
Pokud máte data formátu JSON, ukládat do tabulek Azure SQL, funkce JSON umožňují používat tato data v jakékoli dotazu SQL.

Funkce JSON, které jsou k dispozici v Azure SQL database umožňují považovat datového naformátován jako formát JSON jako jiný typ dat SQL. Můžete snadno rozbalte hodnot z textu JSON a použít JSON data v jakýkoli dotaz:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funkce JSON_VALUE extrahuje hodnotu z textu JSON uložené ve sloupci Data. Tato funkce používá cestu jazyka JavaScript tak, aby odkazovaly na hodnotu v textu JSON k extrakci. K extrahované hodnotě lze v libovolnou část dotazu SQL.

Funkce JSON_QUERY je podobná JSON_VALUE. Na rozdíl od JSON_VALUE tato funkce extrahuje komplexní dílčí objekt, jako je například pole nebo objekty, které jsou umístěny v textu JSON.

Funkce příkazu JSON_MODIFY umožňuje vám zadat cestu k hodnotě text JSON, který by měly být aktualizovány a také novou hodnotu, která přepíší starý. Tímto způsobem můžete snadno aktualizovat v textu JSON bez reparsing celá struktura.

Vzhledem k tomu, že JSON je uložený ve formátu standardního textu, neexistují žádné záruky, že hodnotami uloženými v textu sloupce jsou ve správném formátu. Můžete ověřit, že text uložený ve formátu JSON sloupce je ve správném formátu pomocí standardní omezení check Azure SQL Database a ISJSON funkce:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Pokud je ve správném formátu vstupního textu JSON, ISJSON funkce vrátí hodnotu 1. Na každém vložení nebo aktualizace sloupce JSON bude toto omezení ověřte novou textovou hodnotou není nesprávný formát JSON.

## <a name="transforming-json-into-tabular-format"></a>Transformace v tabulkovém formátu JSON
Databáze SQL Azure také vám umožňuje transformovat kolekce JSON v tabulkovém formátu a zatížení nebo dotaz, data JSON.

OPENJSON je funkce tabulky hodnota, která analyzuje JSON text, vyhledá pole objektů JSON, iteruje v rámci prvků pole a vrátí jeden řádek ve výsledném výstupu pro jednotlivé elementy pole.

![Tabulkové JSON](./media/sql-database-json-features/image_2.png)

V předchozím příkladu jsme můžete určit, kde najít pole JSON, který musí být otevřen (v $. Cesta objednávky), které sloupce má být vrácen jako výsledek a kde najít JSON hodnoty, které bude vrácen jako buněk.

Pole JSON v jsme můžete převést @orders proměnné do sady řádků, analyzovat tuto sadu výsledků nebo vložení řádků do tabulky Standardní:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
Kolekce objednávek naformátovaný jako pole JSON a zadat jako parametr uložené procedury je možné analyzovat a vložit do tabulky objednávky.

## <a name="next-steps"></a>Další postup
Informace o tom, jak integrovat JSON do vaší aplikace, podívejte se na tyto prostředky:

* [Blog na TechNetu](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
* [Dokumentace MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
* [Channel 9 video](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Další informace o různé scénáře pro integraci JSON do své aplikace naleznete v tématu ukázky v tomto [Channel 9 video](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) nebo najít scénáře, který odpovídá váš případ použití v [JSON příspěvky](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

