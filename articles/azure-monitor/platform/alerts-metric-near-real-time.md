---
title: Podporované zdroje pro upozornění metrik ve službě Azure Monitor
description: Odkaz na podporu metriky a protokoly pro upozornění metrik ve službě Azure Monitor
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: be9f8079480438b194f0d5779ac0da50328b5dd3
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53585247"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Podporované zdroje pro upozornění metrik ve službě Azure Monitor

Azure teď podporuje sledování [novou výstrahu typu metrika](../../azure-monitor/platform/alerts-overview.md) která má významné výhody přes starší [klasického upozornění metrik](../../azure-monitor/platform/alerts-classic.overview.md). Metriky jsou k dispozici pro [velké seznam služeb Azure](../../azure-monitor/platform/metrics-supported.md). Novější upozornění podporují podmnožinu typů prostředků (rostoucí). Tento článek uvádí tuto podmnožinu.


Můžete také novějších upozornění metrik na oblíbené protokoly analýzy protokolu extrahovat jako metriky. Další informace získáte [upozornění metriky pro protokoly](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portálu, Powershellu, rozhraní příkazového řádku, REST podpory
V současné době můžete pouze na portálu Azure vytvořit novějších upozornění metrik [rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/), nebo [šablon Resource Manageru](../../azure-monitor/platform/alerts-metric-create-templates.md). Podpora konfigurace novější výstrah pomocí Powershellu a Azure CLI verze 2.0 a vyšší je již brzy.

## <a name="metrics-and-dimensions-supported"></a>Metriky a dimenze podporována
Novějších upozornění metrik podporují výstrahy pro metriky, které dimenze použít. Dimenze můžete použít k filtrování vaší metrika správnou úroveň. Všechny podporované metriky spolu s příslušné dimenze můžete prozkoumat a vizualizovat z [Azure Monitor – Průzkumník metrik](../../azure-monitor/platform/metrics-charts.md).

Tady je úplný seznam podporovaných novější upozornění metriky zdrojů Azure monitor:

|Typ prostředku  |Dimenze podporována  | Metriky, které jsou k dispozici|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Ano        | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Ano   | [Účty služby Automation](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | neuvedeno| [Účty batch](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    neuvedeno     |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    neuvedeno     | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    neuvedeno     | [Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   neuvedeno      |[Škálovací sady virtuálních počítačů](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ano| [Skupiny kontejnerů](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Ano | [Spravované clustery](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Ano| [V1 objekty pro vytváření dat](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Ano     |[Datové továrny V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   neuvedeno      |[DB for MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    neuvedeno     | [DB for PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Ano      |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Ne | [trezory služby](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     neuvedeno    |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    neuvedeno     | [Brány Application Gateway](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | neuvedeno |  [Express Route okruhy](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | neuvedeno| [Zóny DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (pouze pro standardní skladové položky)| Ano| [Nástroje pro vyrovnávání zatížení](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  neuvedeno       |[Veřejné IP adresy](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | neuvedeno | [Kapacity](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | Ano | [Profily Traffic Manageru](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   neuvedeno      |[Vyhledávací služby](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Ano       |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts.     |    Ano     | [Účty úložiště](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Ano    | [Objekt BLOB služby](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [souborové služby](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [fronty služby](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) a [tabulky služeb](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  neuvedeno       | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Ano | [Plány služby App Service](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Ano | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) a [funkce](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Ano | [Sloty služby App Service](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|
|Microsoft.OperationalInsights/workspaces| Ano|[Pracovní prostory log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|



## <a name="payload-schema"></a>Datová část schématu

Operace POST obsahuje následující datovou část JSON a schéma pro všechny v novějších upozornění metrik, když je správně nakonfigurované [skupiny akcí](../../azure-monitor/platform/action-groups.md) se používá:

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

* Další informace o novém [výstrahy prostředí](../../azure-monitor/platform/alerts-overview.md).
* Další informace o [upozornění protokolů ve službě Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Další informace o [výstrah ve službě Azure](../../azure-monitor/platform/alerts-overview.md).
