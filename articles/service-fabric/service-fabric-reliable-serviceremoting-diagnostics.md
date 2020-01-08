---
title: Diagnostika a monitorování Azure ServiceFabric
description: Tento článek popisuje funkce monitorování výkonu v Service Fabric Reliable ServiceRemoting runtime, jako jsou čítače výkonu emitované.
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 31095a619fc4d756fa4ef9c29691d1d511d59ece
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426701"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Monitorování diagnostiky a výkonu pro vzdálenou komunikaci spolehlivé služby
ServiceRemoting runtime Reliable vygeneruje [čítače výkonu](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Poskytují přehled o tom, jak ServiceRemoting pracuje a umožňuje řešení potíží a monitorování výkonu.


## <a name="performance-counters"></a>Čítače výkonu
Reliable ServiceRemoting Runtime definuje následující kategorie čítače výkonu:

| Kategorie | Popis |
| --- | --- |
| Služba Service Fabric |Čítače specifické pro vzdálenou komunikaci služby Azure Service Fabric, například průměrná doba potřebná pro zpracování požadavku |
| Metoda služby Service Fabric |Čítače specifické pro metody implementované Service Fabric služby vzdálené komunikace, například jak často se vyvolala metoda služby |

Každá z předchozích kategorií má jeden nebo více čítačů.

Aplikace [sledování výkonu systému Windows](https://technet.microsoft.com/library/cc749249.aspx) , která je k dispozici ve výchozím nastavení v operačním systému Windows, se dá použít ke shromažďování a zobrazování dat čítače výkonu. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možnost pro shromažďování dat čítače výkonu a jejich nahrání do tabulek Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítače výkonu
Cluster, který má velký počet ServiceRemoting služeb nebo oddílů, má velký počet instancí čítače výkonu. Názvy instancí čítače výkonu vám pomůžou identifikovat konkrétní oddíl a metodu služby (Pokud je k dispozici), ke které je přidružená instance čítače výkonu.

#### <a name="service-fabric-service-category"></a>Kategorie služby Service Fabric
V případě `Service Fabric Service`kategorie jsou názvy instancí čítače v následujícím formátu:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* je řetězcová reprezentace ID oddílu Service Fabric, ke které je instance čítače výkonu přidružena. IDENTIFIKÁTOR oddílu je identifikátor GUID a řetězcová reprezentace je generována prostřednictvím metody [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) s specifikátorem formátu "D".

*ServiceReplicaOrInstanceId* je řetězcová reprezentace ID repliky nebo instance Service Fabric, ke které je instance čítače výkonu přidružená.

*ServiceRuntimeInternalID* je řetězcová reprezentace 64ého celého čísla generovaného modulem runtime služby Fabric pro jeho interní použití. Tato hodnota je obsažena v názvu instance čítače výkonu k zajištění jeho jedinečnosti a zabránění konfliktu s jinými názvy instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

Následuje příklad názvu instance čítače pro čítač, který patří do kategorie `Service Fabric Service`:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

V předchozím příkladu je `2740af29-78aa-44bc-a20b-7e60fb783264` řetězcové vyjádření ID oddílu Service Fabric, `635650083799324046` je řetězcová reprezentace repliky/InstanceId a `5008379932` je 64 ID, které je vygenerováno pro interní použití modulu runtime.

#### <a name="service-fabric-service-method-category"></a>Kategorie metody služby Service Fabric
V případě `Service Fabric Service Method`kategorie jsou názvy instancí čítače v následujícím formátu:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* je název metody služby, ke které je přidružena instance čítače výkonu. Formát názvu metody se určí na základě některé logiky v modulu runtime služby Fabric, který vyrovnává čitelnost názvu s omezeními pro maximální délku názvů instancí čítače výkonu ve Windows.

*ServiceRuntimeMethodId* je řetězcová reprezentace 32ého celého čísla generovaného modulem runtime služby Fabric pro jeho interní použití. Tato hodnota je obsažena v názvu instance čítače výkonu k zajištění jeho jedinečnosti a zabránění konfliktu s jinými názvy instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

*ServiceFabricPartitionID* je řetězcová reprezentace ID oddílu Service Fabric, ke které je instance čítače výkonu přidružena. IDENTIFIKÁTOR oddílu je identifikátor GUID a řetězcová reprezentace je generována prostřednictvím metody [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) s specifikátorem formátu "D".

*ServiceReplicaOrInstanceId* je řetězcová reprezentace ID repliky nebo instance Service Fabric, ke které je instance čítače výkonu přidružená.

*ServiceRuntimeInternalID* je řetězcová reprezentace 64ého celého čísla generovaného modulem runtime služby Fabric pro jeho interní použití. Tato hodnota je obsažena v názvu instance čítače výkonu k zajištění jeho jedinečnosti a zabránění konfliktu s jinými názvy instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

Následuje příklad názvu instance čítače pro čítač, který patří do kategorie `Service Fabric Service Method`:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

V předchozím příkladu `ivoicemailboxservice.leavemessageasync` je název metody, `2` je 32 identifikátor ID generovaný pro interní použití modulu runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` je řetězcová reprezentace ID Service Fabricho oddílu,`635650083804480486` je řetězcové vyjádření ID repliky nebo instance Service Fabric a `5008380` je identifikátor 64 bitů generovaný pro interní použití modulu runtime.

## <a name="list-of-performance-counters"></a>Seznam čítačů výkonu
### <a name="service-method-performance-counters"></a>Čítače výkonu metody služby

Služba Reliable Service runtime zveřejňuje následující čítače výkonu související s prováděním metod služeb.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Metoda služby Service Fabric |Volání za sekundu |Počet, kolikrát se metoda služby vyvolala za sekundu |
| Metoda služby Service Fabric |Průměrný počet milisekund na vyvolání |Čas potřebný k provedení metody služby v milisekundách |
| Metoda služby Service Fabric |Vyvolané výjimky za sekundu |Počet, kolikrát metoda služby vyvolala výjimku za sekundu |

### <a name="service-request-processing-performance-counters"></a>Čítače výkonu zpracování žádostí o služby
Když klient vyvolá metodu prostřednictvím objektu proxy služby, má za následek odeslání zprávy požadavku prostřednictvím sítě do služby vzdálené komunikace. Služba zpracuje zprávu požadavku a pošle odpověď zpět klientovi. Reliable ServiceRemoting runtime zveřejňuje následující čítače výkonu související se zpracováním žádostí o služby.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Služba Service Fabric |počet nezpracovaných žádostí |Počet požadavků zpracovávaných ve službě |
| Služba Service Fabric |Průměrný počet milisekund na požadavek |Doba trvání (v milisekundách), po kterou služba zpracovává požadavek |
| Služba Service Fabric |Průměrný počet milisekund pro deserializaci žádosti |Doba trvání (v milisekundách) k deserializaci zprávy žádosti o službu, když se přijme ve službě |
| Služba Service Fabric |Průměrný počet milisekund serializace odpovědi |Doba trvání (v milisekundách) k serializaci zprávy s odpovědí služby ve službě před odesláním odpovědi klientovi |

## <a name="next-steps"></a>Další kroky
* [Ukázka kódu](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [Zprostředkovatelé EventSource v PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
