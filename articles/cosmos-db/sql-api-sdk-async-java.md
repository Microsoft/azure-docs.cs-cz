---
title: 'Azure Cosmos DB: SQL Async Java API, sady SDK & prostředky'
description: Seznamte se se všemi službami SQL Async Java API a SDK včetně dat vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 509c6e7a7940cb740a3dcbf81deed9f0d26c2bc8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802850"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Async Java SDK pro SQL API: poznámky k verzi a prostředky
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [Sada .NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [Rozhraní .NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Sada Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Jarní data v2](sql-api-sdk-java-spring-v2.md)
> * [Jarní data V3](sql-api-sdk-java-spring-v3.md)
> * [Konektor Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * REST (/rest/api
> * [Poskytovatel prostředků REST](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

Asynchronní sada Java SDK pro SQL API se liší od sady SQL API Java SDK tím, že poskytuje asynchronní operace s podporou pro [knihovnu síťoviny](https://netty.io/). Již existující [rozhraní SQL API Java SDK](sql-api-sdk-java.md) nepodporuje asynchronní operace. 

> [!IMPORTANT]  
> Nejedná *se o* nejnovější sadu Java SDK pro Azure Cosmos DB. Zvažte použití [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) pro váš projekt. Chcete-li provést upgrade, postupujte podle pokynů v příručce [k migraci na Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) a v příručce pro předaný [objekt actor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) . 
>

| |  |
|---|---|
| **Stažení sady SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Dokumentace k rozhraní API** |[Referenční dokumentace k rozhraní Java API](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable&preserve-view=true) | 
|**Přispívání do sady SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Začínáme** | [Začínáme s asynchronní sadou Java SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Ukázka kódu** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Tipy pro zvýšení výkonu**| [Soubor Readme GitHubu](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimální podporovaná doba běhu**|[JDK 8](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>Časté otázky
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také
Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .

