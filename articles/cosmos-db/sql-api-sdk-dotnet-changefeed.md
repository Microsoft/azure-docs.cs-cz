---
title: Rozhraní API procesoru zdroje změn Azure Cosmos DB .NET, poznámky k verzi sady SDK
description: Přečtěte si vše o rozhraní Change Feed Processor API a SDK, včetně dat vydání, dat odchodu do důchodu a změn provedených mezi jednotlivými verzemi sady SDK .NET Change Feed Processor.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 5820778d46f5701b82bb289192350a9e13739d37
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619447"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Sada SDK kanálu .NET Change Feed: Stažení a poznámky k verzi

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [Informační kanál o změně .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Poskytovatel prostředků REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Hromadný vykonavatel - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Hromadný vykonavatel - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK ke stažení**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Dokumentace k rozhraní API**|[Referenční dokumentace rozhraní API knihovny knihovny kanálu Změnit kanál](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Začínáme**|[Začínáme s sadou Change Feed Processor .NET SDK](change-feed.md)|
|**Aktuální podporovaný rámec**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Jádro microsoftu .NET](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Pokud používáte změnit feed procesor, naleznete nejnovější verzi 3.x [.NET SDK](change-feed-processor.md), který má změny krmiva integrované do sady SDK. 

## <a name="release-notes"></a>Poznámky k verzi

### <a name="v2-builds"></a>v2 staví

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Přidána nová `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` metoda a `ICheckpointPartitionProcessorFactory`odpovídající veřejné rozhraní . To umožňuje implementaci `IPartitionProcessor` rozhraní používat integrovaný mechanismus vytváření kontrolních bodů. Nová továrna je podobná `IPartitionProcessorFactory`existujícímu `Create` , s `ILeaseCheckpointer` tím rozdílem, že její metoda také přebírá parametr.
* Pro stejnou `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` `ChangeFeedProcessorBuilder` instanci lze `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`použít pouze jednu ze dvou metod, nebo .

### <a name="228"></a><a name="2.2.8"/>2.2.8
* Zlepšení stability a diagnostiky:
  * Přidána podpora pro detekci čtení změny krmiva trvá dlouhou dobu. Pokud trvá déle než hodnota `ChangeFeedProcessorOptions.ChangeFeedTimeout` zadaná vlastností, jsou podniknuty následující kroky:
    * Operace číst změny kanálu na problematický oddíl je přerušena.
    * Instance procesoru kanálu změn klesne vlastnictví problematické zapůjčení. Zrušené zapůjčení bude vyzvednuto během dalšího kroku získání zapůjčení, který bude proveden stejnou nebo jinou instancí procesoru kanálu. Tímto způsobem bude čtení změny krmiva začít znovu.
    * Problém je hlášen monitoru stavu. Výchozí monitorování vaho odesílá všechny hlášené problémy do protokolu trasování.
  * Přidána nová veřejná nemovitost: `ChangeFeedProcessorOptions.ChangeFeedTimeout`. Výchozí hodnota této vlastnosti je 10 minut.
  * Přidána nová hodnota veřejného výčtu: `Monitoring.MonitoredOperation.ReadChangeFeed`. Pokud `HealthMonitoringRecord.Operation` je hodnota nastavena na `Monitoring.MonitoredOperation.ReadChangeFeed`, označuje problém se stavem souvisí se čtením kanálu změn.

### <a name="227"></a><a name="2.2.7"/>2.2.7
* Vylepšená strategie vyrovnávání zatížení pro scénář při získání všech zapůjčení trvá déle než interval vypršení platnosti zapůjčení, například kvůli problémům se sítí:
  * V tomto scénáři algoritmus vyrovnávání zatížení slouží k falešně považovat zapůjčení jako vypršela, což způsobuje krádež zapůjčení od aktivních vlastníků. To by mohlo vyvolat zbytečné vyvažování mnoho leasingů.
  * Tento problém je vyřešen v této verzi tím, že se zabrání opakování konfliktu při získávání vypršela zapůjčení, které vlastník nezměnil a posponing získání vypršela zapůjčení na další iteraci vyrovnávání zatížení.

### <a name="226"></a><a name="2.2.6"/>2.2.6
* Vylepšené zpracování výjimek observeru.
* Podrobnější informace o chybách pozorovatele:
  * Když je observer uzavřen z důvodu výjimky vyváděné pozorovatele ProcessChangesAsync, CloseAsync nyní obdrží parametr důvod nastavený na ChangeFeedObserverCloseReason.ObserverError.
  * Přidány trasování k identifikaci chyb v rámci uživatelského kódu v observeru.

### <a name="225"></a><a name="2.2.5"/>2.2.5
* Přidána podpora pro zpracování rozdělení v kolekcích, které používají propustnost sdílené databáze.
  * Tato verze opravuje problém, který může nastat během rozdělení v kolekcích pomocí propustnost sdílené databáze při rozdělení výsledku do oddílu re-balancování pouze jeden podřízený oddíl klíč rozsah vytvořený, nikoli dva. V takovém případě změnit feed procesor může uvíznout odstranění zapůjčení pro staré rozsah klíčů oddílu a ne vytváří nové zapůjčení. Problém je vyřešen v této verzi.

### <a name="224"></a><a name="2.2.4"/>2.2.4
* Přidána nová vlastnost ChangeFeedProcessorOptions.StartContinuation pro podporu spuštění kanálu změn z tokenu pokračování požadavku. Používá se pouze v případě, že kolekce zapůjčení je prázdná nebo zapůjčení nemá ContinuationToken nastavit. Pro zapůjčení v kolekci zapůjčení, které mají ContinuationToken nastavena ContinuationToken se používá a ChangeFeedProcessorOptions.StartContinuation je ignorována.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Přidána podpora pro použití vlastního úložiště k zachování tokenů pokračování na oddíl.
  * Například vlastní úložiště zapůjčení může být kolekce zapůjčení Azure Cosmos DB rozdělené oddíly jakýmkoli vlastním způsobem.
  * Vlastní úložiště zapůjčení můžete použít nový rozšiřitelnost bodu ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) a ILeaseStoreManager veřejné rozhraní.
  * Refaktored rozhraní ILeaseManager do více rozhraní role.
* Menší změna porušení: odstraněn bod rozšiřitelnosti ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager), použijte ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) místo.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* Tato verze opravuje problém, ke kterému dochází při zpracování rozdělení ve sledované kolekci a pomocí kolekce rozdělené zapůjčení. Při zpracování zapůjčení pro rozdělený oddíl, zapůjčení odpovídající tomuto oddílu nelze odstranit. Problém je vyřešen v této verzi.

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Opraven výpočet odhadu pro účty Více hlavních a nový formát tokenu relace.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Přidána podpora pro kolekce rozdělených zapůjčení. Klíč oddílu musí být definován jako /id.
* Menší změna rozdělení: metody rozhraní IChangeFeedDocumentClient a třídy ChangeFeedDocumentClient byly změněny tak, aby zahrnovaly parametry RequestOptions a CancellationToken. IChangeFeedDocumentClient je pokročilý bod rozšiřitelnosti, který umožňuje poskytnout vlastní implementaci klienta dokumentu pro použití s change feed processor, například zdobit DocumentClient a zachytit všechna volání k němu provést další trasování, zpracování chyb, atd. S touto aktualizací kód, který implementuje IChangeFeedDocumentClient bude muset být změněn tak, aby zahrnovala nové parametry v implementaci.
* Drobná vylepšení diagnostiky.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Přidáno nové&lt;rozhraní API,&lt;úloha&gt; &gt; IReadOnlyList RemainingPartitionWork IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). To lze získat odhadovanou práci pro každý oddíl.
* Podporuje sadu Microsoft.Azure.DocumentDB SDK 2.0. Vyžaduje Microsoft.Azure.DocumentDB 2.0 nebo novější.

