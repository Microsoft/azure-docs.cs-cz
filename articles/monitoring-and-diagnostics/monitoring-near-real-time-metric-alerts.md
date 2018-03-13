---
title: "Téměř v reálném čase metriky výstrah v monitorování Azure | Microsoft Docs"
description: "Další informace o použití téměř v reálném čase metriky výstrahy monitorovat metriky prostředků Azure s frekvencí co 1 minuta."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: 
ms.openlocfilehash: 88995b1f3350fe485e28efccc93779ae0a42eb97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Téměř v reálném čase metriky výstrahy (preview)
Azure monitorování podporuje nové výstrahy typu s názvem téměř v reálném čase metriky výstrahy (preview). Tato funkce je aktuálně ve verzi public preview.

Téměř v reálném čase metrika výstrahy se liší od regulární metriky výstrahy několika způsoby:

- **Vylepšené latence**: téměř v reálném čase metrika výstrah můžete sledovat změny v metriky hodnoty s frekvencí malá jako jednu minutu.
- **Větší kontrolu nad metriky podmínky**: můžete definovat bohatší pravidla výstrah v téměř v reálném čase metriky výstrahy. Výstrahy podporovat monitorování maximální, minimální, průměrná a celkové hodnoty metrik.
- **Metriky z protokolů**: Z oblíbených protokolu dat pocházejících do [analýzy protokolů](../log-analytics/log-analytics-overview.md), metriky lze extrahovat do Azure monitorování a upozorněni na téměř v reálném čase
- **Kombinaci monitorování více metrik**: téměř v reálném čase metrika výstrah můžete sledovat více metriky (v současné době až dvě metriky) s jedním pravidlem. Výstraha se spustí, pokud obě metriky narušit jejich příslušné prahové hodnoty pro zadané časové období.
- **Modulární oznámení systému**: téměř v reálném čase metrika výstrahy použít [skupiny akcí](monitoring-action-groups.md). Akce skupiny slouží k vytvoření modulární akce. Akce skupiny pro více pravidla výstrah můžete znovu použít.

> [!NOTE]
> Near v reálném čase metriky výstraha je aktuálně je ve verzi public preview. A metriky využívá funkce, které protokoly jsou v *omezené* verzi public preview. Funkce a uživatelské prostředí je mohou podléhat změnám.
>

## <a name="metrics-and-dimensions-supported"></a>Metriky a dimenze podporována
Téměř v reálném čase metriky výstrahy podporují generování výstrah pro metriky, které používají dimenze. Dimenze můžete použít k filtrování vaší metrika se používá k správnou úroveň. Všechny podporované metriky společně s příslušné dimenze můžete prozkoumali a vizualizována z [monitorování Azure – metriky Explorer (Preview)](monitoring-metric-charts.md).

Tady je úplný seznam Azure monitorování na základě metriky zdroje, které jsou podporovány pro téměř v reálném čase metriky výstrahy:

|Název metriky/podrobnosti  |Dimenze podporována  |
|---------|---------|
|Microsoft.ApiManagement/service     | Ano        |
|Microsoft.Automation/automationAccounts     |     neuvedeno    |
|Microsoft.Automation/automationAccounts     |   neuvedeno      |
|Microsoft.Cache/Redis     |    neuvedeno     |
|Microsoft.Compute/virtualMachines     |    neuvedeno     |
|Microsoft.Compute/virtualMachineScaleSets     |   neuvedeno      |
|Microsoft.DataFactory/factories     |   neuvedeno      |
|Microsoft.DBforMySQL/servers     |   neuvedeno      |
|Microsoft.DBforPostgreSQL/servers     |    neuvedeno     |
|Microsoft.EventHub/namespaces     |   neuvedeno      |
|Microsoft.Logic/workflows     |     neuvedeno    |
|Microsoft.Network/applicationGateways     |    neuvedeno     |
|Microsoft.Network/publicipaddresses     |  neuvedeno       |
|Microsoft.Search/searchServices     |   neuvedeno      |
|Microsoft.ServiceBus/namespaces     |  neuvedeno       |
|Microsoft.Storage/storageAccounts.     |    Ano     |
|Microsoft.Storage/storageAccounts/services     |     Ano    |
|Microsoft.StreamAnalytics/streamingjobs     |  neuvedeno       |
|Microsoft.CognitiveServices/accounts     |    neuvedeno     |


Metriky z protokolů, v současné době podporuje následující protokoly OMS oblíbených:
- [Čítače výkonu](../log-analytics/log-analytics-data-sources-performance-counters.md) pro počítače s Windows a Linux
- Záznamy prezenčního signálu pro počítače
- [Správa aktualizací](../operations-management-suite/oms-solution-update-management.md) záznamů

