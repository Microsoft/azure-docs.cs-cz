---
title: Práce s daty v Azure Cosmos DB | Microsoft Docs
description: Další informace o postupu při práci s daty v Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: e587772f-ce9f-498c-a017-a51e7265bb23
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: sngun
ms.openlocfilehash: a1f8b38725eb188b609fbdc8efa09ed5aaaf6ae5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Práce s daty v Azure Cosmos DB
Azure Cosmos DB nabízí flexibilitu schémat a bohaté indexování prostřednictvím nativní [JSON](http://www.json.org) datového modelu. Všechny prostředky Azure Cosmos DB včetně databází, kolekcí, dokumentů a uložené procedury jsou modelovány a ukládány jako dokumenty JSON. Jako požadavek na vrácení přenosné, JSON (a Azure Cosmos DB) podporuje pouze omezenou sadu základních typů: řetězec, číslo, logickou hodnotu, pole, objekt a hodnotu Null. Ale JSON je flexibilní a umožňují vývojářům a architektury představují složitější typy pomocí těchto primitivních elementů a skládání je jako objekty nebo pole. 

Kromě základních typů mnoho aplikace potřebují [data a času](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) typ představují data a časová razítka. Tento článek popisuje, jak mohou vývojáři ukládání, načíst a dotaz na data v databázi Cosmos Azure pomocí sady .NET SDK.

## <a name="storing-datetimes"></a>Ukládání data a času
Ve výchozím nastavení [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) serializuje hodnoty DateTime jako [ISO 8601](http://www.iso.org/iso/catalogue_detail?csnumber=40874) řetězce. Většina aplikací můžete použít výchozí řetězcovou reprezentaci pro data a času z následujících důvodů:

* Lze porovnat s hodnotou řetězce a relativní řazení hodnoty DateTime se zachová, i když jsou transformovány na řetězce. 
* Tento přístup nevyžaduje žádné vlastní kód nebo atributy pro převod z formátu JSON.
* Data, jak je uložen ve formátu JSON jsou lidské čitelné.
* Tento postup můžete využít výhod Azure Cosmos DB index pro výkon rychlé dotazů.

Například následující fragment kódu ukládá `Order` objekt obsahující dvě vlastnosti data a času - `ShipDate` a `OrderDate` jako dokument pomocí sady .NET SDK:

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

Tento dokument se ukládají v Azure Cosmos DB následujícím způsobem:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Alternativně můžete uložit data a času jako systému Unix časová razítka, to znamená, číslo reprezentující počet uplynulá počet sekund od 1. ledna 1970. Interní časové razítko Azure Cosmos DB (`_ts`) vlastnost následuje tento přístup. Můžete použít [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) třída určená k serializaci data a času jako čísla. 

## <a name="indexing-datetimes-for-range-queries"></a>Indexování pro dotazy na rozsah data a času
Dotazy na rozsah jsou běžné s hodnotami data a času. Například pokud budete potřebovat najít všechny objednávky vytvářené od včerejška nebo najít všechny objednávky odeslané za posledních pět minut, budete muset provádět dotazy rozsahu. K úspěšnému provedení tyto dotazy, je nutné nakonfigurovat kolekce pro rozsah indexování řetězce.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Další informace o tom, jak nakonfigurovat zásady indexování na [Azure Cosmos DB indexování zásady](indexing-policies.md).

## <a name="querying-datetimes-in-linq"></a>Dotazování na data a času v technologii LINQ
.NET SDK služby SQL automaticky podporuje dotazování na data uložená v Azure DB Cosmos prostřednictvím LINQ. Například následující fragment kódu ukazuje dotaz LINQ této odeslaných za poslední tři dny objednávek filtry.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Další informace o dotazovací jazyk SQL Azure Cosmos DB a poskytovateli LINQ na [dotazování DB Cosmos](sql-api-sql-query.md).

V tomto článku jsme se podívali na postup ukládání, index a dotaz na data a času v Azure Cosmos DB.

## <a name="next-steps"></a>Další kroky
* Stažení a spuštění [ukázky kódů v Githubu](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Další informace o [dotazy SQL](sql-api-sql-query.md)
* Další informace o [Azure Cosmos DB indexování zásady](indexing-policies.md)
