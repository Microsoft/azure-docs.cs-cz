---
title: Rozhraní Azure Cosmos DB Table API .NET SDK & prostředky
description: Přečtěte si vše o rozhraní Azure Cosmos DB Table API, včetně dat vydání, dat vyřazení a změn provedených mezi jednotlivými verzemi.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: 5a5305ffd388d2573d250d93131c1fed236008b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771615"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Tabulka DB Azure Cosmos .NET API: Poznámky ke stažení a verzi

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK ke stažení**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Quickstart**|[Azure Cosmos DB: Vytvoření aplikace s rozhraním .NET a rozhraním TABLE API](create-table-dotnet.md)|
|**Tutorial**|[Azure Cosmos DB: Vývoj v .NET s využitím rozhraní Table API](tutorial-develop-table-dotnet.md)|
|**Aktuální podporovaný rámec**|[Rozhraní Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Sada .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) je v režimu údržby a brzy se zastaralá. Chcete-li pokračovat v získání nejnovějších funkcí podporovaných rozhraním Table API, upgradujte na novou knihovnu Standard .NET Standard [Microsoft.Azure.Cosmos.Table.](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)

> Pokud jste vytvořili účet Table API během období Preview, vytvořte [nový účet Table API](create-table-dotnet.md#create-a-database-account) pro práci s obecně dostupnými sadami Table API SDK.
>

## <a name="release-notes"></a>Poznámky k verzi

### <a name="212"></a><a name="2.1.2"/>2.1.2

* Opravy chyb

### <a name="210"></a><a name="2.1.0"/>2.1.0

* Opravy chyb

### <a name="200"></a><a name="2.0.0"/>2.0.0

* Přidána podpora zápisu ve více regionech
* Opraveny závislosti balíčků NuGet na microsoft.azure.documentdb, microsoft.odata.core, microsoft.odata.edm, microsoft.spatial

### <a name="113"></a><a name="1.1.3"/>1.1.3

* Opraveny závislosti balíčků NuGet na Microsoft.Azure.Storage.Common a Microsoft.Azure.DocumentDB.
* Opravy chyb při serializaci tabulky při konfiguraci nastavení JsonConvert.DefaultSettings.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Přidáno ověření poškozených ETAGů v přímém režimu.
* Opravena chyba dotazu LINQ v režimu brány.
* Synchronní rozhraní API nyní běží ve fondu vláken s Sync.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Přidat TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism a TableQueryContinuationTokenLimitInKb do TableRequestOptions
* Opravy chyb

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Verze obecné dostupnosti

### <a name="090-preview"></a><a name="0.1.0-preview"/>0.9.0-náhled

* Počáteční verze Preview

## <a name="release-and-retirement-dates"></a>Datum uvolnění a odchodu do důchodu

Společnost Microsoft poskytuje oznámení nejméně **12 měsíců** před vyřazením sady SDK za účelem vyhlazení přechodu na novější/podporovanou verzi.

Knihovna `Microsoft.Azure.CosmosDB.Table` je aktuálně k dispozici pouze pro rozhraní .NET Framework a je v režimu údržby a brzy se zastarala. Nové funkce a funkce a optimalizace jsou přidány pouze do knihovny .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), jako takové je doporučeno upgradovat na [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

Balíček [náhledu WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) byl zastaralá. 15. listopadu 2018 bude sada WindowsAzure.Storage-PremiumTable SDK vyřazena, kdy nebudou povoleny požadavky na vyřazenou sadu SDK. 

Všechny požadavky na Azure Cosmos DB pomocí vyřazené sady SDK jsou odmítnuty službou.
<br/>

| Version | Datum vydání | Datum odchodu do důchodu |
| --- | --- | --- |
| [2.1.2](#2.1.2) |16. září 2019 September 16, 2019| |
| [2.1.0](#2.1.0) |22. ledna 2019|Duben 01, 2020 |
| [2.0.0](#2.0.0) |26. září 2018September 26, 2018|Březen 01, 2020 |
| [1.1.3](#1.1.3) |17. července 2018|Prosinec 01, 2019 |
| [1.1.1](#1.1.1) |26. března 2018|Prosinec 01, 2019 |
| [1.1.0](#1.1.0) |21. února 2018 February 21, 2018|Prosinec 01, 2019 |
| [1.0.0](#1.0.0) |15. listopadu 2017|15. listopadu 2019November 15, 2019 |
| 0.9.0-náhled |11. listopadu 2017November 11, 2017 |11. listopadu 2019November 11, 2019 |

## <a name="troubleshooting"></a>Řešení potíží

Pokud se zobrazí chyba 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

Při pokusu o použití balíčku Microsoft.Azure.CosmosDB.Table NuGet máte dvě možnosti, jak problém vyřešit:

* Pomocí konzoly pro správu balíčků nainstalujte balíček Microsoft.Azure.CosmosDB.Table a jeho závislosti. Chcete-li to provést, zadejte následující do konzole Správce balíčků pro vaše řešení. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Pomocí upřednostňovaného nástroje pro správu balíčků NuGet nainstalujte před instalací sady Microsoft.Azure.CosmosDB.Table balíček Microsoft.Azure.Storage.Common NuGet.

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také

Další informace o rozhraní API tabulky Azure Cosmos DB najdete [v tématu Úvod do rozhraní API tabulky Db Azure Cosmos](table-introduction.md). 
