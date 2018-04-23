---
title: Jak provádět dotazy na tabulková data ve službě Azure Cosmos DB? | Dokumenty Microsoft
description: Zjistěte, jak provádět dotazy na tabulková data ve službě Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: kanshiG
manager: kfile
editor: ''
tags: ''
ms.assetid: 14bcb94e-583c-46f7-9ea8-db010eb2ab43
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: d3b46169f7cc175d1959ecaa7184faa0fd81590c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-table-api"></a>Kurz: Dotazování služby Azure Cosmos DB pomocí rozhraní Table API

Rozhraní [Table API](table-introduction.md) služby Azure Cosmos DB podporuje dotazy OData a [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) na data párů klíč-hodnota (tabulková data).  

Tento článek se zabývá následujícími úkony: 

> [!div class="checklist"]
> * Dotazování dat pomocí rozhraní Table API

Dotazy v tomto článku využívají následující ukázkovou tabulku `People`:

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Ben | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 | 

Podrobnosti o dotazování pomocí rozhraní Table API najdete v tématu [Dotazování tabulek a entit](https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities). 

Další informace o prémiových funkcích, které služba Azure Cosmos DB nabízí, najdete v tématech [Rozhraní Table API služby Azure Cosmos DB](table-introduction.md) a [Vývoj v .NET s využitím rozhraní Table API](tutorial-develop-table-dotnet.md). 

## <a name="prerequisites"></a>Požadavky

Aby tyto dotazy fungovaly, musíte mít účet služby Azure Cosmos DB a data entit v kontejneru. Něco z toho nemáte? Vytvořte účet a naplňte databázi dokončením [pětiminutového rychlého startu](create-table-dotnet.md) nebo [kurzu pro vývojáře](tutorial-develop-table-dotnet.md).

## <a name="query-on-partitionkey-and-rowkey"></a>Dotazování sloupců PartitionKey a RowKey
Vzhledem k tomu, že vlastnosti PartitionKey (Klíč oddílu) a RowKey (Klíč řádku) tvoří primární klíč entity, můžete k identifikaci entity použít následující speciální syntaxi: 

**Dotaz**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**Results**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

Alternativně můžete tyto vlastnosti zadat v rámci možnosti `$filter`, jak je znázorněno v následující části. Poznámka: V názvech vlastností klíčů a hodnotách konstant se rozlišují malá a velká písmena. Vlastnosti PartitionKey i RowKey jsou typu String (řetězec). 

## <a name="query-by-using-an-odata-filter"></a>Dotazování s použitím filtru OData
Při vytváření řetězce filtru mějte na paměti tato pravidla: 

* K porovnání vlastnosti s hodnotou použijte logické operátory definované ve specifikaci protokolu OData. Nezapomeňte, že nemůžete porovnat vlastnost s dynamickou hodnotou. Jedna strana výrazu musí být konstanta. 
* Název vlastnosti, operátor a hodnotu konstanty musí být oddělené mezerami zakódovanými do adresy URL. Mezera se do adresy URL kóduje jako `%20`. 
* Ve všech částech řetězce filtru se rozlišují malá a velká písmena. 
* Hodnota konstanty musí být stejného datového typu jako vlastnost, aby filtr vrátil platné výsledky. Další informace o podporovaných typech vlastností najdete v tématu [Vysvětlení datového modelu služby Table Service](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). 

Tady je příklad dotazu, který ukazuje filtrování podle vlastností PartitionKey a Email pomocí filtru OData `$filter`.

**Dotaz**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

Další informace o vytváření výrazů filtru pro různé datové typy najdete v tématu [Dotazování tabulek a entit](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities).

**Results**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Ben |Smith | Ben@contoso.com| 425-555-0102 |

## <a name="query-by-using-linq"></a>Dotazování pomocí jazyka LINQ 
Dotazovat můžete také pomocí jazyka LINQ, který se přeloží na odpovídající výrazy dotazu OData. Tady je příklad sestavování dotazů pomocí sady .NET SDK:

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("people");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition(PartitionKey, QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition(Email, QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Zjistili jste, jak provádět dotazy pomocí rozhraní Table API.

Teď můžete pokračovat k dalšímu kurzu, kde se dozvíte, jak globálně distribuovat data.

> [!div class="nextstepaction"]
> [Globální distribuce dat](tutorial-global-distribution-table.md)
