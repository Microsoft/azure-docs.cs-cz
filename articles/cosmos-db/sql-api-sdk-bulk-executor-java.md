---
title: 'Azure Cosmos DB: hromadný prováděcí modul Java API, SDK & prostředků'
description: Seznamte se se všemi o rozhraních API pro hromadné prováděcí moduly Java a sadě SDK, včetně dat vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi Azure Cosmos DB hromadného prováděcího modulu Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: 766981d355c5f24e817ea5d626aefd0d0e9bed05
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392076"
---
# <a name="java-bulk-executor-library-download-information"></a>Knihovna hromadného prováděcího modulu Java: informace o stažení

> [!div class="op_single_selector"]
> * [.NET SDK V3](sql-api-sdk-dotnet-standard.md)
> * [Sada .NET SDK v2](sql-api-sdk-dotnet.md)
> * [Sada .NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [Rozhraní .NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Sada Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Popis**|Knihovna hromadného prováděcího modulu umožňuje klientským aplikacím provádět hromadné operace s účty Azure Cosmos DB. Knihovna hromadných prováděcích modulů poskytuje obory názvů BulkImport a BulkUpdate. Modul BulkImport dokáže hromadně ingestovat dokumenty optimalizovaným způsobem tak, že propustnost zřízená pro kolekci se spotřebovává do svého maximálního rozsahu. Modul BulkUpdate může hromadně aktualizovat existující data v kontejnerech Azure Cosmos jako opravy.|
|**Stažení sady SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Knihovna hromadných prováděcích modulů na GitHubu**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Dokumentace k rozhraní API**| [Referenční dokumentace k rozhraní Java API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Začínáme**|[Začínáme s knihovnou hromadných prováděcích knihoven Java SDK](bulk-executor-java.md)|
|**Minimální podporovaná doba běhu**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Opravte pro DocumentAnalyzer. Java, aby byly správně extrahovány hodnoty klíčů vnořeného oddílu z formátu JSON.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Přidejte funkce do operací BulkDelete a zkuste to znovu při konkrétních selháních a taky vraťte seznam selhání pro uživatele, který by se mohl opakovat.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Aktualizace pro Cosmos SDK verze 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Opravte pro ' mergeAll ' pro pokračování na ' ID ' a hodnotu klíče oddílu tak, aby všechny vlastnosti dokumentu s opravou, které jsou umístěny po ' ID ' a hodnota klíče oddílu, se přidaly do seznamu aktualizovaných položek.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Umožňuje aktualizovat počáteční stupeň souběžnosti na 1 a přidaných protokolů ladění pro minibatch.


