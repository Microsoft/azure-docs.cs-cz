---
title: Rozhraní API procesoru kanálu změn Azure Cosmos DB .NET, poznámky k verzi sady SDK
description: Seznamte se s informacemi o rozhraní API procesoru kanálu změn a sadě SDK, včetně dat vydání, dat vyřazení z provozu a změn provedených mezi jednotlivými verzemi sady SDK .NET procesoru kanálu změn.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: b1f2efa887fb56d555f354c6d0a3262a7f178e29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577134"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Sada SDK .NET s procesorem kanálu změn: Stažení a poznámky k verzi
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
>
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [Sada .NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [Sada .NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Sada Java SDK v4](sql-api-sdk-java-v4.md)
> * [Sada Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sada Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Konektor Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [REST Resource Provider](/rest/api
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor – Java](sql-api-sdk-bulk-executor-java.md)

|   | Odkazy  |
|---|---|
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace k rozhraní API knihovny procesoru kanálu změn](/dotnet/api/microsoft.azure.documents.changefeedprocessor)|
|**Začínáme**|[Začínáme se sadou SDK .NET s procesorem kanálu změn](change-feed.md)|
|**Aktuální podporovaná architektura**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Pokud používáte procesor kanálu změn, podívejte se na nejnovější verzi 3.x sady [.NET SDK](change-feed-processor.md), ve které je zabudovaný kanál změn. 

## <a name="release-notes"></a>Poznámky k verzi

### <a name="v2-builds"></a>Buildy v2

### <a name="232"></a><a id="2.3.2"></a>2.3.2
* Přidali jsme kompatibilitu úložiště zapůjčení se sadou SDK v3, která umožňuje cesty migrace horké vrstvy. Aplikace může migrovat na sadu SDK v3 a zpátky do knihovny procesoru kanálu změn bez ztráty stavu.

### <a name="231"></a><a id="2.3.1"></a>2.3.1
* Opravili jsme situaci, kdy se důvod ukončení `FeedProcessing.ChangeFeedObserverCloseReason.Unknown` v případě nenalezení oblasti nebo nesouladu aktuálnosti cílové repliky s relací čtení odeslal do `FeedProcessing.IChangeFeedObserver.CloseAsync`. V těchto případech se teď používají důvody ukončení `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` a `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable`.
* Přidali jsme nový důvod ukončení `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable`, který se odešle za účelem ukončení pozorovatele kanálu změn, když není aktuálnost cílové repliky v souladu s relací čtení.

### <a name="230"></a><a id="2.3.0"></a>2.3.0
* Přidali jsme novou metodu `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` a odpovídající veřejné rozhraní `ICheckpointPartitionProcessorFactory`. To umožňuje implementaci rozhraní `IPartitionProcessor` používat integrovaný mechanismus kontrolních bodů. Nový objekt pro vytváření je podobný stávajícímu objektu `IPartitionProcessorFactory` s tím rozdílem, že jeho metoda `Create` přijímá také parametr `ILeaseCheckpointer`.
* Pro stejnou instanci `ChangeFeedProcessorBuilder` se dá použít jenom jedna z těchto dvou metod, tedy buď `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory`, nebo `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`.

### <a name="228"></a><a id="2.2.8"></a>2.2.8
* Vylepšení stability a diagnostiky:
  * Přidali jsme podporu detekce příliš dlouhé doby čtení kanálu změn. Pokud trvá delší dobu, než uvádí hodnota určená vlastností `ChangeFeedProcessorOptions.ChangeFeedTimeout`, provedou se tyto kroky:
    * Dojde k přerušení operace čtení kanálu změn v problematickém oddílu.
    * Instance procesoru kanálu změn zruší vlastnictví problematického zapůjčení. Zrušené zapůjčení se vybere během příštího kroku získání zapůjčení, který provede stejná nebo jiná instance procesoru kanálu změn. Čtení kanálu změn tak začne zase od začátku.
    * Monitoru stavu se nahlásí problém. Výchozí monitor stavu odesílá všechny nahlášené problémy do protokolu trasování.
  * Přidali jsme novou veřejnou vlastnost: `ChangeFeedProcessorOptions.ChangeFeedTimeout`. Výchozí hodnota této vlastnosti je 10 minut.
  * Přidali jsme novou veřejnou hodnotu výčtu: `Monitoring.MonitoredOperation.ReadChangeFeed`. Když je hodnota `HealthMonitoringRecord.Operation` nastavená na `Monitoring.MonitoredOperation.ReadChangeFeed`, znamená to, že daný problém se stavem souvisí se čtením kanálu změn.

### <a name="227"></a><a id="2.2.7"></a>2.2.7
* Vylepšená strategie vyrovnávání zatížení pro situaci, kdy získání všech zapůjčení trvá déle než interval vypršení platnosti zapůjčení, například kvůli problémům se sítí:
  * V tomto scénáři algoritmus vyrovnávání zatížení nesprávně považoval zapůjčení za zapůjčení s prošlou platností, což vedlo k rušení zapůjčení aktivním vlastníkům. To by mohlo způsobit zbytečné vyrovnávání mnoha zapůjčení.
  * Tento problém jsme v této verzi opravili, takže už se při konfliktech během získávání zapůjčení s prošlou platností, jejichž vlastník se nezměnil, neprovádí opakované pokusy a získání zapůjčení s prošlou platností se odkládá na další iteraci vyrovnávání zatížení.

### <a name="226"></a><a id="2.2.6"></a>2.2.6
* Vylepšené zpracování výjimek pozorovatele.
* Rozšířené informace o chybách pozorovatele:
  * Když se pozorovatel ukončí kvůli výjimce metody ProcessChangesAsync pozorovatele, metoda CloseAsync teď dostane parametr důvodu nastavený na hodnotu ChangeFeedObserverCloseReason.ObserverError.
  * Přidali jsme trasování za účelem identifikaci chyb v uživatelském kódu v pozorovateli.

### <a name="225"></a><a id="2.2.5"></a>2.2.5
* Přidali jsme podporu pro zpracování rozdělení v kolekcích, které využívají propustnost sdílené databáze.
  * Tato verze opravuje problém, ke kterému může dojít během rozdělení v kolekcích využívajících propustnost sdílené databáze, když rozdělení vede k vyrovnávání oddílů jen s jedním vytvořeným rozsahem klíčů podřízeného oddílu namísto dvou. Pokud k tomu dojde, může se procesor kanálu změn při odstraňování zapůjčení starého rozsahu klíčů oddílu zablokovat, takže pak nevytváří nová zapůjčení. Tento problém je v této verzi opravený.

### <a name="224"></a><a id="2.2.4"></a>2.2.4
* Přidali jsme novou vlastnost ChangeFeedProcessorOptions.StartContinuation, která podporuje spuštění kanálu změn od tokenu pro pokračování požadavku. Používá se jen tehdy, když je kolekce zapůjčení prázdná nebo když nemá zapůjčení nastavený token ContinuationToken. U zapůjčení v kolekci zapůjčení, která mají nastavený token ContinuationToken, se použije tento token ContinuationToken a vlastnost ChangeFeedProcessorOptions.StartContinuation se ignoruje.

### <a name="223"></a><a id="2.2.3"></a>2.2.3
* Přidali jsme podporu použití vlastního úložiště k zachování tokenů pro pokračování každého oddílu.
  * Vlastní úložiště zapůjčení může být třeba kolekce zapůjčení Azure Cosmos DB rozdělená jakýmkoli vlastním způsobem.
  * Vlastní úložiště zapůjčení můžou využívat nový bod rozšiřitelnosti ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) a veřejné rozhraní ILeaseStoreManager.
  * Došlo k refaktorování rozhraní ILeaseManager do rozhraní s více rolemi.
