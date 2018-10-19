---
title: Azure Service Fabric výkonu monitorování | Dokumentace Microsoftu
description: Další informace o čítačích výkonu pro monitorování a diagnostiku clustery Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 1e6ea5d6ae321a0443631ec928912611a68346c6
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408009"
---
# <a name="performance-metrics"></a>Metriky výkonu

Metriky by měl být shromažďovány porozumět výkonu váš cluster, stejně jako aplikace spuštěné v ní. Pro clustery Service Fabric doporučujeme shromažďovat následující čítače výkonu.

## <a name="nodes"></a>Uzly

Pro počítače v clusteru vezměte v úvahu shromažďovat následující čítače výkonu pro lepší pochopení zatížení na každém počítači a provést příslušné rozhodnutí o škálování clusteru.

| Kategorie čítače | Název počítadla |
| --- | --- |
| Fyzický disk (na Disk) | Střední Délka fronty disku pro čtení |
| Fyzický disk (na Disk) | Střední Délka fronty disku zápisu |
| Fyzický disk (na Disk) | Střední Doba disku/čtení |
| Fyzický disk (na Disk) | Střední Doby disku/zápis |
| Fyzický disk (na Disk) | Čtení disku/s |
| Fyzický disk (na Disk) | Bajty čtení z disku/s |
| Fyzický disk (na Disk) | Zápis disku/s |
| Fyzický disk (na Disk) | Bajty zapisování na disk/s |
| Memory (Paměť) | Počet MB k dispozici |
| Soubor stránkování | % Využití |
| Processor(Total) | % Času procesoru |
| Proces (pro službu) | % Času procesoru |
| Proces (pro službu) | ID procesu |
| Proces (pro službu) | Privátní bajty |
| Proces (pro službu) | Počet vláken |
| Proces (pro službu) | Virtuální bajty |
| Proces (pro službu) | Pracovní sada |
| Proces (pro službu) | Pracovní sada – privátní |
| Interface(all-instances) sítě | Délka fronty výstupu |
| Interface(all-instances) sítě | Zrušených výstupních paketů |
| Interface(all-instances) sítě | Vyřazené přijaté pakety |
| Interface(all-instances) sítě | Odchozí chyb pakety |
| Interface(all-instances) sítě | Chyby přijaté pakety |

## <a name="net-applications-and-services"></a>Aplikace a služby .NET

Shromážděte následující čítače, pokud provádíte nasazení služeb .NET do clusteru. 

| Kategorie čítače | Název počítadla |
| --- | --- |
| Paměť .NET CLR (pro službu) | ID procesu |
| Paměť .NET CLR (pro službu) | Celkový počet svěřených bajtů |
| Paměť .NET CLR (pro službu) | Počet celkový vyhrazených bajtů |
| Paměť .NET CLR (pro službu) | Počet bajtů ve všech haldách |
| Paměť .NET CLR (pro službu) | Počet úklidů 0 |
| Paměť .NET CLR (pro službu) | # Počet úklidů 1 |
| Paměť .NET CLR (pro službu) | # Počet úklidů 2 |
| Paměť .NET CLR (pro službu) | % Času v uvolňování paměti |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric vlastní čítače výkonu

Service Fabric generuje vyžadovat značné množství vlastní čítače výkonu. Pokud máte nainstalovánu sadu SDK, zobrazí se úplný seznam na svém počítači s Windows v aplikaci sledování výkonu (Spustit > monitorování výkonu). 

V aplikacích provádíte nasazení do clusteru, pokud používáte Reliable Actors, přidejte countes z `Service Fabric Actor` a `Service Fabric Actor Method` kategorie (viz [Service Fabric Reliable Diagnostika objektů actor](service-fabric-reliable-actors-diagnostics.md)).

Pokud používáte Reliable Services, podobně jako máme `Service Fabric Service` a `Service Fabric Service Method` kategorie čítačů, které se mají shromažďovat čítače z. 

Pokud používáte Reliable Collections, doporučujeme přidat `Avg. Transaction ms/Commit` z `Service Fabric Transactional Replicator` shromažďovat latenci průměrné potvrzení na transakci metriku.


## <a name="next-steps"></a>Další postup

* Další informace o [generování událostí na úrovni platformy](service-fabric-diagnostics-event-generation-infra.md) v Service Fabric
* Shromažďování metrik výkonu prostřednictvím [agenta Log Analytics](service-fabric-diagnostics-oms-agent.md)
