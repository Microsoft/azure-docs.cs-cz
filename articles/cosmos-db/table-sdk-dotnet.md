---
title: Rozhraní API .NET SDK & zdroje tabulky Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o Azure Cosmos DB Table API včetně data vydání, vyřazení dat a změny provedené mezi jednotlivými verzemi.
services: cosmos-db
author: rnagpal
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.author: rnagpal
ms.openlocfilehash: d0bd7dba5d50445cb681c16d9575b1bd69167e2f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42061311"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET API: Stáhněte si a poznámky k verzi
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Stažení sady SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace rozhraní API .NET](https://aka.ms/acdbtableapiref)|
|**Rychlý start**|[Azure Cosmos DB: Sestavení aplikace pomocí .NET a rozhraní API tabulky](create-table-dotnet.md)|
|**Kurz**|[Azure Cosmos DB: Vývoj s využitím rozhraní Table API v .NET](tutorial-develop-table-dotnet.md)|
|**Aktuální podporované architektury**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Pokud jste vytvořili účet Table API během období Preview, vytvořte [nový účet Table API](create-table-dotnet.md#create-a-database-account) pro práci s obecně dostupnými sadami Table API SDK.
>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3
* Oprava závislostí balíčku NuGet na Microsoft.Azure.Storage.Common a Microsoft.Azure.DocumentDB.
* Když jsou nakonfigurované JsonConvert.DefaultSettings opravy chyb v tabulce serializace.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Přidání ověřování pro poškozený značek etag v přímém režimu.
* Oprava chyby dotazu LINQ v režimu brány.
* Synchronní rozhraní API teď spouštět ve fondu vláken se třída SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Přidat do TableRequestOptions TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism a TableQueryContinuationTokenLimitInKb
* Opravy chyb

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Verzi všeobecné dostupnosti

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-Preview
* Počáteční verze Preview

## <a name="release-and-retirement-dates"></a>Datum vydání verze a vyřazení z provozu
Společnost Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením z provozu sady SDK ulehčení přechodu na verzi novější nebo podporované.

[WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) balíček ve verzi preview má zastaralé a nahrazují [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) balíčku. Sady SDK WindowsAzure.Storage-PremiumTable skončí 15. listopadu 2018, na dobu žádosti pro staré SDK nedovolí. `Microsoft.Azure.CosmosDB.Table` Knihovny je aktuálně dostupné pro .NET Standard pouze, ještě není k dispozici pro .NET Core.

Nové funkce a funkce a optimalizace jsou přidány pouze aktuální sadu SDK, proto se doporučuje, vždy upgradovat na nejnovější SDK verzi co možná nejdříve. 

Služba odmítne všechny žádosti do služby Azure Cosmos DB pomocí vyřazeno sady SDK.
<br/>

| Verze | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [1.1.3](#1.1.3) |17. července 2018|--- |
| [1.1.1](#1.1.1) |26. března 2018|--- |
| [1.1.0](#1.1.0) |21. února 2018|--- |
| [1.0.0](#1.0.0) |15. listopadu 2017|--- |
| [0.9.0-preview](#0.9.0-preview) |11. listopadu 2017 |--- |

## <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí chyba 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Při pokusu o použití balíčku Microsoft.Azure.CosmosDB.Table NuGet, máte dvě možnosti k vyřešení tohoto problému:

* Pomocí konzoly Správa balíčků nainstalujte Microsoft.Azure.CosmosDB.Table balíček a jeho závislosti. Chcete-li to provést, zadejte následující příkaz v konzole Správce balíčků pro vaše řešení. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Před instalací Microsoft.Azure.CosmosDB.Table pomocí váš preferovaný nástroj pro správu balíčků NuGet nainstalujte balíček Microsoft.Azure.Storage.Common NuGet.

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o rozhraní Azure Cosmos DB Table API najdete v tématu [Úvod do služby Azure Cosmos DB Table API](table-introduction.md). 
