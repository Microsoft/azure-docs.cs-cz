---
title: Práce s daty ve službě Azure Cosmos DB
description: Naučte se ukládat, indexovat a dotazovat objekty DataTime v Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 92fa35fbe8e5eef4dbdc8b6c47a9055affd449a5
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273189"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Práce s daty ve službě Azure Cosmos DB

Azure Cosmos DB zajišťuje flexibilitu schématu a bohatou indexaci prostřednictvím nativního datového modelu [JSON](https://www.json.org) . Všechny prostředky Azure Cosmos DB, včetně databází, kontejnerů, dokumenty a uložené procedury jsou modelovány a ukládány jako dokumenty JSON. Jako požadavek pro vrácení přenosné JSON (a Azure Cosmos DB) podporuje pouze malou sadu základních typů: řetězec, číslo, logickou hodnotu, pole, objekt a hodnotu Null. Ale JSON je flexibilní a umožňují vývojářům a architektur představují složitější typy, pomocí těchto primitivních hodnot a skládání jako objekty nebo pole.

Kromě základních typů potřebuje mnoho aplikací, aby typ DateTime představoval data a časová razítka. Tento článek popisuje, jak mohou vývojáři ukládat, načíst a dotazovat data ve službě Azure Cosmos DB pomocí sady .NET SDK.

## <a name="storing-datetimes"></a>Ukládat data a času

Azure Cosmos DB podporuje typy JSON, jako je řetězec, číslo, logická hodnota, null, Array, Object. Nepodporují přímo typ DateTime. V současné době Azure Cosmos DB nepodporuje lokalizaci dat. Proto je nutné ukládat hodnoty DateTime jako řetězce. Doporučený formát pro řetězce DateTime v Azure Cosmos DB je `YYYY-MM-DDThh:mm:ss.sssZ`, který následuje Standard ISO 8601 UTC. Doporučuje se ukládat všechna data v Azure Cosmos DB jako UTC. Převod řetězců data na tento formát umožní řazení dat lexikograficky. Pokud jsou uložena data, která nejsou ve formátu UTC, musí být logika zpracována na straně klienta. Chcete-li převést místní data typu DateTime na čas UTC, posun musí být ve formátu JSON známý nebo uložen jako vlastnost a klient může použít posun k výpočtu hodnoty DateTime UTC.

Většina aplikace mohly používat výchozí řetězcovou reprezentaci data a času z následujících důvodů:

* Je možné porovnat řetězce a relativní řazení hodnoty data a času se zachová, i když jsou tyto převedeny na řetězce.
* Tento postup nevyžaduje žádné vlastní kód nebo atributy pro převod formátu JSON.
* Data, jak je uložen ve formátu JSON jsou lidské čitelné.
* Tento přístup můžete využít výhod indexu služby Azure Cosmos DB pro zajištění výkonu dotazů rychlé.

Následující fragment kódu například ukládá objekt `Order`, který obsahuje dvě vlastnosti DateTime-`ShipDate` a `OrderDate` jako dokument s použitím sady .NET SDK:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });
```

Tento dokument se ukládají ve službě Azure Cosmos DB následujícím způsobem:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Alternativně můžete ukládat data a času jako systému Unix časová razítka, to znamená, jako číslo představující počet uplynulých sekund od 1. ledna 1970. Tento přístup se řídí vlastností vnitřního časového razítka (`_ts`) Azure Cosmos DB. Můžete použít třídu [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) k serializaci hodnot DateTime jako čísel.

## <a name="querying-datetimes-in-linq"></a>Dotazování na data a času v jazyce LINQ

SQL SDK pro .NET podporuje automaticky dotazování na data uložená ve službě Azure Cosmos DB pomocí LINQ. Například následující fragment kódu ukazuje dotaz LINQ, který filtruje objednávky, které byly odeslány za poslední tři dny:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Přeloženo na následující příkaz jazyka SQL a spuštěno na Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")
```

Další informace Azure Cosmos DB o jazyce SQL Query dotazovacího jazyka a poskytovateli LINQ najdete v [dotazech Cosmos DB v LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indexování pro dotazy na rozsah času

Dotazy jsou běžné s hodnotami data a času. Chcete-li provádět tyto dotazy efektivně, je nutné mít ve filtru dotazu definován index ve všech vlastnostech.

Další informace o konfiguraci zásad indexování najdete v tématu [Azure Cosmos DB zásady indexování](index-policy.md). 

## <a name="next-steps"></a>Další kroky

* Stažení a spuštění [ukázek kódu na GitHubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Další informace o [dotazech SQL](sql-query-getting-started.md)
* Další informace o [Azure Cosmos DB zásadách indexování](index-policy.md)