Tady je úplný seznam na základě protokolu OMS metriky se zdroje, které jsou podporovány pro téměř v reálném čase metriky výstrahy:

Název metriky/podrobnosti  |Dimenze podporována  | Typ protokolu  |
|---------|---------|---------|
|Average_Avg. Doba disku/čtení     |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
| Average_Avg. Doba disku/zápis     |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
| Délka fronty disku Average_Current   |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
| Average_Disk čtení/s    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
| Average_Disk přenosy/s    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
|   Average_ % volného místa    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
| Average_Available MB     |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
| Average_ % potvrzených bajtů v použití    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
| Average_Bytes Received/sec    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
|  Average_Bytes odeslaných za sekundu    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
|  Average_Bytes celkem/s    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
|  Average_ % času procesoru    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
|   Délka fronty Average_Processor    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítačů výkonu systému Windows      |
|   Average_ % volných uzlů Inode   |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_ % volného místa   |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_ % použitých uzlů Inode  |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Využitý prostor v Average_ %   |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Disk přečtených bajtů/s    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Disk čtení/s |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Disk přenosy/s |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Zápis Average_Disk bajty/s   |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Disk zápisů za sekundu    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Free MB |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Logical bajtů disku/s |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Dostupná paměť v % Average_ |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_ % dostupného odkládacího prostoru |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_ % použité paměti  |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Využitý prostor záměny v Average_ %  |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Paměť v megabajtech Average_Available    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Available prostor záměny v MB  |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Page čtení/s |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Page zápisů za sekundu    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Pages za sekundu  |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Used prostor záměny v MB |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Used paměť v MB |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Total bajtů přenesených    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Počet přijatých bajtů Average_Total   |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Total bajtů    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Total pakety odesílané informace  |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Obdržených paketů Average_Total |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Chyby příjmu Average_Total    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Chyby odeslání Average_Total    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Total kolizí   |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Avg. Doba disku/čtení |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Avg. Doba disku/přenos |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Avg. Doba disku/zápis    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Physical bajtů disku/s    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Pct privilegovaného času    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Pct uživatelského času  |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Paměť Average_Used kB |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Virtual sdílené paměti  |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    % Času DPC Average_ |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_ % času nečinnosti    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    % Času přerušení Average_   |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Doba čekání vstupů % Average_ |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_ dobrý čas v %    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_ % privilegovaného času  |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_ % času procesoru   |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_ % uživatelského času    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Free fyzické paměti   |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Free místo ve stránkovacích souborech |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Free virtuální paměti    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Processes  |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Size uložená v stránkovacích souborech    |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Uptime |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Average_Users  |     Ano – počítače, ObjectName, InstanceName, Cesta_k_čítači a SourceSystem    |   Čítače výkonu systému Linux      |
|    Prezenční signál  |     Ano – počítače, OSType, verzi a SourceComputerId    |   Záznamy prezenčního signálu |
|    Aktualizace |     Ano – počítače, produktů, klasifikace, UpdateState, volitelné & schválené    |   Update Management |

> [!NOTE]
> Konkrétní metrika nebo dimenze se zobrazí pouze pokud data pro něj existuje ve zvolené období

## <a name="create-a-near-real-time-metric-alert"></a>Vytvořit výstrahu metriky near v reálném čase
V současné době můžete vytvořit téměř v reálném čase metriky výstrahy pouze na portálu Azure. Podpora konfigurace téměř v reálném čase metriky výstrah pomocí prostředí PowerShell, rozhraní příkazového řádku Azure (Azure CLI) a rozhraní API REST Azure monitorování tu bude brzo dostupná.

Prostředí pro vytváření near v reálném čase metriky výstraha se přesunul do nového **výstrahy (Preview)** stránky. I v případě, že stránka zobrazuje aktuální výstrahy **přidat téměř v reálném čase metrika výstraha**, budete přesměrováni na **výstrahy (Preview)** stránky.

Další postup vytvoření metriky upozornění na near v reálném čase najdete v tématu [vytvořit pravidlo výstrahy na portálu Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Spravovat téměř v reálném čase metriky výstrahy
Po vytvoření metriky upozornění na near v reálném čase, výstrahy můžete spravovat pomocí kroků popsaných v [oznámení na portálu Azure spravovat](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Datová část schématu

Operaci POST obsahuje následující datové části JSON a schématu pro všechny téměř v reálném čase metriky výstrahy, když správně nakonfigurovaný [skupiny akce](monitoring-action-groups.md) se používá:

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>Další postup

* Další informace o nové [výstrahy (Preview) prostředí](monitoring-overview-unified-alerts.md).
* Další informace o [protokolu výstrahy ve výstrahách Azure (Preview)](monitor-alerts-unified-log.md).
* Další informace o [výstrahy v Azure](monitoring-overview-alerts.md).
