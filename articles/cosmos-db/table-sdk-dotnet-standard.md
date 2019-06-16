---
title: Azure Cosmos DB Table API .NET Standard SDK a zdroje informací
description: Přečtěte si všechno o Azure Cosmos DB Table API a .NET Standard SDK, včetně data vydání, vyřazení dat a změny provedené mezi jednotlivými verzemi.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 1c9894772f440a568cbc08890feb961471d84137
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480218"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Standardní rozhraní .NET API tabulky Azure Cosmos DB: Stáhněte si a poznámky k verzi
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Ukázka**|[Ukázka .NET cosmos DB Table API](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Rychlý start**|[Rychlý start](create-table-dotnet.md)|
|**Kurz**|[Kurz](tutorial-develop-table-dotnet.md)|
|**Aktuální podporované architektury**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Opravy chyb

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Verzi všeobecné dostupnosti

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-Preview
* Byly provedeny změny konfigurace CloudTableClient. Nyní využívá objektu TableClientConfiguration během konstrukce. TableClientConfiguration poskytuje různé vlastnosti konfigurace chování klienta v závislosti na tom, jestli je cílový koncový bod Cosmos DB Table API nebo rozhraní API tabulky Azure Storage.
* Přidání podpory pro TableQuery vracejí výsledky v seřazeném pořadí na vlastní sloupec. Tato funkce je podporována pouze na koncové body Cosmos DB Table.
* Přidání podpory pro vystavení RequestCharges na různé typy výsledků. Tato funkce je podporována pouze na koncové body Cosmos DB Table.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Přidání podpory pro SAS token, operace TablePermissions ServiceProperties a ServiceStats proti koncovým bodům Azure Storage Table. 
   > [!NOTE]
   > Některé funkce v předchozí úložiště tabulky sady SDK služby Azure se zatím nepodporují, jako je například šifrování na straně klienta.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-Preview
* Přidání podpory pro jádro CRUD, batch a operace dotazů proti koncovým bodům Azure Storage Table. 
   > [!NOTE]
   > Některé funkce v předchozí úložiště tabulky sady SDK služby Azure se zatím nepodporují, jako je například šifrování na straně klienta.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* Azure Cosmos DB Table .NET Standard SDK je knihovna .NET napříč platformami, která poskytuje efektivní přístup do tabulky datového modelu ve službě Cosmos DB. Tato počáteční verze podporuje úplnou sadu tabulky a Entity CRUD + funkce dotazu se podobá rozhraní API, jako [Cosmos DB Table SDK pro rozhraní .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Koncové body Azure Storage Table se zatím nepodporují 0.9.1-preview verze.

## <a name="release-and-retirement-dates"></a>Datum vydání verze a vyřazení z provozu
Společnost Microsoft poskytuje oznámení alespoň **12 měsíců** před vyřazením z provozu sady SDK ulehčení přechodu na verzi novější nebo podporované.

Tato knihovna .NET Standard multiplatformní [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) nahradí knihovně rozhraní .NET Framework [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

| Version | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [1.0.1](#1.0.1) |19\. dubna 2019 |--- |
| [1.0.0](#1.0.0) |13\. března 2019 |--- |
| [0.11.0-Preview](#0.11.0-preview) |5\. března 2019 |--- |
| [0.10.1-preview](#0.10.1-preview) |22\. ledna 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18\. prosince 2018 |--- |
| [0.9.1-preview](#0.9.1-preview) |18\. října 2018 |--- |


## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o rozhraní Azure Cosmos DB Table API najdete v tématu [Úvod do služby Azure Cosmos DB Table API](table-introduction.md). 
