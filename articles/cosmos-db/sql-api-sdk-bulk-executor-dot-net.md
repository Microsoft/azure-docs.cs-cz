---
title: 'Azure Cosmos DB: Hromadné prováděcí modul .NET API, SDK a prostředky | Dokumentace Microsoftu'
description: Přečtěte si všechno o hromadné prováděcí modul .NET API a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady Azure Cosmos DB hromadné prováděcí modul .NET SDK.
services: cosmos-db
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/14/2018
ms.author: ramkris
ms.openlocfilehash: ffd8f438429cd8769ac0dbff7f489327166e0000
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294454"
---
# <a name="net-bulk-executor-library-download-information"></a>Knihovna .NET hromadné prováděcí modul: Stažení informací 

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
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [Prováděcí modul hromadného – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Prováděcí modul hromadného – Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Popis**</td><td>Knihovna prováděcí modul hromadného umožňuje klientské aplikace budou provádět hromadné operace v účtech služby Azure Cosmos DB. Hromadné prováděcí modul knihovny poskytuje hromadný import, BulkUpdate a hromadného odstranění obory názvů. Hromadný import, které můžete hromadně modul ingestování dokumenty optimálního tak, že je zajištěné propustnosti pro kolekce spotřebované na maximální velikost. BulkUpdate, které modul můžete hromadně aktualizovat stávající data v kontejnerech služby Azure Cosmos DB jako opravy. Modul hromadného odstranění můžete hromadně odstranění dokumentů optimálního tak, že je zajištěné propustnosti pro kolekce spotřebované na maximální velikost.</td></tr>

<tr><td>**Stažení sady SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**BulkExecutor knihovny v Githubu**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**Dokumentace k rozhraní API**</td><td>[Referenční dokumentace rozhraní .net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Začínáme**</td><td>[Začínáme s knihovnou prováděcí modul hromadné sady .NET SDK](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Aktuální podporované architektury**</td><td><ul><li>[Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)(verze > = 2.0.0)</li><li>
[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)(verze > = 9.0.1)
</li></ul></td></tr>
</table></br>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Přidání podpory pro operaci hromadného odstranění pro účty SQL API služby Azure Cosmos DB.
* Přidání podpory pro hromadný import operaci pro účty rozhraní API MongoDB služby Azure Cosmos DB.
* Sadu závislostí sady DocumentDB .NET SDK verze 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Přidání podpory pro hromadný import operaci pro účty Azure Cosmos DB Gremlin API.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Menší opravy hromadný import operací pro účty SQL API služby Azure Cosmos DB.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Přidání podpory pro hromadný import a BulkUpdate operací pro účty SQL API služby Azure Cosmos DB.
