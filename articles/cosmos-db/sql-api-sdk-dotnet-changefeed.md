---
title: 'Azure Cosmos DB: API procesoru informační kanál změn .NET, SDK & zdroje'
description: Další informace o rozhraní API procesoru kanálu změn a sady SDK, včetně data vydání, vyřazení dat a změny provedené mezi každou verzi sady SDK pro procesor informační kanál změn .NET.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 0f6fff5047bc72fa1171e06bb2f160196ecef807
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55300604"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET change Feed procesoru SDK: Stáhněte si a poznámky k verzi
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Kanál změn .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Dokumentace k rozhraní API**|[Změnit referenční dokumentace rozhraní API knihovny Feed Processor](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Začínáme**|[Začínáme se sadou změn kanálu procesoru .NET SDK](change-feed.md)|
|**Aktuální podporované architektury**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Poznámky k verzi

### <a name="v2-builds"></a>sestavení v2

### <a name="a-name226226"></a><a name="2.2.6"/>2.2.6
* Vylepšené zpracování výjimek pozorovatele.
* Podrobnější informace o chybách pozorovatele:
 * Při zavření pozorovatel z důvodu výjimky vyvolané ProcessChangesAsync pozorovatele, CloseAsync teď budete přijímat důvod parametrem nastaveným na ChangeFeedObserverCloseReason.ObserverError.
 * Přidání trasy k identifikaci chyb v uživatelském kódu v pozorovatele.

### <a name="a-name225225"></a><a name="2.2.5"/>2.2.5
* Přidání podpory pro zpracování rozdělit do kolekce, které používají sdílenou databázi propustnost.
  * Tato vydaná verze opravuje problém, ke kterému může dojít během rozdělení v kolekcích použití sdílené databáze propustnost při výsledek rozdělení do oddílů znovu vyrovnávání s rozsah klíčů pouze jeden podřízený prvek oddílu vytvořili místo dvou. Pokud k tomu dojde, Change Feed Processor můžou uváznout odstranit zapůjčení pro staré rozsah klíče oddílu a ne vytvořit nové zapůjčení. Problém je vyřešen v této verzi.

### <a name="a-name224224"></a><a name="2.2.4"/>2.2.4
* Přidání nové vlastnosti ChangeFeedProcessorOptions.StartContinuation pro podporu počáteční změnit informační kanál z žádosti o token pro pokračování. To se používá pouze při zapůjčení kolekce je prázdná nebo zapůjčení nemá ContinuationToken nastavit. Zapůjčení v kolekci zapůjčení, které mají ContinuationToken nastavit se používá token ContinuationToken a ChangeFeedProcessorOptions.StartContinuation se ignoruje.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Přidání podpory pro použití vlastního úložiště a zachová tokeny pokračování na oddíl.
  * Například vlastní zapůjčení úložiště lze rozdělit na oddíly nijak vlastního kolekci zapůjčení služby Azure Cosmos DB.
  * Vlastní zapůjčení úložiště můžete použít nové ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) bodu rozšiřitelnosti a ILeaseStoreManager veřejného rozhraní.
  * Rozhraní ILeaseManager teď vyčleněný do více rolí rozhraní.
* Vedlejší narušující změna: odebrání rozšíření bodu ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), místo toho použijte ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager).

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Tato vydaná verze opravuje problém, ke které dojde během zpracování rozdělení v monitorované kolekci a pomocí zapůjčení dělené kolekce. Při zpracování zapůjčení pro rozdělení oddílů, se možná neodstranily zapůjčení odpovídající daného oddílu. Problém je vyřešen v této verzi.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Opravili jsme Estimator výpočtu pro hlavní server s více účty a nový formát tokenu relace.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Přidání podpory pro zapůjčení dělené kolekce. Klíč oddílu musí být definován jako /id.
* Vedlejší narušující změna: metody rozhraní IChangeFeedDocumentClient a třída ChangeFeedDocumentClient byly změněny na zahrnout RequestOptions a CancellationToken parametry. IChangeFeedDocumentClient je pokročilá rozšíření bod, který umožní, abychom vám poskytli vlastní implementaci klientem dokumentu pomocí Change Feed Processor, třeba vyplnění DocumentClient a zachytit všechna volání do něj udělat dodatečné trasování, zpracování chyb , atd. S touto aktualizací se kód, který implementovat IChangeFeedDocumentClient muset být změněna, aby zahrnovala nové parametry v implementaci.
* Vylepšení diagnostiky podverze.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Added new API, Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). To je možné získat odhad práce pro každý oddíl.
* Podporuje Microsoft.Azure.DocumentDB SDK 2.0. Vyžaduje Microsoft.Azure.DocumentDB 2.0 nebo novější.

### <a name="a-name206206"></a><a name="2.0.6"/>2.0.6
* Přidání ChangeFeedEventHost.HostName veřejné vlastnosti pro kompatibilitu s v1.

### <a name="a-name205205"></a><a name="2.0.5"/>2.0.5
* Opravili jsme časování, ke kterému dochází rozdělení oddílů. Časování může vést k získání zapůjčení a okamžitě ztráty během rozdělení oddílů a způsobí kolizi. Problém stav soupeření vyřešen v této vydané verzi.

