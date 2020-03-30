---
title: 'Azure Cosmos DB: Rozhraní API Jazyka Java a SQL, sada SDK & prostředky'
description: Přečtěte si vše o rozhraní SQL Async Java API a SDK, včetně dat vydání, dat odchodu do důchodu a změn provedených mezi jednotlivými verzemi sady Azure Cosmos DB SQL Async Java SDK.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 05f597093c27f84d7f20cf0abd5858f44645b88d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73574925"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK pro ROZHRANÍ SQL API: Poznámky k vydání a prostředky
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Informační kanál o změně .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Odpočinku](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný vykonavatel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný vykonavatel - Java](sql-api-sdk-bulk-executor-java.md)

Sql API Async Java SDK se liší od SQL API Java SDK tím, že poskytuje asynchronní operace s podporou [knihovny Netty](https://netty.io/). Již existující [sada SQL API Java SDK](sql-api-sdk-java.md) nepodporuje asynchronní operace. 

| |  |
|---|---|
| **SDK ke stažení** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Dokumentace k rozhraní API** |[Referenční dokumentace java api](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Přispívat do sady SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Začínáme** | [Začínáme s asynchronní sadou Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Ukázka kódu** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Tipy pro zvýšení výkonu**| [GitHub readme](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimální podporovaný modul runtime**|[JDK 8](https://aka.ms/azure-jdks) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také
Další informace o Službě Cosmos DB najdete na stránce [služby Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)

