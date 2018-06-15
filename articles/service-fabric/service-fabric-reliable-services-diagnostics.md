---
title: Azure Service Fabric stavové služby Reliable Services diagnostiky | Microsoft Docs
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
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 268ec61515f438fb7f98b6cef7a8ec60ba22e23f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212632"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnostické funkce pro stavové služby Reliable Services
Třída Azure Service Fabric Stateful spolehlivé služby StatefulServiceBase vysílá [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) události, které slouží k ladění služby, poskytují přehled o tom, jak je modul runtime provoz a pomáhají při řešení problémů.

## <a name="eventsource-events"></a>EventSource události
Název EventSource pro třídu Stateful spolehlivé služby StatefulServiceBase je "Microsoft-ServiceFabric-Services." Události z tohoto zdroje událostí se zobrazí ve [diagnostiky události](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) okno při službu, která má být [ladit v sadě Visual Studio](service-fabric-debugging-your-application.md).

Příklady nástroje a technologie, které pomáhají v shromažďování nebo zobrazování událostí EventSource [nástroje PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)a [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Události
| Název události | ID události | Úroveň | Popis události |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informační |Když se úloha RunAsync služba je spuštěna. |
| StatefulRunAsyncCancellation |2 |Informační |Když se úloha RunAsync služby je zrušená. |
| StatefulRunAsyncCompletion |3 |Informační |Když se úloha RunAsync služby je dokončeno. |
| StatefulRunAsyncSlowCancellation |4 |Upozornění |Když se úloha RunAsync služby trvá příliš dlouho dokončení zrušení |
| StatefulRunAsyncFailure |5 |Chyba |Když se vyvolá výjimku, úloha RunAsync služby |

## <a name="interpret-events"></a>Interpretace události
Je užitečné do zapisovače služby pochopit životní cyklus služby, jakož i časování když služba spustí, zruší nebo dokončení StatefulRunAsyncInvocation, StatefulRunAsyncCompletion a StatefulRunAsyncCancellation události. Tato informace může být užitečné při ladění problémů služby nebo pochopení životního cyklu služby.

Služba zapisovače měli věnovat pozornost zavřít StatefulRunAsyncSlowCancellation a StatefulRunAsyncFailure události, protože indikují problémy se službou.

StatefulRunAsyncFailure jsou vydávány vždy, když úloha RunAsync() služby vyvolá výjimku. Výjimka vyvolaná obvykle označuje chyby nebo chyby ve službě. Kromě toho výjimka způsobí selhání, služby, se přesune do jiného uzlu. Tato operace může být drahé a může pozdržet příchozí požadavky během přesouvání služby. Služba zapisovače by měl zjistit příčinu výjimky a pokud je to možné, ji zmírnit.

StatefulRunAsyncSlowCancellation jsou vydávány vždy, když na žádost o zrušení úlohy RunAsync trvá déle než 4 sekundy. Pokud služby trvá příliš dlouho dokončení zrušení, ovlivní schopnost služby rychle restartována na jiný uzel. Tento scénář může mít vliv na celkovou dostupnost služby.

## <a name="performance-counters"></a>Čítače výkonu
Spolehlivé služby modulu runtime definuje následující kategorie čítače výkonu:

| Kategorie | Popis |
| --- | --- |
| Transakční replikátor Service Fabricu |Čítače specifické pro transakční Replikátor Azure Service Fabric |

Transakční Replikátor Service Fabric používá [spolehlivé správce stavu](service-fabric-reliable-services-reliable-collections-internals.md) replikace transakce v rámci dané sady [repliky](service-fabric-concepts-replica-lifecycle.md). 

[Sledování výkonu systému Windows](https://technet.microsoft.com/library/cc749249.aspx) aplikace, která je k dispozici v operačním systému Windows ve výchozím nastavení lze shromažďovat a zobrazovat data čítače výkonu. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možností pro shromažďování dat čítačů výkonu a pak ho nahrát do tabulek Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítače výkonu
Cluster, který má velký počet spolehlivé služby nebo oddílů spolehlivé služby bude mít velký počet instancí čítače výkonu transakční replikátor. Názvy instancí čítače výkonu může pomoci při identifikaci konkrétní [oddílu](service-fabric-concepts-partitioning.md) a repliky služby, které je přidružené instance čítače výkonu.

#### <a name="service-fabric-transactional-replicator-category"></a>Kategorie transakční Replikátor Service Fabric
Pro kategorii `Service Fabric Transactional Replicator`, názvy instancí čítače jsou v následujícím formátu:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* je řetězcová reprezentace Service Fabric ID oddílu, který je přidružen instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcovou reprezentaci vygeneruje prostřednictvím [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) s specifikátor formátu "D".

*ServiceFabricReplicaId* je ID přidružené k dané repliky spolehlivé služby. Název instance čítače výkonu k zajištění jeho jedinečnosti a aby nedošlo ke konfliktu s další instance čítače výkonu generovaných stejného oddílu je součástí ID repliky. Můžete najít další podrobnosti o repliky a jejich role v spolehlivé služby [zde](service-fabric-concepts-replica-lifecycle.md).

Název instance následující čítač je typické pro čítače v části `Service Fabric Transactional Replicator` kategorie:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

V předchozím příkladu `00d0126d-3e36-4d68-98da-cc4f7195d85e` je řetězcová reprezentace Identifikátor oddílu Service Fabric a `131652217797162571` je ID repliky.

### <a name="transactional-replicator-performance-counters"></a>Transakční Replikátor čítače výkonu

Spolehlivé služby modulu runtime vysílá následující události v rámci `Service Fabric Transactional Replicator` kategorie

 Název čítače | Popis |
| --- | --- |
| Operace zahájení transakce/s | Počet nové zápisu transakce vytvořené za sekundu.|
| Operace s transakcemi/s | Počet operací přidání, aktualizace nebo odstranění provést u spolehlivé kolekcí za sekundu.|
| Střední Vyprázdnění latence (ms) | Počet bajtů za sekundu, které transakční Replikátor vyprazdňuje na disk |
| Omezené operace/s | Počet operací odmítl za sekundu, které transakční Replikátor kvůli omezování. |
| Střední Transakce ms/potvrzení | Potvrzení Průměrná latence v jedné transakci v milisekundách |
| Střední Vyprázdnění latence (ms) | Průměrná doba operace vyprazdňování disku iniciovaná transakční Replikátor v milisekundách |

## <a name="next-steps"></a>Další postup
[Zprostředkovatelé EventSource v nástroje PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
