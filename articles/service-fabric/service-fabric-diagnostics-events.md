---
title: Události azure service fabric
description: Další informace o události Service Fabric poskytované po vybalení, které vám pomohou sledovat cluster Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 638b650e485ad3e83bd6021639a7e55b540d9cdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451733"
---
# <a name="service-fabric-events"></a>Události služby Service Fabric 

Platforma Service Fabric zapisuje několik strukturovaných událostí pro klíčové provozní aktivity, které se dějí v rámci clusteru. Ty se pohybují od upgradů clusteru až po rozhodnutí o umístění replik. Každá událost, kterou Service Fabric zveřejňuje mapy na jednu z následujících entit v clusteru:
* Cluster
* Aplikace
* Služba
* Oddíl
* Replika 
* Kontejner

Chcete-li zobrazit úplný seznam událostí vystavených platformou - [Seznam událostí Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Tady je několik příkladů scénářů, pro které byste měli vidět události ve vašem clusteru. 
* Události životního cyklu uzlu: jako uzly přijít, přejít dolů, škálování dovnitř / ven, restartovat a jsou aktivovány / deaktivovány, budou tyto události vystaveny ukazuje, co se stalo, a pomůže vám zjistit, zda je něco v nepořádku se samotným počítačem, nebo pokud tam byl rozhraní API, který byl volán přes SF změnit stav uzlu.
* Upgrade clusteru: při upgradu clusteru (verze SF nebo změna konfigurace) se zobrazí inicializace, projeďte každou z upgradovacích domén a dokončíte (nebo vrácení zpět). 
* Upgrady aplikací: stejně jako upgrady clusteru, je komplexní sada událostí, jak upgrade projíždí. Tyto události mohou být užitečné pochopit, kdy byl naplánován upgrade, aktuální stav upgradu a celkový sled událostí. To je užitečné pro ohlédnutí za tím, jaké upgrady byly úspěšně zavedeny nebo zda bylo spuštěno vrácení zpět.
* Nasazení /odstranění aplikace/služby: existují události pro každou aplikaci, službu a kontejner, které jsou vytvářeny nebo odstraňovány a jsou užitečné při škálování například při škálování například při škálování replik
* Oddíl se přesune (změna konfigurace): kdykoli stavový oddíl prochází rekonfigurací (změna v sadě replik), je zaznamenána událost. To je užitečné, pokud se pokoušíte pochopit, jak často se sada replik oddílů mění nebo přejimá převzetí mno ství nebo sledujete, který uzel byl v libovolném okamžiku spuštěn primární replikou.
* Události chaosu: při použití služby [Chaos](service-fabric-controlled-chaos.md) služby Service Fabric se události zobrazí při každém spuštění nebo zastavení služby nebo při aplikaci chyby v systému.
* Události stavu: Service Fabric zveřejňuje události stavu pokaždé, když je vytvořena zpráva o stavu upozornění nebo chyby nebo entita přejde zpět do stavu OK nebo vyprší platnost sestavy stavu. Tyto události jsou velmi užitečné pro sledování historických statistik stavu entity. 

## <a name="how-to-access-events"></a>Jak získat přístup k událostem

Existuje několik různých způsobů, kterými lze přistupovat k událostem Service Fabric:
* Události jsou protokolovány prostřednictvím standardních kanálů, jako jsou protokoly událostí ETW/Windows a lze je vizualizovat libovolný nástroj pro monitorování, který podporuje tyto protokoly, jako jsou protokoly Azure Monitor. Ve výchozím nastavení mají clustery vytvořené na portálu zapnutou diagnostiku a agent diagnostiky Windows Azure odesílá události do úložiště tabulek Azure, ale pořád to potřebujete integrovat s vaším prostředkem analýzy protokolů. Přečtěte si další informace o konfiguraci [agenta Diagnostiky Azure](service-fabric-diagnostics-event-aggregation-wad.md) pro úpravu konfigurace diagnostiky vašeho clusteru tak, aby bylo možné zachytit další protokoly nebo čítače výkonu, a [integraci protokolů Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* Pravidla pro zbytek služby EventStore, která umožňují dotazovat se přímo na cluster nebo prostřednictvím klientské knihovny Service Fabric. Informace [o událostech úložiště událostí dotazu naleznete v tématu Query EventStore .](service-fabric-diagnostics-eventstore-query.md)

## <a name="next-steps"></a>Další kroky
* Další informace o monitorování clusteru – [sledování clusteru a platformy](service-fabric-diagnostics-event-generation-infra.md).
* Další informace o službě EventStore – [přehled služby EventStore](service-fabric-diagnostics-eventstore.md)
