---
title: Podporované prostředky pro výstrahy metriky v Azure Monitor
description: Referenční informace o metrikách a protokolech podpory pro výstrahy metrik v Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 12/17/2019
ms.subservice: alerts
ms.openlocfilehash: 9585e74170d21ad899224462b6e41e7e9bb75727
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664810"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Podporované prostředky pro výstrahy metriky v Azure Monitor

Azure Monitor teď podporuje [nový typ výstrahy metriky](../../azure-monitor/platform/alerts-overview.md) , který má významné výhody oproti starším [klasickým výstrahám metrik](../../azure-monitor/platform/alerts-classic.overview.md). K dispozici jsou metriky pro [velký seznam služeb Azure](../../azure-monitor/platform/metrics-supported.md). Novější výstrahy podporují (rostoucí) podmnožinu typů prostředků. Tento článek obsahuje seznam této podmnožiny.

Můžete také použít novější výstrahy metriky pro oblíbená data protokolu uložená v Log Analytics pracovním prostoru extrahované jako metriky. Další informace najdete v [upozorněních metriky pro protokoly](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, podpora REST
V současné době můžete vytvořit novější výstrahy metrik pouze v [šablonách](../../azure-monitor/platform/alerts-metric-create-templates.md)Azure Portal, [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)nebo správce prostředků. Brzy se připravuje podpora pro konfiguraci novějších výstrah pomocí PowerShellu a Azure CLI verze 2,0 a novější.

## <a name="metrics-and-dimensions-supported"></a>Podporované metriky a dimenze
Novější výstrahy metrik podporují upozorňování na metriky, které používají dimenze. Dimenze můžete použít k filtrování metriky na správnou úroveň. Všechny podporované metriky spolu s použitelnými rozměry lze prozkoumat a rozlišit z [Azure monitor-Průzkumník metrik](../../azure-monitor/platform/metrics-charts.md).

Tady je úplný seznam zdrojů metrik Azure monitorů podporovaných novějšími výstrahami:

|Typ prostředku  |Podporované dimenze |Výstrahy s více prostředky| Dostupné metriky|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Ano| Ne | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft. AppPlatform/pružina |Ne| Ano|
|Microsoft. Automation/automationAccounts | Ano| Ne | [Účty služby Automation](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | NEUŽÍVÁ SE.| Ne | [Účty Batch](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Ano| Ne |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic|Ne|Ano|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/blobServices|Ne|Ano|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/služby|Ne|Ano|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/queueServices|Ne|Ano|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/tableServices|Ne|Ano| |
|Microsoft.CognitiveServices/accounts| NEUŽÍVÁ SE. | Ne | [Kognitivní služby](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Ano | Ano | [Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |NEUŽÍVÁ SE. | Ano |[Škálovací sady virtuálních počítačů](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ano| Ne | [Skupiny kontejnerů](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Ano | Ne | [Spravované clustery](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Ano | Ano | |
|Microsoft. DataFactory/DataFactory| Ano| Ne | [Datové továrny v1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft. DataFactory/továrny |Ano | Ne |[Datové továrny v2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft. datashare/Accounts |Ne| Ano|
|Microsoft.DBforMySQL/servers |NEUŽÍVÁ SE.| Ne |[DB pro MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |NEUŽÍVÁ SE. | Ne | [DB pro PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | NEUŽÍVÁ SE. | Ne |[IoT Hub metriky](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Ano | Ne |[Metrika DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft. EventGrid/domény|Ne|Ano| |
|Microsoft.EventGrid/topics |Ano | Ne |[Event Grid témata](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft. EventHub/clustery |Ano| Ne |[Clustery Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Ano| Ne |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Ne |Ne |[Trezory](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |NEUŽÍVÁ SE. | Ne |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Ano| Ne | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Network/applicationGateways|NEUŽÍVÁ SE.| Ne |  |
|Microsoft.Network/dnsZones | NEUŽÍVÁ SE.| Ne | [Zóny DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | NEUŽÍVÁ SE. | Ne |[Okruhy Express Route](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/loadBalancers (pouze pro standardní SKU)| Ano| Ne | [Nástroje pro vyrovnávání zatížení](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways|Ne|Ano|
|Microsoft. Network/privateEndpoints|Ne|Ano|
|Microsoft. Network/privateLinkServices|Ne|Ano|
|Microsoft.Network/publicipaddresses |NEUŽÍVÁ SE. | Ne |[Veřejné IP adresy](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ano | Ne | [Profily Traffic Manager](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Ano | Ne | [Log Analytics pracovní prostory](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. Relay/obory názvů | Ano | Ne | [Posílá](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft. peering/peeringServices|Ne|Ano|
|Microsoft.PowerBIDedicated/capacities | NEUŽÍVÁ SE. | Ne | [Schopností](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |NEUŽÍVÁ SE.|Ne | [Hledat služby](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Ano| Ne |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |   Ne | Ano |
|Microsoft.Sql/servers/databases    | Ne | Ano |
|Microsoft.Storage/storageAccounts |Ano | Ne | [Účty úložiště](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Ano| Ne | Služby [BLOB Services](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [souborové](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices)služby, [služby front](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) a [tabulkové služby](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |NEUŽÍVÁ SE.| Ne | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft. Microsoft. VMWareCloudSimple/virtualMachines |Ano|Ne |[CloudSimple Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/serverfarms | Ano | Ne | [Plány App Service](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Ano | Ne | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) a [funkce](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Ano | Ne | [Sloty App Service](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Schéma datové části

> [!NOTE]
> Můžete také použít [běžné schéma výstrah](https://aka.ms/commonAlertSchemaDocs), které poskytuje pro integraci Webhooku jedinou rozšiřitelnou a jednotnou datovou část napříč všemi službami výstrahy v Azure monitor. [Přečtěte si o běžných definicích schémat výstrah.](https://aka.ms/commonAlertSchemaDefinitions)


Operace POST obsahuje následující datovou část a schéma JSON pro všechny blízké novější výstrahy metriky, když se používá vhodně konfigurovaná [Skupina akcí](../../azure-monitor/platform/action-groups.md) :

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
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
            "metricValue": 1
          }
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

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o novém [prostředí výstrah](../../azure-monitor/platform/alerts-overview.md).
* Přečtěte si informace o [upozorněních protokolu v Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Přečtěte si o [výstrahách v Azure](../../azure-monitor/platform/alerts-overview.md).
