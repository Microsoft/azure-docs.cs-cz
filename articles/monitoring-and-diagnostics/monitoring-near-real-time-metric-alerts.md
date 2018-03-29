---
title: Novější metriky výstrah v monitorování Azure podporované prostředky | Microsoft Docs
description: Odkaz na podporu metriky a protokoly pro novější Azure téměř v reálném čase metriky výstrahy.
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
ms.date: 03/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 6ccb095f3739a90bdab2408965a742f9cbc19359
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="newer-metric-alerts-for-azure-services-in-the-azure-portal"></a>Novější metriky výstrahy pro služby Azure na portálu Azure
Azure monitorování teď podporuje nový typ metriky výstrahy. Novější výstrahy se liší od [classic metriky výstrahy](insights-alerts-portal.md) několika způsoby:

- **Vylepšené latence**: novější metriky výstrahy můžete spustit často každou minutu. Starší metriky výstrahy vždy spustit v intervalu 5 minut. Výstrahy protokolu ještě delší než 1 minuta zpoždění kvůli době je potřeba k ingestování protokoly. 
- **Podpora pro multidimenzionální metriky**: můžete výstrahy na dimenzí metriky, které umožňuje sledovat jenom segment zajímavé metriky. 
- **Větší kontrolu nad metriky podmínky**: můžete definovat bohatší pravidla výstrah. Výstrahy novější podporují monitorování maximální, minimální, průměrná a celkové hodnoty metrik. 
- **Kombinaci monitorování více metrik**: můžete sledovat více metriky (v současné době až dvě metriky) s jedním pravidlem. Výstraha se spustí, pokud obě metriky narušit jejich příslušné prahové hodnoty pro zadané časové období. 
- **Lepší oznámení systému**: použijte všechny novější výstrahy [skupiny akcí](monitoring-action-groups.md), které jsou s názvem skupiny oznámení a akce, které lze znovu použít ve více výstrah. Classic metriky výstrahy a starší výstrahy analýzy protokolů nepoužívejte skupiny akcí. 
- **Metriky z protokolů** (verze public preview omezené): protokolu dat přejdete do analýzy protokolů můžete nyní extrahována a převést do Azure monitorování metriky a potom zobrazí upozornění na stejně jako jiné metriky. 

Naučte se vytvářet upozornění na novější metriky na portálu Azure, najdete v tématu [vytvořit pravidlo výstrahy na portálu Azure](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal). Po vytvoření, můžete spravovat výstrahy pomocí kroků popsaných v [oznámení na portálu Azure spravovat](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).


## <a name="portal-powershell-cli-rest-support"></a>Portálu, prostředí PowerShell, rozhraní příkazového řádku, REST podporu
V současné době můžete vytvořit novější metriky výstrahy pouze v portálu Azure nebo REST API. Podpora konfigurace novější výstrah pomocí prostředí PowerShell a rozhraní příkazového řádku Azure (Azure CLI 2.0) je již brzy.

## <a name="metrics-and-dimensions-supported"></a>Metriky a dimenze podporována
Novější metriky výstrahy podporují generování výstrah pro metriky, které používají dimenze. Dimenze můžete použít k filtrování vaší metrika se používá k správnou úroveň. Všechny podporované metriky společně s příslušné dimenze můžete prozkoumali a vizualizována z [monitorování Azure – metriky Explorer (Preview)](monitoring-metric-charts.md).

Tady je úplný seznam Azure monitorování metriky zdroje nepodporuje novější výstrahy:

|Typ prostředku  |Dimenze podporována  | Metriky, které jsou k dispozici|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Ano        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Ano   | [Účty Automation](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Nevztahuje se.| [Účty batch](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    Nevztahuje se.     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    Nevztahuje se.     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   Nevztahuje se.      |[Sady škálování virtuálního počítače](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.DataFactory/factories     |   Ano     |[Data Factory V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   Nevztahuje se.      |[Databáze pro databázi MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    Nevztahuje se.     | [DB pro PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Ano      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.Logic/workflows     |     Nevztahuje se.    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    Nevztahuje se.     | [Application Gateway](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/publicipaddresses     |  Nevztahuje se.       |[Veřejná IP adresa Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Search/searchServices     |   Nevztahuje se.      |[Služby vyhledávání](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Ano       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts.     |    Ano     | [Účty úložiště](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Ano    | [Objekt BLOB služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [pro služby souborového](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [fronty služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) a [tabulky služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  Nevztahuje se.       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    Nevztahuje se.     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (Preview) | Ano|[Pracovní prostory analýzy protokolů](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>Přihlaste se jako metriky pro výstrahy analýzy protokolů 

Můžete taky novější metriky výstrahy na oblíbených protokoly analýzy protokolů extrahovat jako metriky jako součást metriky z verze Preview protokoly.  
- [Čítače výkonu](../log-analytics/log-analytics-data-sources-performance-counters.md) pro počítače s Windows a Linux
- [Záznamy prezenčního signálu pro stav agenta](../operations-management-suite/oms-solution-agenthealth.md)
- [Správa aktualizací](../operations-management-suite/oms-solution-update-management.md) záznamů
 
> [!NOTE]
> Konkrétní metrika nebo dimenze se zobrazí pouze pokud data pro něj existuje ve zvolené období. Tyto metriky jsou dostupné pro zákazníky s nimi ve východní USA, západní centrální USA a západní Evropa, kteří zvolili možnosti ve verzi preview. Pokud chcete být součástí této verze preview, zaregistrujte se pomocí [zjišťování](https://aka.ms/MetricLogPreview).

Následující seznam zdrojů metriky na základě protokolu analýzy protokolů je podporována:

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
|    Aktualizovat |     Ano – počítače, produktů, klasifikace, UpdateState, volitelné & schválené    |   Správa aktualizací |



## <a name="payload-schema"></a>Datová část schématu

Operaci POST obsahuje následující datové části JSON a schématu pro všechny téměř novější metriky výstrahy, když se správně nakonfigurované [skupiny akce](monitoring-action-groups.md) se používá:

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