### <a name="206"></a><a name="2.0.6"/>2.0.6
* Přidána veřejná vlastnost ChangeFeedEventHost.HostName pro kompatibilitu s v1.

### <a name="205"></a><a name="2.0.5"/>2.0.5
* Opravena spor, ke kterému dochází během rozdělení oddílu. Spor může vést k získání zapůjčení a okamžitě ztratí během rozdělení oddílu a způsobuje sváry. Spor problém je vyřešen s touto verzí.

### <a name="204"></a><a name="2.0.4"/>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-předběžná verze
* Opravené následující problémy:
  * Když dojde k rozdělení oddílu, může být duplicitní zpracování dokumentů změněno před rozdělením.
  * Rozhraní API GetEstimatedRemainingWork vrátilo 0, když v kolekci zapůjčení nebyly přítomny žádné zapůjčení.

* Následující výjimky jsou zveřejněny. Rozšíření, která implementují IPartitionProcessor může vyvolat tyto výjimky.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcesor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcesor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-předběžná verze
* Drobné změny rozhraní API:
  * Odebrána ChangeFeedProcessorOptions.IsAutoCheckpointEnabled, která byla označena jako zastaralá.

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-předběžná verze
* Zlepšení stability:
  * Lepší zpracování inicializace úložiště pronájmu. Pokud je úložiště zapůjčení prázdné, může ji inicializovat pouze jedna instance procesoru, ostatní budou čekat.
  * Stabilnější/efektivnější obnovení/uvolnění zapůjčení. Obnovení a uvolnění zapůjčení jeden oddíl je nezávislý na obnovení ostatní. V v1, který byl proveden postupně pro všechny oddíly.
