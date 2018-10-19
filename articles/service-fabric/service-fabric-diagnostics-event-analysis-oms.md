---
title: Analýza událostí Azure Service Fabric pomocí Log Analytics | Dokumentace Microsoftu
description: Další informace o vizualizaci a analýzu událostí pomocí Log Analytics pro monitorování a diagnostiku clustery Azure Service Fabric.
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
ms.openlocfilehash: 6dee895ba9fc024baac0500619b7d6cc62167b6d
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404473"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Události analýzy a vizualizace s využitím Log Analytics
Log Analytics shromažďuje a analyzuje telemetrii z aplikace a služby hostované v cloudu a poskytuje analýzy nástroje, které vám umožní maximalizovat jejich dostupnost a výkon. Tento článek popisuje, jak spouštět dotazy v Log Analytics k získání přehledu a řešení potíží s co se děje ve vašem clusteru. Se podrobněji probírají následující běžné otázky:

* Jak řešit události stavu
* Jak poznám, kdy se uzel ocitne mimo provoz?
* Jak poznám, že pokud služby vaší aplikace mají spouštění nebo zastavování?

## <a name="log-analytics-workspace"></a>Pracovní prostor Log Analytics

Log Analytics shromažďuje data ze spravovaných prostředků, včetně tabulku úložiště Azure nebo agenta a udržuje v centrálním úložišti. Data lze potom slouží pro analýzy, výstrahy a vizualizace nebo další exportu. Log Analytics podporuje události, údaje o výkonu nebo jiné vlastní data. Podívejte se na [postup pro konfiguraci rozšíření diagnostiky pro agregaci událostí](service-fabric-diagnostics-event-aggregation-wad.md) a [kroky k vytvoření pracovního prostoru Log Analytics pro čtení z události ve službě storage](service-fabric-diagnostics-oms-setup.md) k Ujistěte se, že se data přenášejí do Log Analytics .

Po přijetí dat pomocí Log Analytics, Azure nabízí několik *řešení pro správu* , která jsou připravená řešení pro monitorování příchozích dat, přizpůsobit tak, aby několik scénářů. Mezi ně patří *analýza služby Service Fabric* řešení a *kontejnery* řešení, které jsou dva nejdůležitější ty, které Diagnostika a monitorování clusterů Service Fabric. Tento článek popisuje, jak použít řešení analýza služby Service Fabric, který je vytvořen s pracovním prostorem.

## <a name="access-the-service-fabric-analytics-solution"></a>Přístup k řešení analýza služby Service Fabric

1. Na webu Azure Portal přejděte do skupiny prostředků, ve které jste vytvořili řešení analýza služby Service Fabric.

2. Vyberte prostředek **ServiceFabric\<nameOfOMSWorkspace\>**.

2. Stručně řečeno zobrazí se dlaždice ve formě grafu pro každou z řešení povolené, včetně pro Service Fabric. Klikněte na tlačítko **Service Fabric** graph (první obrázek níže) a pokračujte v Service Fabric analytického řešení (druhý obrázek níže).

    ![Řešení Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

    ![Řešení Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

Na obrázku výše je domovská stránka řešení analýza služby Service Fabric. Toto je zobrazení snímku, co se děje ve vašem clusteru. Pokud jste povolili Diagnostika při vytváření clusteru, zobrazí se události pro 

* [Provozní kanál](service-fabric-diagnostics-event-generation-operational.md): Chcete získat podrobnější popis operace, které platforma Service Fabric (kolekce služeb system) provádí.
* [Programovacího modelu Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Programovacího modelu Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Kromě provozní kanál můžete má shromažďovat podrobné systémové události [aktualizací konfigurace diagnostického rozšíření](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Zobrazit události Service Fabric včetně akcí na uzlech

1. Na stránce Analýza služby Service Fabric, klikněte na graf pro **události Service Fabric**.

    ![Service Fabric řešení provozní kanál](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

2. Klikněte na tlačítko **seznamu** k zobrazení událostí v seznamu. Jakmile tady se zobrazí všechny události systému, které byly shromážděny. Pro srovnání ty pocházejí od WADServiceFabricSystemEventsTable v účtu Azure Storage a podobně modelu reliable services a události objektů actor, které se zobrazí vedle jsou z těchto tabulek příslušných.
    
    ![Provozní kanál dotazu](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Můžete případně klikněte na lupu na levé straně a používat jazyk dotaz Kusto najít, co hledáte. Například pokud chcete najít všechny akce prováděné na uzlech v clusteru, můžete použít následující dotaz. ID událostí používá pod se nacházejí v [provozní kanál události – referenční informace](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Můžete zadávat dotazy na mnoho více polí, jako je například konkrétním uzlům (počítač) služby system (název úlohy).

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Události služby Reliable Service Fabric zobrazení a objektu Actor

1. Na stránce Analýza služby Service Fabric, klikněte na graf pro **Reliable Services**.

    ![Řešení Service Fabric Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

2. Klikněte na tlačítko **seznamu** k zobrazení událostí v seznamu. Zde můžete zobrazit události z modelu reliable services. Můžete zobrazit jednotlivé události pro při runasync služby je spuštěno a dokončeno, obvykle probíhá na nasazení a upgrade. 

    ![Reliable Services v dotazu](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Události Reliable actors můžou zobrazit podobným způsobem. Pro konfiguraci podrobnější událostí reliable actors, budete muset změnit `scheduledTransferKeywordFilter` v konfiguraci rozšíření diagnostiky (viz dole). Podrobnosti o hodnoty pro tyto [události reliable actors – referenční informace](service-fabric-reliable-actors-diagnostics.md#keywords).

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

Je výkonný dotazovací jazyk Kusto. Další cenné dotaz, který můžete spustit je a zjistěte uzlů, které generují nejvíce událostem. Dotaz na snímku obrazovky níže zobrazí provozní události Service Fabric agregovat pomocí konkrétní službu a uzel.

![Dotaz události za uzel](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Další postup

* Pokud chcete povolit infrastruktury, například monitorování čítače výkonu, přejděte na [Přidání agenta Log Analytics](service-fabric-diagnostics-oms-agent.md). Agent shromažďuje čítače výkonu a přidá je do existujícího pracovního prostoru.
* Log Analytics nabízí místními clustery brány (dopředu proxy server HTTP), který slouží k odesílání dat do Log Analytics. Další informace o, že v [počítače bez připojení k Internetu připojení k Log Analytics pomocí Log Analytics gateway](../log-analytics/log-analytics-oms-gateway.md).
* Konfigurace [automatické upozorňování](../log-analytics/log-analytics-alerts.md) pro usnadnění detekce a Diagnostika.
* Seznamte se s funkcemi [prohledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) nabízenými jako součást Log Analytics.
* Získejte podrobnější přehled o Log Analytics a navíc nabízejí, přečtěte si [co je služba Log Analytics?](../operations-management-suite/operations-management-suite-overview.md).
