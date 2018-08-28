---
title: Diagnostika Azure Service Fabric stavové služby Reliable Services | Dokumentace Microsoftu
description: Diagnostické funkce pro stavové služby Reliable Services v Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: a7ba92d871bb440b7b8c8a12c1e90f9aa10df3be
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43105337"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnostické funkce pro stavové služby Reliable Services
Azure Service Fabric stavová spolehlivé služby StatefulServiceBase třídy vysílá [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) události, které slouží k ladění služby, poskytují přehled o fungování modul runtime je a odstraňování potíží.

## <a name="eventsource-events"></a>Událostí EventSource
EventSource – název pro třídu StatefulServiceBase služby Reliable Stateful je "Microsoft-ServiceFabric-Services." Události z tohoto zdroje událostí se zobrazí v [diagnostické události](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) okna, je-li tato služba je právě [ladit v sadě Visual Studio](service-fabric-debugging-your-application.md).

Příklady nástrojů a technologií, které pomáhají při shromažďování nebo zobrazování událostí EventSource [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)a [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Události
| Název události | ID události | Úroveň | Popis události |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informační |Když je spuštěná úloha RunAsync služby |
| StatefulRunAsyncCancellation |2 |Informační |Emitovány při zrušení úkolu RunAsync služby |
| StatefulRunAsyncCompletion |3 |Informační |Po dokončení úlohy RunAsync služby, protože ho |
| StatefulRunAsyncSlowCancellation |4 |Upozornění |Když se úloha RunAsync služby trvá moc dlouho na dokončení zrušení |
| StatefulRunAsyncFailure |5 |Chyba |Když se vyvolá výjimku, RunAsync úloha služby |

## <a name="interpret-events"></a>Interpretovat události
StatefulRunAsyncInvocation StatefulRunAsyncCompletion a StatefulRunAsyncCancellation události jsou užitečné pro zapisovač služby pochopit životní cyklus služby, jakož i časování když služba spustí, zruší nebo dokončení. Tyto informace můžou být užitečné při ladění problémů se službou a pochopení životního cyklu služeb.

Služba zapisovače by měl pozornosti StatefulRunAsyncSlowCancellation a StatefulRunAsyncFailure události vzhledem k tomu, že indikují problémy se službou.

StatefulRunAsyncFailure je vygenerován pokaždé, když se úloha RunAsync() služby dojde k výjimce. Výjimka vyvolaná obvykle znamená chybu nebo chyby ve službě. Kromě toho výjimky způsobí, že služba selže, tak se přesune na jiný uzel. Tato operace může být drahé a může zpozdit příchozí požadavky, zatímco se přesune služby. Služba zapisovače by měl určit příčinu chyby a pokud je to možné zmírnit.

StatefulRunAsyncSlowCancellation je vygenerován pokaždé, když se požadavek na zrušení úkolu RunAsync trvá déle než čtyři sekundy. Pokud služby trvá příliš dlouho na dokončení zrušení, ovlivňuje službu schopnost rychle restartovat na jiném uzlu. Tento scénář může mít vliv na celkovou dostupnost služby.

## <a name="performance-counters"></a>Čítače výkonu
Reliable Services v modulu runtime definuje následující kategorie čítačů výkonu:

| Kategorie | Popis |
| --- | --- |
| Transakční replikátor Service Fabricu |Čítače specifické pro transakční Replikátor Service Fabricu Azure |
| Service Fabric TStore |Čítače specifické pro Azure Service Fabric TStore |

Transakční Replikátor Service Fabricu používá [Reliable State Manager](service-fabric-reliable-services-reliable-collections-internals.md) k replikaci transakcí v rámci dané sady [repliky](service-fabric-concepts-replica-lifecycle.md).

Je součástí používanou v Service Fabric TStore [Reliable Collections](service-fabric-reliable-services-reliable-collections-internals.md) pro ukládání a načítání páry klíč hodnota.

[Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) aplikace, která je k dispozici ve výchozím nastavení v operačním systému Windows umožňuje shromažďovat a zobrazovat data čítače výkonu. [Diagnostika Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možností pro shromažďování dat čítače výkonu a pak ho nahrát do tabulky Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítačů výkonu
Cluster, který má velký počet modelu reliable services nebo spolehlivé služby oddílů bude mít velký počet instancí čítače výkonu transakční replikátor. To platí také pro čítače výkonu TStore, ale také se vynásobí číslo spolehlivé slovníky a spolehlivé fronty používají. Názvy instancí čítačů výkonu může pomoci při identifikaci konkrétní [oddílu](service-fabric-concepts-partitioning.md), repliku služby a poskytovatel stavu v případě TStore, který je přidružen instance čítače výkonu.

#### <a name="service-fabric-transactional-replicator-category"></a>Kategorie transakční Replikátor Service Fabricu
Kategorie `Service Fabric Transactional Replicator`, názvy instancí čítačů jsou v následujícím formátu:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* je řetězcové vyjádření ID oddílu Service Fabric, který je přidružen instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcovou reprezentaci je generován prostřednictvím [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) specifikátorem formátu "D".

*ServiceFabricReplicaId* je ID přidružené k dané repliky spolehlivé služby. ID repliky je součástí název instance čítače výkonu a zajistí jeho jedinečnost nedošlo ke konfliktu s jinými instancemi čítače výkonu generovaných stejného oddílu. Najdete další podrobnosti o repliky a jejich role v modelu reliable services [tady](service-fabric-concepts-replica-lifecycle.md).

Následující název instance čítačů je typické pro čítače v rámci `Service Fabric Transactional Replicator` kategorie:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

V předchozím příkladu `00d0126d-3e36-4d68-98da-cc4f7195d85e` je řetězcové vyjádření ID oddílu Service Fabric a `131652217797162571` je ID repliky.

#### <a name="service-fabric-tstore-category"></a>Service Fabric TStore kategorie
Kategorie `Service Fabric TStore`, názvy instancí čítačů jsou v následujícím formátu:

`ServiceFabricPartitionId:ServiceFabricReplicaId:ServiceFabricStateProviderId_PerformanceCounterInstanceDifferentiator`

*ServiceFabricPartitionId* je řetězcové vyjádření ID oddílu Service Fabric, který je přidružen instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcovou reprezentaci je generován prostřednictvím [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) specifikátorem formátu "D".

*ServiceFabricReplicaId* je ID přidružené k dané repliky spolehlivé služby. ID repliky je součástí název instance čítače výkonu a zajistí jeho jedinečnost nedošlo ke konfliktu s jinými instancemi čítače výkonu generovaných stejného oddílu. Najdete další podrobnosti o repliky a jejich role v modelu reliable services [tady](service-fabric-concepts-replica-lifecycle.md).

*ServiceFabricStateProviderId* je Identifikátor přidružený stav poskytovatele v rámci spolehlivé služby. ID stavu poskytovatel je součástí název instance čítače výkonu k rozlišení TStore z jiného.

*PerformanceCounterInstanceDifferentiator* je odlišující ID přidružené k instanci čítače výkonu v rámci zprostředkovatele stavu. Tento rozdíl placenými je název instance čítače výkonu a zajistí jeho jedinečnost nedošlo ke konfliktu s jinými instancemi čítače výkonu generovaných stejný zprostředkovatel stavu součástí.

Následující název instance čítačů je typické pro čítače v rámci `Service Fabric TStore` kategorie:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337`

V předchozím příkladu `00d0126d-3e36-4d68-98da-cc4f7195d85e` je řetězcové vyjádření ID oddílu Service Fabric `131652217797162571` je ID repliky `142652217797162571` je Identifikátor zprostředkovatele stavu a `1337` je rozdíl placenými výkonu čítač instance.

### <a name="transactional-replicator-performance-counters"></a>Čítače výkonu transakční Replikátor

Modul runtime Reliable Services generuje následující události v rámci `Service Fabric Transactional Replicator` kategorie

 Název čítače | Popis |
| --- | --- |
| Operace zahájení transakce/s | Počet nových zápisu transakce vytvořené za sekundu.|
| Operace s transakcemi/s | Počet operací přidání, aktualizace nebo odstranění provedených na spolehlivé kolekce za sekundu.|
| Střední Latence vyprázdnění (ms) | Počet bajtů vyprazdňuje na disk transakční Replikátor za sekundu |
| Omezené operace/s | Počet operací odmítl za sekundu, které transakční Replikátor kvůli omezování. |
| Střední Ms transakce na potvrzení | Latence průměrné potvrzení na transakci v milisekundách |
| Střední Latence vyprázdnění (ms) | Průměrná doba trvání vyprázdnění operací disku Inicializuje transakční Replikátor v milisekundách |

### <a name="tstore-performance-counters"></a>Čítače výkonu TStore

Modul runtime Reliable Services generuje následující události v rámci `Service Fabric TStore` kategorie

 Název čítače | Popis |
| --- | --- |
| Počet položek | Počet položek v úložišti.|
| Velikost disku | Celkové velikosti disku, v bajtech souborů kontrolních bodů pro úložiště.|
| Bajty zápisu do souboru kontrolního bodu/s | Počet bajtů zapsaných za sekundu pro aktuální soubor kontrolního bodu.|
| Bajty přenosu při kopírování na disk / s | Počet bajtů disku (v primární replice) čteným nebo zapsaným (na sekundární replice) za sekundu během kopii úložiště.|

## <a name="next-steps"></a>Další postup
[Poskytovatelé EventSource v PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