* Menší změna způsobující chybu: Došlo k odebrání bodu rozšiřitelnosti ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), použijte místo něj bod rozšiřitelnosti ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager).

### <a name="222"></a><a id="2.2.2"></a>2.2.2
* Tato verze opravuje problém, ke kterému dochází při zpracování rozdělení v monitorované kolekci a při používání kolekce zapůjčení rozdělené na oddíly. Při zpracování zapůjčení pro rozdělený oddíl se může stát, že se neodstraní zapůjčení odpovídající tomuto oddílu. Tento problém je v této verzi opravený.

### <a name="221"></a><a id="2.2.1"></a>2.2.1
* Opravený výpočet nástroje pro posouzení pro účty s více oblastmi zápisu a novým formátem tokenu relace.

### <a name="220"></a><a id="2.2.0"></a>2.2.0
* Přidali jsme podporu kolekcí zapůjčení rozdělených na oddíly. Klíč oddílu musí být definovaný jako /id.
* Menší změna způsobující chybu: Došlo ke změně metod rozhraní IChangeFeedDocumentClient interface a třídy ChangeFeedDocumentClient, takže teď zahrnují parametry RequestOptions a CancellationToken. IChangeFeedDocumentClient je rozšířený bod rozšiřitelnosti, který umožňuje poskytovat vlastní implementaci klienta dokumentu pro použití s procesorem kanálu změn, například k přizpůsobení třídy DocumentClient a zachycení všech volání této třídy za účelem dalšího trasování, zpracování chyb atd. V důsledku této aktualizace bude potřeba změnit kód, který implementuje bod IChangeFeedDocumentClient, aby v implementaci zahrnoval nové parametry.
* Menší vylepšení diagnostiky.

