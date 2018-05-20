---
title: Monitorování a Diagnostika aktéři | Microsoft Docs
description: Tento článek popisuje diagnostiky a funkce v modulu runtime Service Fabric Reliable Actors, včetně události a čítače výkonu vysílaných ho pro sledování výkonu.
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: timlt
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: 9b4825be7ce7fb05b109310f21cd65cfe3819ae8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostika a sledování výkonu služby Reliable Actors
Modul runtime Reliable Actors vysílá [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) události a [čítače výkonu](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Tyto poskytují přehled o tom, jak pracuje modul runtime a pomáhají při řešení potíží a monitorování výkonu.

## <a name="eventsource-events"></a>EventSource události
Název zprostředkovatele EventSource pro modul runtime Reliable Actors je "Microsoft-ServiceFabric-aktéři". Události z tohoto zdroje událostí se zobrazí ve [diagnostiky události](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) okno při objektu actor aplikace se [ladit v sadě Visual Studio](service-fabric-debugging-your-application.md).

Příklady nástroje a technologie, které pomáhají v shromažďování nebo zobrazování událostí EventSource [nástroje PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [sémantické protokolování](https://msdn.microsoft.com/library/dn774980.aspx)a [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Klíčová slova
Všechny události, které patří do spolehlivé EventSource aktéři jsou přidruženy k jedné nebo více klíčových slov. To umožňuje filtrování událostí, které byly shromážděny. Následující bits – klíčové slovo jsou definovány.

| Bit | Popis |
| --- | --- |
| 0x1 |Nastavit o důležitých událostech, které shrnují operaci modulu runtime aktéři prostředků infrastruktury. |
| 0x2 |Sadu událostí, které popisují volání objektu actor metod. Další informace najdete v tématu [úvodní téma na aktéři](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Sada událostí souvisejících s stavu objektu actor. Další informace naleznete v tématu na [správy stavu objektu actor](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Sada událostí souvisejících s na základě zapnout souběžnost v objektu actor. Další informace naleznete v tématu na [souběžnosti](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Čítače výkonu
Modul runtime Reliable Actors definuje následující kategorie čítače výkonu.

| Kategorie | Popis |
| --- | --- |
| Objekt actor pro Service Fabric |Čítače specifické pro Azure Service Fabric aktéři, například čas potřebný k uložení stavu objektu actor |
| Metoda objektu actor pro Service Fabric |Čítače specifické pro metody implementované aktéři Service Fabric, například jak často objektu actor je volána metoda |

Každá z výše uvedených kategorií má jeden nebo více čítačů.

[Sledování výkonu systému Windows](https://technet.microsoft.com/library/cc749249.aspx) aplikace, která je k dispozici v operačním systému Windows ve výchozím nastavení lze shromažďovat a zobrazovat data čítače výkonu. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možností pro shromažďování dat čítačů výkonu a pak ho nahrát do tabulek Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítače výkonu
Cluster, který má velký počet služeb objektu actor nebo oddílů služby objektu actor bude mít velký počet instancí čítače výkonu objektu actor. Názvy instancí čítače výkonu může pomoci při identifikaci konkrétní [oddílu](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) metoda objektu actor (pokud existuje) a který je přidružen instance čítače výkonu.

#### <a name="service-fabric-actor-category"></a>Kategorie prostředků infrastruktury služby objektu Actor
Pro kategorii `Service Fabric Actor`, názvy instancí čítače jsou v následujícím formátu:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* je řetězcová reprezentace Service Fabric ID oddílu, který je přidružen instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcovou reprezentaci vygeneruje prostřednictvím [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoda s specifikátor formátu "D".

*ActorRuntimeInternalID* je řetězcová reprezentace 64bitové celé číslo, který je generovaný runtime aktéři prostředků infrastruktury pro interní použití. To je součástí název instance čítače výkonu k zajištění jeho jedinečnosti a aby nedošlo ke konfliktu s další názvy instancí čítače výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

Následuje příklad názvu instance čítače čítače, které patří `Service Fabric Actor` kategorie:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

V příkladu nahoře `2740af29-78aa-44bc-a20b-7e60fb783264` je řetězcová reprezentace Identifikátor oddílu Service Fabric a `635650083799324046` je 64-bit ID, které se generuje pro modul runtime interní použití.

#### <a name="service-fabric-actor-method-category"></a>Metoda objektu Actor pro Service Fabric kategorie
Pro kategorii `Service Fabric Actor Method`, názvy instancí čítače jsou v následujícím formátu:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* je název objektu actor metody, které je přidružené instance čítače výkonu. Formát název metody je určen na základě některé postupu v modulu runtime aktéři prostředků infrastruktury, který vyrovnává čitelnost názvu s omezeními na maximální délku názvy instancí čítače výkonu v systému Windows.

*ActorsRuntimeMethodId* je řetězcová reprezentace 32bitové celé číslo, který je generovaný runtime aktéři prostředků infrastruktury pro interní použití. To je součástí název instance čítače výkonu k zajištění jeho jedinečnosti a aby nedošlo ke konfliktu s další názvy instancí čítače výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

*ServiceFabricPartitionID* je řetězcová reprezentace Service Fabric ID oddílu, který je přidružen instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcovou reprezentaci vygeneruje prostřednictvím [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoda s specifikátor formátu "D".

*ActorRuntimeInternalID* je řetězcová reprezentace 64bitové celé číslo, který je generovaný runtime aktéři prostředků infrastruktury pro interní použití. To je součástí název instance čítače výkonu k zajištění jeho jedinečnosti a aby nedošlo ke konfliktu s další názvy instancí čítače výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

Následuje příklad názvu instance čítače čítače, které patří `Service Fabric Actor Method` kategorie:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

V příkladu nahoře `ivoicemailboxactor.leavemessageasync` je název metody `2` je 32bitové ID generovaná pro interní použití modulu runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` je řetězcová reprezentace Identifikátor oddílu Service Fabric a `635650083804480486` je ID 64-bit, vygeneruje pro modul runtime interní používat.

## <a name="list-of-events-and-performance-counters"></a>Seznam událostí a čítačů výkonu
### <a name="actor-method-events-and-performance-counters"></a>Události metod objektu actor a čítače výkonu
Modul runtime Reliable Actors vysílá následující události související s [metody objektu actor](service-fabric-reliable-actors-introduction.md).

| Název události | ID události | Úroveň | – Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Podrobný |0x2 |Modul runtime aktéři je vyvolat metodu objektu actor. |
| ActorMethodStop |8 |Podrobný |0x2 |Metoda objektu actor byl dokončen. To znamená vrátila modul runtime asynchronního volání metody objektu actor a dokončení úlohy vráceném metodou objektu actor. |
| ActorMethodThrewException |9 |Upozornění |0x3 |Došlo k výjimce během provádění metody objektu actor během asynchronního volání metody objektu actor modul runtime nebo během provádění úlohy vráceném metodou objektu actor. Tato událost ukazuje nějaká chyba v objektu actor kód, který potřebuje šetření. |

Modul runtime Reliable Actors publikuje týkající se spouštění metody objektu actor následující čítače výkonu.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Metoda objektu actor pro Service Fabric |Vyvolání/s |Počet pokusů, které metody služby objektu actor vyvolala za sekundu |
| Metoda objektu actor pro Service Fabric |Průměrný počet milisekund na vyvolání |Doba v milisekundách, jakou trvalo spuštění metody služby objektu actor |
| Metoda objektu actor pro Service Fabric |Vyvolané výjimky/s |Počet pokusů, že metoda služby objektu actor výjimku za sekundu |

### <a name="concurrency-events-and-performance-counters"></a>Concurrency události a čítače výkonu
Modul runtime Reliable Actors vysílá následující události související s [souběžnosti](service-fabric-reliable-actors-introduction.md#concurrency).

| Název události | ID události | Úroveň | – Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Podrobný |0x8 |Tato událost se zapíše na začátku každé nové zapnout v objektu actor. Obsahuje počet nevyřízených volání objektu actor čekajících na získání zámku na objektu actor, která vynucuje na základě zapnout souběžnosti. |

Modul runtime Reliable Actors publikuje následující čítače výkonu související s souběžnosti.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Objekt actor pro Service Fabric |Počet volání objektu actor čekajících na jeho zámek |Počet nevyřízených volání objektu actor čekajících na získání zámku na objektu actor, která vynucuje na základě zapnout souběžnosti |
| Objekt actor pro Service Fabric |Průměrný počet milisekund čekání na zámek |Doba (v milisekundách) získat zámek na objektu actor, která vynucuje na základě zapnout souběžnosti |
| Objekt actor pro Service Fabric |Průměrný počet milisekund blokování zámku objektu actor |Čas (v milisekundách), pro kterou je blokován zámek na objektu actor |

### <a name="actor-state-management-events-and-performance-counters"></a>Události správy stavu objektu actor a čítače výkonu
Modul runtime Reliable Actors vysílá následující události související s [správy stavu objektu actor](service-fabric-reliable-actors-state-management.md).

| Název události | ID události | Úroveň | – Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Podrobný |0x4 |Modul runtime aktéři je uložení stavu objektu actor. |
| ActorSaveStateStop |11 |Podrobný |0x4 |Modul runtime aktéři dokončil ukládání stavu objektu actor. |

Modul runtime Reliable Actors publikuje následující čítače výkonu související se správou stavu objektu actor.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Objekt actor pro Service Fabric |Průměrný počet milisekund na operaci uložení stavu |Doba v milisekundách, jakou trvalo uložení stavu objektu actor |
| Objekt actor pro Service Fabric |Průměrný počet milisekund na operaci načtení stavu |Doba v milisekundách, jakou trvalo načtení stavu objektu actor |

### <a name="events-related-to-actor-replicas"></a>Události související s repliky objektu actor
Modul runtime Reliable Actors vysílá následující události související s [objektu actor repliky](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Název události | ID události | Úroveň | – Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informační |0x1 |Repliky objektu actor změnit primární roli. To znamená, že aktéři pro tento oddíl bude vytvořen uvnitř této repliky. |
| ReplicaChangeRoleFromPrimary |2 |Informační |0x1 |Repliky objektu actor změní na není primární roli. To znamená, že aktéři pro tento oddíl bude vytvořen již uvnitř této repliky. Žádné nové požadavky budou doručeny do aktéři už vytvořený v rámci této repliky. Po dokončení všech žádostí probíhá budou aktéři zničena. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Objektu actor aktivace a deaktivace události a čítače výkonu
Modul runtime Reliable Actors vysílá následující události související s [objektu actor aktivace a deaktivace](service-fabric-reliable-actors-lifecycle.md).

| Název události | ID události | Úroveň | – Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informační |0x1 |Objekt actor byl aktivován. |
| ActorDeactivated |6 |Informační |0x1 |Objekt actor bylo deaktivováno. |

Modul runtime Reliable Actors publikuje následující čítače výkonu související s objektu actor aktivace a deaktivace.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Objekt actor pro Service Fabric |Průměrná doba metody OnActivateAsync v milisekundách |Doba v milisekundách, jakou trvalo provádění metody OnActivateAsync |

### <a name="actor-request-processing-performance-counters"></a>Čítače výkonu zpracování žádosti objektu actor
Když klient vyvolá metodu prostřednictvím objektu actor proxy, výsledkem zprávu požadavku služby objektu actor odesílána přes síť. Služba zpracovává zprávu požadavku a odešle odpověď zpět klientovi. Modul runtime Reliable Actors publikuje následující čítače výkonu související s zpracování žádosti objektu actor.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Objekt actor pro Service Fabric |Počet zbývajících požadavků |Počet požadavků zpracovaných ve službě |
| Objekt actor pro Service Fabric |Průměrný počet milisekund na požadavek |Doba trvání (v milisekundách) pomocí služby zpracovat požadavek |
| Objekt actor pro Service Fabric |Průměrný počet milisekund deserializace požadavků |Doba (v milisekundách) k deserializaci zprávu žádosti objektu actor, když je obdržena na službu |
| Objekt actor pro Service Fabric |Průměrný počet milisekund serializace odpovědí |Doba (v milisekundách) k serializaci objektu actor zprávu odpovědi na službu předtím, než odešle odpověď klienta |

## <a name="next-steps"></a>Další postup
* [Jak používat Reliable Actors platformy Service Fabric](service-fabric-reliable-actors-platform.md)
* [Referenční dokumentace rozhraní API objektu actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázka kódu](https://github.com/Azure/servicefabric-samples)
* [Zprostředkovatelé EventSource v nástroje PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
