---
title: Azure Cosmos DB rozhraní API pro tabulky & prostředků sady .NET SDK
description: Přečtěte si všechno o Azure Cosmos DB rozhraní API pro tabulky pro .NET, včetně dat o verzích, data odchodu a změn provedených mezi jednotlivými verzemi.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.custom: devx-track-dotnet
ms.openlocfilehash: 5af77f34f8e82edf187c1a68b61995ec5c82a8d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566825"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB rozhraní .NET API: stažení a poznámky k verzi
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Odkazy|
|---|---|
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**Rychlý start**|[Azure Cosmos DB: sestavení aplikace s využitím .NET a rozhraní API pro tabulky](create-table-dotnet.md)|
|**Kurz**|[Azure Cosmos DB: Vývoj v .NET s využitím rozhraní Table API](tutorial-develop-table-dotnet.md)|
|**Aktuální podporovaná architektura**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Sada .NET Framework SDK [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) je v režimu údržby a bude brzy zastaralá. Upgradujte prosím na novou .NET Standard knihovny [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , abyste mohli dál získávat nejnovější funkce, které rozhraní API pro tabulky podporuje.

> Pokud jste vytvořili účet Table API během období Preview, vytvořte [nový účet Table API](create-table-dotnet.md#create-a-database-account) pro práci s obecně dostupnými sadami Table API SDK.
>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="212"></a><a name="2.1.2"></a>2.1.2

* Opravy chyb

### <a name="210"></a><a name="2.1.0"></a>2.1.0

* Opravy chyb

### <a name="200"></a><a name="2.0.0"></a>2.0.0

* Přidání podpory pro zápis ve více oblastech
* Pevné závislosti balíčků NuGet na Microsoft.Azure.DocumentDB, Microsoft. OData. Core, Microsoft. OData. Edm, Microsoft. prostor

### <a name="113"></a><a name="1.1.3"></a>1.1.3

* Pevné závislosti balíčků NuGet v Microsoft. Azure. Storage. Common a Microsoft.Azure.DocumentDB.
* Opravy chyb při serializaci tabulky, když jsou nakonfigurovány JsonConvert. DefaultSettings.

### <a name="111"></a><a name="1.1.1"></a>1.1.1

* Bylo přidáno ověřování chybných značek ETAG v přímém režimu.
* Opravila se chyba dotazu LINQ v režimu brány.
* Synchronní rozhraní API se teď spouštějí ve fondu vláken pomocí třída SynchronizationContext.

### <a name="110"></a><a name="1.1.0"></a>1.1.0

* Přidání TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism a TableQueryContinuationTokenLimitInKb do TableRequestOptions
* Opravy chyb

### <a name="100"></a><a name="1.0.0"></a>1.0.0

* Verze pro obecné dostupnosti

### <a name="090-preview"></a><a name="0.1.0-preview"></a>0.9.0 – Preview

* Počáteční verze Preview

## <a name="release-and-retirement-dates"></a>Data o uvolnění a vyřazení

Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením sady SDK z důvodu hladkého přechodu na novější/podporovanou verzi.

`Microsoft.Azure.CosmosDB.Table`Knihovna je aktuálně dostupná jenom pro .NET Framework a je v režimu údržby a bude brzy zastaralá. Nové funkce a funkce a optimalizace se přidávají jenom do knihovny .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), protože se doporučuje upgradovat na [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

Balíček [windowsazure. Storage-Premium](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) v Preview se už nepoužívá. Sada WindowsAzure. Storage-Premium SDK se vyřadí 15. listopadu 2018, kdy se požadavky na vyřazenou sadu SDK nepovolují.

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [2.1.2](#2.1.2) |16. září 2019| |
| [2.1.0](#2.1.0) |22. ledna 2019|Duben 01, 2020 |
| [2.0.0](#2.0.0) |26. září 2018|Března 01, 2020 |
| [1.1.3](#1.1.3) |17. července 2018|1. prosince 2019 |
| [1.1.1](#1.1.1) |26. března 2018|1. prosince 2019 |
| [1.1.0](#1.1.0) |21. února 2018|1. prosince 2019 |
| [1.0.0](#1.0.0) |15. listopadu 2017|15. listopadu 2019 |
| 0.9.0 – Preview |11. listopadu 2017 |11. listopadu 2019 |

## <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí chyba 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Při pokusu o použití balíčku NuGet Microsoft. Azure. CosmosDB. Table máte dvě možnosti, jak tento problém vyřešit:

* Pomocí konzoly pro správu balíčků nainstalujte balíček Microsoft. Azure. CosmosDB. Table a jeho závislosti. Pokud to chcete provést, zadejte následující příkaz v konzole správce balíčků pro vaše řešení. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Pomocí preferovaného nástroje pro správu balíčků NuGet před instalací Microsoft. Azure. CosmosDB. Table nainstalujte balíček NuGet Microsoft. Azure. Storage. Common.

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také

Další informace o rozhraní API pro tabulky Azure Cosmos DB najdete v tématu [Úvod do Azure Cosmos DB rozhraní API pro tabulky](table-introduction.md). 