### <a name="210"></a><a id="2.1.0"></a>2.1.0
* Přidali jsme nové rozhraní API Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Dá se použít k získání odhadu objemu práce v každém oddílu.
* Podporuje sadu Microsoft.Azure.DocumentDB SDK 2.0. Vyžaduje Microsoft.Azure.DocumentDB 2.0 nebo vyšší.

### <a name="206"></a><a id="2.0.6"></a>2.0.6
* Kvůli kompatibilitě s verzí 1 jsme přidali veřejnou vlastnost ChangeFeedEventHost.HostName.

### <a name="205"></a><a id="2.0.5"></a>2.0.5
* Opravili jsme konflikt časování, ke kterému dochází při rozdělení oddílu. Konflikt časování může vést k tomu, že se během dělení oddílu získá zapůjčení a hned se zase zruší, což může způsobovat spory. Problém s konfliktem časování je v této verzi vyřešený.

### <a name="204"></a><a id="2.0.4"></a>2.0.4
* Sada GA SDK

### <a name="203-prerelease"></a><a id="2.0.3-prerelease"></a>2.0.3 – předběžná verze
* Opravené následující problémy:
  * Po rozdělení oddílu by mohlo docházet k duplicitnímu zpracování dokumentů upravených před rozdělením.
  * Když kolekce zapůjčení neobsahovala žádná zapůjčení, rozhraní API GetEstimatedRemainingWork vrátilo hodnotu 0.

* Zveřejnili jsme následující výjimky. Tyto výjimky můžou vydávat rozšíření, která implementují rozhraní IPartitionProcessor.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a id="2.0.2-prerelease"></a>2.0.2 – předběžná verze
* Menší změny rozhraní API:
  * Došlo k odebrání položky ChangeFeedProcessorOptions.IsAutoCheckpointEnabled, kterou jsme označili za zastaralou.

### <a name="201-prerelease"></a><a id="2.0.1-prerelease"></a>2.0.1 – předběžná verze
* Vylepšení stability:
  * Lepší zpracování inicializace úložiště zapůjčení. Pokud je úložiště zapůjčení prázdné, může ho inicializovat jenom jedna instance procesoru a ostatní budou čekat.
  * Stabilnější/efektivnější prodloužení/uvolnění zapůjčení. Prodloužení a uvolnění zapůjčení z jednoho oddílu není závislé na prodloužení jiných zapůjčení. Ve verzi 1 tento proces probíhal postupně pro všechny oddíly.
* Nové rozhraní API verze 2:
  * Vzor tvůrce pro flexibilní konstrukci procesoru: třída ChangeFeedProcessorBuilder.
    * Přijímá libovolnou kombinaci parametrů.
    * Může přijmout instanci DocumentClient pro monitorování nebo kolekci zapůjčení (ve verzi 1 není k dispozici).
  * IChangeFeedObserver.ProcessChangesAsync teď přijímá parametr CancellationToken.
  * IRemainingWorkEstimator – nástroj pro posouzení zbývající práce se dá používat nezávisle na procesoru.
  * Nové body rozšiřitelnosti:
    * IPartitionLoadBalancingStrategy – pro vlastní vyrovnávání zatížení oddílů mezi instancemi procesoru.
    * ILease, ILeaseManager – pro vlastní správu zapůjčení.
    * IPartitionProcessor – pro vlastní změny zpracování v určitém oddílu.
