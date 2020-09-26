---
title: Azure Cosmos DB poznámky a zdroje informací k verzi Java SDK v4 pro SQL API
description: Přečtěte si všechno o Azure Cosmos DB Java SDK v4 pro SQL API a SDK včetně data vydání, data odchodu a změn provedených mezi jednotlivými verzemi Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 92f1b722e39083463fd7fa57fdf8508c2c4084cd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326639"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB rozhraní Java SDK v4 pro Core (SQL) API: poznámky k verzi a prostředky
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
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 for Core (SQL) kombinuje asynchronní rozhraní API a synchronizační rozhraní API do jednoho artefaktu Maven. Sada v4 SDK přináší Vylepšený výkon, nové funkce rozhraní API a asynchronní podporu založenou na reaktoru projektů a v [knihovně sítí](https://netty.io/). Uživatelé můžou očekávat vyšší výkon pomocí Azure Cosmos DB Java SDK v4 a [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) a [Azure Cosmos DB synchronizaci Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Tyto poznámky k verzi platí jenom pro Azure Cosmos DB Java SDK v4. Pokud aktuálně používáte starší verzi než v4, přečtěte si článek průvodce [migrací do Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) , který vám pomůže s upgradem na V4.
>
> Tady jsou tři kroky pro rychlé zprovoznění.
> 1. Nainstalujte [Minimální podporovaný běhový modul Java, JDK 8,](/java/azure/jdk/?view=azure-java-stable) abyste mohli používat sadu SDK.
> 2. Projděte si [příručku pro rychlý Start pro Azure Cosmos DB Java SDK v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) , která vám umožní přístup k artefaktu Maven a provede základní žádosti o Azure Cosmos DB.
> 3. Pokud chcete optimalizovat sadu SDK pro vaši aplikaci, přečtěte si [tipy k výkonu](performance-tips-java-sdk-v4-sql.md) Azure Cosmos DB Java SDK v4 a průvodci [odstraňováním potíží](troubleshoot-java-sdk-v4-sql.md) .
>
> [Azure Cosmos DB semináře a cvičení](https://aka.ms/cosmosworkshop) jsou dalším skvělým prostředkem, který se naučí používat Azure Cosmos DB Java SDK v4!
>

## <a name="helpful-content"></a>Užitečný obsah

| Obsah | Odkaz |
|---|---|
|**Stažení sady SDK**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Dokumentace k rozhraní API** | [Referenční dokumentace k rozhraní Java API](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Přispívání do sady SDK** | [Azure SDK pro centrální úložiště Java na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Začínáme** | [Rychlý Start: Vytvoření aplikace Java pro správu Azure Cosmos DB dat rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [Úložiště GitHub s kódem pro rychlý Start](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Ukázky základních kódů** | [Azure Cosmos DB: příklady v Javě pro rozhraní SQL API](sql-api-java-sdk-samples.md) <br> [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konzolová aplikace se změněným kanálem**| [Ukázka změny kanálu – Java SDK v4](create-sql-api-java-changefeed.md) <br> [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Ukázka webové aplikace**| [Vytvoření webové aplikace pomocí sady Java SDK v4](sql-api-java-application.md) <br> [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Tipy pro zvýšení výkonu**| [Tipy ke zvýšení výkonu pro Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Řešení potíží** | [Řešení potíží se sadou Java SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrace na V4 ze starší sady SDK** | [Migrace na Java V4 SDK](migrate-java-v4-sdk.md) |
| **Minimální podporovaná doba běhu**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB semináře a cvičení** |[Domovská stránka inCosmos DBch Workshops](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>Časté otázky
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>Další kroky
Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) .