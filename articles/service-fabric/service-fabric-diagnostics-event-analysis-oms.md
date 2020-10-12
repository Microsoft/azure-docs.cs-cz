---
title: Analýza událostí v Azure Service Fabric pomocí protokolů Azure Monitor
description: Seznamte se s vizualizací a analýzou událostí pomocí protokolů Azure Monitor pro monitorování a diagnostiku clusterů Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: bd952449cb088a383f0b9241fb7856522fbeeb10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257669"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Analýza a vizualizace událostí pomocí protokolů Azure Monitor
 Protokoly Azure Monitor shromažďuje a analyzuje telemetrii z aplikací a služeb hostovaných v cloudu a poskytuje analytické nástroje, které vám pomůžou maximalizovat jejich dostupnost a výkon. Tento článek popisuje, jak spouštět dotazy v protokolech Azure Monitor a získat přehledy a řešit potíže s tím, co se děje ve vašem clusteru. Řeší se tyto běžné otázky:

* Návody řešení potíží s událostmi stavu?
* Návody informace o tom, kdy uzel zmizí?
* Návody zjistit, jestli se služby Moje aplikace spustily nebo zastavily?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Přehled pracovního prostoru Log Analytics

>[!NOTE] 
>I když je ve výchozím nastavení v době vytváření clusteru povolené diagnostické úložiště, musíte nastavit pracovní prostor Log Analytics pro čtení z diagnostického úložiště.

Protokoly Azure Monitor shromažďuje data ze spravovaných prostředků, včetně tabulky Azure Storage nebo agenta, a udržuje je v centrálním úložišti. Data se pak dají použít k analýze, upozorňování a vizualizaci nebo k dalšímu exportu. Protokoly Azure Monitor podporují události, údaje o výkonu nebo jiná vlastní data. Projděte [si postup konfigurace diagnostického rozšíření pro agregované události](service-fabric-diagnostics-event-aggregation-wad.md) a [kroky pro vytvoření pracovního prostoru Log Analytics ke čtení z událostí v úložišti](service-fabric-diagnostics-oms-setup.md) , aby bylo zajištěno, že budou data předávána do protokolů Azure monitor.

Po přijetí dat pomocí protokolů Azure Monitor má Azure několik *řešení monitorování* , která jsou předbalená řešení nebo provozní řídicí panely pro monitorování příchozích dat přizpůsobených několika scénářům. Patří mezi ně *Service Fabric Analytics* řešení a řešení *kontejnerů* , což jsou dvě nejdůležitější nástroje pro diagnostiku a monitorování při použití Service Fabric clusterů. Tento článek popisuje, jak používat řešení Service Fabric Analytics, které je vytvořené v pracovním prostoru.

## <a name="access-the-service-fabric-analytics-solution"></a>Přístup k řešení Service Fabric Analytics

Na webu [Azure Portal](https://portal.azure.com)přejdete do skupiny prostředků, ve které jste vytvořili řešení Service Fabric Analytics.

Vyberte **ServiceFabric \<nameOfOMSWorkspace\> **prostředku.

V nástroji `Summary` se zobrazí dlaždice ve formě grafu pro každé povolené řešení, včetně jednoho pro Service Fabric. Kliknutím na graf **Service Fabric** pokračujte v řešení Service Fabric Analytics.

![Řešení Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Následující obrázek ukazuje domovskou stránku Service Fabric Analytics řešení. Tato domovská stránka nabízí snímek toho, co se děje ve vašem clusteru.

![Řešení Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Pokud jste při vytváření clusteru povolili diagnostiku, můžete zobrazit události pro 

* [Service Fabric události clusteru](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors události programovacího modelu](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services události programovacího modelu](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Kromě Service Fabric událostí z pole mohou být shromážděny podrobnější systémové události prostřednictvím [aktualizace konfigurace diagnostického rozšíření](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Zobrazit Service Fabric události, včetně akcí na uzlech

Na stránce Service Fabric Analytics klikněte na graf pro **události Service Fabric**.

![Provozní kanál Service Fabric řešení](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Kliknutím na **seznam** zobrazíte události v seznamu. Jakmile se zobrazí všechny systémové události, které byly shromážděny. Azure Storage v případě, že se jedná o referenci z **WADServiceFabricSystemEventsTable** účtu, a podobně události Reliable Services a Actors, které vidíte, jsou z příslušných tabulek.
    
![Provozní kanál dotazů](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Případně můžete kliknout na tlačítko lupy vlevo a pomocí dotazovacího jazyka Kusto vyhledat, co hledáte. Pokud například chcete najít všechny akce prováděné na uzlech v clusteru, můžete použít následující dotaz. ID událostí, která se používají níže, najdete v [referenčních událostech pro události provozních kanálů](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Můžete zadávat dotazy na mnoho dalších polí, například na konkrétní uzly (počítač) systémové služby (název úlohy).

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Zobrazení Service Fabric spolehlivých událostí služby a objektu actor

Na stránce Service Fabric Analytics klikněte na graf pro **Reliable Services**.

![Reliable Services řešení Service Fabric](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Kliknutím na **seznam** zobrazíte události v seznamu. Tady vidíte události ze spolehlivých služeb. V případě, že je služba RunAsync spuštěná a dokončená, což se obvykle stává při nasazení a upgradech, se můžete podívat na různé události. 

![Reliable Services dotazů](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Spolehlivé události objektu actor lze zobrazit podobným způsobem. Chcete-li nakonfigurovat podrobnější události pro Reliable Actors, je nutné změnit `scheduledTransferKeywordFilter` v konfiguraci diagnostického rozšíření (viz níže). Podrobnosti o hodnotách pro tyto položky jsou uvedeny v referenčních informacích o [událostech Reliable Actors](service-fabric-reliable-actors-diagnostics.md#keywords).

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

Dotazovací jazyk Kusto je výkonný. Další užitečný dotaz, který můžete spustit, je zjistit, které uzly generují nejvíc událostí. Dotaz na snímku obrazovky níže ukazuje Service Fabric provozní události agregované se specifickou službou a uzlem.

![Události dotazu na uzel](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Další kroky

* Aby bylo možné povolit monitorování infrastruktury, například čítače výkonu, převzetí služeb při [přidávání agenta Log Analytics](service-fabric-diagnostics-oms-agent.md). Agent shromáždí čítače výkonu a přidá je do existujícího pracovního prostoru.
* U místních clusterů nabízí Azure Monitor protokoly bránu (proxy server HTTP, která se dá použít k odesílání dat do protokolů Azure Monitor. Přečtěte si další informace o tom, jak v [počítačích připojit počítače bez přístupu k internetu Azure monitor protokoly pomocí brány Log Analytics](../azure-monitor/platform/gateway.md).
* Nakonfigurujte  [automatizované upozorňování](../azure-monitor/platform/alerts-overview.md) na pomoc při detekci a diagnostice.
* Seznamte se s funkcemi [prohledávání protokolů a dotazování](../azure-monitor/log-query/log-query-overview.md) , které nabízí jako součást protokolů Azure monitor.
* Podrobnější přehled Azure Monitor protokolů a co nabízí, najdete v článku [co je Azure monitor protokolů?](../azure-monitor/overview.md).
