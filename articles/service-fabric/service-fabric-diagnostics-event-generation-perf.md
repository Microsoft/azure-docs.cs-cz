---
title: Azure Service Fabric sledování výkonu
description: Další informace o čítačí výkonu pro monitorování a diagnostiku clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464699"
---
# <a name="performance-metrics"></a>Metriky výkonu

Metriky by měly být shromažďovány pochopit výkon clusteru, stejně jako aplikace spuštěné v něm. Pro clustery Service Fabric doporučujeme shromažďovat následující čítače výkonu.

## <a name="nodes"></a>Uzly

Pro počítače ve vašem clusteru zvažte shromažďování následujících čítačů výkonu, abyste lépe porozuměli zatížení každého počítače a učinili příslušná rozhodnutí o škálování clusteru.

| Kategorie čítače | Název čítače |
| --- | --- |
| Logický disk | Volné místo na logickém disku |
| Fyzický disk (na disk) | Avg. Délka fronty čtení disku |
| Fyzický disk (na disk) | Délka fronty zápisu vpichu |
| Fyzický disk (na disk) | Vně disku sec/čtení |
| Fyzický disk (na disk) | Vg. Disk sec/Zápis |
| Fyzický disk (na disk) | Čtení disku/s |
| Fyzický disk (na disk) | Čtení bajtů disku/s |
| Fyzický disk (na disk) | Zápisy na disk/s |
| Fyzický disk (na disk) | Bajty zápisu disku/s |
| Memory (Paměť) | Dostupné mbajty |
| Stránkovací soubor | % využití |
| Procesor (celkem) | Procesorový čas v % |
| Proces (na službu) | Procesorový čas v % |
| Proces (na službu) | Proces ID |
| Proces (na službu) | Soukromé bajty |
| Proces (na službu) | Počet vláken |
| Proces (na službu) | Virtuální bajty |
| Proces (na službu) | Pracovní sada |
| Proces (na službu) | Pracovní sada - soukromá |
| Síťové rozhraní (všechny instance) | Rektované bajty |
| Síťové rozhraní (všechny instance) | Odeslané bajty |
| Síťové rozhraní (všechny instance) | Celkem bajty |
| Síťové rozhraní (všechny instance) | Délka výstupní fronty |
| Síťové rozhraní (všechny instance) | Zahozené odchozí pakety |
| Síťové rozhraní (všechny instance) | Přijaté přijaté pakety byly zahozeny |
| Síťové rozhraní (všechny instance) | Chyby odchozích paketů |
| Síťové rozhraní (všechny instance) | Chyby přijatých paketů |

## <a name="net-applications-and-services"></a>Aplikace a služby .NET

Pokud nasazujete služby .NET do clusteru, shromážděte následující čítače. 

| Kategorie čítače | Název čítače |
| --- | --- |
| Paměť CLR .NET (na službu) | ID procesu |
| Paměť CLR .NET (na službu) | # Celkem potvrzených bajtů |
| Paměť CLR .NET (na službu) | # Celkem rezervované bajty |
| Paměť CLR .NET (na službu) | # Bytes ve všech hromadách |
| Paměť CLR .NET (na službu) | Velikost haldy velkých objektů |
| Paměť CLR .NET (na službu) | # Gc úchyty |
| Paměť CLR .NET (na službu) | # Gen 0 Sbírky |
| Paměť CLR .NET (na službu) | # Gen 1 Sbírky |
| Paměť CLR .NET (na službu) | # Gen 2 Sbírky |
| Paměť CLR .NET (na službu) | % času v GC |

### <a name="service-fabrics-custom-performance-counters"></a>Vlastní čítače výkonu service fabric

Service Fabric generuje značné množství vlastníčí výkon čítače. Pokud máte nainstalovanou sadu SDK, zobrazí se úplný seznam v počítači se systémem Windows v aplikaci Sledování výkonu (Start > Sledování výkonu). 

V aplikacích, které nasazujete do clusteru, pokud `Service Fabric Actor` používáte spolehlivé objekty actor, přidejte čítače z kategorií a `Service Fabric Actor Method` kategorie (viz [Diagnostika důvěryhodných aktérů service fabric).](service-fabric-reliable-actors-diagnostics.md)

Pokud používáte spolehlivé služby nebo vzdálené komunikace `Service Fabric Service` `Service Fabric Service Method` služeb, máme podobně a čítače kategorií, které byste měli shromažďovat čítače z, viz [monitorování s vzdálené komunikace služby](service-fabric-reliable-serviceremoting-diagnostics.md) a spolehlivé [služby čítače výkonu](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Pokud používáte spolehlivé kolekce, doporučujeme přidat `Avg. Transaction ms/Commit` z `Service Fabric Transactional Replicator` shromažďovat průměrnou latenci potvrzení na metriku transakce.


## <a name="next-steps"></a>Další kroky

* Další informace o [generování událostí na úrovni platformy](service-fabric-diagnostics-event-generation-infra.md) v Service Fabric
* Shromažďování metrik výkonu prostřednictvím [agenta Log Analytics](service-fabric-diagnostics-oms-agent.md)
