---
title: Analýza události služby Azure Service Fabric s analýzy protokolů | Microsoft Docs
description: Další informace o vizualizaci a analýzu událostí pomocí analýzy protokolů pro monitorování a Diagnostika Azure Service Fabric clusterů.
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
ms.date: 05/29/2018
ms.author: srrengar
ms.openlocfilehash: 49d9b5306a0fcf51cc0de036c725fca8345cd0ec
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302178"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Analýza události a vizualizace s analýzy protokolů
Analýzy protokolů shromažďuje a analyzuje příchozí telemetrická data z aplikace a služby hostované v cloudu a poskytuje nástrojů pro analýzu můžete maximalizovat jejich dostupnost a výkon. Tento článek ukazuje, jak je ke spouštění dotazů v analýzy protokolů a získáte přehled o řešení potíží s co se děje v clusteru. Následující běžné otázky se podrobněji:

* Jak odstranit události stavu?
* Jak poznám, kdy uzlu přestane fungovat?
* Jak poznám, pokud mají spouštění nebo zastavování služeb Moje aplikace?

## <a name="log-analytics-workspace"></a>Pracovní prostor Log Analytics

Analýzy protokolů shromažďuje data z spravované prostředky, včetně úložiště Azure table nebo agenta a udržuje v centrálním úložišti. Data lze poté použít pro analýzy, výstrahy a vizualizace nebo další export. Analýzy protokolů podporuje události, údaje o výkonu nebo jiná vlastní data. Podívejte se na [postup pro konfiguraci rozšíření diagnostiky pro agregaci událostí](service-fabric-diagnostics-event-aggregation-wad.md) a [kroky k vytvoření pracovního prostoru analýzy protokolů číst z událostí v úložišti](service-fabric-diagnostics-oms-setup.md) a ujistěte se, je dat odesílaných do analýzy protokolů .

Po přijetí dat podle analýzy protokolů Azure má několik *řešení pro správu* hotových řešení ke sledování příchozích dat, upravit tak, aby několik scénářů, které jsou. Mezi ně patří *Service Fabric Analytics* řešení a *kontejnery* řešení, které jsou dvě nejdůležitější ty diagnostiky a monitorování při použití clusterů Service Fabric. Tento článek popisuje, jak používat řešení Service Fabric analýzy, která je vytvořena s pracovním prostoru.

## <a name="access-the-service-fabric-analytics-solution"></a>Přístup k řešení Service Fabric Analytics

1. Na portálu Azure přejděte do skupiny prostředků, ve které jste vytvořili řešení Service Fabric analýzy.

2. Vyberte prostředek **ServiceFabric\<nameOfOMSWorkspace\>**.

2. V souhrnu uvidíte dlaždice ve formě grafu pro každou z řešení povoleno, včetně jeden pro Service Fabric. Klikněte **Service Fabric** graf (první obrázek níže) nadále řešení Service Fabric analýzy (druhý obrázek níže).

    ![Řešení Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

    ![Řešení Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

Na obrázku výše je domovské stránce řešení Service Fabric analýzy. Toto je snímek zobrazení co se děje v clusteru. Pokud jste povolili diagnostiky při vytváření clusteru, zobrazí se události pro 

* [Provozní kanál](service-fabric-diagnostics-event-generation-operational.md): vyšší úrovně operace, které provádí platformy Service Fabric (kolekce služeb systému).
* [Programování událostí modelu Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Spolehlivé služby programovací model události](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Kromě provozní kanál, může shromáždit podrobnější systémové události [aktualizace konfigurace vaší rozšíření diagnostiky](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Zobrazit události prostředků infrastruktury služby včetně akce na uzlech

1. Na stránce služby Fabric Analytics klikněte na graf pro **události služby Fabric**.

    ![Provozní kanál řešení služby prostředků infrastruktury](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

2. Klikněte na tlačítko **seznamu** k zobrazení událostí, v seznamu. Jakmile se v tomto poli se zobrazí všechny události systému, které byly shromážděny. Pro odkaz jedná se o z WADServiceFabricSystemEventsTable v účtu úložiště Azure a podobně reliable services a aktéři události, které se zobrazí vedle jsou z těchto příslušných tabulek.
    
    ![Provozní kanál dotazu](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Můžete případně klikněte na tlačítko lupy na levé straně a použít Kusto dotazovací jazyk najít, co hledáte. Například pokud chcete vyhledat všechny akce prováděné na uzlech v clusteru, můžete použít následující dotaz. ID událostí použít níže se nacházejí v [reference pro provozní kanál události](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Můžete zadat dotaz na mnoho další pole, jako je například konkrétním uzlům (počítače) služby system (TaskName).

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Události zobrazení Service Fabric spolehlivé Service a objektu Actor

1. Na stránce služby Fabric Analytics klikněte na graf pro **spolehlivé služby**.

    ![Služba Fabric řešení spolehlivé služby](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

2. Klikněte na tlačítko **seznamu** k zobrazení událostí, v seznamu. Zde můžete zobrazit události z spolehlivé služby. Různé událostí pro můžete zobrazit, když runasync služby je spuštěno a dokončeno, což obvykle se odehrává na nasazení a upgrady. 

    ![Spolehlivé služby dotazu](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Spolehlivé objektu actor události lze zobrazit podobným způsobem. Ke konfiguraci podrobnější události reliable actors, budete muset změnit `scheduledTransferKeywordFilter` v konfiguraci rozšíření diagnostiky (zobrazené dole). Podrobnosti na hodnoty pro tyto jsou v [spolehlivé aktéři události odkaz](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Dotazovací jazyk Kusto je výkonný. Jiné cenné dotaz, který můžete spustit je a zjistěte, uzlů, které generují nejvíce událostem. Dotaz na tomto snímku obrazovky zobrazuje provozní události Service Fabric agregovat pomocí konkrétní službu a uzel.

![Události dotaz na uzel](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Další postup

* Pokud chcete povolit sledování tj čítače výkonu infrastruktury, přejděte na [Přidání agenta analýzy protokolů](service-fabric-diagnostics-oms-agent.md). Agent shromažďuje čítače výkonu a přidá je do vaší stávající pracovního prostoru.
* Pro místní clusterů analýzy protokolů nabízí brány (dál server Proxy protokolu HTTP), který slouží k odesílání dat k analýze protokolů. Další informace o v [propojení počítačů bez připojení k Internetu k analýze protokolů pomocí brány OMS](../log-analytics/log-analytics-oms-gateway.md)
* Konfigurace [automatizované výstrahy](../log-analytics/log-analytics-alerts.md) pro usnadnění detekce a Diagnostika
* Získat familiarized s [vyhledávání a dotazování protokolu](../log-analytics/log-analytics-log-searches.md) funkcím poskytovaným jako součást analýzy protokolů
* Získat podrobnější přehled analýzy protokolů a co nabízí, přečtěte si [co je Log Analytics?](../operations-management-suite/operations-management-suite-overview.md)
