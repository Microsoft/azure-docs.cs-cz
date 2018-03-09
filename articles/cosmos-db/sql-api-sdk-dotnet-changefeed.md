---
title: "Azure Cosmos DB: .NET změnu kanálu procesoru API, sadu SDK a prostředky | Microsoft Docs"
description: "Další informace o rozhraní API pro změnu kanálu procesoru a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi .NET změnu kanálu procesoru SDK."
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kirillg
editor: mimig1
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/07/2018
ms.author: maquaran
ms.openlocfilehash: 7e53cf67bc6e03a5f45de5ad4e23ff91624874f4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Informační kanál procesor změnu .NET SDK: Stažení a poznámky k verzi
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Informační kanál změnu rozhraní .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

|   |   |
|---|---|
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Dokumentaci k rozhraní API**|[Změnit referenční dokumentace rozhraní API knihovny kanálu procesoru](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Začínáme**|[Začínáme s změnu kanálu procesoru .NET SDK](change-feed.md)|
|**Aktuální podporovaných prostředí**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Rozhraní Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Poznámky k verzi

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Zlepšení stability.
* Podpora pro ruční vytváření kontrolních bodů.
* Kompatibilní s [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.21 a vyšší.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Přidává podporu pro standardní rozhraní .NET 2.0. Balíček teď podporuje `netstandard2.0` a `net451` monikery framework.
* Kompatibilní s [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.17.0 a vyšší.
* Kompatibilní s [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) verze 1.5.1 a vyšší.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Řeší problém s výpočtu odhad zbývající práce při změnu kanálu byla prázdná nebo žádná práce se čekalo na zpracování.
* Kompatibilní s [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.13.2 a vyšší.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Přidat metodu k získání odhad zbývající práce mají být zpracovány v kanálu změnu.
* Kompatibilní s [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.13.2 a vyšší.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Kompatibilní s [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.14.1 a níže.

## <a name="release--retirement-dates"></a>Verze & vyřazení kalendářních dat
Microsoft bude poskytovat oznámení alespoň **dobu 12 měsíců** předem vyřazení sady SDK k funkce smooth přechodu na novější nebo podporované verzi.

Nové funkce a funkce a optimalizace, jsou přidány pouze v aktuální sadě SDK, jako takový se doporučuje, aby vždy upgradu na nejnovější verze sady SDK v míře. 

Každá žádost o DB Cosmos pomocí vyřazeno sady SDK budou odmítnuty službou.

<br/>

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [1.3.0](#1.3.0) |07 března 2018 |--- |
| [1.2.0](#1.2.0) |31. října 2017 |--- |
| [1.1.1](#1.1.1) |29 srpen 2017 |--- |
| [1.1.0](#1.1.0) |13 srpen 2017 |--- |
| [1.0.0](#1.0.0) |07 července 2017 |--- |


## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby. 