### <a name="a-name204204"></a><a name="2.0.4"/>2.0.4
* GA SDK

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-prerelease
* Opravené následující problémy:
  * Když se stane rozdělení oddílů, může dojít k duplicitní zpracování dokumentů před rozdělení.
  * GetEstimatedRemainingWork API vrátilo 0, pokud žádné zapůjčení, se vyskytoval v kolekci zapůjčení.

* Následující výjimky jsou zveřejněny. Rozšíření, které implementují IPartitionProcessor může vyvolat tyto výjimky.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Menší změny rozhraní API:
  * Odebrat ChangeFeedProcessorOptions.IsAutoCheckpointEnabled, které bylo označené jako zastaralé.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Vylepšení stability:
  * Lepší zpracování inicializace úložiště zapůjčení. Pokud zapůjčení úložiště je prázdné, pouze jednu instanci procesoru inicializovat, bude čekat na ostatních.
  * Další obnovení zapůjčení stable/efektivní a verze. Prodlužuje se platnost a uvolnění zapůjčení jeden oddíl je nezávislý na ostatních obnovení. Ve v1, který bylo provedeno postupně pro všechny oddíly.
* Nové rozhraní API v2:
  * Tvůrce vzor pro flexibilní konstrukce procesoru: ChangeFeedProcessorBuilder třídy.
    * Můžete využít libovolnou kombinaci parametrů.
    * Může trvat instance DocumentClient pro monitorování a/nebo zapůjčení kolekci (není k dispozici ve v1).
  * IChangeFeedObserver.ProcessChangesAsync now takes CancellationToken.
  * IRemainingWorkEstimator - odhad zbývající práce, je možné samostatně z procesoru.
  * Nové rozšíření body:
    * IPartitionLoadBalancingStrategy - pro vlastní Vyrovnávání zatížení oddílů mezi instancemi procesoru.
    * ILease, ILeaseManager – pro správu vlastní zapůjčení.
    * IPartitionProcessor - pro vlastní zpracování změn na oddíl.
* Protokolování - využívá [LibLog](https://github.com/damianh/LibLog) knihovny.
* 100 % zpětně kompatibilní s rozhraním API v1.
* Nové základu kódu.
* Kompatibilní s [SQL SDK pro .NET](sql-api-sdk-dotnet.md) verze 1.21.1 a vyšší.

### <a name="v1-builds"></a>sestavení V1

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Přidat další protokolování.
* Oprava DocumentClient nevracení při volání odhadu nedokončené práce více než jednou.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Opravy v odhadu nedokončené práce.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Vylepšení stability.
  * Oprava pro zpracování problém zrušené úlohy, které by mohly vést k zastavení pozorovatelů na několik oddílů.
* Podpora pro ruční vytváření kontrolních bodů.
* Kompatibilní s [SQL SDK pro .NET](sql-api-sdk-dotnet.md) verze 1.21 a vyšší.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Přidává podporu pro .NET Standard 2.0. Balíček teď podporuje `netstandard2.0` a `net451` framework monikery.
* Kompatibilní s [SQL SDK pro .NET](sql-api-sdk-dotnet.md) verze 1.17.0 a vyšší.
* Kompatibilní s [SQL SDK pro .NET Core](sql-api-sdk-dotnet-core.md) verze 1.5.1 a vyšší.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Řeší problém s výpočtu odhadu zbývající práce při Change Feed byla prázdná nebo žádná práce byla čekající na vyřízení.
* Kompatibilní s [SQL SDK pro .NET](sql-api-sdk-dotnet.md) verze 1.13.2 a vyšší.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Přidat metodu k získání odhad zbývající práce na zpracování v Change Feed.
* Kompatibilní s [SQL SDK pro .NET](sql-api-sdk-dotnet.md) verze 1.13.2 a vyšší.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Kompatibilní s [SQL SDK pro .NET](sql-api-sdk-dotnet.md) verze 1.14.1 a nižší.


## <a name="release--retirement-dates"></a>Datum vydání verze a vyřazení z provozu
Microsoft bude poskytovat oznámení alespoň **12 měsíců** před vyřazením z provozu sady SDK ulehčení přechodu na verzi novější nebo podporované.

Nové funkce a funkce a optimalizace jsou přidány pouze aktuální sadu SDK, proto se doporučuje, vždy upgradovat na nejnovější SDK verzi co možná nejdříve. 

Jakoukoli žádost do služby Cosmos DB pomocí vyřazeno sady SDK budou odmítnuty službou.

<br/>

| Verze | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
| [2.2.6](#2.2.6) |29. ledna 2019 |--- |
| [2.2.5](#2.2.5) |13. prosince 2018 |--- |
| [2.2.4](#2.2.4) |29. listopadu 2018 |--- |
| [2.2.3](#2.2.3) |19. listopadu 2018 |--- |
| [2.2.2](#2.2.2) |31. října 2018 |--- |
| [2.2.1](#2.2.1) |24. října 2018 |--- |
| [1.3.3](#1.3.3) |08. května 2018 |--- |
| [1.3.2](#1.3.2) |18. dubna 2018 |--- |
| [1.3.1](#1.3.1) |13. března 2018 |--- |
| [1.2.0](#1.2.0) |31. října 2017 |--- |
| [1.1.1](#1.1.1) |29. srpna 2017 |--- |
| [1.1.0](#1.1.0) |13. srpna 2017 |--- |
| [1.0.0](#1.0.0) |07. července 2017 |--- |


## <a name="faq"></a>Nejčastější dotazy
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o službě Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby. 

