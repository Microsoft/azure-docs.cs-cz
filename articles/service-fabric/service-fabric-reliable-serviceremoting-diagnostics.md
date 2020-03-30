---
title: Diagnostika a monitorování Azure ServiceFabric
description: Tento článek popisuje funkce sledování výkonu v service fabric reliable serviceremoting runtime, jako čítače výkonu vyzařované to.
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 31095a619fc4d756fa4ef9c29691d1d511d59ece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282273"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostika a monitorování výkonu pro spolehlivé vzdálené komunikace služeb
Za běhu Reliable ServiceRemoting vyzařuje [čítače výkonu](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Ty poskytují přehled o tom, jak ServiceRemoting funguje a pomáhají při řešení potíží a sledování výkonu.


## <a name="performance-counters"></a>Čítače výkonu
Za běhu Reliable ServiceRemoting definuje následující kategorie čítačů výkonu:

| Kategorie | Popis |
| --- | --- |
| Service Fabric Service |Čítače specifické pro vzdálené komunikace služby Azure Service Fabric , například průměrná doba zpracování požadavku |
| Metoda služby Fabric Service Service Service Service |Čítače specifické pro metody implementované službou Vzdálené komunikace service fabric, například jak často je vyvolána metoda služby |

Každá z předchozích kategorií má jeden nebo více čítačů.

Aplikace [Sledování výkonu systému Windows,](https://technet.microsoft.com/library/cc749249.aspx) která je ve výchozím nastavení k dispozici v operačním systému Windows, lze použít ke shromažďování a zobrazení dat čítače výkonu. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možnost pro shromažďování dat čítače výkonu a jejich nahrání do tabulek Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítače výkonu
Cluster, který má velký počet ServiceRemoting služby nebo oddíly mají velký počet instancí čítače výkonu. Názvy instancí čítače výkonu mohou pomoci při identifikaci konkrétního oddílu a metody Service (pokud je k dispozici), ke které je přidružena instance čítače výkonu.

#### <a name="service-fabric-service-category"></a>Kategorie služby Fabric Service Service Service
Pro kategorii `Service Fabric Service`jsou názvy instancí čítačů v následujícím formátu:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* je řetězcová reprezentace ID oddílu Service Fabric, ke kterému je přidružena instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcová reprezentace je generována metodou s specifikátorem [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) formátu "D".

*ServiceReplicaOrInstanceId* je řetězcová reprezentace ID repliky/instance fabric služby, ke které je přidružena instance čítače výkonu.

*ServiceRuntimeInternalID* je řetězcová reprezentace 64bitové celé číslo, které je generováno runtime služby Fabric pro jeho vnitřní použití. To je součástí názvu instance čítače výkonu, aby byla zajištěna jeho jedinečnost a zabránilo se konfliktu s názvy jiných instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

Následuje příklad názvu instance čítače pro čítač, který patří do `Service Fabric Service` kategorie:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

V předchozím příkladu `2740af29-78aa-44bc-a20b-7e60fb783264` je řetězcová reprezentace ID `635650083799324046` oddílu Service Fabric, je `5008379932` řetězcová reprezentace Repliky/InstanceId a je 64bitové ID, které je generováno pro interní použití za běhu.

#### <a name="service-fabric-service-method-category"></a>Kategorie Metody služby Fabric
Pro kategorii `Service Fabric Service Method`jsou názvy instancí čítačů v následujícím formátu:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* je název metody služby, ke které je přidružena instance čítače výkonu. Formát názvu metody je určen na základě některé logiky v runtime služby Fabric Service, která vyvažuje čitelnost názvu s omezeními na maximální délku názvů instancí čítače výkonu v systému Windows.

*ServiceRuntimeMethodId* je řetězcová reprezentace 32bitového celého čísla, která je generována runtime služby Fabric pro jeho vnitřní použití. To je součástí názvu instance čítače výkonu, aby byla zajištěna jeho jedinečnost a zabránilo se konfliktu s názvy jiných instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

*ServiceFabricPartitionID* je řetězcová reprezentace ID oddílu Service Fabric, ke kterému je přidružena instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcová reprezentace je generována metodou s specifikátorem [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) formátu "D".

*ServiceReplicaOrInstanceId* je řetězcová reprezentace ID repliky/instance fabric služby, ke které je přidružena instance čítače výkonu.

*ServiceRuntimeInternalID* je řetězcová reprezentace 64bitové celé číslo, které je generováno runtime služby Fabric pro jeho vnitřní použití. To je součástí názvu instance čítače výkonu, aby byla zajištěna jeho jedinečnost a zabránilo se konfliktu s názvy jiných instancí čítače výkonu. Uživatelé by se neměli pokoušet interpretovat tuto část názvu instance čítače výkonu.

Následuje příklad názvu instance čítače pro čítač, který patří do `Service Fabric Service Method` kategorie:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

V předchozím příkladu `ivoicemailboxservice.leavemessageasync` je název `2` metody, je 32bitové ID generované pro interní `89383d32-e57e-4a9b-a6ad-57c6792aa521` použití za běhu, je řetězcová`635650083804480486` reprezentace ID oddílu Service Fabric, `5008380` je řetězcová reprezentace ID repliky/instance fabric služby a je 64bitové ID generované pro interní použití za běhu.

## <a name="list-of-performance-counters"></a>Seznam čítačů výkonu
### <a name="service-method-performance-counters"></a>Čítače výkonu metody služby

Za běhu spolehlivé služby publikuje následující čítače výkonu související s prováděním metod služby.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Metoda služby Fabric Service Service Service Service |Vyvolání/s |Počet vyvolání metody služby za sekundu |
| Metoda služby Fabric Service Service Service Service |Průměrné milisekundy na vyvolání |Doba spouštění metody služby v milisekundách |
| Metoda služby Fabric Service Service Service Service |Vyvolání výjimek/s |Počet, kolikrát metoda služby vyvolala výjimku za sekundu |

### <a name="service-request-processing-performance-counters"></a>Čítače výkonu zpracování žádosti o službu
Když klient vyvolá metodu prostřednictvím objektu proxy služby, výsledkem je zpráva požadavku odeslaná prostřednictvím sítě službě vzdálené komunikace. Služba zpracuje zprávu požadavku a odešle odpověď zpět klientovi. Za běhu Reliable ServiceRemoting publikuje následující čítače výkonu související se zpracováním požadavků na službu.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Service Fabric Service |Počet nevyřízených žádostí |Počet zpracovávaných požadavků ve službě |
| Service Fabric Service |Průměrná milisekunda na požadavek |Doba, kterou služba (v milisekundách) zpracovala ke zpracování požadavku |
| Service Fabric Service |Průměrná milisekunda pro deserializaci požadavku |Doba trvalá (v milisekundách) k rekonstrukci zprávy žádosti o službu při přijetí ve službě |
| Service Fabric Service |Průměrná milisekunda pro serializaci odezvy |Čas doby trvalou (v milisekundách) serializovat zprávu o odezvě služby ve službě před odesláním odpovědi klientovi |

## <a name="next-steps"></a>Další kroky
* [Ukázkový kód](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [Zprostředkovatelé Zdroje událostí v PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
