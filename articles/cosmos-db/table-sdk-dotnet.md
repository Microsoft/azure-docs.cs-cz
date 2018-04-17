---
title: Tabulky Azure CosmosDB rozhraní API .NET SDK & prostředky | Microsoft Docs
description: Další informace o Cosmos DB tabulky rozhraní API služby Azure včetně data vydání, vyřazení dat a změny provedené mezi každou verzi.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/26/2018
ms.author: rnagpal
ms.openlocfilehash: 7e012e07b8f93554ea44404c611a7bc0eb64a0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB tabulky .NET API: Stažení a poznámky k verzi
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Stažení sady SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Dokumentaci k rozhraní API**|[Referenční dokumentace rozhraní API .NET](https://aka.ms/acdbtableapiref)|
|**Rychlý start**|[Azure Cosmos DB: Sestavení aplikace pomocí rozhraní .NET a Table API](create-table-dotnet.md)|
|**Kurz**|[Azure Cosmos DB: Vývoj s tabulkou rozhraní API v rozhraní .NET](tutorial-develop-table-dotnet.md)|
|**Aktuální podporovaných prostředí**|[Rozhraní Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Pokud jste vytvořili účet Table API během období Preview, vytvořte [nový účet Table API](create-table-dotnet.md#create-a-database-account) pro práci s obecně dostupnými sadami Table API SDK.
>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Přidání ověření poškozený značky etag binárním rozsáhlým v režimu přímého.
* Opravené chyby dotazu LINQ v režimu brány.
* Synchronní rozhraní API ve fondu vláken s SynchronizationContext nyní spustit.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Přidání TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism a TableQueryContinuationTokenLimitInKb do TableRequestOptions
* Opravy chyb

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Obecné dostupnosti verze

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-Preview
* Počáteční verze Preview

## <a name="release-and-retirement-dates"></a>Datum vydání a vyřazování z provozu
Společnost Microsoft poskytuje oznámení alespoň **dobu 12 měsíců** předem vyřazení sady SDK k funkce smooth přechodu na novější nebo podporované verzi.

[WindowsAzure.Storage PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) preview balíček je zastaralý a nahrazuje [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) balíčku. Sadu SDK WindowsAzure.Storage PremiumTable vyřadí na 15. listopadu 2018, na dobu žádosti není povoleno vyřazeno SDK.

Nové funkce a funkce a optimalizace, jsou přidány pouze v aktuální sadě SDK, jako takový se doporučuje, aby vždy upgradu na nejnovější verze sady SDK v míře. 

Služba odmítne všechny požadavky pro Azure DB Cosmos pomocí vyřazeno sady SDK.
<br/>

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [1.1.1](#1.1.1) |26 března 2018|--- |
| [1.1.0](#1.1.0) |21. února 2018|--- |
| [1.0.0](#1.0.0) |15 listopadu 2017|--- |
| [0.9.0-preview](#0.9.0-preview) |11 listopadu 2017 |--- |

## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k chybě 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Při pokusu o použití balíčku Microsoft.Azure.CosmosDB.Table NuGet, máte dvě možnosti vyřešit problém:

* Pomocí konzoly Správa balíček nainstalovat Microsoft.Azure.CosmosDB.Table balíček a jeho závislé součásti. Chcete-li to provést, zadejte následující příkaz v konzole Správce balíčků pro vaše řešení. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* Nainstalujte balíček Microsoft.Azure.Storage.Common Nuget před instalací Microsoft.Azure.CosmosDB.Table se pomocí vaší upřednostňované nástroj pro správu balíček Nuget.

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o rozhraní API služby Azure DB Cosmos tabulky, najdete v části [Úvod do rozhraní API služby Azure Cosmos DB tabulky](table-introduction.md). 
