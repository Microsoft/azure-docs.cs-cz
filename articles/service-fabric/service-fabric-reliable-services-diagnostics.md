---
title: Diagnostika spolehlivých spolehlivých služeb Azure Service Fabric
description: Diagnostické funkce pro stavové spolehlivé služby ve službě Azure Service Fabric
author: dkkapur
ms.topic: conceptual
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: 37162287e130b05dc41453c579b3a628ac878fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282260"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnostické funkce pro stavové služby Reliable Services
Azure Service Fabric stavové spolehlivé služby StatefulServiceBase třída vyzařuje [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) události, které lze použít k ladění služby, poskytují přehled o tom, jak runtime funguje a pomoc při řešení potíží.

## <a name="eventsource-events"></a>Události EventSource
Název EventSource pro třídu Stateful Reliable Services StatefulServiceBase je "Microsoft-ServiceFabric-Services". Události z tohoto zdroje událostí se zobrazí v okně [Události diagnostiky,](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) když je služba [laděna v sadě Visual Studio](service-fabric-debugging-your-application.md).

Příklady nástrojů a technologií, které pomáhají při shromažďování a/nebo prohlížení událostí EventSource, jsou [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Diagnostika Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)a [Knihovna Microsoft TraceEvent](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Události
| Název události | ID události | Úroveň | Popis události |
| --- | --- | --- | --- |
| Stavová RunAsyncInvocation |1 |Informační |Vyzařované při spuštění úlohy RunAsync služby |
| Zrušení funkce Stavová runasynchronizace |2 |Informační |Vyzařované při zrušení úlohy RunAsync služby |
| Dokončení funkce Stavová runasynchronizace |3 |Informační |Vyzařované po dokončení úlohy RunAsync služby |
| Zrušení funkce Stavová funkce RunAsyncSlow |4 |Upozornění |Vyzařované, když služba RunAsync úloha trvá příliš dlouho k dokončení zrušení |
| Selhání funkce Stavová runasynchronní |5 |Chyba |Vyzařované, když úloha RunAsync služby vyvolá výjimku |

## <a name="interpret-events"></a>Interpretace událostí
StatefulRunAsyncInvocation, StatefulRunAsyncCompletion a StatefulRunAsyncCancellation události jsou užitečné pro zapisovač služby pochopit životní cyklus služby, stejně jako načasování při spuštění služby, zruší nebo dokončí. Tyto informace mohou být užitečné při ladění problémů se službou nebo pochopení životního cyklu služby.

Autoři služby by měli věnovat velkou pozornost událostem StatefulRunAsyncSlowCancellation a StatefulRunAsyncFailure, protože označují problémy se službou.

StatefulRunAsyncFailure je emitován vždy, když úloha runasync(služby) vyvolá výjimku. Obvykle vyvolána výjimka označuje chybu nebo chybu ve službě. Kromě toho výjimka způsobí selhání služby, takže je přesunuta do jiného uzlu. Tato operace může být nákladné a může zpozdit příchozí požadavky při přesunutí služby. Autoři služby by měl určit příčinu výjimky a pokud je to možné zmírnit.

StatefulRunAsyncSlowCancellation je emitován vždy, když požadavek na zrušení úlohy RunAsync trvá déle než čtyři sekundy. Pokud služba trvá příliš dlouho k dokončení zrušení, ovlivňuje schopnost služby rychle restartovat na jiném uzlu. Tento scénář může ovlivnit celkovou dostupnost služby.

## <a name="performance-counters"></a>Čítače výkonu
Za běhu služby Spolehlivé služby definuje následující kategorie čítačů výkonu:

| Kategorie | Popis |
| --- | --- |
| Transakční replikátor service fabric |Čítače specifické pro transakční replikátor infrastruktury služby Azure |
| Service Fabric TStore |Čítače specifické pro Azure Service Fabric TStore |

Transakční replikátor Service Fabric používá [správce spolehlivého stavu](service-fabric-reliable-services-reliable-collections-internals.md) k replikaci transakcí v rámci dané sady [replik](service-fabric-concepts-replica-lifecycle.md).

Service Fabric TStore je součást používaná v [spolehlivé kolekce](service-fabric-reliable-services-reliable-collections-internals.md) pro ukládání a načítání párů klíč hodnota.

Aplikace [Sledování výkonu systému Windows,](https://technet.microsoft.com/library/cc749249.aspx) která je ve výchozím nastavení k dispozici v operačním systému Windows, lze použít ke shromažďování a zobrazení dat čítače výkonu. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možnost pro shromažďování dat čítače výkonu a jejich nahrání do tabulek Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítače výkonu
Cluster, který má velký počet spolehlivých služeb nebo spolehlivé oddíly služby bude mít velký počet instancí čítače výkonu transakční replikátory. To je také případ čítače výkonu TStore, ale je také vynásobeno počet spolehlivé slovníky a spolehlivé fronty používané. Názvy instancí čítače výkonu mohou pomoci při identifikaci konkrétního [oddílu](service-fabric-concepts-partitioning.md), repliky služby a zprostředkovatele stavu v případě TStore, ke kterému je přidružena instance čítače výkonu.

#### <a name="service-fabric-transactional-replicator-category"></a>Kategorie transakčního replikátoru service fabric
Pro kategorii `Service Fabric Transactional Replicator`jsou názvy instancí čítačů v následujícím formátu:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* je řetězcová reprezentace ID oddílu Service Fabric, ke kterému je přidružena instance čítače výkonu. ID oddílu je identifikátor GUID a jeho [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) řetězcová reprezentace je generována pomocí specifikátoru formátu "D".

*ServiceFabricReplicaId* je ID přidružené k dané replice spolehlivé služby. ID repliky je součástí názvu instance čítače výkonu, aby byla zajištěna jeho jedinečnost a zabránilo se konfliktu s jinými instancemi čítače výkonu generovanými stejným oddílem. Další podrobnosti o replikách a jejich roli ve spolehlivých službách naleznete [zde](service-fabric-concepts-replica-lifecycle.md).

Následující název instance čítače je `Service Fabric Transactional Replicator` typický pro čítač v rámci kategorie:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

V předchozím příkladu `00d0126d-3e36-4d68-98da-cc4f7195d85e` je řetězcová reprezentace ID oddílu Service Fabric a `131652217797162571` je ID repliky.

#### <a name="service-fabric-tstore-category"></a>Kategorie TStore service fabric
Pro kategorii `Service Fabric TStore`jsou názvy instancí čítačů v následujícím formátu:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* je řetězcová reprezentace ID oddílu Service Fabric, ke kterému je přidružena instance čítače výkonu. ID oddílu je identifikátor GUID a jeho [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) řetězcová reprezentace je generována pomocí specifikátoru formátu "D".

*ServiceFabricReplicaId* je ID přidružené k dané replice spolehlivé služby. ID repliky je součástí názvu instance čítače výkonu, aby byla zajištěna jeho jedinečnost a zabránilo se konfliktu s jinými instancemi čítače výkonu generovanými stejným oddílem. Další podrobnosti o replikách a jejich roli ve spolehlivých službách naleznete [zde](service-fabric-concepts-replica-lifecycle.md).

*StateProviderId* je ID přidružené k poskytovateli stavu v rámci spolehlivé služby. ID zprostředkovatele stavu je součástí názvu instance čítače výkonu k odlišení TStore od jiného.

*PerformanceCounterInstanceDifferentiator* je rozlišení ID přidružené k instanci čítače výkonu v rámci zprostředkovatele stavu. Tento rozlišovač je součástí názvu instance čítače výkonu, aby byla zajištěna jeho jedinečnost a zabránilo se konfliktu s jinými instancemi čítačů výkonu generovanými stejným poskytovatelem stavu.

*StateProviderName* je název přidružený k poskytovateli stavu v rámci spolehlivé služby. Název zprostředkovatele stavu je součástí názvu instance čítače výkonu pro uživatele snadno identifikovat, jaký stav poskytuje.

Následující název instance čítače je `Service Fabric TStore` typický pro čítač v rámci kategorie:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

V předchozím příkladu `00d0126d-3e36-4d68-98da-cc4f7195d85e` je řetězcová reprezentace ID `131652217797162571` oddílu Service Fabric, je ID repliky, `142652217797162571` je ID zprostředkovatele stavu a `1337` je odlišovací jednotkou instance čítače výkonu. `urn:MyReliableDictionary/dataStore`je název zprostředkovatele stavu, který ukládá `urn:MyReliableDictionary`data pro kolekci s názvem .

### <a name="transactional-replicator-performance-counters"></a>Čítače výkonu transakčníreplikátory

Za běhu služby Spolehlivé služby `Service Fabric Transactional Replicator` vyzařuje následující události v rámci kategorie

 Název čítače | Popis |
| --- | --- |
| Zahájení operací Txn/s | Počet nových transakcí zápisu vytvořených za sekundu.|
| Operace Txn/s | Počet operací přidání/aktualizace/odstranění prováděných u spolehlivých kolekcí za sekundu.|
| Počet bajtů vyprázdnění protokolu/s | Počet bajtů vyprázdněných na disk transakčním replikátorem za sekundu |
| Omezené operace/s | Počet operací odmítnutých každou sekundu transakčním replikátorem z důvodu omezení. |
| Vzdu si transakce ms/commit | Průměrná latence potvrzení na transakci v milisekundách |
| Vg. Latence splachování (ms) | Průměrná doba trvání operací vyprázdnění disku iniciovaných transakčním replikátorem v milisekundách |

### <a name="tstore-performance-counters"></a>Čítače výkonu TStore

Za běhu služby Spolehlivé služby `Service Fabric TStore` vyzařuje následující události v rámci kategorie

 Název čítače | Popis |
| --- | --- |
| Počet položek | Počet položek v obchodě.|
| Velikost disku | Celková velikost disku v bajtech souborů kontrolního bodu pro úložiště.|
| Bajty zápisu souboru kontrolního bodu za sekundu | Počet bajtů zapsaných za sekundu pro nejnovější soubor kontrolního bodu.|
| Kopírovat bajty přenosu disku/s | Počet přečtených diskových bajtů (na primární replice) nebo zapsaných (na sekundární replice) za sekundu během kopírování úložiště.|

## <a name="next-steps"></a>Další kroky
[Zprostředkovatelé Zdroje událostí v PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
