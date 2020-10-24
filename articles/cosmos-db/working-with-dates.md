---
title: Práce s daty v Azure Cosmos DB
description: Naučte se ukládat, indexovat a dotazovat objekty DataTime v Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: a876c5ba9c289f0edbbfdf8727e9957e7937b781
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476242"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Práce s daty v Azure Cosmos DB

Azure Cosmos DB zajišťuje flexibilitu schématu a bohatou indexaci prostřednictvím nativního datového modelu [JSON](https://www.json.org) . Všechny prostředky Azure Cosmos DB, včetně databází, kontejnerů, dokumentů a uložených procedur, jsou modelovány a uloženy jako dokumenty JSON. Jako požadavek na přenos, JSON (a Azure Cosmos DB) podporuje jenom malou sadu základních typů: String, Number, Boolean, Array, Object a null. JSON je však flexibilní a umožňuje vývojářům a architekturám znázornit komplexnější typy pomocí těchto primitiv a jejich sestavování jako objektů nebo polí.

Kromě základních typů potřebuje mnoho aplikací, aby typ DateTime představoval data a časová razítka. Tento článek popisuje, jak můžou vývojáři ukládat, načítat a dotazovat data v Azure Cosmos DB pomocí sady .NET SDK.

## <a name="storing-datetimes"></a>Ukládání DateTime

Azure Cosmos DB podporuje typy JSON, jako je řetězec, číslo, logická hodnota, null, Array, Object. Nepodporují přímo typ DateTime. V současné době Azure Cosmos DB nepodporuje lokalizaci dat. Proto je nutné ukládat hodnoty DateTime jako řetězce. Doporučený formát pro řetězce DateTime v Azure Cosmos DB je podle `yyyy-MM-ddTHH:mm:ss.fffffffZ` standardu ISO 8601 UTC. Doporučuje se ukládat všechna data v Azure Cosmos DB jako UTC. Převod řetězců data na tento formát umožní řazení dat lexikograficky. Pokud jsou uložena data, která nejsou ve formátu UTC, musí být logika zpracována na straně klienta. Chcete-li převést místní datový typ DateTime na čas UTC, posun musí být ve formátu JSON znám nebo uložen jako vlastnost a klient může použít posun k výpočtu hodnoty DateTime UTC.

Dotazy rozsahu s řetězci DateTime jako filtry jsou podporovány pouze v případě, že jsou řetězce DateTime všechny v UTC a mají stejnou délku. V Azure Cosmos DB systémová funkce [GetCurrentDateTime](sql-query-getcurrentdatetime.md) vrátí aktuální datum a čas UTC 8601 hodnoty řetězce ve formátu: `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

Většina aplikací může použít výchozí řetězcovou reprezentaci pro datový typ DateTime z následujících důvodů:

* Řetězce lze porovnat a relativní řazení hodnot DateTime je zachováno při transformaci na řetězce.
* Tento přístup nevyžaduje žádný vlastní kód ani atributy pro převod JSON.
* Data uložená ve formátu JSON jsou lidmi čitelná.
* Tento přístup může využít výhod indexu Azure Cosmos DB pro rychlý výkon dotazů.

Například následující fragment kódu uchovává `Order` objekt, který obsahuje dvě vlastnosti DateTime – `ShipDate` a `OrderDate` jako dokument používající sadu .NET SDK:

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

Tento dokument je uložený v Azure Cosmos DB následujícím způsobem:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Případně můžete ukládat hodnoty DateTime jako časová razítka systému UNIX, tj. číslo představující počet uplynulých sekund od 1. ledna 1970. `_ts`Tento přístup se řídí vnitřní vlastností Timestamp () Azure Cosmos DB. Můžete použít třídu [UnixDateTimeConverter](/dotnet/api/microsoft.azure.documents.unixdatetimeconverter) k serializaci hodnot DateTime jako čísel.

## <a name="querying-datetimes-in-linq"></a>Dotazování na hodnoty DateTime v LINQ

Sada SQL .NET SDK automaticky podporuje dotazování na data uložená v Azure Cosmos DB prostřednictvím LINQ. Například následující fragment kódu ukazuje dotaz LINQ, který filtruje objednávky, které byly odeslány za poslední tři dny:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Přeloženo na následující příkaz jazyka SQL a spuštěno na Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

Další informace Azure Cosmos DB o jazyce SQL Query dotazovacího jazyka a poskytovateli LINQ najdete v [dotazech Cosmos DB v LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indexování dat typu DateTime pro dotazy na rozsah

Dotazy jsou běžné s hodnotami data a času. Chcete-li provádět tyto dotazy efektivně, je nutné mít ve filtru dotazu definován index ve všech vlastnostech.

Další informace o konfiguraci zásad indexování najdete v tématu [Azure Cosmos DB zásady indexování](index-policy.md). 

## <a name="next-steps"></a>Další kroky

* Stažení a spuštění [ukázek kódu na GitHubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Další informace o [dotazech SQL](sql-query-getting-started.md)
* Další informace o [Azure Cosmos DB zásadách indexování](index-policy.md)