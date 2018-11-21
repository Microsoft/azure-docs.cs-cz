---
title: Azure ServiceFabric Diagnostika a monitorování | Dokumentace Microsoftu
description: Tento článek popisuje funkce monitorování výkonu v modulu runtime Service Fabric Reliable ServiceRemoting, jako jsou čítače výkonu, protože ho vygeneroval ho.
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
ms.openlocfilehash: e1dec182f09eccebfe03ab9727018dbf34128acd
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275244"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostika a sledování výkonu pro vzdálenou komunikaci Reliable Service
Generuje runtime spolehlivé ServiceRemoting [čítače výkonu](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Tyto poskytují přehled o tom, jak funguje ServiceRemoting a pomoci při řešení potíží a monitorování výkonu.


## <a name="performance-counters"></a>Čítače výkonu
Spolehlivé ServiceRemoting runtime definuje následující kategorie čítačů výkonu:

| Kategorie | Popis |
| --- | --- |
| Služba Service Fabric |Čítače specifické pro Azure Service Fabric vzdálené komunikace služby, například průměrná doba zpracování žádosti |
| Metoda služby Service Fabric |Čítače specifické pro metody implementované vzdálené komunikace služby Service Fabric, například jak často je vyvolána metoda služby |

Každý z výše uvedených skupin má jeden nebo více čítačů.

[Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) aplikace, která je k dispozici ve výchozím nastavení v operačním systému Windows umožňuje shromažďovat a zobrazovat data čítače výkonu. [Diagnostika Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) je další možností pro shromažďování dat čítače výkonu a pak ho nahrát do tabulky Azure.

### <a name="performance-counter-instance-names"></a>Názvy instancí čítačů výkonu
Cluster, který má velký počet ServiceRemoting služby nebo oddíly mají velký počet instancí čítače výkonu. Názvy instancí čítačů výkonu může pomoci při identifikaci konkrétní oddíl a metodu služby (Pokud je k dispozici), že instance čítače výkonu je přidružené.

#### <a name="service-fabric-service-category"></a>Kategorie služby Service Fabric
Kategorie `Service Fabric Service`, názvy instancí čítačů jsou v následujícím formátu:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* je řetězcové vyjádření ID oddílu Service Fabric, který je přidružen instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcovou reprezentaci je generován prostřednictvím [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoda se specifikátorem formátu "D".

*ServiceReplicaOrInstanceId* je řetězcové vyjádření ID repliky a Instance prostředků infrastruktury služby, který je přidružen instance čítače výkonu.

*ServiceRuntimeInternalID* je řetězcové vyjádření 64bitové celé číslo, který generuje modul runtime Fabric Service pro interní použití. To je součástí název instance čítače výkonu a zajistí jeho jedinečnost nedošlo ke konfliktu s další názvy instancí čítačů výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

Následuje příklad název instance čítač pro čítač, který patří `Service Fabric Service` kategorie:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

V předchozím příkladu `2740af29-78aa-44bc-a20b-7e60fb783264` je řetězcové vyjádření ID oddílu Service Fabric `635650083799324046` je řetězcové vyjádření repliky/InstanceId a `5008379932` je ID 64-bit, který je generován pro vnitřní modul runtime používat.

#### <a name="service-fabric-service-method-category"></a>Metoda služby Service Fabric kategorie
Kategorie `Service Fabric Service Method`, názvy instancí čítačů jsou v následujícím formátu:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* je název metody služby, který je přidružen instance čítače výkonu. Formát názvu metody je určen na základě nějaké logiky v modulu runtime Fabric Service, který vyrovnává čitelnost k názvu omezení na maximální délce názvy instancí čítačů výkonu Windows.

*ServiceRuntimeMethodId* je řetězcové vyjádření 32bitové celé číslo, který generuje modul runtime Fabric Service pro interní použití. To je součástí název instance čítače výkonu a zajistí jeho jedinečnost nedošlo ke konfliktu s další názvy instancí čítačů výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

*ServiceFabricPartitionID* je řetězcové vyjádření ID oddílu Service Fabric, který je přidružen instance čítače výkonu. ID oddílu je identifikátor GUID a jeho řetězcovou reprezentaci je generován prostřednictvím [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metoda se specifikátorem formátu "D".

*ServiceReplicaOrInstanceId* je řetězcové vyjádření ID repliky a Instance prostředků infrastruktury služby, který je přidružen instance čítače výkonu.

*ServiceRuntimeInternalID* je řetězcové vyjádření 64bitové celé číslo, který generuje modul runtime Fabric Service pro interní použití. To je součástí název instance čítače výkonu a zajistí jeho jedinečnost nedošlo ke konfliktu s další názvy instancí čítačů výkonu. Uživatelé by se neměl pokoušet interpretovat tuto část název instance čítače výkonu.

Následuje příklad název instance čítač pro čítač, který patří `Service Fabric Service Method` kategorie:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

V předchozím příkladu `ivoicemailboxservice.leavemessageasync` je název metody `2` je 32-bit ID vygenerované pro interní použití modulu runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` je řetězcové vyjádření ID oddílu Service Fabric`635650083804480486` je řetězcové vyjádření ID služby technologie Fabric repliky nebo Instance a `5008380` je použít 64-bit ID vygenerované pro vnitřní modul runtime.

## <a name="list-of-performance-counters"></a>Seznam čítačů výkonu
### <a name="service-method-performance-counters"></a>Čítače výkonu služby – metoda

Modul runtime spolehlivé služby zveřejňuje následující čítače výkonu související s provedením metody služby.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Metoda služby Service Fabric |Vyvolání/s |Kolikrát vyvolala metoda služby za sekundu |
| Metoda služby Service Fabric |Průměrný počet milisekund na vyvolání |Čas potřebný k provedení metody služby v milisekundách |
| Metoda služby Service Fabric |Vyvolané výjimky/s |Počet pokusů, že metoda služby došlo k výjimce za sekundu |

### <a name="service-request-processing-performance-counters"></a>Čítače výkonu zpracování žádosti o službu
Když klient volá metodu prostřednictvím objektu proxy služby, výsledkem zprávu požadavku, odeslání přes síť do vzdálené komunikace služby. Služba zpracovává zprávy s požadavkem a odešle odpověď zpět klientovi. Modul runtime spolehlivé ServiceRemoting publikuje následující čítače výkonu související s zpracování žádosti o služby.

| Název kategorie | Název čítače | Popis |
| --- | --- | --- |
| Služba Service Fabric |Počet zbývajících požadavků |Počet požadavků zpracovávaných ve službě |
| Služba Service Fabric |Průměrný počet milisekund na požadavek |Doba trvání (v milisekundách) ve službě pro zpracování požadavku |
| Služba Service Fabric |Průměrný počet milisekund deserializace požadavků |Doba trvání (v milisekundách) k deserializaci zprávy požadavku služby při přijetí na službu |
| Služba Service Fabric |Průměrný počet milisekund serializace odpovědí |Doba trvání (v milisekundách) k serializaci zprávy s odpovědí služby na službu, před odesláním odpovědi klientovi |

## <a name="next-steps"></a>Další postup
* [Ukázka kódu](https://azure.microsoft.com/en-us/resources/samples/?service=service-fabric&sort=0)
* [Poskytovatelé EventSource v PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
