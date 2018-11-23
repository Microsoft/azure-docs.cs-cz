---
title: Události Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o události Service Fabric poskytuje mimo pole, které vám pomohou monitorovat vašeho clusteru Azure Service Fabric.
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
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 936a47593b9db6e4989c30b2df37dfd82c286c59
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290514"
---
# <a name="service-fabric-events"></a>Události služby Service Fabric 

Platforma Service Fabric zapisuje několik strukturovaných události pro klíče provozní činnosti děje ve vašem clusteru. Tyto sahají od upgrady clusteru rozhodnutí o umístění repliky. Každá událost, Service Fabric poskytuje mapuje na jednu z následujících entit v clusteru:
* Cluster
* Aplikace
* Služba
* Oddíl
* Replika 
* Kontejner

Pokud chcete zobrazit úplný seznam událostí, které jsou vystavené platformy – [události seznamu Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Tady je několik příkladů scénářů, zobrazí se události ve vašem clusteru. 
* Události životního cyklu uzlu: za uzly objevují, přejděte, horizontální snížení nebo navýšení kapacity, restartování a jsou aktivace nebo deaktivace, tyto události se zveřejní zobrazí, co se stalo a mohli snadno identifikovat, pokud se něco stalo s celý počítač nebo pokud došlo rozhraní API, která byla volá se, prostřednictvím SF změnit stav uzlu.
* Upgradovat cluster: během upgradu clusteru (SF verze nebo konfigurace změnit), zobrazí se upgrade zahájit, vrátit přes jednotlivé domény upgradu a dokončit (nebo vrácení zpět). 
* Upgrady aplikací: stejně jako upgrady clusteru, je komplexní sadu událostí jako prochází upgradem. Tyto události může být užitečné k pochopení, kdy bylo naplánováno upgradu, aktuální stav upgradu a celkový posloupnost událostí. To je užitečné pro vyhledávání zpět zobrazíte co upgrady byly vráceny úspěšně nebo určuje, zda byla aktivována vrácení zpět.
* Nasazení aplikace/služby / odstranění: nejsou akce pro každou aplikaci, služby a container právě vytvořené nebo odstraněné a užitečné při horizontálním snížení nebo navýšení kapacity třeba zvýšit počet replik
* Oddíl přesune (konfigurace): pokaždé, když se stavové oddílu prochází Rekonfigurace (Změna sady replik), zaprotokoluje událost, která. To je užitečné, pokud chcete pochopit, jak často je sada replik oddílů mění nebo převzetí služeb při selhání nebo sledování, který uzel byl spuštěn primární repliky v libovolném bodě v čase.
* Chaos události: při použití Service Fabric [Chaos](service-fabric-controlled-chaos.md) service, zobrazí se události pokaždé, když služba spuštěna nebo zastavena, nebo když ho vkládá chybu v systému.
* Události stavu: Service Fabric zpřístupňuje události týkající se stavu pokaždé, když se vytvoří upozornění nebo stavu zprávu o chybách, nebo entity se vrátí do stavu OK stavu nebo sestava stavu vypršení platnosti. Tyto události jsou velmi užitečné ke sledování stavu historické statistiky pro entitu. 

## <a name="how-to-access-events"></a>Jak získat přístup k události

Existuje několik různých způsobů, pomocí kterých můžete získat přístup události Service Fabric:
* Události se neprotokolují pomocí standardních kanálů, jako jsou protokoly událostí trasování událostí pro Windows a Windows a můžete vizualizovat pomocí monitorovacího nástroje, který podporuje tyto například služba Log Analytics. Ve výchozím nastavení clustery vytvořené na portálu mají zapnutou diagnostikou a agenta diagnostiky Azure pro Windows, odesílání událostí do služby Azure table storage, ale je stále potřeba to integrovat s vaším prostředkem Log Analytics. Další informace o konfiguraci [agenta diagnostiky Azure](service-fabric-diagnostics-event-aggregation-wad.md) upravit konfiguraci diagnostiky vašeho clusteru, aby se získaly další protokoly a čítače výkonu a [integrace Log Analytics](service-fabric-diagnostics-event-analysis-oms.md)
* Eventstoru služby rozhraní Rest API, které umožňují dotazování clusteru přímo nebo prostřednictvím klientské knihovny Service Fabric. Zobrazit [rozhraní API Eventstoru dotazu pro události clusteru](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Další postup
* Další informace na monitorování vašeho clusteru – [monitorování clusteru a platforma](service-fabric-diagnostics-event-generation-infra.md).
* Další informace o službě Eventstoru - [služby přehled Eventstoru](service-fabric-diagnostics-eventstore.md)
