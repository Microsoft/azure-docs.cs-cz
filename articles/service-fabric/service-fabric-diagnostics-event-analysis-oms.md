---
title: Protokoly Azure Analysis Service Fabric události prostřednictvím služby Azure Monitor | Dokumentace Microsoftu
description: Další informace o vizualizaci a analýzu událostí pomocí Azure monitoru protokoly pro monitorování a diagnostiku clustery Azure Service Fabric.
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
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 2f3106b33ab0cbea95efe2ac42c05a8543719190
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246912"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Události analýzy a vizualizace s protokoly Azure monitoru
 Protokoly Azure monitoru shromažďuje a analyzuje telemetrii z aplikace a služby hostované v cloudu a poskytuje analytické nástroje, které vám pomůžou maximálně využít jejich dostupnost a výkon. Tento článek popisuje, jak spouštět dotazy v protokolech Azure Monitor k získání přehledu a řešení potíží s co se děje ve vašem clusteru. Se podrobněji probírají následující běžné otázky:

* Jak řešit události stavu
* Jak poznám, kdy se uzel ocitne mimo provoz?
* Jak poznám, že pokud služby vaší aplikace mají spouštění nebo zastavování?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Přehled pracovního prostoru Log Analytics

>[!NOTE] 
>Diagnostické úložiště je povolen ve výchozím nastavení při vytváření clusteru, stále musíte vytvořit pracovní prostor Log Analytics pro čtení z úložiště diagnostiky.

Azure Monitor protokoluje shromažďuje data ze spravovaných prostředků, včetně tabulku úložiště Azure nebo agenta a udržuje v centrálním úložišti. Data lze potom slouží pro analýzy, výstrahy a vizualizace nebo další exportu. Azure Monitor protokoly podporuje události, údaje o výkonu nebo jiné vlastní data. Podívejte se na [postup pro konfiguraci rozšíření diagnostiky pro agregaci událostí](service-fabric-diagnostics-event-aggregation-wad.md) a [kroky k vytvoření pracovního prostoru Log Analytics pro čtení z události ve službě storage](service-fabric-diagnostics-oms-setup.md) k Ujistěte se, že se data přenášejí do služby Azure Monitor ukládá do protokolu.

Po přijetí dat pomocí Azure monitoru protokoly Azure obsahuje několik *řešení pro monitorování* , která jsou připravená řešení nebo provozní řídicí panely se budou monitorovat příchozí data upravit tak, aby několik scénářů. Mezi ně patří *analýza služby Service Fabric* řešení a *kontejnery* řešení, které jsou dva nejdůležitější ty, které Diagnostika a monitorování clusterů Service Fabric. Tento článek popisuje, jak použít řešení analýza služby Service Fabric, který je vytvořen s pracovním prostorem.

## <a name="access-the-service-fabric-analytics-solution"></a>Přístup k řešení analýza služby Service Fabric

V [webu Azure Portal](https://portal.azure.com), přejděte do skupiny prostředků, ve které jste vytvořili řešení analýza služby Service Fabric.

Vyberte prostředek **ServiceFabric\<nameOfOMSWorkspace\>**.

V `Summary`, zobrazí se dlaždice ve formě grafu pro každou z řešení povolené, včetně pro Service Fabric. Klikněte na tlačítko **Service Fabric** grafu a pokračujte v Service Fabric analytického řešení.

![Řešení Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Následující obrázek ukazuje domovské stránce řešení analýza služby Service Fabric. Tato stránka Domovská stránka obsahuje zobrazení snímku, co se děje ve vašem clusteru.

![Řešení Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Pokud jste povolili Diagnostika při vytváření clusteru, zobrazí se události pro 

* [Události clusteru Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Programovacího modelu Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Programovacího modelu Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Kromě události Service Fabric připravené, můžete shromážděná podrobnější systémové události [aktualizací konfigurace diagnostického rozšíření](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Zobrazit události Service Fabric, včetně akce na uzlech

Na stránce Analýza služby Service Fabric, klikněte na graf pro **události Service Fabric**.

![Service Fabric řešení provozní kanál](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Klikněte na tlačítko **seznamu** k zobrazení událostí v seznamu. Jakmile tady se zobrazí všechny události systému, které byly shromážděny. Pro srovnání jde z **WADServiceFabricSystemEventsTable** ve službě Azure Storage jsou z těchto tabulek příslušných účtu a podobně spolehlivé služby a objekty actor události se zobrazí vedle.
    
![Provozní kanál dotazu](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Můžete případně klikněte na lupu na levé straně a používat jazyk dotaz Kusto najít, co hledáte. Například pokud chcete najít všechny akce prováděné na uzlech v clusteru, můžete použít následující dotaz. ID událostí používá pod se nacházejí v [provozní kanál události – referenční informace](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Můžete zadávat dotazy na mnoho více polí, jako je například konkrétním uzlům (počítač) služby system (název úlohy).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Události služby Reliable Service Fabric zobrazení a objektu Actor

Na stránce Analýza služby Service Fabric, klikněte na graf pro **Reliable Services**.

![Řešení Service Fabric Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Klikněte na tlačítko **seznamu** k zobrazení událostí v seznamu. Zde můžete zobrazit události z modelu reliable services. Můžete zobrazit jednotlivé události pro při runasync služby je spuštěno a dokončeno, obvykle probíhá na nasazení a upgrade. 

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
* Místními clustery protokoly Azure Monitor nabízí brány (dopředu proxy server HTTP), který slouží k odesílání dat do Azure monitoru protokoly. Další informace o, že v [připojení počítače bez připojení k Internetu pomocí brány Log Analytics protokoly Azure monitoru](../azure-monitor/platform/gateway.md).
* Konfigurace [automatické upozorňování](../log-analytics/log-analytics-alerts.md) pro usnadnění detekce a Diagnostika.
* Seznamte se s [prohledávání protokolů a dotazování](../log-analytics/log-analytics-log-searches.md) funkce nabízí jako součást protokoly Azure monitoru.
* Získejte podrobnější přehled o protokoly Azure monitoru a navíc nabízejí, přečtěte si [co je Azure Monitor protokoly?](../operations-management-suite/operations-management-suite-overview.md).
