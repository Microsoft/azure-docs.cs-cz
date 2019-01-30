---
title: Azure Cosmos DB Table API .NET Standard SDK a zdroje informací
description: Přečtěte si všechno o Azure Cosmos DB Table API a .NET Standard SDK, včetně data vydání, vyřazení dat a změny provedené mezi jednotlivými verzemi.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/18/2018
ms.openlocfilehash: ce7cc489b107ce4bd95270b9a7f8cb560a2d2398
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249642"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Standardní rozhraní .NET API tabulky Azure Cosmos DB: Stáhněte si a poznámky k verzi
> [!div class="op_single_selector"]

> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Aktuální podporované architektury**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Poznámky k verzi

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

| Verze | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [0.10.1-preview](#0.10.1-preview) |22. ledna 2019 |--- |
| [0.10.0-preview](#0.10.0-preview) |18. prosince 2018 |--- |
| [0.9.1-preview](#0.9.1-preview) |18. října 2018 |--- |


## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o rozhraní Azure Cosmos DB Table API najdete v tématu [Úvod do služby Azure Cosmos DB Table API](table-introduction.md). 
