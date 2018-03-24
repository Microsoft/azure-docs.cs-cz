---
title: Téměř v reálném čase metriky výstrah v monitorování Azure | Microsoft Docs
description: Další informace o použití téměř v reálném čase metriky výstrahy monitorovat metriky prostředků Azure s frekvencí co 1 minuta.
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 15b9b0b69f3805b3e3af1d3973fd3a77bea62ab9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="use-the-newer-metric-alerts-for-azure-services-in-azure-portal"></a>Použít novější metriky výstrahy pro služby Azure na portálu Azure
Azure monitorování podporuje nové výstrahy typu s názvem téměř v reálném čase metriky výstrahy. 

Téměř v reálném čase metrika výstrahy se liší od [classic metriky výstrahy](insights-alerts-portal.md) několika způsoby:

- **Vylepšené latence**: téměř v reálném čase metrika výstrahy můžete spustit často každou minutu. Starší metriky výstrahy vždy spustit v intervalu 5 minut.
- **Podpora pro multidimenzionální metriky**: můžete výstrahy na dimenzí metriky, které umožňuje sledovat segment zajímavé metriky.
- **Větší kontrolu nad metriky podmínky**: můžete definovat bohatší pravidla výstrah v téměř v reálném čase metriky výstrahy. Výstrahy podporovat monitorování maximální, minimální, průměrná a celkové hodnoty metrik.
- **Kombinaci monitorování více metrik**: téměř v reálném čase metrika výstrah můžete sledovat více metriky (v současné době až dvě metriky) s jedním pravidlem. Výstraha se spustí, pokud obě metriky narušit jejich příslušné prahové hodnoty pro zadané časové období.
- **Modulární oznámení systému**: téměř v reálném čase metrika výstrahy použít [skupiny akcí](monitoring-action-groups.md). Akce skupiny slouží k vytvoření modulární akce. Akce skupiny pro více pravidla výstrah můžete znovu použít.
- **Metriky z protokolů**: Z oblíbených protokolu dat pocházejících do [analýzy protokolů](../log-analytics/log-analytics-overview.md), metriky lze extrahovat do Azure monitorování a upozorněni na téměř v reálném čase.


## <a name="metrics-and-dimensions-supported"></a>Metriky a dimenze podporována
Téměř v reálném čase metriky výstrahy podporují generování výstrah pro metriky, které používají dimenze. Dimenze můžete použít k filtrování vaší metrika se používá k správnou úroveň. Všechny podporované metriky společně s příslušné dimenze můžete prozkoumali a vizualizována z [monitorování Azure – metriky Explorer (Preview)](monitoring-metric-charts.md).

Tady je úplný seznam Azure monitorování na základě metriky zdroje, které jsou podporovány pro téměř v reálném čase metriky výstrahy:

|Typ prostředku  |Dimenze podporována  | Metriky, které jsou k dispozici|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Ano        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Ano   | [Účty Automation](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | neuvedeno| [Účty batch](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    neuvedeno     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    neuvedeno     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   neuvedeno      |[Sady škálování virtuálního počítače](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.DataFactory/factories     |   Ano     |[Data Factory V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   neuvedeno      |[Databáze pro databázi MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    neuvedeno     | [DB pro PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Ano      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.Logic/workflows     |     neuvedeno    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    neuvedeno     | [Application Gateway](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/publicipaddresses     |  neuvedeno       |[Veřejná IP adresa Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Search/searchServices     |   neuvedeno      |[Služby vyhledávání](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Ano       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts.     |    Ano     | [Účty úložiště](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Ano    | [Objekt BLOB služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [pro služby souborového](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [fronty služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) a [tabulky služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  neuvedeno       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    neuvedeno     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (Preview) | Ano|[Pracovní prostory analýzy protokolů](#support-for-oms-logs-as-metrics-for-alerting)|


## <a name="create-a-newer-metric-alert"></a>Vytvořit výstrahu pro novější metriky
V současné době můžete vytvořit novější metriky výstrahy pouze v portálu Azure nebo REST API. Podpora pro konfiguraci téměř v reálném čase metriky výstrah pomocí prostředí PowerShell, rozhraní příkazového řádku Azure (Azure CLI) bude brzo.

Naučte se vytvářet upozornění na novější metriky na portálu Azure, najdete v tématu [vytvořit pravidlo výstrahy na portálu Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-newer-metric-alerts"></a>Správa novější metriky výstrah
Po vytvoření metriky upozornění na near v reálném čase, výstrahy můžete spravovat pomocí kroků popsaných v [oznámení na portálu Azure spravovat](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="support-for-oms-logs-as-metrics-for-alerting"></a>Podpora pro protokoly OMS jako metriky pro výstrahy

Můžete také použít téměř v reálném čase metriky výstrahy na oblíbených protokoly OMS extrahovat jako metriky jako součást metriky z verze Preview protokoly.  
- [Čítače výkonu](../log-analytics/log-analytics-data-sources-performance-counters.md) pro počítače s Windows a Linux
- [Záznamy prezenčního signálu pro stav agenta](../operations-management-suite/oms-solution-agenthealth.md)
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
> Konkrétní metrika nebo dimenze se zobrazí pouze pokud data pro něj existuje ve zvolené období. Tyto metriky jsou dostupné pro zákazníky s nimi ve východní USA, západní centrální USA a západní Evropa, kteří zvolili možnosti ve verzi preview. Pokud chcete být součástí této verze preview, zaregistrujte se pomocí [zjišťování](https://aka.ms/MetricLogPreview).


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

* Další informace o nové [výstrahy prostředí](monitoring-overview-unified-alerts.md).
* Další informace o [protokolu výstrahy v Azure](monitor-alerts-unified-log.md).
* Další informace o [výstrahy v Azure](monitoring-overview-alerts.md).
