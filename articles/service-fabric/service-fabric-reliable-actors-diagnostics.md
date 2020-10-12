---
title: Diagnostika a monitorování objektů actor
description: Tento článek popisuje funkce monitorování diagnostiky a výkonu v prostředí Service Fabric Reliable Actors runtime, včetně událostí a čítačů výkonu, které vysílá.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: a38a11d9cf062cd0a45890d43afe9b2530b2b7bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258457"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostika a sledování výkonu služby Reliable Actors
Modul runtime Reliable Actors generuje události [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1) a [čítače výkonu](/dotnet/api/system.diagnostics.performancecounter?view=dotnet-plat-ext-3.1). Tyto informace poskytují přehled o tom, jak modul runtime pracuje a který umožňuje řešení potíží a monitorování výkonu.

## <a name="eventsource-events"></a>Události EventSource
Název zprostředkovatele EventSource pro modul runtime Reliable Actors je "Microsoft-ServiceFabric-Actors". Události z tohoto zdroje událostí se zobrazí v okně [diagnostické události](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) při ladění aplikace objektu actor [v aplikaci Visual Studio](service-fabric-debugging-your-application.md).

Příklady nástrojů a technologií, které vám pomohou při shromažďování a zobrazování událostí EventSource, jsou [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [sémantické protokolování](/previous-versions/msp-n-p/dn774980(v=pandp.10))a [Knihovna Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Klíčová slova
Všechny události, které patří do Reliable Actors EventSource, jsou přidruženy k jednomu nebo více klíčovým slovům. To umožňuje filtrovat události, které jsou shromažďovány. Jsou definovány následující bity klíčového slova.

| 40bitového | Description |
| --- | --- |
| 0x1 |Sada důležitých událostí, které shrnují operace modulu runtime Fabric Actors. |
| 0x2 |Sada událostí, které popisují volání metod objektu actor. Další informace najdete v [úvodním tématu o hercích](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Sada událostí souvisejících se stavem objektu actor. Další informace najdete v tématu [Správa stavu objektu actor](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Sada událostí souvisejících s podporou souběžnosti v objektu actor. Další informace najdete v tématu o [souběžnosti](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Čítače výkonu
Modul runtime Reliable Actors definuje následující kategorie čítače výkonu.

| Kategorie | Popis |
| --- | --- |
| Objekt actor Service Fabric |Čítače specifické pro Azure Service Fabric Actors, například čas potřebný k uložení stavu objektu actor |
| Service Fabric – metoda objektu actor |Čítače specifické pro metody implementované Service Fabricmi aktéry, např. jak často je vyvolána metoda objektu actor |

Každá z výše uvedených kategorií má jeden nebo více čítačů.

Aplikace [sledování výkonu systému Windows](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) , která je k dispozici ve výchozím nastavení v operačním systému Windows, se dá použít ke shromažďování a zobrazování dat čítače výkonu. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možnost pro shromažďování dat čítače výkonu a jejich nahrání do tabulek Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítače výkonu
Cluster, který má velký počet služeb actor nebo oddílů služby objektu actor, bude mít velký počet instancí čítače výkonu objektu actor. Názvy instancí čítače výkonu vám pomůžou identifikovat konkrétní [oddíl](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) a metodu actor (Pokud je k dispozici), ke které je přidružená instance čítače výkonu.

#### <a name="service-fabric-actor-category"></a>Service Fabric kategorie objektu actor
Pro kategorii `Service Fabric Actor` jsou názvy instancí čítače v následujícím formátu:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* je řetězcová reprezentace ID oddílu Service Fabric, ke které je instance čítače výkonu přidružena. IDENTIFIKÁTOR oddílu je identifikátor GUID a řetězcová reprezentace je generována prostřednictvím [`Guid.ToString`](/dotnet/api/system.guid.tostring?view=netcore-3.1#System_Guid_ToString_System_String_) metody s specifikátorem formátu "D".

*ActorRuntimeInternalID* je řetězcová reprezentace 64ého celého čísla generovaného modulem runtime Fabric Actors pro své interní použití. Tato hodnota je obsažena v názvu instance čítače výkonu k zajištění jeho jedinečnosti a zabránění konfliktu s jinými názvy instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

Následuje příklad názvu instance čítače pro čítač, který patří do `Service Fabric Actor` Kategorie:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

V předchozím příkladu `2740af29-78aa-44bc-a20b-7e60fb783264` je řetězcová reprezentace ID oddílu Service Fabric a `635650083799324046` je 64 ID, které je generováno pro interní použití modulu runtime.

#### <a name="service-fabric-actor-method-category"></a>Kategorie metody Service Fabric objektu actor
Pro kategorii `Service Fabric Actor Method` jsou názvy instancí čítače v následujícím formátu:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* je název metody objektu actor, ke které je přidružena instance čítače výkonu. Formát názvu metody se určí na základě některé logiky v modulu runtime Fabric Actors, který vyrovnává čitelnost názvu s omezeními pro maximální délku názvů instancí čítače výkonu ve Windows.

*ActorsRuntimeMethodId* je řetězcová reprezentace 32ého celého čísla generovaného modulem runtime Fabric Actors pro své interní použití. Tato hodnota je obsažena v názvu instance čítače výkonu k zajištění jeho jedinečnosti a zabránění konfliktu s jinými názvy instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

*ServiceFabricPartitionID* je řetězcová reprezentace ID oddílu Service Fabric, ke které je instance čítače výkonu přidružena. IDENTIFIKÁTOR oddílu je identifikátor GUID a řetězcová reprezentace je generována prostřednictvím [`Guid.ToString`](/dotnet/api/system.guid.tostring?view=netcore-3.1#System_Guid_ToString_System_String_) metody s specifikátorem formátu "D".

*ActorRuntimeInternalID* je řetězcová reprezentace 64ého celého čísla generovaného modulem runtime Fabric Actors pro své interní použití. Tato hodnota je obsažena v názvu instance čítače výkonu k zajištění jeho jedinečnosti a zabránění konfliktu s jinými názvy instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

Následuje příklad názvu instance čítače pro čítač, který patří do `Service Fabric Actor Method` Kategorie:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

V předchozím příkladu `ivoicemailboxactor.leavemessageasync` je názvem metody, je `2` 32 ID generované pro interní použití modulu runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` je řetězcové vyjádření ID oddílu Service Fabric a `635650083804480486` je 64 identifikátor ID generovaný pro interní použití modulu runtime.

## <a name="list-of-events-and-performance-counters"></a>Seznam událostí a čítačů výkonu
### <a name="actor-method-events-and-performance-counters"></a>Události a čítače výkonu metody objektu actor
Modul runtime Reliable Actors emituje následující události související s [metodami objektu actor](service-fabric-reliable-actors-introduction.md).

| Název události | ID události | Úroveň | Klíčové slovo | Description |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Verbose |0x2 |Modul runtime objektů Actors se chystá vyvolat metodu objektu actor. |
| ActorMethodStop |8 |Verbose |0x2 |Metoda objektu actor byla dokončena. To znamená, že bylo vráceno asynchronní volání modulu runtime do metody actor a úloha vrácená metodou actor byla dokončena. |
| ActorMethodThrewException |9 |Upozornění |0x3 |Při provádění metody actor došlo k výjimce, buď během asynchronního volání modulu runtime do metody Actor, nebo během provádění úlohy vrácené metodou objektu actor. Tato událost označuje určitý druh selhání v kódu objektu actor, který potřebuje prozkoumat. |

Modul runtime Reliable Actors zveřejňuje následující čítače výkonu související s prováděním metod actor.

| Název kategorie | Název čítače | Description |
| --- | --- | --- |
| Service Fabric – metoda objektu actor |Volání za sekundu |Počet volání metody služby objektu actor za sekundu |
| Service Fabric – metoda objektu actor |Průměrný počet milisekund na vyvolání |Doba potřebná k provedení metody služby objektu actor v milisekundách |
| Service Fabric – metoda objektu actor |Vyvolané výjimky za sekundu |Počet, kolikrát vyvolala metoda služby objektu actor výjimku za sekundu |

### <a name="concurrency-events-and-performance-counters"></a>Události souběžnosti a čítače výkonu
Modul runtime Reliable Actors emituje následující události týkající se [souběžnosti](service-fabric-reliable-actors-introduction.md#concurrency).

| Název události | ID události | Úroveň | Klíčové slovo | Description |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Verbose |0x8 |Tato událost je zapsána na začátku každého nového objektu actor. Obsahuje počet čekajících volání objektu actor, která čekají na získání zámku na základě objektu actor, který vynutil souběžnost založenou na funkci. |

Modul runtime Reliable Actors zveřejňuje následující čítače výkonu týkající se souběžnosti.

| Název kategorie | Název čítače | Description |
| --- | --- | --- |
| Objekt actor Service Fabric |Počet volání objektu actor čekajících na zámek objektu actor |Počet nevyřízených volání objektu actor čekajících na získání zámku na základě objektu actor, který vynutil souběžnost založenou na funkci |
| Objekt actor Service Fabric |Průměrný počet milisekund čekání na zámek |Doba trvání (v milisekundách) pro získání zámku na základě objektu actor, který vynutil souběžnost založenou na funkci |
| Objekt actor Service Fabric |Průměrný počet milisekund blokování zámku objektu actor |Čas (v milisekundách), po který se zablokuje zámek jednotlivých objektů actor |

### <a name="actor-state-management-events-and-performance-counters"></a>Události správy stavu objektu actor a čítače výkonu
Modul runtime Reliable Actors emituje následující události související se [správou stavu objektu actor](service-fabric-reliable-actors-state-management.md).

| Název události | ID události | Úroveň | Klíčové slovo | Description |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Verbose |0x4 |Modul runtime objektů Actors se chystá uložit stav objektu actor. |
| ActorSaveStateStop |11 |Verbose |0x4 |Modul runtime objektů actor dokončil ukládání stavu objektu actor. |

Modul runtime Reliable Actors zveřejňuje následující čítače výkonu související se správou stavu objektu actor.

| Název kategorie | Název čítače | Description |
| --- | --- | --- |
| Objekt actor Service Fabric |Průměrný počet milisekund na operaci uložení stavu |Doba potřebná k uložení stavu objektu actor v milisekundách |
| Objekt actor Service Fabric |Průměrný počet milisekund na operaci načtení stavu |Doba potřebná k načtení stavu objektu actor v milisekundách |

### <a name="events-related-to-actor-replicas"></a>Události související s replikami objektu actor
Modul runtime Reliable Actors emituje následující události související s [replikami objektu actor](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Název události | ID události | Úroveň | Klíčové slovo | Description |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informační |0x1 |Replika objektu actor změnila roli na primární. To znamená, že objekty Actors pro tento oddíl budou vytvořeny v rámci této repliky. |
| ReplicaChangeRoleFromPrimary |2 |Informační |0x1 |Replika objektu actor změnila roli na jinou než primární. To znamená, že objekty Actors pro tento oddíl již nebudou v rámci této repliky vytvořeny. Do aktérů již vytvořených v rámci této repliky nebudou doručeny žádné nové žádosti. Objekty actor budou po dokončení všech probíhajících žádostí zničeny. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Události aktivace a deaktivace objektu actor a čítače výkonu
Modul runtime Reliable Actors emituje následující události týkající se [Aktivace a deaktivace objektu actor](service-fabric-reliable-actors-lifecycle.md).

| Název události | ID události | Úroveň | Klíčové slovo | Description |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informační |0x1 |Byl aktivován objekt actor. |
| ActorDeactivated |6 |Informační |0x1 |Objekt actor byl deaktivován. |

Modul runtime Reliable Actors zveřejňuje následující čítače výkonu související s aktivací a deaktivací objektu actor.

| Název kategorie | Název čítače | Description |
| --- | --- | --- |
| Objekt actor Service Fabric |Průměrná OnActivateAsync milisekundy |Čas potřebný k provedení metody OnActivateAsync v milisekundách |

### <a name="actor-request-processing-performance-counters"></a>Čítače výkonu zpracovávající žádosti objektu actor
Když klient vyvolá metodu prostřednictvím objektu proxy objektu actor, dojde k odeslání zprávy požadavku prostřednictvím sítě do služby objektu actor. Služba zpracuje zprávu požadavku a pošle odpověď zpět klientovi. Modul runtime Reliable Actors zveřejňuje následující čítače výkonu související se zpracováním žádosti objektu actor.

| Název kategorie | Název čítače | Description |
| --- | --- | --- |
| Objekt actor Service Fabric |počet nezpracovaných žádostí |Počet požadavků zpracovávaných ve službě |
| Objekt actor Service Fabric |Průměrný počet milisekund na požadavek |Doba trvání (v milisekundách), po kterou služba zpracovává požadavek |
| Objekt actor Service Fabric |Průměrný počet milisekund pro deserializaci žádosti |Doba trvání (v milisekundách) k deserializaci zprávy požadavku objektu actor, když je přijata ve službě |
| Objekt actor Service Fabric |Průměrný počet milisekund serializace odpovědi |Doba trvání (v milisekundách) k serializaci zprávy s odpovědí objektu actor ve službě před odesláním odpovědi klientovi |

## <a name="next-steps"></a>Další kroky
* [Jak Reliable Actors používat platformu Service Fabric](service-fabric-reliable-actors-platform.md)
* [Referenční dokumentace k rozhraní API actor](/previous-versions/azure/dn971626(v=azure.100))
* [Ukázka kódu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Zprostředkovatelé EventSource v PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
