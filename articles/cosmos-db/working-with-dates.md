---
title: Práce s daty ve službě Azure Cosmos DB
description: Další informace o tom, jak pracovat s daty ve službě Azure Cosmos DB.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: ea7880d051303afad01ad8ba4a2d68d7331c6a89
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71291149"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Práce s daty ve službě Azure Cosmos DB
Azure Cosmos DB nabízí flexibilitu schémat a bohaté indexování prostřednictvím nativní [JSON](https://www.json.org) datového modelu. Všechny prostředky Azure Cosmos DB, včetně databází, kontejnerů, dokumenty a uložené procedury jsou modelovány a ukládány jako dokumenty JSON. Jako požadavek na přenos, JSON (a Azure Cosmos DB) podporuje jenom malou sadu základních typů: Řetězec, číslo, logická hodnota, pole, objekt a hodnota null. Ale JSON je flexibilní a umožňují vývojářům a architektur představují složitější typy, pomocí těchto primitivních hodnot a skládání jako objekty nebo pole. 

Kromě základních typů potřebuje mnoho aplikací, aby typ DateTime představoval data a časová razítka. Tento článek popisuje, jak mohou vývojáři ukládat, načíst a dotazovat data ve službě Azure Cosmos DB pomocí sady .NET SDK.

## <a name="storing-datetimes"></a>Ukládat data a času

Azure Cosmos DB je vymezen na typy JSON, které neobsahují typ DateTime. Takže v Azure Cosmos DB musí být kalendářní data uložená jako řetězce. V současné době Azure Cosmos DB nepodporuje lokalizaci dat. Doporučený formát pro řetězce DateTime v Azure Cosmos DB je `YYYY-MM-DDThh:mm:ss.sssZ` podle standardu ISO 8601 UTC. Formátování řetězců v tomto formátu umožní řazení dat lexikograficky. Logika pro zpracování kalendářních dat typu non-UTC musí být definována klientem. Většina aplikace mohly používat výchozí řetězcovou reprezentaci data a času z následujících důvodů:

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