* Logging – využívá knihovnu [LibLog](https://github.com/damianh/LibLog).
* 100% zpětně kompatibilní s rozhraním API verze 1.
* Nový základ kódu.
* Kompatibilní se sadou [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.21.1 a vyšší.

### <a name="v1-builds"></a>Buildy v1

### <a name="133"></a><a id="1.3.3"></a>1.3.3
* Přidali jsme další protokolování.
* Opravili jsme únik třídy DocumentClient při vícenásobném volání odhadu nedokončené práce.

### <a name="132"></a><a id="1.3.2"></a>1.3.2
* Opravuje odhad nedokončené práce.

### <a name="131"></a><a id="1.3.1"></a>1.3.1
* Vylepšení stability.
  * Oprava problému se zpracováním zrušených úloh, který mohl v některých oddílech způsobit zastavení pozorovatelů.
* Podpora ručního vytváření kontrolních bodů.
* Kompatibilní se sadou [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.21 a vyšší.

### <a name="120"></a><a id="1.2.0"></a>1.2.0
* Přidává podporu platformy .NET Standard 2.0. Balíček teď podporuje monikery architektury `netstandard2.0` a `net451`.
* Kompatibilní se sadou [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.17.0 a vyšší.
* Kompatibilní se sadou [.NET Core SDK](sql-api-sdk-dotnet-core.md) verze 1.5.1 a vyšší.

### <a name="111"></a><a id="1.1.1"></a>1.1.1
* Opravuje problém s výpočtem odhadu zbývající práce, pokud byl kanál změn prázdný nebo nebyla k dispozici žádná nevyřízená práce.
* Kompatibilní se sadou [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.13.2 a vyšší.

### <a name="110"></a><a id="1.1.0"></a>1.1.0
* Přidali jsme metodu pro získání odhadu zbývající práce, která se má zpracovat v kanálu změn.
* Kompatibilní se sadou [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.13.2 a vyšší.

### <a name="100"></a><a id="1.0.0"></a>1.0.0
* Sada GA SDK
* Kompatibilní se sadou [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.14.1 nebo nižší.

## <a name="release--retirement-dates"></a>Data vydání a vyřazení z provozu

Microsoft bude o vyřazení sady SDK informovat s předstihem nejméně **12 měsíců**, aby mohlo dojít k bezproblémovému přechodu na novější/podporovanou verzi. Nové funkce, možnosti a optimalizace se přidávají jen do aktuální sady SDK, proto vám doporučujeme, abyste vždy co nejdříve provedli aktualizaci na nejnovější verzi sady SDK.

> [!WARNING]
> Od 31. srpna 2022 už Azure Cosmos DB nebude provádět opravy chyb, přidávat nové funkce ani poskytovat podporu pro verze 1.x sad SDK Azure Cosmos DB .NET nebo .NET Core pro rozraní SQL API. Pokud nechcete provést upgrade, služba Azure Cosmos DB bude dál zpracovávat požadavky odeslané z verze 1.x sady SDK.

<br/>

| Verze | Datum vydání | Datum vyřazení |
| --- | --- | --- |
| [2.3.2](#2.3.2) |11. srpna 2020 |--- |
| [2.3.1](#2.3.1) |30. července 2020 |--- |
| [2.3.0](#2.3.0) |2\. dubna 2020 |--- |
| [2.2.8](#2.2.8) |28. října 2019 |--- |
| [2.2.7](#2.2.7) |14. května 2019 |--- |
| [2.2.6](#2.2.6) |29. ledna 2019 |--- |
| [2.2.5](#2.2.5) |13. prosince 2018 |--- |
| [2.2.4](#2.2.4) |29. listopadu 2018 |--- |
| [2.2.3](#2.2.3) |19. listopadu 2018 |--- |
| [2.2.2](#2.2.2) |31. října 2018 |--- |
| [2.2.1](#2.2.1) |24. října 2018 |--- |
| [1.3.3](#1.3.3) |8\. května 2018 |--- |
| [1.3.2](#1.3.2) |18. dubna 2018 |--- |
| [1.3.1](#1.3.1) |13. března 2018 |--- |
| [1.2.0](#1.2.0) |31. října 2017 |--- |
| [1.1.1](#1.1.1) |29. srpna 2017 |--- |
| [1.1.0](#1.1.0) |13. srpna 2017 |--- |
| [1.0.0](#1.0.0) |7\. července 2017 |--- |

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také

Další informace o Cosmos DB najdete na stránce služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
