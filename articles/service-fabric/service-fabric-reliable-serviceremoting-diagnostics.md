---
title: Azure ServiceFabric diagnostics a monitorování | Microsoft Docs
description: Tento článek popisuje funkce monitorování výkonu v modulu runtime Service Fabric spolehlivé ServiceRemoting jako vygenerované tímto čítače výkonu.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: d462ba0955a362c27b786ee6a5670eec20c52a22
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostika a sledování výkonu pro vzdálenou komunikaci spolehlivé služby
Modul runtime spolehlivé ServiceRemoting vysílá [čítače výkonu](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Tyto poskytují přehled o tom, jak pracuje ServiceRemoting a pomoci při řešení potíží a monitorování výkonu.


## <a name="performance-counters"></a>Čítače výkonu
Modul runtime spolehlivé ServiceRemoting definuje následující kategorie čítače výkonu:

| Kategorie | Popis |
| --- | --- |
| Služba Service Fabric |Čítače specifické pro Azure Service Fabric služby vzdálené komunikace, například průměrná doba zpracování požadavku |
| Metoda služby Service Fabric |Čítače specifické pro metody implementované Fabric vzdálené komunikace služby, například jak často je volána metoda služby |

Každá z výše uvedených skupin má jeden nebo více čítačů.

[Sledování výkonu systému Windows](https://technet.microsoft.com/library/cc749249.aspx) aplikace, která je k dispozici v operačním systému Windows ve výchozím nastavení lze shromažďovat a zobrazovat data čítače výkonu. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možností pro shromažďování dat čítačů výkonu a pak ho nahrát do tabulek Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítače výkonu
Cluster, který má velký počet ServiceRemoting služby nebo oddíly mít velký počet instancí čítače výkonu. Názvy instancí čítače výkonu může pomoci při identifikaci konkrétní oddíl a metoda služby (pokud existuje), je spojen instance čítače výkonu.

#### <a name="service-fabric-service-category"></a>Kategorie Service Fabric Service
Pro kategorii `Service Fabric Service`, názvy instancí čítače jsou v následujícím formátu:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* je řetězcová reprezentace Service Fabric ID oddílu, který je přidružen instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcovou reprezentaci vygeneruje prostřednictvím [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoda s specifikátor formátu "D".

*ServiceReplicaOrInstanceId* je řetězcová reprezentace ID repliky nebo instanci služby prostředků infrastruktury, které je přidružené instance čítače výkonu.

*ServiceRuntimeInternalID* je řetězcová reprezentace 64bitové celé číslo, které se generují ve službě technologie Fabric runtime pro interní použití. To je součástí název instance čítače výkonu k zajištění jeho jedinečnosti a aby nedošlo ke konfliktu s další názvy instancí čítače výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

Následuje příklad názvu instance čítače čítače, které patří `Service Fabric Service` kategorie:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

V předchozím příkladu `2740af29-78aa-44bc-a20b-7e60fb783264` je řetězcová reprezentace ID oddílu Service Fabric, `635650083799324046` je řetězcová reprezentace repliky nebo InstanceId a `5008379932` je 64-bit ID, které se generuje pro modul runtime interní použití.

#### <a name="service-fabric-service-method-category"></a>Kategorie metody služby Service Fabric
Pro kategorii `Service Fabric Service Method`, názvy instancí čítače jsou v následujícím formátu:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* je název služby metody, které je přidružené instance čítače výkonu. Formát název metody je určen na základě některé postupu v modulu runtime Fabric Service, který vyrovnává čitelnost názvu s omezeními na maximální délku názvy instancí čítače výkonu v systému Windows.

*ServiceRuntimeMethodId* je řetězcová reprezentace 32bitové celé číslo, které se generují ve službě technologie Fabric runtime pro interní použití. To je součástí název instance čítače výkonu k zajištění jeho jedinečnosti a aby nedošlo ke konfliktu s další názvy instancí čítače výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

*ServiceFabricPartitionID* je řetězcová reprezentace Service Fabric ID oddílu, který je přidružen instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcovou reprezentaci vygeneruje prostřednictvím [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoda s specifikátor formátu "D".

*ServiceReplicaOrInstanceId* je řetězcová reprezentace ID repliky nebo instanci služby prostředků infrastruktury, které je přidružené instance čítače výkonu.

*ServiceRuntimeInternalID* je řetězcová reprezentace 64bitové celé číslo, které se generují ve službě technologie Fabric runtime pro interní použití. To je součástí název instance čítače výkonu k zajištění jeho jedinečnosti a aby nedošlo ke konfliktu s další názvy instancí čítače výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

Následuje příklad názvu instance čítače čítače, které patří `Service Fabric Service Method` kategorie:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

V předchozím příkladu `ivoicemailboxservice.leavemessageasync` je název metody `2` je 32bitové ID generovaná pro interní použití modulu runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` je řetězcová reprezentace ID oddílu Service Fabric,`635650083804480486` je řetězcová reprezentace ID repliky nebo instanci služby prostředků infrastruktury a `5008380` je ID 64-bit, vygeneruje pro modul runtime interní používat.

## <a name="list-of-performance-counters"></a>Seznam čítačů výkonu
### <a name="service-method-performance-counters"></a>Čítače výkonu služby – metoda

Spolehlivé služby modulu runtime publikuje následující čítače výkonu týkající se spouštění služby metody.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Metoda služby Service Fabric |Vyvolání/s |Kolikrát za sekundu je vyvolána metoda služby |
| Metoda služby Service Fabric |Průměrný počet milisekund na vyvolání |Doba k provedení metody služby v milisekundách |
| Metoda služby Service Fabric |Vyvolané výjimky/s |Počet, metoda služby došlo k výjimce za sekundu |

### <a name="service-request-processing-performance-counters"></a>Čítače výkonu zpracování žádosti o služby
Když klient vyvolá metodu prostřednictvím objekt proxy služby, výsledkem zprávu požadavku odesílány přes síť ke službě vzdálené komunikace. Služba zpracovává zprávu požadavku a odešle odpověď zpět klientovi. Modul runtime spolehlivé ServiceRemoting publikuje následující čítače výkonu související s zpracování žádosti o služby.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Služba Service Fabric |Počet zbývajících požadavků |Počet požadavků zpracovaných ve službě |
| Služba Service Fabric |Průměrný počet milisekund na požadavek |Doba trvání (v milisekundách) pomocí služby zpracovat požadavek |
| Služba Service Fabric |Průměrný počet milisekund deserializace požadavků |Doba (v milisekundách) k deserializaci zprávu požadavku služby, když je obdržena na službu |
| Služba Service Fabric |Průměrný počet milisekund serializace odpovědí |Doba (v milisekundách) k serializaci služby zprávu odpovědi na službu předtím, než odešle odpověď klienta |

## <a name="next-steps"></a>Další postup
* [Ukázka kódu](https://github.com/Azure/servicefabric-samples)
* [Zprostředkovatelé EventSource v nástroje PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
