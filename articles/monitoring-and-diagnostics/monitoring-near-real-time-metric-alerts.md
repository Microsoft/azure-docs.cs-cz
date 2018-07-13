---
title: Podporované zdroje informací pro novější upozornění metrik Azure monitoru
description: Odkaz na podporu metriky a protokoly pro novější Azure téměř v reálném čase upozornění metrik.
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 4d51b099532d3052acc190231ec4be17765a427e
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971019"
---
# <a name="introduction"></a>Úvod
Azure teď podporuje sledování [novou výstrahu typu metrika](monitoring-overview-unified-alerts.md) která má významné výhody přes starší [klasického upozornění metrik](insights-alerts-portal.md). Metriky jsou k dispozici pro [velké seznam služeb Azure](monitoring-supported-metrics.md). Novější upozornění podporují podmnožinu typů prostředků (rostoucí). Tento článek uvádí tuto podmnožinu. 

Novějších upozornění metrik můžete použít také u oblíbených protokoly analýzy protokolu extrahovat jako metriky jako součást metriky z protokolů (Preview)  
- [Čítače výkonu](../log-analytics/log-analytics-data-sources-performance-counters.md) u počítačů s Windows a Linuxu
- [Záznamy prezenčního signálu služby Agent Health](../operations-management-suite/oms-solution-agenthealth.md)
- [Správa aktualizací](../operations-management-suite/oms-solution-update-management.md) záznamů
- [Data události](../log-analytics/log-analytics-data-sources-windows-events.md) protokoly
 
> [!NOTE]
> Určité metriky a/nebo dimenze pouze zobrazí-li data pro něj existuje za vybrané období. Tyto metriky jsou k dispozici pro zákazníky s pracovních prostorů Azure Log Analytics v oblastech východní USA, západní USA, střed USA a západní Evropa. Metriky z Log Analytics je aktuálně ve verzi public preview a může se měnit.

## <a name="portal-powershell-cli-rest-support"></a>Portálu, Powershellu, rozhraní příkazového řádku, REST podpory
V současné době můžete pouze na portálu Azure vytvořit novějších upozornění metrik [rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate) nebo [šablon Resource Manageru](monitoring-create-metric-alerts-with-templates.md). Podpora konfigurace novější upozornění pomocí prostředí PowerShell a rozhraní příkazového řádku Azure (Azure CLI 2.0) už brzy bude.

## <a name="metrics-and-dimensions-supported"></a>Metriky a dimenze podporována
Novějších upozornění metrik podporují výstrahy pro metriky, které dimenze použít. Dimenze můžete použít k filtrování vaší metrika správnou úroveň. Všechny podporované metriky spolu s příslušné dimenze můžete prozkoumat a vizualizovat z [Azure Monitor – Průzkumník metrik (Preview)](monitoring-metric-charts.md).

Tady je úplný seznam podporovaných novější upozornění metriky zdrojů Azure monitor:

|Typ prostředku  |Dimenze podporována  | Metriky, které jsou k dispozici|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Ano        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Ano   | [Účty služby Automation](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | neuvedeno| [Účty batch](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    neuvedeno     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    neuvedeno     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    neuvedeno     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   neuvedeno      |[Škálovací sady virtuálních počítačů](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ano| [Skupiny kontejnerů](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Ano | [Spravované clustery](monitoring-supported-metrics.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Ano| [V1 objekty pro vytváření dat](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Ano     |[Datové továrny V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   neuvedeno      |[DB for MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    neuvedeno     | [DB for PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Ano      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Ne | [Trezory služby](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     neuvedeno    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    neuvedeno     | [Application Gateway](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | neuvedeno |  [Express Route okruhy](monitoring-supported-metrics.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | neuvedeno| [Zóny DNS](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (pouze pro standardní skladové položky)| Ano| [Nástroje pro vyrovnávání zatížení](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  neuvedeno       |[Veřejná IP adresa Addreses](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | neuvedeno | [Kapacity](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | Ano | [Profily Traffic Manageru](monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   neuvedeno      |[Vyhledávací služby](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Ano       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Ano     | [Účty úložiště](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Ano    | [Objekt BLOB služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [souborové služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [fronty služby](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) a [tabulky služeb](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  neuvedeno       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Ano | [Plány služby App Service](monitoring-supported-metrics.md#microsoftwebserverfarms)  |
|Microsoft.OperationalInsights/workspaces (Preview) | Ano|[Pracovní prostory log Analytics](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces)|



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
