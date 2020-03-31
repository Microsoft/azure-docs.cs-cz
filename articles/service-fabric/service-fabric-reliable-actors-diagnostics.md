---
title: Diagnostika a monitorování herců
description: Tento článek popisuje funkce diagnostiky a monitorování výkonu v prostředí Service Fabric Reliable Actors, včetně událostí a čítačů výkonu, které vydává.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: e6e9fb66368461e0d3ebdd2709f4ced0e796bea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282325"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostika a sledování výkonu služby Reliable Actors
Za běhu Reliable Actors vyzařuje události [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) a [čítače výkonu](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Ty poskytují přehled o tom, jak běží za běhu a pomáhají při řešení potíží a sledování výkonu.

## <a name="eventsource-events"></a>Události EventSource
Název zprostředkovatele EventSource pro zaběhu Reliable Actors je "Microsoft-ServiceFabric-Actors". Události z tohoto zdroje událostí se zobrazí v okně [Události diagnostiky,](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) když je aplikace objektu actor [laděna v sadě Visual Studio](service-fabric-debugging-your-application.md).

Příklady nástrojů a technologií, které pomáhají při shromažďování a/nebo prohlížení událostí EventSource jsou [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Diagnostika Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [Sémantické protokolování](https://msdn.microsoft.com/library/dn774980.aspx)a [Knihovna Microsoft TraceEvent](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Klíčová slova
Všechny události, které patří do zdroje událostí reliable actors jsou přidruženy k jednomu nebo více klíčovým slovům. To umožňuje filtrování událostí, které jsou shromažďovány. Jsou definovány následující klíčové slovo bity.

| Bit | Popis |
| --- | --- |
| 0x1 |Sada důležitých událostí, které shrnují provoz objektu Fabric Actors runtime. |
| 0x2 |Sada událostí, které popisují volání metody actor. Další informace naleznete v [úvodním tématu o aktérech](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Sada událostí souvisejících se stavem objektu actor. Další informace naleznete v tématu o [správě stavu objektu actor](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Sada událostí souvisejících s tahovou souběžnosti v objektu actor. Další informace naleznete v tématu [souběžnosti](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Čítače výkonu
Za běhu Spolehlivé objekty actor definuje následující kategorie čítače výkonu.

| Kategorie | Popis |
| --- | --- |
| Service Fabric Actor |Čítače specifické pro objekty objektů Azure Service Fabric, například čas zaúčtořízený k uložení stavu objektu actor. |
| Metoda objektu actor service fabric |Čítače specifické pro metody implementované objekty actor Service Fabric, například jak často je vyvolána metoda objektu actor |

Každá z výše uvedených kategorií má jeden nebo více čítačů.

Aplikace [Sledování výkonu systému Windows,](https://technet.microsoft.com/library/cc749249.aspx) která je ve výchozím nastavení k dispozici v operačním systému Windows, lze použít ke shromažďování a zobrazení dat čítače výkonu. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možnost pro shromažďování dat čítače výkonu a jejich nahrání do tabulek Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítače výkonu
Cluster, který má velký počet služeb actor nebo oddíly služby actor bude mít velký počet instancí čítače výkonu objektu actor. Názvy instancí čítače výkonu mohou pomoci při identifikaci konkrétní [metody oddílu](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) a objektu actor (pokud je k dispozici), ke které je přidružena instance čítače výkonu.

#### <a name="service-fabric-actor-category"></a>Kategorie actor service fabric
Pro kategorii `Service Fabric Actor`jsou názvy instancí čítačů v následujícím formátu:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* je řetězcová reprezentace ID oddílu Service Fabric, ke kterému je přidružena instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcová reprezentace je generována metodou s specifikátorem [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) formátu "D".

*ActorRuntimeInternalID* je řetězcová reprezentace 64bitové celé číslo, které je generováno objektem Fabric Actors runtime pro jeho vnitřní použití. To je součástí názvu instance čítače výkonu, aby byla zajištěna jeho jedinečnost a zabránilo se konfliktu s názvy jiných instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

Následuje příklad názvu instance čítače pro čítač, který patří do `Service Fabric Actor` kategorie:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

Ve výše uvedeném příkladu `2740af29-78aa-44bc-a20b-7e60fb783264` je řetězcová reprezentace `635650083799324046` ID oddílu Service Fabric a je 64bitové ID, které je generováno pro interní použití za běhu.

#### <a name="service-fabric-actor-method-category"></a>Kategorie Metoda objektu actor service fabric
Pro kategorii `Service Fabric Actor Method`jsou názvy instancí čítačů v následujícím formátu:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* je název metody actor, ke které je přidružena instance čítače výkonu. Formát názvu metody je určen na základě některé logiky v objektu Fabric Actors runtime, který vyvažuje čitelnost názvu s omezeními na maximální délku názvů instancí čítače výkonu v systému Windows.

*ActorsRuntimeMethodId* je řetězcová reprezentace 32bitové celé číslo, které je generováno objektem fabric actor pro jeho vnitřní použití. To je součástí názvu instance čítače výkonu, aby byla zajištěna jeho jedinečnost a zabránilo se konfliktu s názvy jiných instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

*ServiceFabricPartitionID* je řetězcová reprezentace ID oddílu Service Fabric, ke kterému je přidružena instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcová reprezentace je generována metodou s specifikátorem [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) formátu "D".

*ActorRuntimeInternalID* je řetězcová reprezentace 64bitové celé číslo, které je generováno objektem Fabric Actors runtime pro jeho vnitřní použití. To je součástí názvu instance čítače výkonu, aby byla zajištěna jeho jedinečnost a zabránilo se konfliktu s názvy jiných instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

Následuje příklad názvu instance čítače pro čítač, který patří do `Service Fabric Actor Method` kategorie:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

Ve výše uvedeném příkladu `ivoicemailboxactor.leavemessageasync` `2` je název metody, je 32bitové ID generované `89383d32-e57e-4a9b-a6ad-57c6792aa521` pro interní použití za běhu, je `635650083804480486` řetězcová reprezentace ID oddílu Service Fabric a je 64bitové ID generované pro interní použití za běhu.

## <a name="list-of-events-and-performance-counters"></a>Seznam událostí a čítačů výkonu
### <a name="actor-method-events-and-performance-counters"></a>Události metody objektu actor a čítače výkonu
Za běhu Reliable Actors vydává následující události související s [metodami objektu actor](service-fabric-reliable-actors-introduction.md).

| Název události | ID události | Úroveň | Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| Objekt ActorMethodStart |7 |Verbose |0x2 |Objekty actor runtime se chystá vyvolat metodu objektu actor. |
| ActorMethodStop |8 |Verbose |0x2 |Metoda objektu actor dokončila provádění. To znamená, že asynchronní volání modulu runtime k metodě objektu actor bylo vráceno a úloha vrácená metodou objektu actor byla dokončena. |
| ActorMethodThrewException |9 |Upozornění |0x3 |Výjimka byla vyvolána během provádění metody actor, a to buď během asynchronnívolání modulu runtime na metodu actor nebo během provádění úlohy vrácené metodou actor. Tato událost označuje nějaký druh selhání v kódu objektu actor, který potřebuje šetření. |

Prostředí Reliable Actors zakládá následující čítače výkonu související s prováděním metod objektu actor.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Metoda objektu actor service fabric |Vyvolání/s |Počet vyvolání metody služby objektu actor za sekundu |
| Metoda objektu actor service fabric |Průměrné milisekundy na vyvolání |Čas spuštění metody služby objektu actor v milisekundách |
| Metoda objektu actor service fabric |Vyvolání výjimek/s |Počet, kolikrát metoda služby objektu actor vyvolala výjimku za sekundu |

### <a name="concurrency-events-and-performance-counters"></a>Události souběžnosti a čítače výkonu
Za běhu Reliable Actors vyzařuje následující události související s [souběžnosti](service-fabric-reliable-actors-introduction.md#concurrency).

| Název události | ID události | Úroveň | Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ActorMethodvoláníWaitingForlock |12 |Verbose |0x8 |Tato událost je napsána na začátku každého nového tahu v herci. Obsahuje počet čekajících volání objektu actor, které čekají na získání zámku objektu actor, který vynucuje tahovou souběžnost. |

Za běhu Reliable Actors publikuje následující čítače výkonu související souběžnosti.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Service Fabric Actor |Počet herců čeká na zámek herce |Počet čekajících volání objektu actor čekajících na získání zámku objektu actor, který vynucuje tahovou souběžnost |
| Service Fabric Actor |Průměrné milisekundy na zámek čekání |Čas trvalou (v milisekundách) získat zámek objektu actor, který vynucuje tahovou souběžnost |
| Service Fabric Actor |Průměrná milisekundová zámek actor byl držen |Čas (v milisekundách), pro který je zámek objektu actor držen |

### <a name="actor-state-management-events-and-performance-counters"></a>Události správy stavu objektu actor a čítače výkonu
Za běhu Reliable Actors vydává následující události související se [správou stavu objektu actor](service-fabric-reliable-actors-state-management.md).

| Název události | ID události | Úroveň | Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Verbose |0x4 |Actors runtime se chystá zachránit stav objektu actor. |
| ActorSaveStateStop |11 |Verbose |0x4 |Čas běhu herek dokončil uložení stavu objektu actor. |

Za běhu Reliable Actors publikuje následující čítače výkonu související se správou stavu objektu actor.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Service Fabric Actor |Průměrná milisekunda na operaci stavu uložení |Čas strávený uložením stavu objektu actor v milisekundách |
| Service Fabric Actor |Průměrná milisekunda na operaci stavu zatížení |Čas načtení stavu objektu actor v milisekundách |

### <a name="events-related-to-actor-replicas"></a>Události související s replikami objektu actor
Za běhu Reliable Actors vydává následující události související s [replikami objektu actor](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Název události | ID události | Úroveň | Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informační |0x1 |Replika objektu actor změnila roli na Primární. To znamená, že objekty actor pro tento oddíl budou vytvořeny uvnitř této repliky. |
| ReplicaChangeRoleFromPrimary |2 |Informační |0x1 |Replika objektu actor změnila roli na neprimární. To znamená, že objekty actor pro tento oddíl již nebudou vytvořeny uvnitř této repliky. Žádné nové požadavky budou doručeny objekty actor již vytvořené v rámci této repliky. Po dokončení všech probíhajících žádostí budou objekty actor zničeny. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Události aktivace a deaktivace objektu actor a čítače výkonu
Za běhu Reliable Actors vydává následující události související s [aktivací a deaktivací objektu actor](service-fabric-reliable-actors-lifecycle.md).

| Název události | ID události | Úroveň | Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informační |0x1 |Herec byl aktivován. |
| ActorDeaktivova |6 |Informační |0x1 |Herec byl deaktivován. |

Prostředí Za běhu Spolehlivé objekty actor publikuje následující čítače výkonu související s aktivací a deaktivací objektu actor.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Service Fabric Actor |Průměrná milisekundaonu OnActivateAsync |Doba spouštění metody OnActivateAsync v milisekundách |

### <a name="actor-request-processing-performance-counters"></a>Čítače výkonu zpracování požadavku objektu actor
Když klient vyvolá metodu prostřednictvím objektu proxy objektu objektu actor, výsledkem je zpráva požadavku odesílaná prostřednictvím sítě službě objektu actor. Služba zpracuje zprávu požadavku a odešle odpověď zpět klientovi. Za běhu spolehlivé objekty actor publikuje následující čítače výkonu související se zpracováním požadavku objektu actor.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Service Fabric Actor |Počet nevyřízených žádostí |Počet zpracovávaných požadavků ve službě |
| Service Fabric Actor |Průměrná milisekunda na požadavek |Doba, kterou služba (v milisekundách) zpracovala ke zpracování požadavku |
| Service Fabric Actor |Průměrná milisekunda pro deserializaci požadavku |Čas trvalou (v milisekundách) rekonstruovat zprávu požadavku objektu actor při přijetí ve službě |
| Service Fabric Actor |Průměrná milisekunda pro serializaci odezvy |Čas doby trvalou (v milisekundách) serializovat zprávu odpovědi objektu actor ve službě před odesláním odpovědi klientovi |

## <a name="next-steps"></a>Další kroky
* [Jak spolehliví aktéři používají platformu Service Fabric](service-fabric-reliable-actors-platform.md)
* [Referenční dokumentace rozhraní API actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázkový kód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Zprostředkovatelé Zdroje událostí v PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
