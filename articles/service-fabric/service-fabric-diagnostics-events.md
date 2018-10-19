---
title: Události Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o události Service Fabric poskytuje mimo pole, které vám pomohou monitorovat vašeho clusteru Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: ca63d67f6d7c19b4ca6928c4cc0f9ccb06eace2b
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49402977"
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

Tady je několik příkladů důležitých scénářů, zobrazí se události ve vašem clusteru. 
1. Události životního cyklu uzlu: jako uzly objevují, přejděte, se aktivace nebo deaktivace nebo restartování, události se zveřejní zobrazí, co se stalo a mohli snadno identifikovat, pokud se něco stalo s celý počítač nebo pokud se rozhraní API, která byla volána prostřednictvím SF do Změňte stav uzlu.
1. Upgradovat cluster: během upgradu clusteru (SF verze nebo konfigurace změnit), zobrazí se upgrade zahájit, vrátit prostřednictvím všech vašich aktualizačními doménami a dokončit (nebo vrácení zpět). 
1. Upgrady aplikací: podobně clusteru upgraduje, se vám komplexní sadu událostí, které prochází upgradem. Tyto události může být užitečné k pochopení, kdy bylo naplánováno upgradu, aktuální stav upgradu a celkový posloupnost událostí. To je užitečné pro vyhledávání zpět na zobrazení, co upgrady byly vráceny úspěšně.
1. Nasazení aplikace/služby / odstranění: nejsou akce pro každou aplikaci, službě a kontejner, vytváření nebo odstraňování.
1. Oddíl přesune (konfigurace): pokaždé, když se stavové oddílu prochází Rekonfigurace (Změna sady replik), zaprotokoluje událost, která. To je užitečné, pokud chcete pochopit, jak často se mění sady replik oddílu, nebo sledování, který uzel byl spuštěn primární repliky v libovolném bodě v čase.
1. Chaos události: při použití Service Fabric [Chaos](service-fabric-controlled-chaos.md) service, zobrazí se události pokaždé, když služba spuštěna nebo zastavena, nebo když ho vkládá chybu v systému.
1. Události stavu: Service Fabric zpřístupňuje události týkající se stavu pokaždé, když se vytvoří upozornění nebo stavu zprávu o chybách, nebo entity se vrátí do stavu OK stavu nebo sestava stavu vypršení platnosti. Tyto události jsou velmi užitečné ke sledování stavu historické statistiky pro entitu. 

## <a name="how-to-access-events"></a>Jak získat přístup k události

Existuje několik různých způsobů, pomocí kterých můžete získat přístup události Service Fabric:
* prostřednictvím provozní kanál. Ty můžete shromážděné prostřednictvím rozšíření Azure Diagnostics a odesílána do tabulky úložiště k využití nebo příjem do nástroje, jako je Azure Log Analytics. Když "Diagnostika" je povolená pro cluster, agenta diagnostiky Azure je nasazený na clusteru a je ve výchozím nastavení nakonfigurovaný tak, aby si přečíst protokoly z provozní kanál. Další informace o konfiguraci [agenta diagnostiky Azure](service-fabric-diagnostics-event-aggregation-wad.md) upravit konfiguraci diagnostiky vašeho clusteru, aby se získaly další protokoly a čítače výkonu. 
* prostřednictvím rozhraní Rest API Eventstoru služby, které umožňují dotazování clusteru přímo nebo prostřednictvím klientské knihovny Service Fabric. Zobrazit [rozhraní API Eventstoru dotazu pro události clusteru](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Další postup
* Další informace na monitorování vašeho clusteru – [monitorování clusteru a platforma](service-fabric-diagnostics-event-generation-infra.md).
* Další informace o službě Eventstoru - [služby přehled Eventstoru](service-fabric-diagnostics-eventstore.md)
