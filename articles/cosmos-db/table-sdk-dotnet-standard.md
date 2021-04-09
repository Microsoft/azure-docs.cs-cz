---
title: Prostředky & Azure Cosmos DB rozhraní API pro tabulky .NET Standard SDK
description: Seznamte se s Azure Cosmos DB rozhraní API pro tabulky a .NET Standard SDK, včetně dat vydání, data vyřazení a změn provedených mezi jednotlivými verzemi.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.custom: devx-track-dotnet
ms.openlocfilehash: f41a895aef3476386b8b530fc3e265ffaa21e8c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597578"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET Standard API: stažení a poznámky k verzi
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   | Odkazy  |
|---|---|
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Ukázka**|[Ukázka Cosmos DB rozhraní API pro tabulky .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Rychlý start**|[Rychlý start](create-table-dotnet.md)|
|**Kurz**|[Kurz](tutorial-develop-table-dotnet.md)|
|**Aktuální podporovaná architektura**|[Microsoft .NET Standard 2,0](https://www.nuget.org/packages/NETStandard.Library)|
|**Nahlásit problém**|[Nahlásit problém](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Poznámky k verzi pro řady 2.0.0
2.0.0 Series přebírá závislost na [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)s vylepšeními výkonu a konsolidací oboru názvů na Cosmos DB koncový bod.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0 – Preview
* počáteční verze Preview sady 2.0.0 Table SDK, která přebírá závislost na [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)s vylepšeními výkonu a konsolidací oboru názvů na Cosmos DB koncový bod. Veřejné rozhraní API zůstává stejné.

## <a name="release-notes-for-100-series"></a>Poznámky k verzi pro řady 1.0.0
1.0.0 Series přebírá závislost na [Microsoft.Azure.DocumentDB. Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="108"></a><a name="1.0.8"></a>1.0.8
* Přidání podpory pro nastavení vlastnosti TTL, pokud se jedná o koncový bod cosmosdb 
* Dodržovat zásady opakování po vypršení časového limitu a zrušení úlohy
* Oprava přerušované přerušované úlohy v aplikacích ASP .NET se zobrazila výjimka
* Oprava načtení služby Azure Table Storage ze sekundárního koncového bodu pouze v režimu umístění
* Aktualizovat `Microsoft.Azure.DocumentDB.Core` verzi závislosti na 2.11.2, která řeší přerušovanou výjimku nulového odkazu
* Aktualizovat `Odata.Core` verzi závislosti na 7.6.4, která opravuje konflikt kompatibility s prostředím Azure Shell

### <a name="107"></a><a name="1.0.7"></a>1.0.7
* Zlepšení výkonu nastavením sady Table SDK výchozí úroveň trasování na SourceLevels. off, kterou lze zařadit prostřednictvím app.config

### <a name="105"></a><a name="1.0.5"></a>1.0.5
* Zaveďte novou konfiguraci v části TableClientConfiguration, která bude používat vykonavatele REST ke komunikaci s Cosmos DB rozhraní API pro tabulky

### <a name="105-preview"></a><a name="1.0.5-preview"></a>1.0.5 – Preview
* Opravy chyb

### <a name="104"></a><a name="1.0.4"></a>1.0.4
* Opravy chyb
* Poskytněte možnost HttpClientTimeout pro RestExecutorConfiguration.

### <a name="104-preview"></a><a name="1.0.4-preview"></a>1.0.4 – Preview
* Opravy chyb
* Poskytněte možnost HttpClientTimeout pro RestExecutorConfiguration.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* Opravy chyb

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* Verze pro obecné dostupnosti

### <a name="0110-preview"></a><a name="0.11.0-preview"></a>0.11.0 – Preview
* Byly provedeny změny, jak lze nakonfigurovat Cloudtableclient vám. Nyní během konstrukce vezme objekt TableClientConfiguration. TableClientConfiguration poskytuje různé vlastnosti pro konfiguraci chování klienta v závislosti na tom, jestli je cílový koncový bod Cosmos DB rozhraní API pro tabulky nebo Azure Storage rozhraní API pro tabulky.
* Přidání podpory do TableQuery pro vrácení výsledků v setříděném pořadí ve vlastním sloupci Tato funkce je podporována pouze u Cosmos DBch koncových bodů tabulky.
* Přidání podpory pro vystavení RequestCharges na různých typech výsledků. Tato funkce je podporována pouze u Cosmos DBch koncových bodů tabulky.

### <a name="0101-preview"></a><a name="0.10.1-preview"></a>0.10.1 – Preview
* Přidání podpory pro tokeny SAS, operace TablePermissions, ServiceProperties a ServiceStats do koncových bodů tabulky Azure Storage. 
   > [!NOTE]
   > Některé funkce v předchozích sadách SDK pro Azure Storage tabulek ještě nejsou podporované, třeba šifrování na straně klienta.

### <a name="0100-preview"></a><a name="0.10.0-preview"></a>0.10.0 – Preview
* Přidání podpory pro základní operace CRUD, Batch a dotazu proti Azure Storage koncovým bodům tabulky 
   > [!NOTE]
   > Některé funkce v předchozích sadách SDK pro Azure Storage tabulek ještě nejsou podporované, třeba šifrování na straně klienta.

### <a name="091-preview"></a><a name="0.9.1-preview"></a>0.9.1 – Preview
* Sada Azure Cosmos DB Table .NET Standard SDK je knihovna .NET pro různé platformy, která poskytuje efektivní přístup k datovému modelu tabulky v Cosmos DB. Tato počáteční verze podporuje úplnou sadu tabulek a entit CRUD + Query s podobnými rozhraními API, jako je [sada SDK pro Cosmos DB tabulku pro .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Ve verzi 0.9.1-Preview se zatím nepodporují koncové body tabulky Azure Storage.

## <a name="release-and-retirement-dates"></a>Data o uvolnění a vyřazení
Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením sady SDK z důvodu hladkého přechodu na novější/podporovanou verzi.

Tato knihovna .NET Standard knihovně [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) nahradí knihovnu .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>řada 2.0.0
| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [2.0.0 – Preview](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>řada 1.0.0
| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13. září 2019 |--- |
| [1.0.5 – Preview](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4 – Preview](#1.0.4-preview) |26. července 2019 |--- |
| 1.0.2 – Preview |2. května 2019 |--- |
| [1.0.1](#1.0.1) |19. dubna 2019 |--- |
| [1.0.0](#1.0.0) |13. března 2019 |--- |
| [0.11.0 – Preview](#0.11.0-preview) |5. března 2019 |--- |
| [0.10.1 – Preview](#0.10.1-preview) |22. ledna 2019 |--- |
| [0.10.0 – Preview](#0.10.0-preview) |18. prosince 2018 |--- |
| [0.9.1 – Preview](#0.9.1-preview) |18. října 2018 |--- |


## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také
Další informace o rozhraní API pro tabulky Azure Cosmos DB najdete v tématu [Úvod do Azure Cosmos DB rozhraní API pro tabulky](table-introduction.md).
