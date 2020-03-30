---
title: Práce s daty v Azure Cosmos DB
description: Zjistěte, jak ukládat, indexovat a dotazovat objekty DataTime v Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 92fa35fbe8e5eef4dbdc8b6c47a9055affd449a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273189"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Práce s daty v Azure Cosmos DB

Azure Cosmos DB poskytuje flexibilitu schématu a bohaté indexování prostřednictvím nativního datového modelu [JSON.](https://www.json.org) Všechny prostředky Azure Cosmos DB včetně databází, kontejnerů, dokumentů a uložených procedur jsou modelovány a uloženy jako dokumenty JSON. Jako požadavek pro přenositelnost json (a Azure Cosmos DB) podporuje pouze malou sadu základních typů: Řetězec, Číslo, Logická hodnota, Pole, Objekt a Null. JSON je však flexibilní a umožňují vývojářům a rozhraní mů e představovat složitější typy pomocí těchto primitiv a jejich skládání jako objekty nebo pole.

Kromě základních typů mnoho aplikací potřebují DateTime typ představují data a časová razítka. Tento článek popisuje, jak mohou vývojáři ukládat, načítat a dotazovat se na data v Azure Cosmos DB pomocí sady .NET SDK.

## <a name="storing-datetimes"></a>Ukládání časů

Azure Cosmos DB podporuje typy JSON, jako je - řetězec, číslo, logická hodnota, null, pole, objekt. Nepodporuje přímo DateTime typu. V současné době Azure Cosmos DB nepodporuje lokalizaci dat. Takže je třeba uložit DateTimes jako řetězce. Doporučený formát pro řetězce DateTime v Azure `YYYY-MM-DDThh:mm:ss.sssZ` Cosmos DB je, který se řídí standardem ISO 8601 UTC. Doporučujeme ukládat všechna data v Azure Cosmos DB jako UTC. Převod datových řetězců do tohoto formátu umožní lexikograficky seřadit data. Pokud jsou uložena data mimo UTC, logika musí být zpracována na straně klienta. Chcete-li převést místní DateTime na UTC, posun musí být známý/uložen jako vlastnost v JSON a klient může použít posun k výpočtu Hodnoty UTC DateTime.

Většina aplikací může použít výchozí řetězcovou reprezentaci pro DateTime z následujících důvodů:

* Řetězce lze porovnat a relativní pořadí DateTime hodnoty je zachována při jejich transformaci na řetězce.
* Tento přístup nevyžaduje žádný vlastní kód nebo atributy pro převod JSON.
* Data uložená v JSON jsou čitelná pro člověka.
* Tento přístup můžete využít indexu Azure Cosmos DB pro rychlý výkon dotazu.

Například následující výstřižek `Order` ukládá objekt obsahující dvě `ShipDate` vlastnosti DateTime - a `OrderDate` jako dokument pomocí sady .NET SDK:

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

Tento dokument se v Azure Cosmos DB ukládá takto:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Případně můžete uložit DateTimes jako unixová časová razítka, tj. Azure Cosmos DB vnitřní časové`_ts`razítko ( ) vlastnost následuje tento přístup. Třídu [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) můžete použít k serializaci DateTimes jako čísel.

## <a name="querying-datetimes-in-linq"></a>Dotazování DateTimes v LINQ

Sada SQL .NET SDK automaticky podporuje dotazování dat uložených v Azure Cosmos DB prostřednictvím LINQ. Například následující úryvek zobrazuje dotaz LINQ, který filtruje objednávky, které byly dodány v posledních třech dnech:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Přeloženo do následujícího příkazu SQL a spuštěno v Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")
```

Další informace o dotazovacím jazyce SQL služby Azure Cosmos DB a poskytovateli LINQ najdete v článku Dotazování se na [Cosmos DB v LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indexování DateTimes pro dotazy rozsahu

Dotazy jsou společné s DateTime hodnoty. Chcete-li tyto dotazy provést efektivně, musíte mít index definovaný na všechny vlastnosti ve filtru dotazu.

Další informace o konfiguraci zásad indexování najdete v [zásadách indexování Azure Cosmos DB](index-policy.md). 

## <a name="next-steps"></a>Další kroky

* Stažení a spuštění [ukázek kódu na GitHubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Další informace o [dotazech SQL](sql-query-getting-started.md)
* Další informace o [zásadách indexování Azure Cosmos DB](index-policy.md)
