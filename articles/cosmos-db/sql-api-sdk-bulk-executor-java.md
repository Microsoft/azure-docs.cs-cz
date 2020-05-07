---
title: 'Azure Cosmos DB: hromadný prováděcí modul Java API, SDK & prostředků'
description: Seznamte se se všemi o rozhraních API pro hromadné prováděcí moduly Java a sadě SDK, včetně dat vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi Azure Cosmos DB hromadného prováděcího modulu Java SDK.
author: milismsft
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/21/2018
ms.author: adrianmi
ms.openlocfilehash: 0030f974a36dc80dc8c4112000aa5934126a2482
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836440"
---
# <a name="java-bulk-executor-library-download-information"></a>Knihovna hromadného prováděcího modulu Java: informace o stažení

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanál změn .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný prováděcí modul – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Popis**|Knihovna hromadného prováděcího modulu umožňuje klientským aplikacím provádět hromadné operace s účty Azure Cosmos DB. Knihovna hromadných prováděcích modulů poskytuje obory názvů BulkImport a BulkUpdate. Modul BulkImport dokáže hromadně ingestovat dokumenty optimalizovaným způsobem tak, že propustnost zřízená pro kolekci se spotřebovává do svého maximálního rozsahu. Modul BulkUpdate může hromadně aktualizovat existující data v kontejnerech Azure Cosmos jako opravy.|
|**Stažení sady SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Knihovna hromadných prováděcích modulů na GitHubu**|[GitHubu](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Dokumentace k rozhraní API**| [Referenční dokumentace k rozhraní Java API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Začínáme**|[Začínáme s knihovnou hromadných prováděcích knihoven Java SDK](bulk-executor-java.md)|
|**Minimální podporovaná doba běhu**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* Opravte pro DocumentAnalyzer. Java, aby byly správně extrahovány hodnoty klíčů vnořeného oddílu z formátu JSON.

### <a name="294"></a><a name="2.9.4"/>2.9.4

* Přidejte funkce do operací BulkDelete a zkuste to znovu při konkrétních selháních a taky vraťte seznam selhání pro uživatele, který by se mohl opakovat.

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Aktualizace pro Cosmos SDK verze 2.4.7.

### <a name="292"></a><a name="2.9.2"/>2.9.2

* Opravte pro ' mergeAll ' pro pokračování na ' ID ' a hodnotu klíče oddílu tak, aby všechny vlastnosti dokumentu s opravou, které jsou umístěny po ' ID ' a hodnota klíče oddílu, se přidaly do seznamu aktualizovaných položek.

### <a name="291"></a><a name="2.9.1"/>2.9.1

* Umožňuje aktualizovat počáteční stupeň souběžnosti na 1 a přidaných protokolů ladění pro minibatch.


