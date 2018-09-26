---
title: Funkce Azure SQL databáze JSON | Dokumentace Microsoftu
description: Azure SQL Database vám umožní parsovat, dotaz a formátování dat v zápisu JavaScript Object Notation (JSON).
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: dfbefd2ff4c2e99268c1e7e84154e93d58f542bb
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163626"
---
# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Začínáme s funkcemi JSON ve službě Azure SQL Database
Azure SQL Database umožňuje analyzovat a dotazování dat v jazyce JavaScript Object Notation [(JSON)](http://www.json.org/) formátování a exportovat relačních dat jako JSON text.

JSON je oblíbenými datovými formát používaný pro výměnu dat v moderních webových a mobilních aplikací. JSON se také používá pro ukládání částečně strukturovaných dat v souborech protokolů nebo databáze NoSQL jako [služby Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/). Mnoho webových služeb REST vráceny výsledky formátovat jako JSON text nebo přijímat data naformátovaná jako JSON. Většina Azure services, jako [Azure Search](https://azure.microsoft.com/services/search/), [služby Azure Storage](https://azure.microsoft.com/services/storage/), a [služby Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) mít koncové body REST, které vrátí nebo využívají JSON.

Azure SQL Database vám umožní snadno pracovat s daty JSON a integrovat moderních služeb vaší databáze.

## <a name="overview"></a>Přehled
Azure SQL Database poskytuje následující funkce pro práci s daty JSON:

![Funkce JSON](./media/sql-database-json-features/image_1.png)

Pokud máte JSON text, můžete extrahovat data z formátu JSON nebo ověřte, že je správně ve formátu JSON pomocí integrovaných funkcí [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), a [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). [Příkazu json_modify je](https://msdn.microsoft.com/library/dn921892.aspx) funkce vám umožní aktualizovat hodnotu uvnitř JSON text. Pokročilejší dotazy a analýzy, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) funkce můžete transformovat pole objektů JSON do sady řádků. Jakýkoli dotaz SQL je možné provést v sadě vrácených výsledků. Nakonec je [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) klauzuli, která vám umožní formátování data uložená v relačních tabulkách jako JSON text.

## <a name="formatting-relational-data-in-json-format"></a>Formátování relační data ve formátu JSON
Pokud máte webovou službu, formátovat přijímá data z databáze vrstvu a poskytuje odpověď ve formátu JSON nebo architektury JavaScriptu na straně klienta nebo knihovny, které přijímají data naformátovaná jako dokumenty JSON, můžete naformátovat obsahu databáze jako dokumenty JSON přímo v příkazu jazyka SQL. Už máte k zápisu kódu aplikace, která formátuje výsledky ze služby Azure SQL Database jako dokumenty JSON, nebo do některé knihovně serializace JSON pro převod tabulky výsledků a pak serializaci objektů do formátu JSON. Místo toho můžete použít v klauzuli FOR JSON formátování výsledků dotazu SQL jako dokumenty JSON ve službě Azure SQL Database a použití přímo v aplikaci.

V následujícím příkladu jsou řádky z tabulky Sales.Customer formátovány jako dokumenty JSON pomocí klauzule FOR JSON:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Klauzule FOR JSON PATH zformátuje výsledky dotazu jako JSON text. Názvy sloupců se používají jako klíče, zatímco hodnoty buňky jsou generovány jako hodnoty JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Sada výsledků je formátován jako pole JSON, kde každý řádek je formátován jako samostatný objekt JSON.

CESTA označuje, že můžete přizpůsobit formát výstupu výsledku JSON pomocí zápisu s tečkou v aliasy sloupců. Následující dotaz změní název "Zákazníka" klíč ve výstupním formátu JSON a vloží číslo telefonu a faxu v podobjektu "Kontakt":

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Výstup tohoto dotazu by měl vypadat takto:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

V tomto příkladu jsme vrátí jeden objekt JSON místo pole tak, že zadáte [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) možnost. Tuto možnost můžete použít, pokud víte, že se vrátí jeden objekt výsledku dotazu.

Hlavní hodnota v klauzuli FOR JSON je, že umožňuje vrátit komplexní hierarchická data z databáze naformátované jako vnořené objekty JSON nebo pole. Následující příklad ukazuje, jak zahrnout objednávky, které patří k zákazníkovi jako vnořeného pole objednávek:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Místo abyste odesílali samostatné dotazy k získání dat zákazníků a načíst seznam souvisejících objednávek, můžete pak získat všechna potřebná data pomocí jediného dotazu a jak je znázorněno v následující ukázkový výstup:

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
Pokud nemáte výhradně strukturovaná data, pokud už máte složité podřízených objektů, pole nebo hierarchických dat nebo datových struktur v průběhu času vyvíjejí, formát JSON vám mohou pomoci při představují komplexní datová struktura.

JSON je textový formát, který lze použít jako jakýkoli jiný typ řetězce ve službě Azure SQL Database. Můžete odeslat nebo ukládat JSON data jako standardní NVARCHAR:

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

Data JSON, který je použitý v tomto příkladu je reprezentována pomocí typu NVARCHAR(MAX). JSON můžete vložit do této tabulky nebo zadaný jako argument uložené procedury pomocí standardní syntaxe jazyka Transact-SQL, jak je znázorněno v následujícím příkladu:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Libovolný jazyk na straně klienta nebo knihovnu, která funguje s řetězcovými daty ve službě Azure SQL Database budou fungovat i pro JSON data. JSON mohou být uloženy v libovolné tabulce, která podporuje typ NVARCHAR, jako je například paměťově optimalizované tabulky nebo -systémovou tabulku. JSON nezavádí žádné omezení v kódu na straně klienta nebo v databázové vrstvě.

## <a name="querying-json-data"></a>Dotazování dat JSON
Pokud máte data formátovaná jako dokumenty JSON uložené v tabulkách Azure SQL, JSON funkce umožňují tato data použít v jakékoli jazyka SQL.

Funkce JSON, které jsou k dispozici v Azure SQL database umožňuje zpracovávat data formátovaná jako JSON jako jakékoli jiné datový typ SQL. Můžete snadno extrakci hodnot z textu JSON a používat JSON data každého dotazu:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funkce JSON_VALUE extrahuje hodnotu z textu JSON uložené v sloupci Data. Tato funkce využívá jazyka JavaScript cestu pro odkaz na hodnotu v textu JSON se extrahovat. Extrahované hodnoty lze použít v jakékoli části dotazu SQL.

Funkce JSON_QUERY je podobný JSON_VALUE. Na rozdíl od JSON_VALUE tato funkce extrahuje komplexní dílčí objekt, jako je například pole nebo objekty, které jsou umístěny v textu JSON.

Funkce příkazu json_modify je vám umožní zadat cestu k hodnotě v textu JSON, který by se měla aktualizovat a také novou hodnotu, která přepíše starý. Tímto způsobem můžete snadno aktualizovat JSON text bez reparsing celou jejich strukturu.

Protože JSON je uložený ve formátu standardního textu, neexistují žádné záruky, že hodnoty uložené v textové sloupce jsou ve správném formátu. Můžete ověřit, že text uložený ve sloupci JSON je ve správném formátu pomocí standardní omezení check Azure SQL Database a ISJSON funkce:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Pokud je ve správném formátu vstupního textu JSON, ISJSON funkce vrátí hodnotu 1. Na každém vložení nebo aktualizace sloupce JSON bude toto omezení ověřte, že novou textovou hodnotou není nesprávný formát JSON.

## <a name="transforming-json-into-tabular-format"></a>Transformace JSON na tabulkovém formátu
Azure SQL Database také umožňuje transformovat kolekce JSON do tabulkového formátu a zatížení nebo dotaz data JSON.

OPENJSON je funkce hodnota tabulky, která analyzuje JSON text, vyhledá pole objektů JSON, provede iteraci prvků pole a vrátí jeden řádek ve výstupu výsledku pro každý prvek pole.

![Tabulkové JSON](./media/sql-database-json-features/image_2.png)

V předchozím příkladu jsme můžete určit, kde najít pole JSON, který by měl být otevřen (v $. Cesta objednávky), jaké sloupce se vrátit jako výsledek a kde najít hodnoty JSON, které budou vráceny jako buňky.

Transformujeme pole JSON v @orders proměnné do sady řádků, analyzovat Tato sada výsledků dotazu nebo vložení řádků do tabulky standard:

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
Kolekci objednávek naformátovaný jako pole JSON a k dispozici jako parametr uložené procedury lze analyzovat a vloženy do tabulky objednávky.

## <a name="next-steps"></a>Další postup
Zjistěte, jak integrace JSON do vaší aplikace, prostudujte tyto materiály:

* [TechNet Blog](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
* [Dokumentace MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
* [Video pro kanál 9](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Další informace o různých scénářích pro integraci JSON do vaší aplikace, najdete v ukázkách, které v tomto [videa Channel 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) nebo najít scénář, který odpovídá vašemu případu použití v [příspěvky do blogu JSON](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

