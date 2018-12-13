---
title: Práce s daty ve službě Azure Cosmos DB
description: Další informace o tom, jak pracovat s daty ve službě Azure Cosmos DB.
services: cosmos-db
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/25/2017
ms.openlocfilehash: 48879eb3ae4fc3a54d50a4e896b4a1eb94acabf9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53073801"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Práce s daty ve službě Azure Cosmos DB
Azure Cosmos DB nabízí flexibilitu schémat a bohaté indexování prostřednictvím nativní [JSON](https://www.json.org) datového modelu. Všechny prostředky Azure Cosmos DB, včetně databází, kontejnerů, dokumenty a uložené procedury jsou modelovány a ukládány jako dokumenty JSON. Jako požadavek pro vrácení přenosné JSON (a Azure Cosmos DB) podporuje pouze malou sadu základních typů: řetězec, číslo, logickou hodnotu, pole, objekt a hodnotu Null. Ale JSON je flexibilní a umožňují vývojářům a architektur představují složitější typy, pomocí těchto primitivních hodnot a skládání jako objekty nebo pole. 

Kromě základních typů, mnoho aplikací se musí [data a času](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) typ představující datum a časové razítko. Tento článek popisuje, jak mohou vývojáři ukládat, načíst a dotazovat data ve službě Azure Cosmos DB pomocí sady .NET SDK.

## <a name="storing-datetimes"></a>Ukládat data a času
Ve výchozím nastavení [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) serializuje hodnoty data a času jako [ISO 8601](https://www.iso.org/iso/catalogue_detail?csnumber=40874) řetězce. Většina aplikace mohly používat výchozí řetězcovou reprezentaci data a času z následujících důvodů:

* Je možné porovnat řetězce a relativní řazení hodnoty data a času se zachová, i když jsou tyto převedeny na řetězce. 
* Tento postup nevyžaduje žádné vlastní kód nebo atributy pro převod formátu JSON.
* Data, jak je uložen ve formátu JSON jsou lidské čitelné.
* Tento přístup můžete využít výhod indexu služby Azure Cosmos DB pro zajištění výkonu dotazů rychlé.

Například následující fragment kódu ukládá `Order` objekt obsahující dvě vlastnosti data a času – `ShipDate` a `OrderDate` jako dokument pomocí sady .NET SDK:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Tento dokument se ukládají ve službě Azure Cosmos DB následujícím způsobem:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Alternativně můžete ukládat data a času jako systému Unix časová razítka, to znamená, jako číslo představující počet uplynulých sekund od 1. ledna 1970. Azure Cosmos DB interní časové razítko (`_ts`) vlastnost následuje tento přístup. Můžete použít [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) třída určená k serializaci data a času jako čísla. 

## <a name="indexing-datetimes-for-range-queries"></a>Indexování pro dotazy na rozsah času
Dotazy na rozsah jsou společné s hodnotami data a času. Například pokud je potřeba najít všechny objednávky vytvořili od včerejška nebo najít všechny objednávky odeslané za posledních pět minut, potřebujete provádět dotazy na rozsah. K provádění těchto dotazů efektivně, je nutné nakonfigurovat kolekce pro rozsah indexování na řetězce.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Další informace o tom, jak nakonfigurovat zásady indexování na [zásady indexování Azure Cosmos DB](index-policy.md).

## <a name="querying-datetimes-in-linq"></a>Dotazování na data a času v jazyce LINQ
SQL SDK pro .NET podporuje automaticky dotazování na data uložená ve službě Azure Cosmos DB pomocí LINQ. Například následující fragment kódu ukazuje dotaz LINQ odeslaných za posledních tří dnů objednávek tohoto filtry.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Další informace o dotazovací jazyk SQL služby Azure Cosmos DB a zprostředkovatele LINQ na [dotazování Cosmos DB](how-to-sql-query.md).

V tomto článku jsme se podívali na tom, jak ukládání, indexování a dotazovat data a času ve službě Azure Cosmos DB.

## <a name="next-steps"></a>Další kroky
* Stáhněte a spusťte [ukázky kódu na Githubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Další informace o [dotazy SQL](how-to-sql-query.md)
* Další informace o [zásady indexování Azure Cosmos DB](index-policy.md)
