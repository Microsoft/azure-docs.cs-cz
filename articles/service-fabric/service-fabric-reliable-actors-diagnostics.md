---
title: Monitorování a Diagnostika objektů actor | Dokumentace Microsoftu
description: Tento článek popisuje, Diagnostika a monitorování funkce v modulu runtime Service Fabric Reliable Actors, včetně událostí a čítačů výkonu, protože ho vygeneroval jeho výkonu.
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
ms.openlocfilehash: 61c01e8ea3b4cbe7b5f7ab83ab35383d74df3105
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234920"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostika a sledování výkonu služby Reliable Actors
Generuje runtime Reliable Actors [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) události a [čítače výkonu](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Tyto poskytují přehled o jak modul runtime pracuje a pomoci při řešení potíží a monitorování výkonu.

## <a name="eventsource-events"></a>Událostí EventSource
Název zprostředkovatele EventSource pro modul runtime Reliable Actors je "Microsoft-ServiceFabric – objekty actor". Události z tohoto zdroje událostí se zobrazí v [diagnostické události](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) okno, když se aplikace objektu actor [ladit v sadě Visual Studio](service-fabric-debugging-your-application.md).

Příklady nástrojů a technologií, které pomáhají při shromažďování nebo zobrazování událostí EventSource [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [sémantického protokolování](https://msdn.microsoft.com/library/dn774980.aspx)a [ Knihovna Microsoft knihovny TraceEvent](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Klíčová slova
Všechny události, které patří k Reliable Actors EventSource jsou přidruženy k jedné nebo více klíčových slov. To umožňuje filtrování událostí, které byly shromážděny. Jsou definovány následující bits – klíčové slovo.

| Bit | Popis |
| --- | --- |
| 0x1 |Nastavit o důležitých událostech, které shrnují operace Fabric Actors modulu runtime. |
| 0x2 |Sadu událostí, které popisují volání metody objektu actor. Další informace najdete v tématu [úvodní téma na objekty actor](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Sada událostí souvisejících s stavu objektu actor. Další informace naleznete v tématu o [Správa stavu objektu actor](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Sada událostí souvisejících s souběžnosti na základě zapnout v objektu actor. Další informace naleznete v tématu o [souběžnosti](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Čítače výkonu
Reliable Actors runtime definuje následující kategorie čítačů výkonu.

| Kategorie | Popis |
| --- | --- |
| Objekt actor pro Service Fabric |Čítače specifické pro Azure Service Fabric actors, třeba čas potřebný k uložení stavu objektu actor |
| Metoda objektu actor pro Service Fabric |Čítače specifické pro metody implementované pomocí Service Fabric actors, například jak často objektu actor vyvolání metody |

Každý z výše uvedených kategoriích má jeden nebo více čítačů.

[Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) aplikace, která je k dispozici ve výchozím nastavení v operačním systému Windows umožňuje shromažďovat a zobrazovat data čítače výkonu. [Diagnostika Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možností pro shromažďování dat čítače výkonu a pak ho nahrát do tabulky Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítačů výkonu
Cluster, který má velký počet služeb objektu actor nebo oddílů služby objektu actor se mají velký počet instancí čítače výkonu objektu actor. Názvy instancí čítačů výkonu může pomoci při identifikaci konkrétní [oddílu](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) a metodou objektu actor (Pokud je k dispozici), který je přidružen instance čítače výkonu.

#### <a name="service-fabric-actor-category"></a>Service Fabric Actor v kategorii
Kategorie `Service Fabric Actor`, názvy instancí čítačů jsou v následujícím formátu:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* je řetězcové vyjádření ID oddílu Service Fabric, který je přidružen instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcovou reprezentaci je generován prostřednictvím [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoda se specifikátorem formátu "D".

*ActorRuntimeInternalID* je řetězcové vyjádření 64bitové celé číslo, který je generován Fabric Actors modulu runtime pro interní použití. To je součástí název instance čítače výkonu a zajistí jeho jedinečnost nedošlo ke konfliktu s další názvy instancí čítačů výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

Následuje příklad název instance čítač pro čítač, který patří `Service Fabric Actor` kategorie:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

V příkladu výše `2740af29-78aa-44bc-a20b-7e60fb783264` je řetězcové vyjádření ID oddílu Service Fabric a `635650083799324046` je ID 64-bit, který je generován pro vnitřní modul runtime používat.

#### <a name="service-fabric-actor-method-category"></a>Metoda objektu Actor pro Service Fabric kategorie
Kategorie `Service Fabric Actor Method`, názvy instancí čítačů jsou v následujícím formátu:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* je název metody objektu actor, který je přidružen instance čítače výkonu. Formát názvu metody je určen na základě nějaké logiky v Fabric Actors modulu runtime, který vyrovnává čitelnost k názvu omezení na maximální délce názvy instancí čítačů výkonu Windows.

*ActorsRuntimeMethodId* je řetězcové vyjádření 32bitové celé číslo, který je generován Fabric Actors modulu runtime pro interní použití. To je součástí název instance čítače výkonu a zajistí jeho jedinečnost nedošlo ke konfliktu s další názvy instancí čítačů výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

*ServiceFabricPartitionID* je řetězcové vyjádření ID oddílu Service Fabric, který je přidružen instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcovou reprezentaci je generován prostřednictvím [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoda se specifikátorem formátu "D".

*ActorRuntimeInternalID* je řetězcové vyjádření 64bitové celé číslo, který je generován Fabric Actors modulu runtime pro interní použití. To je součástí název instance čítače výkonu a zajistí jeho jedinečnost nedošlo ke konfliktu s další názvy instancí čítačů výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

Následuje příklad název instance čítač pro čítač, který patří `Service Fabric Actor Method` kategorie:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

V příkladu výše `ivoicemailboxactor.leavemessageasync` je název metody `2` je 32-bit ID vygenerované pro interní použití modulu runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` je řetězcové vyjádření ID oddílu Service Fabric a `635650083804480486` je vygenerován pro ID 64-bit modul runtime pro interní použití.

## <a name="list-of-events-and-performance-counters"></a>Seznam událostí a čítačů výkonu
### <a name="actor-method-events-and-performance-counters"></a>Události metod objektu actor a čítače výkonu
Modul runtime Reliable Actors vysílá následující události související s [metody objektu actor](service-fabric-reliable-actors-introduction.md).

| Název události | ID události | Úroveň | Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Podrobný |0x2 |Modul runtime actors je volání metody objektu actor. |
| ActorMethodStop |8 |Podrobný |0x2 |Metoda objektu actor byl dokončen. To znamená vrátila modul runtime asynchronního volání metody objektu actor a dokončení úlohy vrácený metodou objektu actor. |
| ActorMethodThrewException |9 |Upozornění |0x3 |Došlo k výjimce při provádění metody objektu actor, buď během asynchronního volání metody objektu actor modulu runtime nebo během provádění úkolu vrácený metodou objektu actor. Tato událost ukazuje na nějaké chyby v kódu objektu actor, který je potřeba vyšetřit. |

Modul runtime Reliable Actors publikuje následující čítače výkonu související s provedením metody objektu actor.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Metoda objektu actor pro Service Fabric |Vyvolání/s |Počet pokusů, které metody služby objektu actor vyvolala za sekundu |
| Metoda objektu actor pro Service Fabric |Průměrný počet milisekund na vyvolání |Doba v milisekundách, jakou trvalo spuštění metody služby objektu actor |
| Metoda objektu actor pro Service Fabric |Vyvolané výjimky/s |Počet pokusů, že metoda služby objektu actor vyvolala výjimku za sekundu |

### <a name="concurrency-events-and-performance-counters"></a>Souběžnost událostí a čítačů výkonu
Modul runtime Reliable Actors vysílá následující události související s [souběžnosti](service-fabric-reliable-actors-introduction.md#concurrency).

| Název události | ID události | Úroveň | Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Podrobný |0x8 |Tato událost se zapíše na začátku každé nové zapnout v prvek "actor". Obsahuje počet nevyřízených volání objektu actor, které čekají na získání zámku na objektu actor, který vynucuje souběžnosti založená na řadě vy. |

Modul runtime Reliable Actors publikuje následující čítače výkonu související se souběžností.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Objekt actor pro Service Fabric |Počet volání objektu actor čekajících na jeho zámek |Počet nevyřízených volání objektu actor čekajících na získání zámku na objektu actor, který vynucuje souběžnosti založená na řadě vy |
| Objekt actor pro Service Fabric |Průměrný počet milisekund čekání na zámek |Doba (v milisekundách) trvalo získání zámku na objektu actor, který vynucuje souběžnosti založená na řadě vy |
| Objekt actor pro Service Fabric |Průměrný počet milisekund blokování zámku objektu actor |Čas (v milisekundách), pro kterou je držen zámek na objektu actor |

### <a name="actor-state-management-events-and-performance-counters"></a>Události správy stavu objektu actor a čítače výkonu
Modul runtime Reliable Actors vysílá následující události související s [Správa stavu objektu actor](service-fabric-reliable-actors-state-management.md).

| Název události | ID události | Úroveň | Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Podrobný |0x4 |Modul runtime actors je uložení stavu objektu actor. |
| ActorSaveStateStop |11 |Podrobný |0x4 |Modul runtime actors bylo dokončeno ukládání stavu objektu actor. |

Modul runtime Reliable Actors publikuje následující čítače výkonu související se správou stavu objektu actor.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Objekt actor pro Service Fabric |Průměrný počet milisekund na operaci uložení stavu |Doba v milisekundách, jakou trvalo uložení stavu objektu actor |
| Objekt actor pro Service Fabric |Průměrný počet milisekund na operaci načtení stavu |Doba v milisekundách, jakou trvalo načtení stavu objektu actor |

### <a name="events-related-to-actor-replicas"></a>Události související s objektů actor
Modul runtime Reliable Actors vysílá následující události související s [objektů actor](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Název události | ID události | Úroveň | Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informační |0x1 |Objekt actor repliky změnit na primární role. Z toho vyplývá, že objekty actor pro tento oddíl se vytvoří v této replice. |
| ReplicaChangeRoleFromPrimary |2 |Informační |0x1 |Repliky objektu actor změnit na jiné než primární role. Z toho vyplývá, že actors pro tento oddíl se již nelze v této replice. Žádné nové požadavky bude doručen do už vytvořené v rámci této replice objektů actor. Po dokončení všech žádostí v průběhu aktéry zničení. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Objekt actor aktivace a deaktivace událostí a čítačů výkonu
Modul runtime Reliable Actors vysílá následující události související s [objektu actor aktivace a deaktivace](service-fabric-reliable-actors-lifecycle.md).

| Název události | ID události | Úroveň | Klíčové slovo | Popis |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informační |0x1 |Prvek "actor" byla aktivována. |
| ActorDeactivated |6 |Informační |0x1 |Prvek "actor" je deaktivovaný. |

Modul runtime Reliable Actors publikuje následující čítače výkonu související s objektu actor aktivace a deaktivace.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Objekt actor pro Service Fabric |Průměrná doba metody OnActivateAsync v milisekundách |Doba v milisekundách, jakou trvalo provádění metody OnActivateAsync |

### <a name="actor-request-processing-performance-counters"></a>Čítače výkonu zpracování požadavku objektu actor
Když klient volá metodu přes proxy server objektu actor, výsledkem zprávu požadavku, odeslání přes síť do služby objektu actor. Služba zpracovává zprávy s požadavkem a odešle odpověď zpět klientovi. Modul runtime Reliable Actors publikuje následující čítače výkonu související s zpracování požadavku na objekt actor.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Objekt actor pro Service Fabric |Počet zbývajících požadavků |Počet požadavků zpracovávaných ve službě |
| Objekt actor pro Service Fabric |Průměrný počet milisekund na požadavek |Doba trvání (v milisekundách) ve službě pro zpracování požadavku |
| Objekt actor pro Service Fabric |Průměrný počet milisekund deserializace požadavků |Doba trvání (v milisekundách) k deserializaci zprávy požadavku objektu actor, při přijetí na službu |
| Objekt actor pro Service Fabric |Průměrný počet milisekund serializace odpovědí |Doba trvání (v milisekundách) k serializaci zprávy s odpovědí objektu actor na službu, před odesláním odpovědi klientovi |

## <a name="next-steps"></a>Další postup
* [Jak objekty Reliable Actors využívají platformu Service Fabric](service-fabric-reliable-actors-platform.md)
* [Referenční dokumentace rozhraní API objektu actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Ukázka kódu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Poskytovatelé EventSource v PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
