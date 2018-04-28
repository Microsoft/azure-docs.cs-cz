---
title: Azure Service Fabric události | Microsoft Docs
description: Další informace o události Service Fabric poskytuje předinstalované vám pomohou monitorovat cluster Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 6379f463d5c2e9027e1d8d4dd90db61cb72cdd44
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="service-fabric-events"></a>Události Service Fabric 

Platforma Service Fabric zapíše několik strukturovaných událostí pro klíče provozní aktivity děje ve vašem clusteru. Tyto v rozsahu od clusteru upgrady rozhodování umístění repliky. Každá událost, Service Fabric zpřístupní mapy na jednu z následujících entit v clusteru:
* Cluster
* Aplikace
* Služba
* Oddíl
* Replika 
* Kontejner

Pokud chcete zobrazit úplný seznam událostí, které jsou vystavené platforma - [seznamu Service Fabric události](service-fabric-diagnostics-event-generation-operational.md).

Zde jsou některé příklady scénářů důležité, aby měli byste vidět událostí v clusteru. 
1. Události životního cyklu uzlu: jako uzly se spustit, přejděte, jsou aktivace nebo deaktivace nebo restartovat, události se zveřejní zobrazuje, co se stalo a vám pomůže určit, pokud vyskytl se problém s počítačem sám sebe nebo pokud se rozhraní API, která byla volána prostřednictvím SF na Změňte stav uzlu.
1. Upgrade clusteru: během upgradu clusteru (SF verze nebo konfigurace změnit), zobrazí se upgrade zahájení, vrácení prostřednictvím všechny vaše UDs a dokončete (nebo vrácení změn). 
1. Upgrady aplikací: podobně clusteru provede upgrade, po upgradu prostřednictvím je komplexní sadu událostí. Tyto události může být užitečné pochopit naplánovaného upgrade, aktuální stav upgradu a celkové posloupnost událostí. To je užitečné pro vyhledávání zpět zobrazíte co upgrady byly vráceny úspěšně.
1. Nasazení aplikace nebo služby nebo odstranění: nejsou k dispozici události pro jednotlivé aplikace, služby a kontejneru, vytváření nebo odstraňování.
1. Oddíl přesune (Rekonfigurace): při každém stavová oddíl prochází Rekonfigurace (změnit sady replik), se zaprotokoluje událost. To je užitečné, pokud chcete pochopit, jak často se mění vaší sady replik oddílu, nebo sledovat, který uzel byl spuštěn primární repliky v libovolném bodě v čase.
1. Události Chaos: při použití Service Fabric [Chaos](service-fabric-controlled-chaos.md) služby, události se zobrazí pokaždé, když je služba spuštěna nebo zastavená, nebo když se vloží chybu v systému.
1. Stav události: Service Fabric zpřístupní události stavu pokaždé, když se vytvoří upozornění nebo stavu zprávu o chybách, nebo entity přejde zpět do stavu OK stavu nebo vypršení platnosti sestavy stavu. Tyto události jsou velmi užitečný pro sledování stavu historických statistiky pro entitu. 

## <a name="how-to-access-events"></a>Jak přistupovat k událostem

Existuje několik různých způsobů, pomocí kterých je přístupná Service Fabric události:
* prostřednictvím provozní kanálu. Tyto můžete shromážděné prostřednictvím rozšíření diagnostiky Azure a odeslaných do tabulky úložiště pro spotřebu nebo přijímání do některého nástroje, například analýzy protokolů OMS. Pokud "Diagnostika" je povolena pro cluster, agent Azure Diagnostics je nasazený na clusteru a je ve výchozím nastavení nakonfigurován tak, aby si přečíst protokoly z provozní kanál. Další informace o konfiguraci [agenta Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) ke změně konfigurace diagnostiky clusteru a vyberte další protokoly nebo čítače výkonu. 
* prostřednictvím služby EventStore Rest API, která vám umožní dotazu clusteru přímo nebo prostřednictvím knihovny klienta služby prostředků infrastruktury. V tématu [dotazu EventStore rozhraní API pro události clusteru](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Další postup
* Další informace na monitorování cluster - [monitorování clusteru a platforma](service-fabric-diagnostics-event-generation-infra.md).
* Další informace o službě EventStore - [EventStore služba – Přehled](service-fabric-diagnostics-eventstore.md)
