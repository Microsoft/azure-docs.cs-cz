---
title: Azure Cosmos DB Table API .NET Standard SDK & prostředky
description: Přečtěte si vše o rozhraní Azure Cosmos DB Table API a .NET Standard SDK včetně dat vydání, dat vyřazení a změn provedených mezi jednotlivými verzemi.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 48fd85e27feb3d0c7f7f722dbbd502be55684385
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771594"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Tabulka DB Azure Cosmos .NET Standard API: Poznámky ke stažení a verzi
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK ke stažení**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Ukázka**|[Ukázka rozhraní API tabulky Cosmos DB .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Quickstart**|[Quickstart](create-table-dotnet.md)|
|**Tutorial**|[Tutorial](tutorial-develop-table-dotnet.md)|
|**Aktuální podporovaný rámec**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Problém sestavy**|[Problém sestavy](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Poznámky k verzi pro řadu 2.0.0
Řada 2.0.0 přebírá závislost na [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)s vylepšeními výkonu a konsolidací oboru názvů do koncového bodu Cosmos DB.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-náhled
* počáteční náhled sady SDK tabulky 2.0.0, která přebírá závislost na [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), s vylepšeními výkonu a konsolidací oboru názvů do koncového bodu Cosmos DB. Veřejné rozhraní API zůstává stejné.

## <a name="release-notes-for-100-series"></a>Poznámky k verzi pro řadu 1.0.0
Řada 1.0.0 přebírá závislost na [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="105"></a><a name="1.0.5"/>1.0.5
* Zavedení nové konfigurace v části TableClientConfiguration pro použití rest executoru ke komunikaci s rozhraním COSMOS DB Table API

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5-náhled
* Opravy chyb

### <a name="104"></a><a name="1.0.4"/>1.0.4
* Opravy chyb
* Zadejte možnost HttpClientTimeout pro RestExecutorConfiguration.

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4-náhled
* Opravy chyb
* Zadejte možnost HttpClientTimeout pro RestExecutorConfiguration.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Opravy chyb

### <a name="100"></a><a name="1.0.0"/>1.0.0
* Verze obecné dostupnosti

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0-náhled
* Byly provedeny změny, jak cloudtableclient lze nakonfigurovat. Nyní trvá TableClientConfiguration objektu během výstavby. TableClientConfiguration poskytuje různé vlastnosti pro konfiguraci chování klienta v závislosti na tom, zda cílový koncový bod je Cosmos DB Table API nebo Rozhraní API tabulky úložiště Azure.
* Přidána podpora TableQuery pro vrácení výsledků v seřazené mač ve vlastním sloupci. Tato funkce je podporována pouze v koncových bodech tabulky Cosmos DB.
* Přidána podpora pro vystavení RequestCharges na různých typech výsledků. Tato funkce je podporována pouze v koncových bodech tabulky Cosmos DB.

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1-náhled
* Přidejte podporu pro token SAS, operace TablePermissions, ServiceProperties a ServiceStats proti koncovým bodům tabulky úložiště Azure. 
   > [!NOTE]
   > Některé funkce v předchozích sadách Azure Storage Table SDK ještě nejsou podporované, jako je šifrování na straně klienta.

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0-náhled
* Přidejte podporu pro základní crud, dávkové operace a operace dotazů oproti koncovým bodům tabulky úložiště Azure. 
   > [!NOTE]
   > Některé funkce v předchozích sadách Azure Storage Table SDK ještě nejsou podporované, jako je šifrování na straně klienta.

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1-náhled
* Azure Cosmos DB Table .NET Standard SDK je knihovna .NET napříč platformami, která poskytuje efektivní přístup k datovému modelu Tabulka v Cosmos DB. Tato počáteční verze podporuje úplnou sadu funkcí TABULKA a Entity CRUD + Query s podobnými rozhraními API jako [cosmos DB Table SDK pro rozhraní .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Koncové body tabulky úložiště Azure ještě nejsou podporované ve verzi s verzí s 0.9.1-Preview.

## <a name="release-and-retirement-dates"></a>Datum uvolnění a odchodu do důchodu
Společnost Microsoft poskytuje oznámení nejméně **12 měsíců** před vyřazením sady SDK za účelem vyhlazení přechodu na novější/podporovanou verzi.

Tato knihovna [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) pro různé platformy nahradí knihovnu Rozhraní .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>Řada 2.0.0
| Version | Datum vydání | Datum odchodu do důchodu |
| --- | --- | --- |
| [2.0.0-náhled](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>Řada 1.0.0
| Version | Datum vydání | Datum odchodu do důchodu |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13. září 2019September 13, 2019 |--- |
| [1.0.5-náhled](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-náhled](#1.0.4-preview) |26. července 2019July 26, 2019 |--- |
| 1.0.2-náhled |2. května 2019 |--- |
| [1.0.1](#1.0.1) |19. dubna 2019April 19, 2019 |--- |
| [1.0.0](#1.0.0) |13. března 2019 |--- |
| [0.11.0-náhled](#0.11.0-preview) |5. března 2019 |--- |
| [0.10.1-náhled](#0.10.1-preview) |22. ledna 2019 |--- |
| [0.10.0-náhled](#0.10.0-preview) |18. prosince 2018December 18, 2018 |--- |
| [0.9.1-náhled](#0.9.1-preview) |18. října 2018October 18, 2018 |--- |


## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také
Další informace o rozhraní API tabulky Azure Cosmos DB najdete [v tématu Úvod do rozhraní API tabulky Db Azure Cosmos](table-introduction.md).
