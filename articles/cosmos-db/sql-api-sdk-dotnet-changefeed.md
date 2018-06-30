---
title: 'Azure Cosmos DB: .NET změnu kanálu procesoru API, sadu SDK a prostředky | Microsoft Docs'
description: Další informace o rozhraní API pro změnu kanálu procesoru a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi .NET změnu kanálu procesoru SDK.
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/21/2018
ms.author: maquaran
ms.openlocfilehash: f47b847b3a356540e5f366235713b8f99aea3404
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113713"
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
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - rozhraní .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Dokumentaci k rozhraní API**|[Změnit referenční dokumentace rozhraní API knihovny kanálu procesoru](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Začínáme**|[Začínáme s změnu kanálu procesoru .NET SDK](change-feed.md)|
|**Aktuální podporovaných prostředí**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Rozhraní Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Poznámky k verzi

### <a name="v2-builds"></a>sestavení v2

### <a name="a-name204204"></a><a name="2.0.4"/>verze 2.0.4
* GA SDK

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* Opravené následující problémy:
  * Když se stane rozdělení oddílů, může dojít k duplicitní zpracování dokumentů před rozdělení upravit.
  * Rozhraní API GetEstimatedRemainingWork vrátit 0, pokud žádné zapůjčení, se vyskytoval v kolekci zapůjčení.

* Následující výjimky jsou vytvářeny veřejné. Rozšíření, které implementují IPartitionProcessor můžete vyvolat tyto výjimky.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Méně závažné změny rozhraní API:
  * Odebrat ChangeFeedProcessorOptions.IsAutoCheckpointEnabled, která byla označena jako zastaralé.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Zlepšení stability:
  * Lepší zpracování inicializace úložiště zapůjčení. Pokud zapůjčení úložiště je prázdné, pouze jedna instance procesoru můžete inicializovat ho, bude čekat ostatní.
  * Další obnovení zapůjčení stabilní/efektivní a verze. Obnovení a uvolněním jeden oddíl zapůjčení je nezávislé na jiné obnovení. V v1, které bylo provedeno postupně pro všechny oddíly.
* Nové rozhraní API v2:
  * Tvůrce vzor flexibilní konstrukce procesoru: Třída ChangeFeedProcessorBuilder.
    * Může trvat libovolnou kombinací parametrů.
    * Může trvat instance DocumentClient pro monitorování a/nebo zapůjčení kolekci (není k dispozici v v1).
  * IChangeFeedObserver.ProcessChangesAsync nyní trvá CancellationToken.
  * IRemainingWorkEstimator - zbývající práce odhadu dá se použít samostatně z procesoru.
  * Nové body rozšiřitelnosti:
    * IParitionLoadBalancingStrategy - pro vlastní Vyrovnávání zatížení oddílů mezi instancemi procesoru.
    * ILease, ILeaseManager - pro správu vlastní zapůjčení.
    * IPartitionProcessor – vlastní zpracování změny na oddíl.
* Protokolování - používá [LibLog](https://github.com/damianh/LibLog) knihovny.
* 100 % zpětně kompatibilní s rozhraním API v1.
* Nový kód základní.
* Kompatibilní s [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.21.1 a vyšší.

### <a name="v1-builds"></a>sestavení V1

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Přidat další protokolování.
* Pevné nevrácenou DocumentClient při volání metody odhad čekající pracovní vícekrát.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Opravy v odhad čekající práci.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Zlepšení stability.
  * Oprava pro zpracování zrušené úlohy problém, který může vést k zastavena pozorovatelů na některé oddíly.
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
| [1.3.3](#1.3.3) |08 může 2018 |--- |
| [1.3.2](#1.3.2) |18. dubna 2018 |--- |
| [1.3.1](#1.3.1) |13. března 2018 |--- |
| [1.2.0](#1.2.0) |31. října 2017 |--- |
| [1.1.1](#1.1.1) |29 srpen 2017 |--- |
| [1.1.0](#1.1.0) |13 srpen 2017 |--- |
| [1.0.0](#1.0.0) |07 července 2017 |--- |


## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby. 

