---
title: Azure Service Fabric stavová Diagnostika Reliable Services
description: Diagnostické funkce pro stavové Reliable Services v Azure Service Fabric
ms.topic: conceptual
ms.date: 8/24/2018
ms.openlocfilehash: 774a771d0c9701076a5d6c070963bf6224a571dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789326"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnostické funkce pro stavové služby Reliable Services
Stavová Reliable Services StatefulServiceBase třídy Azure Service Fabric emituje události [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) , které se dají použít k ladění služby, poskytování přehledů o fungování modulu runtime a k řešení potíží.

## <a name="eventsource-events"></a>Události EventSource
Název EventSource pro stavovou Reliable Services třídy StatefulServiceBase je "Microsoft-ServiceFabric-Services". Události z tohoto zdroje událostí se zobrazí v okně [diagnostické události](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) při [ladění služby v aplikaci Visual Studio](service-fabric-debugging-your-application.md).

Příklady nástrojů a technologií, které vám pomůžou při shromažďování a zobrazování událostí EventSource, jsou [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)a [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Události
| Název události | ID události | Level | Popis události |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informační |Vygenerováno při spuštění úlohy služby RunAsync |
| StatefulRunAsyncCancellation |2 |Informační |Vygenerováno při zrušení úlohy RunAsync služby |
| StatefulRunAsyncCompletion |3 |Informační |Vygenerováno po dokončení úkolu služby RunAsync |
| StatefulRunAsyncSlowCancellation |4 |Upozornění |Vygenerováno, když úloha RunAsync služby trvá příliš dlouho k dokončení zrušení |
| StatefulRunAsyncFailure |5 |Chyba |Vygenerováno, když úloha RunAsync služby vyvolá výjimku. |

## <a name="interpret-events"></a>Interpretace událostí
Události StatefulRunAsyncInvocation, StatefulRunAsyncCompletion a StatefulRunAsyncCancellation jsou užitečné pro zapisovač služby pro pochopení životního cyklu služby a také časování při spuštění služby, zrušení nebo dokončení. Tyto informace mohou být užitečné při ladění problémů se službou nebo porozumění životnímu cyklu služby.

Zapisovače služeb by měly věnovat pozornost všem událostem StatefulRunAsyncSlowCancellation a StatefulRunAsyncFailure, protože označují problémy se službou.

StatefulRunAsyncFailure je generována vždy, když úloha RunAsync () vyvolá výjimku. Vyvolaná výjimka obvykle indikuje chybu nebo chybu ve službě. Kromě toho výjimka způsobí selhání služby, takže je přesunuta do jiného uzlu. Tato operace může být náročná a může zpozdit příchozí požadavky při přesunu služby. Zapisovače služeb by měly určit příčinu výjimky a, pokud je to možné, zmírnit je.

StatefulRunAsyncSlowCancellation je vygenerována vždy, když žádost o zrušení úlohy RunAsync trvá déle než čtyři sekundy. V případě, že dokončení zrušení služby trvá příliš dlouho, má vliv na možnost rychlého restartování služby na jiném uzlu. Tento scénář může mít vliv na celkovou dostupnost služby.

## <a name="performance-counters"></a>Čítače výkonu
Reliable Services Runtime definuje následující kategorie čítače výkonu:

| Kategorie | Popis |
| --- | --- |
| Service Fabric transakčního replikátoru |Čítače specifické pro transakční Replikátor Azure Service Fabric |
| Service Fabric TStore |Čítače specifické pro Azure Service Fabric TStore |

Service Fabric transakční Replikátor používá [správce spolehlivého stavu](./service-fabric-reliable-services-introduction.md) k replikaci transakcí v dané sadě [replik](service-fabric-concepts-replica-lifecycle.md).

Service Fabric TStore je komponenta používaná ve [spolehlivých kolekcích](./service-fabric-reliable-services-introduction.md) pro ukládání a načítání párů klíč-hodnota.

Aplikace [sledování výkonu systému Windows](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) , která je k dispozici ve výchozím nastavení v operačním systému Windows, se dá použít ke shromažďování a zobrazování dat čítače výkonu. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možnost pro shromažďování dat čítače výkonu a jejich nahrání do tabulek Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítače výkonu
Cluster s velkým počtem spolehlivých služeb nebo spolehlivých oddílů služby bude mít velký počet instancí čítače výkonu transakční replikace. To platí i pro čítače výkonu TStore, ale vynásobí se také počtem spolehlivých slovníků a spolehlivých front. Název instance čítače výkonu může pomáhat při identifikaci konkrétního [oddílu](service-fabric-concepts-partitioning.md), repliky služby a poskytovatele stavu v případě TStore, ke kterému je přiřazena instance čítače výkonu.

#### <a name="service-fabric-transactional-replicator-category"></a>Service Fabric kategorie transakčního replikátoru
Pro kategorii `Service Fabric Transactional Replicator` jsou názvy instancí čítače v následujícím formátu:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* je řetězcová reprezentace ID oddílu Service Fabric, ke které je instance čítače výkonu přidružena. IDENTIFIKÁTOR oddílu je identifikátor GUID a řetězcová reprezentace je generována pomocí [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) specifikátoru formátu "D".

*ServiceFabricReplicaId* je ID přidružené k dané replice spolehlivé služby. ID repliky je zahrnuté v názvu instance čítače výkonu, aby se zajistila jeho jedinečnost a nedocházelo ke konfliktu s dalšími instancemi čítače výkonu generovanými stejným oddílem. Další podrobnosti o replikách a jejich roli ve spolehlivých službách najdete [tady](service-fabric-concepts-replica-lifecycle.md).

Následující název instance čítače je typický pro čítač v `Service Fabric Transactional Replicator` kategorii:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

V předchozím příkladu `00d0126d-3e36-4d68-98da-cc4f7195d85e` je řetězcová reprezentace ID oddílu Service Fabric a `131652217797162571` je ID repliky.

#### <a name="service-fabric-tstore-category"></a>Service Fabric kategorie TStore
Pro kategorii `Service Fabric TStore` jsou názvy instancí čítače v následujícím formátu:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* je řetězcová reprezentace ID oddílu Service Fabric, ke které je instance čítače výkonu přidružena. IDENTIFIKÁTOR oddílu je identifikátor GUID a řetězcová reprezentace je generována pomocí [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) specifikátoru formátu "D".

*ServiceFabricReplicaId* je ID přidružené k dané replice spolehlivé služby. ID repliky je zahrnuté v názvu instance čítače výkonu, aby se zajistila jeho jedinečnost a nedocházelo ke konfliktu s dalšími instancemi čítače výkonu generovanými stejným oddílem. Další podrobnosti o replikách a jejich roli ve spolehlivých službách najdete [tady](service-fabric-concepts-replica-lifecycle.md).

*StateProviderId* je ID přidružené ke zprostředkovateli stavu v rámci spolehlivé služby. ID zprostředkovatele stavu je součástí názvu instance čítače výkonu, aby bylo možné odlišit TStore od jiného.

*PerformanceCounterInstanceDifferentiator* je rozdílné ID přidružené k instanci čítače výkonu v rámci zprostředkovatele stavu. Tento rozdíl je obsažen v názvu instance čítače výkonu, aby bylo zajištěno jeho jedinečnost a bylo bráněno v konfliktu s jinými instancemi čítače výkonu generovanými stejným poskytovatelem stavu.

*StateProviderName* je název přidružený ke zprostředkovateli stavu v rámci spolehlivé služby. Název zprostředkovatele stavu je obsažen v názvu instance čítače výkonu, aby uživatelé mohli snadno identifikovat stav, který poskytuje.

Následující název instance čítače je typický pro čítač v `Service Fabric TStore` kategorii:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

V předchozím příkladu `00d0126d-3e36-4d68-98da-cc4f7195d85e` je řetězcové vyjádření ID oddílu Service Fabric, `131652217797162571` je ID repliky, `142652217797162571` je ID zprostředkovatele stavu a `1337` je rozdíl instance čítače výkonu. `urn:MyReliableDictionary/dataStore` je název zprostředkovatele stavu, který ukládá data pro kolekci s názvem `urn:MyReliableDictionary` .

### <a name="transactional-replicator-performance-counters"></a>Čítače výkonu transakčního replikátoru

Modul runtime Reliable Services emituje následující události pod kategorií. `Service Fabric Transactional Replicator`

 Název čítače | Description |
| --- | --- |
| Zahájit operace TXN za sekundu | Počet nových transakcí zápisu vytvořených za sekundu.|
| Operace TXN/s | Počet operací přidání/aktualizace/odstranění provedených na spolehlivých kolekcích za sekundu.|
| Bajty vyprázdnění protokolu/s | Počet bajtů vyprázdněných na disk službou Transaction Replicator za sekundu |
| Omezené operace za sekundu | Počet operací, které Replikátor transakčních operací zamítl za sekundu kvůli omezení. |
| Průměrná transakce MS/Commit | Průměrná latence potvrzení na transakci v milisekundách |
| Střední latence vyprázdnění (MS) | Průměrná doba trvání operací vyprázdnění disku inicializovaných transakčním replikátorem v milisekundách |

### <a name="tstore-performance-counters"></a>Čítače výkonu TStore

Modul runtime Reliable Services emituje následující události pod kategorií. `Service Fabric TStore`

 Název čítače | Description |
| --- | --- |
| Počet položek | Počet položek v úložišti.|
| Velikost disku | Celková velikost disku (v bajtech) souborů kontrolních bodů pro úložiště.|
| Bajty zápisu do souboru kontrolního bodu/s | Počet bajtů zapsaných za sekundu pro poslední soubor kontrolního bodu.|
| Kopírovat bajty přenosů disku/s | Počet přečtených bajtů disku (na primární replice) nebo napsaný (v sekundární replice) za sekundu během kopie úložiště.|

## <a name="next-steps"></a>Další kroky
[Zprostředkovatelé EventSource v PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
