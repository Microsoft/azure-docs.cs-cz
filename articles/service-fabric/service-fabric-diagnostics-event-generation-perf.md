---
title: Monitorování výkonu služby Azure Service Fabric
description: Seznamte se s čítači výkonu pro monitorování a diagnostiku clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464699"
---
# <a name="performance-metrics"></a>Metriky výkonu

Metriky by měly být shromažďovány, aby bylo možné pochopit výkon clusteru i aplikace, které jsou v něm spuštěné. U Service Fabricch clusterů doporučujeme shromažďovat následující čítače výkonu.

## <a name="nodes"></a>Uzly

Pro počítače ve vašem clusteru zvažte shromáždění následujících čítačů výkonu, abyste lépe pochopili zatížení jednotlivých počítačů a zajistili vhodná rozhodnutí o škálování clusteru.

| Kategorie čítače | Název čítače |
| --- | --- |
| Logický Disk | Volné místo logického disku |
| Fyzický disk (na disk) | Průměrná délka fronty čtení z disku |
| Fyzický disk (na disk) | Průměrná délka fronty zápisu na disk |
| Fyzický disk (na disk) | Střední doba disku/čtení |
| Fyzický disk (na disk) | Střední doba disku/zápis |
| Fyzický disk (na disk) | Čtení disku/s |
| Fyzický disk (na disk) | Bajty čtení z disku/s |
| Fyzický disk (na disk) | Zápis disku/s |
| Fyzický disk (na disk) | Bajty zapisování na disk/s |
| Paměť | Počet MB k dispozici |
| PagingFile | % Využití |
| Procesor (celkem) | % Času procesoru |
| Proces (za službu) | % Času procesoru |
| Proces (za službu) | ID procesu |
| Proces (za službu) | Soukromé bajty |
| Proces (za službu) | Počet vláken |
| Proces (za službu) | Virtuální bajty |
| Proces (za službu) | Pracovní sada |
| Proces (za službu) | Pracovní sada – soukromá |
| Síťové rozhraní (všechny instance) | Bajty recd |
| Síťové rozhraní (všechny instance) | Odeslané bajty |
| Síťové rozhraní (všechny instance) | Bajty celkem |
| Síťové rozhraní (všechny instance) | Délka fronty výstupu |
| Síťové rozhraní (všechny instance) | Zrušené odchozí pakety |
| Síťové rozhraní (všechny instance) | Zrušené přijaté pakety |
| Síťové rozhraní (všechny instance) | Odchozí chyby paketů |
| Síťové rozhraní (všechny instance) | Přijaté pakety – chyby |

## <a name="net-applications-and-services"></a>Aplikace a služby .NET

Pokud nasazujete služby .NET do clusteru, shromážděte následující čítače. 

| Kategorie čítače | Název čítače |
| --- | --- |
| Paměť .NET CLR (na službu) | ID procesu |
| Paměť .NET CLR (na službu) | Celkový počet potvrzených bajtů |
| Paměť .NET CLR (na službu) | Celkový počet rezervovaných bajtů |
| Paměť .NET CLR (na službu) | Počet bajtů ve všech haldách |
| Paměť .NET CLR (na službu) | Velikost haldy Large Object |
| Paměť .NET CLR (na službu) | Počet popisovačů GC |
| Paměť .NET CLR (na službu) | Počet kolekcí 0. generace |
| Paměť .NET CLR (na službu) | # Počet úklidů 1 |
| Paměť .NET CLR (na službu) | # Počet úklidů 2 |
| Paměť .NET CLR (na službu) | % Času v GC |

### <a name="service-fabrics-custom-performance-counters"></a>Vlastní čítače výkonu Service Fabric

Service Fabric generuje podstatné množství vlastních čítačů výkonu. Pokud sadu SDK máte nainstalovanou, můžete na počítači s Windows v aplikaci sledování výkonu Zobrazit úplný seznam (Spustit > sledování výkonu). 

Pokud používáte Reliable Actors v aplikacích, které nasazujete do clusteru, přidejte čítače z kategorií `Service Fabric Actor` a `Service Fabric Actor Method` (viz [diagnostika Service Fabric Reliable Actors](service-fabric-reliable-actors-diagnostics.md)).

Pokud používáte Reliable Services nebo vzdálenou komunikaci služby, máme podobně kategorie čítačů `Service Fabric Service` a `Service Fabric Service Method`, ze kterých byste měli shromažďovat čítače, a to v tématu monitorování s [čítači výkonu](service-fabric-reliable-services-diagnostics.md#performance-counters) [vzdálené komunikace služeb](service-fabric-reliable-serviceremoting-diagnostics.md) a spolehlivé služby. 

Pokud používáte spolehlivé kolekce, doporučujeme přidat `Avg. Transaction ms/Commit` z `Service Fabric Transactional Replicator` a shromažďovat tak průměrnou latenci potvrzení na transakci.


## <a name="next-steps"></a>Další kroky

* Další informace o [generování událostí najdete na úrovni platformy](service-fabric-diagnostics-event-generation-infra.md) v Service Fabric
* Shromažďovat metriky výkonu prostřednictvím [agenta Log Analytics](service-fabric-diagnostics-oms-agent.md)
