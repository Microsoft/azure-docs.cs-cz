---
title: Monitorování výkonu služby Azure Service Fabric
description: Seznamte se s čítači výkonu pro monitorování a diagnostiku clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75464699"
---
# <a name="performance-metrics"></a>Metriky výkonu

Metriky by měly být shromažďovány, aby bylo možné pochopit výkon clusteru i aplikace, které jsou v něm spuštěné. U Service Fabricch clusterů doporučujeme shromažďovat následující čítače výkonu.

## <a name="nodes"></a>Uzly

Pro počítače ve vašem clusteru zvažte shromáždění následujících čítačů výkonu, abyste lépe pochopili zatížení jednotlivých počítačů a zajistili vhodná rozhodnutí o škálování clusteru.

| Kategorie čítače | Název čítače |
| --- | --- |
| Logický disk | Volné místo na logickém disku |
| Fyzický disk (na disk) | Průměrná délka fronty čtení z disku |
| Fyzický disk (na disk) | Průměrná délka fronty zápisu na disk |
| Fyzický disk (na disk) | Střední doba disku/čtení |
| Fyzický disk (na disk) | Střední doba disku/zápis |
| Fyzický disk (na disk) | Čtení z disku/s |
| Fyzický disk (na disk) | Bajty čtení z disku/s |
| Fyzický disk (na disk) | Zápisy na disk/s |
| Fyzický disk (na disk) | Bajty zápisu na disk/s |
| Memory (Paměť) | Dostupná paměť v megabajtech |
| PagingFile | % Využití |
| Procesor (celkem) | Procesorový čas v % |
| Proces (za službu) | Procesorový čas v % |
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
| Paměť .NET CLR (na službu) | Počet kolekcí 1. generace |
| Paměť .NET CLR (na službu) | Počet kolekcí generace 2 |
| Paměť .NET CLR (na službu) | % Času v GC |

### <a name="service-fabrics-custom-performance-counters"></a>Vlastní čítače výkonu Service Fabric

Service Fabric generuje podstatné množství vlastních čítačů výkonu. Pokud sadu SDK máte nainstalovanou, můžete na počítači s Windows v aplikaci sledování výkonu Zobrazit úplný seznam (spustit > sledování výkonu). 

Pokud používáte Reliable Actors v aplikacích, které nasazujete do vašeho clusteru, přidejte čítače z `Service Fabric Actor` `Service Fabric Actor Method` kategorie a (viz [Diagnostika Service Fabric Reliable Actors](service-fabric-reliable-actors-diagnostics.md)).

Pokud používáte službu Reliable Services nebo vzdálenou komunikaci služby, máme `Service Fabric Service` podobně `Service Fabric Service Method` kategorie čítače, ze kterých byste měli shromažďovat čítače, a to v tématu monitorování s [čítači výkonu](service-fabric-reliable-services-diagnostics.md#performance-counters) [vzdálené komunikace služeb](service-fabric-reliable-serviceremoting-diagnostics.md) a spolehlivé služby. 

Pokud používáte spolehlivé kolekce, doporučujeme přidat `Avg. Transaction ms/Commit` z `Service Fabric Transactional Replicator` ke shromáždění průměrné latence potvrzení na transakci metriky.


## <a name="next-steps"></a>Další kroky

* Další informace o [generování událostí najdete na úrovni platformy](service-fabric-diagnostics-event-generation-infra.md) v Service Fabric
* Shromažďovat metriky výkonu prostřednictvím [agenta Log Analytics](service-fabric-diagnostics-oms-agent.md)
