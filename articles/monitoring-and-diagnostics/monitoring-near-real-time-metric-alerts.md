---
title: Podporované zdroje informací pro novější upozornění metrik Azure monitoru
description: Odkaz na podporu metriky a protokoly pro novější Azure téměř v reálném čase upozornění metrik.
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 01c0b5897ab47a2a5091646aed1977779cf0234c
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868016"
---
# <a name="supported-metrics-and-creation-methods-for-new-metric-alerts"></a>Podporované metody metrik a vytváření nových metrik výstrah
Azure teď podporuje sledování [novou výstrahu typu metrika](monitoring-overview-unified-alerts.md) která má významné výhody přes starší [klasického upozornění metrik](insights-alerts-portal.md). Podporují starší výstrahy [velké seznam metrik](monitoring-supported-metrics.md). Novější upozornění podporují podmnožinu větší seznamu (rostoucí). Tento článek uvádí tuto podmnožinu. 

## <a name="portal-powershell-cli-rest-support"></a>Portálu, Powershellu, rozhraní příkazového řádku, REST podpory
V současné době můžete pouze na portálu Azure vytvořit novějších upozornění metrik [rozhraní REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate) nebo [šablon Resource Manageru](monitoring-create-metric-alerts-with-templates.md). Podpora konfigurace novější upozornění pomocí prostředí PowerShell a rozhraní příkazového řádku Azure (Azure CLI 2.0) už brzy bude.

## <a name="metrics-and-dimensions-supported"></a>Metriky a dimenze podporována
Novějších upozornění metrik podporují výstrahy pro metriky, které dimenze použít. Dimenze můžete použít k filtrování vaší metrika správnou úroveň. Všechny podporované metriky spolu s příslušné dimenze můžete prozkoumat a vizualizovat z [Azure Monitor – Průzkumník metrik (Preview)](monitoring-metric-charts.md).

Tady je úplný seznam podporovaných novější upozornění metriky zdrojů Azure monitor:

|Typ prostředku  |Dimenze podporována  | Metriky, které jsou k dispozici|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Ano        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Ano   | [Účty služby Automation](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | neuvedeno| [Účty batch](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    neuvedeno     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    neuvedeno     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   neuvedeno      |[Škálovací sady virtuálních počítačů](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ano| [Skupiny kontejnerů](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.DataFactory/datafactories| Ano| [V1 objekty pro vytváření dat](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Ano     |[Datové továrny V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   neuvedeno      |[DB for MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    neuvedeno     | [DB for PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Ano      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Ne | [Trezory služby](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     neuvedeno    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    neuvedeno     | [Application Gateway](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | neuvedeno| [Zóny DNS](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (pouze pro standardní skladové položky)| Ano| [Nástroje pro vyrovnávání zatížení](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  neuvedeno       |[Veřejná IP adresa Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | neuvedeno | [Kapacity](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   neuvedeno      |[Vyhledávací služby](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Ano       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Ano     | [Účty úložiště](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Ano    | [Objekt BLOB služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [souborové služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [fronty služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) a [tabulky služeb](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  neuvedeno       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    neuvedeno     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (Preview) | Ano|[Pracovní prostory log Analytics](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>Log Analytics zaznamená jako metriky pro generování výstrah 

Můžete také novějších upozornění metrik na oblíbené protokoly analýzy protokolu extrahovat jako metriky jako součást metriky z protokolů ve verzi Preview.  
- [Čítače výkonu](../log-analytics/log-analytics-data-sources-performance-counters.md) u počítačů s Windows a Linuxu
- [Záznamy prezenčního signálu služby Agent Health](../operations-management-suite/oms-solution-agenthealth.md)
- [Správa aktualizací](../operations-management-suite/oms-solution-update-management.md) záznamů
 
> [!NOTE]
> Určité metriky a/nebo dimenze pouze zobrazí-li data pro něj existuje za vybrané období. Tyto metriky jsou k dispozici pro zákazníky s pracovními prostory v oblastech východní USA, západní USA, střed USA a západní Evropa, kteří se přihlásili k verzi preview. Pokud chcete být součástí této verze preview, zaregistrujte pomocí [zjišťování](https://aka.ms/MetricLogPreview).

Následující seznam zdrojů metriky založené na protokolu Log Analytics se podporuje:

Metriky názvem/podrobnostmi  |Dimenze podporována  | Typ protokolu  |
|---------|---------|---------|
|Average_Avg. Doba disku/čtení     |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
| Average_Avg. Doby disku/zápis     |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
| Délka fronty disku Average_Current   |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
| Average_Disk přečtené strany/s    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
| Average_Disk přenosy/s    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
|   Average_ % volného místa    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
| Average_Available paměť v megabajtech     |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
| Average_ % potvrzených bajtů    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
| Přijaté Average_Bytes/s    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
|  Odeslané Average_Bytes/s    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
|  Average_Bytes celkem/s    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
|  Average_ % času procesoru    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
|   Délka fronty Average_Processor    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Windows      |
|   Average_ % volných uzlů Inode   |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_ % volného místa   |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_ % použitých uzlů Inode  |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_ % využitého místa   |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Disk přečtené bajty/s    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Disk přečtené strany/s |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Disk přenosy/s |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Disk zapsané bajty/s   |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Disk zapsané strany/s    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Free v megabajtech |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Logical bajtů disku/s |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Dostupná paměť v % Average_ |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_ % dostupného odkládacího prostoru |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_ % využité paměti  |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Využitý prostor záměny v Average_ %  |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Available paměť v MB    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Available prostor záměny v MB  |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Page přečtené strany/s |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Page zapsané strany/s    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Pages za sekundu  |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Used prostor záměny v MB |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Used paměť v MB |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Odeslané bajty Average_Total    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Přijaté bajty Average_Total   |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Total bajtů    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Total pakety odesílané informace  |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Total obdržených paketů |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Chyby příjmu Average_Total    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Chyby odeslání Average_Total    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Total kolizí   |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Avg. Doba disku/čtení |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Avg. Doba disku/přenos |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Avg. Doby disku/zápis    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Physical bajtů disku/s    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Pct privilegovaného času    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Pct uživatelského času  |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    KB paměti Average_Used |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Virtual sdílené paměti  |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    % Času DPC Average_ |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_ čas nečinnosti v %    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    % Času přerušení Average_   |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Doba čekání na vstupně-výstupních operací Average_ % |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_ dobrý čas v %    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_ % privilegovaného času  |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_ % času procesoru   |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_ % uživatelského času    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Free fyzické paměti   |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Free místo ve stránkovacích souborech |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Free virtuální paměti    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Processes  |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Size uložená v stránkovacích souborech    |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Uptime |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Average_Users  |     Ano – počítače, název_objektu, InstanceName, Cesta_k_čítači & SourceSystem    |   Čítače výkonu Linuxu      |
|    Prezenční signál  |     Ano – počítače, OSType, verzi a SourceComputerId    |   Záznamy prezenčního signálu |
|    Aktualizace |     Ano – počítače, produktů, klasifikace, UpdateState volitelné & schválené    |   Update Management |



## <a name="payload-schema"></a>Datová část schématu

Operace POST obsahuje následující datovou část JSON a schéma pro všechny v novějších upozornění metrik, když je správně nakonfigurované [skupiny akcí](monitoring-action-groups.md) se používá:

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

* Další informace o novém [výstrahy prostředí](monitoring-overview-unified-alerts.md).
* Další informace o [upozornění protokolů ve službě Azure](monitor-alerts-unified-log.md).
* Další informace o [výstrah ve službě Azure](monitoring-overview-alerts.md).
