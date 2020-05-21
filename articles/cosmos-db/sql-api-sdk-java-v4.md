---
title: Azure Cosmos DB poznámky a zdroje informací k verzi Java SDK v4 pro SQL API
description: Přečtěte si všechno o Azure Cosmos DB Java SDK v4 pro SQL API a SDK včetně data vydání, data odchodu a změn provedených mezi jednotlivými verzemi Azure Cosmos DB SQL Async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: b222a94ee754b24192261451d8ddc429886e705c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725648"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB rozhraní Java SDK v4 pro Core (SQL) API: poznámky k verzi a prostředky
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanál změn .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET](sql-api-sdk-bulk-executor-dot-net.md)
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

| |  |
|---|---|
| **Stažení sady SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Dokumentace k rozhraní API** | [Referenční dokumentace k rozhraní Java API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1-beta.3/index.html) |
|**Přispívání do sady SDK** | [Azure SDK pro centrální úložiště Java na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Začínáme** | [Rychlý Start: Vytvoření aplikace Java pro správu Azure Cosmos DB dat rozhraní SQL API](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [Úložiště GitHub s kódem pro rychlý Start](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Ukázky základních kódů** | [Azure Cosmos DB: Příklady jazyka Java pro rozhraní SQL API](sql-api-java-sdk-samples.md) · [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konzolová aplikace se změněným kanálem**| [Změna kanálu – ukázka pro Java SDK v4](create-sql-api-java-changefeed.md) · [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Ukázka webové aplikace**| [Vytvoření webové aplikace pomocí Java SDK v4](sql-api-java-application.md) · [Úložiště GitHub s ukázkovým kódem](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Tipy pro zvýšení výkonu**| [Tipy ke zvýšení výkonu pro Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Odstraňování potíží** | [Řešení potíží se sadou Java SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrace na V4 ze starší sady SDK** | [Migrace na Java V4 SDK](migrate-java-v4-sdk.md) |
| **Minimální podporovaná doba běhu**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB semináře a cvičení** |[Domovská stránka inCosmos DBch Workshops](https://aka.ms/cosmosworkshop)