* Nové v2 API:
  * Builder vzor pro flexibilní konstrukci procesoru: ChangeFeedProcessorBuilder třídy.
    * Může trvat libovolnou kombinaci parametrů.
    * Může trvat DocumentClient instance pro monitorování a/nebo kolekce zapůjčení (není k dispozici v v1).
  * IChangeFeedObserver.ProcessChangesAsync nyní přebírá CancellationToken.
  * IRemainingWorkEstimator - zbývající odhad práce lze použít odděleně od procesoru.
  * Nové body rozšiřitelnosti:
    * IPartitionLoadBalancingStrategy - pro vlastní vyrovnávání zatížení oddílů mezi instancemi procesoru.
    * ILease, ILeaseManager - pro vlastní správu pronájmu.
    * IPartitionProcessor - pro vlastní změny zpracování na oddílu.
* Protokolování - používá [knihovnu LibLog.](https://github.com/damianh/LibLog)
* 100% zpětně kompatibilní s v1 API.
* Nový základ kódu.
* Kompatibilní s [implementací SQL .NET SDK](sql-api-sdk-dotnet.md) verze mi1.21.1 a vyšší.

### <a name="v1-builds"></a>v1 staví

### <a name="133"></a><a name="1.3.3"/>1.3.3
* Přidáno další protokolování.
* Opravena nevracení DocumentClient při volání čekající práce odhad vícekrát.

### <a name="132"></a><a name="1.3.2"/>1.3.2
* Opravy v odhadu čekající práce.

### <a name="131"></a><a name="1.3.1"/>1.3.1
* Zlepšení stability.
  * Oprava pro zpracování zrušených úkolů problém, který by mohl vést k zastavené pozorovatele na některých oddílech.
* Podpora pro ruční kontrolní body.
* Kompatibilní s [sql .NET SDK](sql-api-sdk-dotnet.md) verze 1.21 a vyšší.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Přidá podporu pro rozhraní .NET Standard 2.0. Balíček nyní `netstandard2.0` `net451` podporuje a framework zástupné názvy.
* Kompatibilní s [implementací SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.17.0 a vyšší.
* Kompatibilní s [implementací SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) verze 1.5.1 a vyšší.

### <a name="111"></a><a name="1.1.1"/>1.1.1
* Opravuje problém s výpočtem odhadu zbývající práce, když byl kanál změn prázdný nebo nebyla čeká na vyřízení žádná práce.
* Kompatibilní s [implementací SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.13.2 a vyšší.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Byla přidána metoda pro získání odhadu zbývající práce, která má být zpracována v kanálu změn.
* Kompatibilní s [implementací SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.13.2 a vyšší.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Kompatibilní s [implementací SQL .NET SDK](sql-api-sdk-dotnet.md) verze 1.14.1 a nižší.

## <a name="release--retirement-dates"></a>Uvolnit & data odchodu do důchodu

Společnost Microsoft bude poskytovat oznámení nejméně **12 měsíců** před vyřazením sady SDK za účelem hladkého přechodu na novější/podporovanou verzi.

Nové funkce a funkce a optimalizace jsou přidány pouze do aktuální sady SDK, jako takové se doporučuje vždy upgradovat na nejnovější verzi sady SDK co nejdříve. 

Jakýkoli požadavek na Cosmos DB pomocí vyřazené sady SDK bude službou odmítnut.

<br/>

| Version | Datum vydání | Datum odchodu do důchodu |
| --- | --- | --- |
| [2.3.0](#2.3.0) |2. dubna 2020 |--- |
| [2.2.8](#2.2.8) |28. října 2019October 28, 2019 |--- |
| [2.2.7](#2.2.7) |14. května 2019 |--- |
| [2.2.6](#2.2.6) |29. ledna 2019 January 29, 2019 |--- |
| [2.2.5](#2.2.5) |13. prosince 2018December 13, 2018 |--- |
| [2.2.4](#2.2.4) |29. listopadu 2018November 29, 2018 |--- |
| [2.2.3](#2.2.3) |19. listopadu 2018November 19, 2018 |--- |
| [2.2.2](#2.2.2) |31. října 2018 |--- |
| [2.2.1](#2.2.1) |24. října 2018October 24, 2018 |--- |
| [1.3.3](#1.3.3) |Květen 08, 2018 |--- |
| [1.3.2](#1.3.2) |18. dubna 2018April 18, 2018 |--- |
| [1.3.1](#1.3.1) |13. března 2018 |--- |
| [1.2.0](#1.2.0) |31. října 2017 |--- |
| [1.1.1](#1.1.1) |29. srpna 2017August 29, 2017 |--- |
| [1.1.0](#1.1.0) |13. srpna 2017August 13, 2017 |--- |
| [1.0.0](#1.0.0) |7. července 2017July 07, 2017 |--- |

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Viz také

Další informace o Službě Cosmos DB najdete na stránce [služby Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)
