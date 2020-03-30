---
title: Analýza událostí azure service fabric pomocí protokolů Azure Monitor
description: Další informace o vizualizaci a analýze událostí pomocí protokolů Azure Monitor pro monitorování a diagnostiku clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 40dd930aa21e3056d5ecc908359215d6874ed8ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464737"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Analýza událostí a vizualizace pomocí protokolů Azure Monitoru
 Protokoly Azure Monitor shromažďuje a analyzuje telemetrie z aplikací a služeb hostovaných v cloudu a poskytuje analytické nástroje, které vám pomohou maximalizovat jejich dostupnost a výkon. Tento článek popisuje, jak spouštět dotazy v protokolech Azure Monitor získat přehledy a řešení potíží, co se děje ve vašem clusteru. Řeší se tyto běžné otázky:

* Jak lze řešit problémy se zdravotními událostmi?
* Jak poznám, že dojde k pádu uzlu?
* Jak poznám, že služby aplikace byly spuštěny nebo zastaveny?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Přehled pracovního prostoru Log Analytics

>[!NOTE] 
>Zatímco diagnostické úložiště je ve výchozím nastavení povoleno v době vytvoření clusteru, musíte stále nastavit pracovní prostor Log Analytics pro čtení z diagnostického úložiště.

Protokoly Azure Monitor shromažďuje data ze spravovaných prostředků, včetně tabulky úložiště Azure nebo agenta, a udržuje je v centrálním úložišti. Data pak lze použít pro analýzu, výstrahy a vizualizaci nebo další export. Protokoly Azure Monitor podporuje události, data o výkonu nebo jiná vlastní data. Podívejte se na [kroky konfigurace rozšíření diagnostiky pro agregaci událostí](service-fabric-diagnostics-event-aggregation-wad.md) a kroky k vytvoření [pracovního prostoru Log Analytics ke čtení z událostí v úložišti](service-fabric-diagnostics-oms-setup.md) a ujistěte se, že data proudí do protokolů Azure Monitor.

Po přijetí dat protokoly Azure Monitor, Azure má několik *řešení monitorování,* které jsou předem zabalená řešení nebo provozní řídicí panely pro monitorování příchozích dat, přizpůsobené několika scénářů. Patří mezi ně service *fabric analytics* řešení a *kontejnery* řešení, které jsou dvě nejdůležitější pro diagnostiku a monitorování při použití clusterů Service Fabric. Tento článek popisuje, jak používat service fabric analytics řešení, které je vytvořeno s pracovním prostorem.

## <a name="access-the-service-fabric-analytics-solution"></a>Přístup k řešení Service Fabric Analytics

Na [webu Azure Portal](https://portal.azure.com)přejděte do skupiny prostředků, ve které jste vytvořili řešení Service Fabric Analytics.

Vyberte název **servicefabric\<\>zdrojeOfOMSWorkspace**.

V `Summary`tématu se zobrazí dlaždice ve formě grafu pro každé povolené řešení, včetně jednoho pro service fabric. Kliknutím na graf **Service Fabric** přenesete k řešení Service Fabric Analytics.

![Service Fabric řešení](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Následující obrázek znázorňuje domovskou stránku řešení Service Fabric Analytics. Tato domovská stránka poskytuje snímek zobrazení toho, co se děje ve vašem clusteru.

![Service Fabric řešení](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Pokud jste při vytváření clusteru povolili diagnostiku, můžete zobrazit události pro 

* [Události clusteru Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Události programovacího modelu spolehlivých herců](service-fabric-reliable-actors-diagnostics.md)
* [Události programovacího modelu spolehlivých služeb](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Kromě události Service Fabric po vybalení, podrobnější systémové události lze shromažďovat [aktualizací konfigurace rozšíření diagnostiky](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Zobrazit události service fabric, včetně akcí na uzlech

Na stránce Service Fabric Analytics klikněte na graf událostí **service fabric**.

![Operační kanál řešení service fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Kliknutím na **Seznam** zobrazíte události v seznamu. Jakmile zde uvidíte všechny systémové události, které byly shromážděny. Pro referenci jsou z **WADServiceFabricSystemEventsTable** v účtu Azure Storage a podobně spolehlivé služby a objekty actor události, které vidíte další jsou z těchto příslušných tabulek.
    
![Dotaz na operační kanál](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Případně můžete kliknout na lupu vlevo a pomocí dotazovacího jazyka Kusto najít to, co hledáte. Chcete-li například vyhledat všechny akce provedené v uzlech v clusteru, můžete použít následující dotaz. ID událostí použitá níže se nacházejí v [odkazu na události operačního kanálu](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Můžete dotaz ovat v mnoha dalších polích, jako jsou konkrétní uzly (počítač) systémová služba (TaskName).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Zobrazit události spolehlivé služby a objektu actor service fabric

Na stránce Service Fabric Analytics klikněte na graf **pro spolehlivé služby**.

![Spolehlivé služby service fabric řešení](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Kliknutím na **Seznam** zobrazíte události v seznamu. Zde si můžete prohlédnout události ze spolehlivých služeb. Můžete zobrazit různé události pro při spuštění a dokončení služby runasync, které se obvykle děje na nasazení a upgrady. 

![Dotaz na spolehlivé služby](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Spolehlivé události herce lze prohlížet podobným způsobem. Chcete-li nakonfigurovat podrobnější události pro spolehlivé `scheduledTransferKeywordFilter` objekty actor, je třeba změnit v konfiguraci pro rozšíření diagnostiky (viz níže). Podrobnosti o hodnotách pro tyto jsou v [odkazna události spolehlivé objekty](service-fabric-reliable-actors-diagnostics.md#keywords).

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

Dotazovací jazyk Kusto je silný. Dalším cenným dotazem, který můžete spustit, je zjistit, které uzly generují nejvíce událostí. Dotaz na následujícím snímku obrazovky ukazuje provozní události Service Fabric agregované s konkrétní službou a uzlem.

![Události dotazu na uzel](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Další kroky

* Chcete-li povolit monitorování infrastruktury, tj. [adding the Log Analytics agent](service-fabric-diagnostics-oms-agent.md) Agent shromažďuje čítače výkonu a přidává je do existujícího pracovního prostoru.
* Pro místní clustery protokoly Azure Monitor nabízí bránu (HTTP forward proxy), který lze použít k odesílání dat do protokolů Azure Monitor. Další informace o tom načtete v [části Připojení počítačů bez přístupu k internetu k protokolům Azure Monitor pomocí brány Log Analytics](../azure-monitor/platform/gateway.md).
* Nakonfigurujte [automatické upozorňování](../log-analytics/log-analytics-alerts.md) tak, aby pomáhalo při detekci a diagnostice.
* Seznamte se s funkcemi [pro vyhledávání protokolů a dotazování,](../log-analytics/log-analytics-log-searches.md) které jsou nabízeny jako součást protokolů Azure Monitoru.
* Získejte podrobnější přehled protokolů Azure Monitoru a co nabízí, přečtěte si [článek Co je protokoly Azure Monitoru?](../operations-management-suite/operations-management-suite-overview.md).
