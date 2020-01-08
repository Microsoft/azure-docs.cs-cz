---
title: Azure Cosmos DB rozhraní API změny rozhraní .NET pro zpracování kanálu, poznámky k verzi sady SDK
description: Seznamte se s rozhraním API pro změny kanálu a sadou SDK včetně data vydání, dat o vyřazení a změn provedených mezi jednotlivými verzemi sady SDK pro změny rozhraní .NET pro zpracování datových kanálů.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 9252e3e41d0c639231a2abe20202499c6b3ee32a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444862"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Sada SDK pro rozhraní Change feed pro .NET: stažení a poznámky k verzi

> [!div class="op_single_selector"]
>
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
> * [Hromadný prováděcí modul – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný prováděcí modul – Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**Stažení sady SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace k rozhraní API pro změnu knihovny procesoru v informačním kanálu](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Začínáme**|[Začínáme s rozhraním .NET SDK Change feed Processor](change-feed.md)|
|**Aktuální podporované architektury**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Pokud používáte procesor Change feed, přečtěte si prosím nejnovější verzi 3. x sady [.NET SDK](change-feed-processor.md), která obsahuje kanál změn integrovaný do sady SDK. 

## <a name="release-notes"></a>Poznámky k verzi

### <a name="v2-builds"></a>V2 sestavení

### <a name="a-name228228"></a><a name="2.2.8"/>2.2.8
* Vylepšení stability a diagnostiky:
  * Přidala se podpora pro detekci kanálu změny čtení a trvá to dlouhou dobu. Pokud trvá déle než hodnota zadaná vlastností `ChangeFeedProcessorOptions.ChangeFeedTimeout`, provedou se tyto kroky:
    * Operace čtení kanálu změn u problematického oddílu se přerušila.
    * Instance procesoru změny kanálu převezme vlastnictví problematického zapůjčení. Zrušené zapůjčení se vybere během příštího kroku získání zapůjčení, který bude proveden stejnou nebo jinou instancí Change feed Processor. Tímto způsobem se zahájí čtení kanálu změn.
    * Monitorování stavu oznamuje problém. Výchozí monitorování stavu odesílá všechny nahlášené problémy do protokolu trasování.
  * Přidání nové veřejné vlastnosti: `ChangeFeedProcessorOptions.ChangeFeedTimeout`. Výchozí hodnota této vlastnosti je 10 minut.
  * Byla přidána nová hodnota veřejného výčtu: `Monitoring.MonitoredOperation.ReadChangeFeed`. Je-li hodnota `HealthMonitoringRecord.Operation` nastavena na hodnotu `Monitoring.MonitoredOperation.ReadChangeFeed`, znamená to, že problém se stavem souvisí se čtením kanálu změn.

### <a name="a-name227227"></a><a name="2.2.7"/>2.2.7
* Vylepšená strategie vyrovnávání zatížení pro situaci, kdy se u všech zapůjčení trvá déle než interval vypršení platnosti zapůjčení, např. kvůli problémům se sítí:
  * V tomto scénáři se algoritmus vyrovnávání zatížení, který se používá k nepravdivému zvážení zapůjčených adres, což způsobilo odcizení zapůjčení od aktivních vlastníků. To může aktivovat zbytečné opětovné vyrovnávání zapůjčených adres.
  * Tento problém se v této verzi opravil tím, že se vyhnete opakování při pokusu o konflikt při získání vypršení zapůjčení, které vlastník nezměnil, a posponing pro další iteraci vyrovnávání zatížení.

### <a name="a-name226226"></a><a name="2.2.6"/>2.2.6
* Vylepšené zpracování výjimek pozorovatele.
* Rozšířené informace o chybách pozorovatele:
  * Když je pozorovatel uzavřený kvůli výjimce vyvolané ProcessChangesAsync pozorovatele, CloseAsync teď obdrží parametr důvod nastavený na ChangeFeedObserverCloseReason. ObserverError.
  * Přidání trasování pro identifikaci chyb v uživatelském kódu v pozorovateli.

### <a name="a-name225225"></a><a name="2.2.5"/>2.2.5
* Přidání podpory pro zpracování rozdělení v kolekcích, které používají propustnost sdílené databáze.
  * Tato verze opravuje problém, ke kterému může dojít během rozdělení v kolekcích pomocí propustnosti sdílené databáze při rozdělení výsledku do opětovného vyrovnávání oddílů s vytvořeným pouze jedním podřízeným rozsahem klíče oddílu, nikoli dvěma. Pokud k tomu dojde, může procesor Change feed zablokovat odstranění zapůjčení pro starý rozsah klíčů oddílu a nevytvářet nové zapůjčení. Tento problém je opravený v této verzi.

### <a name="a-name224224"></a><a name="2.2.4"/>2.2.4
* Byla přidána nová vlastnost ChangeFeedProcessorOptions. StartContinuation, která podporuje spuštění kanálu změn z tokenu pro pokračování žádosti. Používá se pouze v případě, že je shromažďování zapůjčení prázdné nebo zapůjčení nemá token continuationtoken sadu. Pro zapůjčení v kolekci zapůjčení, které mají token continuationtoken sadu, se použije token continuationtoken a ChangeFeedProcessorOptions. StartContinuation se ignoruje.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Přidání podpory pro použití vlastního úložiště k uchování tokenů pokračování na oddíl.
  * Vlastní úložiště zapůjčení můžete například Azure Cosmos DB zapůjčení dělit jakýmkoli vlastním způsobem.
  * Vlastní úložiště zapůjčení můžou používat nové veřejné rozhraní ChangeFeedProcessorBuilder. WithLeaseStoreManager (ILeaseStoreManager) a ILeaseStoreManager.
  * Rozhraní ILeaseManager se refaktoruje na více rozhraních role.
* Menší zásadní změna: odebraný bod rozšíření ChangeFeedProcessorBuilder. WithLeaseManager (ILeaseManager) použijte místo toho ChangeFeedProcessorBuilder. WithLeaseStoreManager (ILeaseStoreManager).

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Tato verze opravuje problém, ke kterému došlo během zpracování rozdělení v monitorované kolekci a použití dělené kolekce zapůjčení. Při zpracování zapůjčení rozděleného oddílu nemusí být zapůjčení odpovídající tomuto oddílu smazáno. Tento problém je opravený v této verzi.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Opravený výpočet Estimator pro více hlavních účtů a nový formát tokenu relace.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Přidání podpory pro dělené kolekce zapůjčení Klíč oddílu musí být definovaný jako/ID.
* Menší Průlomová změna: metody rozhraní IChangeFeedDocumentClient a třídy ChangeFeedDocumentClient se změnily tak, aby zahrnovaly parametry RequestOptions a CancellationToken. IChangeFeedDocumentClient je pokročilý bod rozšiřitelnosti, který vám umožňuje poskytovat vlastní implementaci klienta dokumentů pro použití s procesorem Change feed, například upravit DocumentClient a zachytit všechna volání do něj za účelem dodatečného trasování, zpracování chyb. atd. V této aktualizaci bude nutné kód, který implementuje IChangeFeedDocumentClient, změnit tak, aby zahrnoval nové parametry v implementaci.
* Menší vylepšení diagnostiky.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Bylo přidáno nové rozhraní API, Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator. GetEstimatedRemainingWorkPerPartitionAsync (). To se dá použít k získání odhadované práce pro každý oddíl.
* Podporuje Microsoft. Azure. DocumentDB SDK 2,0. Vyžaduje Microsoft. Azure. DocumentDB 2,0 nebo novější.

### <a name="a-name206206"></a><a name="2.0.6"/>2.0.6
* Byla přidána veřejná vlastnost ChangeFeedEventHost. HostName pro kompatibilitu s v1.

### <a name="a-name205205"></a><a name="2.0.5"/>2.0.5
* Opravili jste konflikt časování, ke kterému dojde během rozdělení oddílu. Konflikt časování může vést k tomu, že získá zapůjčení a hned ho ztratí během rozdělení oddílů a způsobuje spor. V této verzi je vyřešen problém s podmínkou časování.

### <a name="a-name204204"></a><a name="2.0.4"/>2.0.4
* GA SDK

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3 – Předběžná verze
* Opravené následující problémy:
  * Pokud dojde k rozdělení oddílu, mohlo by dojít ke změně duplicitního zpracování dokumentů před rozdělením.
  * Rozhraní GetEstimatedRemainingWork API vrátilo hodnotu 0, pokud v kolekci zapůjčení nebylo přítomno žádné zapůjčení.

* Následující výjimky jsou zveřejněny. Rozšíření implementující IPartitionProcessor můžou tyto výjimky vyvolat.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2 – Předběžná verze
* Dílčí změny rozhraní API:
  * Byl odebrán ChangeFeedProcessorOptions. IsAutoCheckpointEnabled, který byl označen jako zastaralý.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1 – předběžná verze
* Vylepšení stability:
  * Lepší zpracování inicializace úložiště zapůjčení. Pokud je úložiště pro zapůjčení prázdné, může ho inicializovat jenom jedna instance procesoru, ostatní budou čekat.
  * Stabilnější/efektivní zapůjčení/vydání. Obnovení a uvolnění zapůjčení jeden oddíl nezávisí na obnovení ostatních. V v1 byly postupně provedeny pro všechny oddíly.
* Nové rozhraní API v2:
  * Vzor tvůrce pro flexibilní konstrukci procesoru: třída ChangeFeedProcessorBuilder
    * Může mít libovolnou kombinaci parametrů.
    * Může převzít instanci DocumentClient pro monitorování nebo shromažďování zapůjčení (není k dispozici ve verzi V1).
  * IChangeFeedObserver. ProcessChangesAsync teď přijímá CancellationToken.
  * IRemainingWorkEstimator – zbývající pracovní Estimator se dají použít odděleně od procesoru.
  * Nové body rozšiřitelnosti:
    * IPartitionLoadBalancingStrategy – pro vlastní vyrovnávání zatížení oddílů mezi instancemi procesoru.
    * ILease, ILeaseManager – pro vlastní správu zapůjčení.
    * IPartitionProcessor – pro vlastní změny zpracování oddílu.
* Protokolování – používá knihovnu [LibLog](https://github.com/damianh/LibLog) .
* 100% zpětně kompatibilní s rozhraním API v1.
* Nová základna kódu.
* Kompatibilní s [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.21.1 a vyšší.

### <a name="v1-builds"></a>sestavení v1

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* Bylo přidáno další protokolování.
* Opravili jsme únik DocumentClient při volání nedokončené práce několikrát.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* Opravy ve odhadu nedokončené práce.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Vylepšení stability.
  * Oprava pro zpracování problému zrušených úkolů, který by mohl vést k zastavení pozorovatelů na některých oddílech.
* Podpora ručního vytvoření kontrolního bodu.
* Kompatibilní s [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1,21 a vyšší.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Přidá podporu pro .NET Standard 2,0. Balíček teď podporuje `netstandard2.0` a monikery rozhraní `net451` Framework.
* Kompatibilní s [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.17.0 a vyšší.
* Kompatibilní s verzemi [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 a vyšší.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Opravuje problém s výpočtem odhadu zbývající práce, pokud byl kanál změn prázdný nebo nečeká na zpracování.
* Kompatibilní s [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.13.2 a vyšší.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Přidali jsme metodu pro získání odhadu zbývající práce, která se má zpracovat v kanálu změn.
* Kompatibilní s [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.13.2 a vyšší.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Kompatibilní se sadou [SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.14.1 a nižší.

## <a name="release--retirement-dates"></a>Data vyřazení & vydání

Microsoft bude poskytovat oznámení alespoň **12 měsíců** před vyřazením z provozu sady SDK ulehčení přechodu na verzi novější nebo podporované.

Nové funkce a funkce a optimalizace se přidávají jenom do aktuální sady SDK, protože se tak doporučuje kdykoli nejdříve upgradovat na nejnovější verzi sady SDK. 

Jakoukoli žádost do služby Cosmos DB pomocí vyřazeno sady SDK budou odmítnuty službou.

<br/>

| Verze | Datum vydání | Datum vyřazení z provozu |
| --- | --- | --- |
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

## <a name="faq"></a>Časté otázky

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Další informace najdete v tématech

Další informace o službě Cosmos DB najdete v tématu [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) stránku služby.
