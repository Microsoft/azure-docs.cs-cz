---
title: Podporované prostředky pro upozornění na metriky v Azure Monitoru
description: Odkaz na metriky podpory a protokoly pro upozornění na metriky v Azure Monitoru
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: c036fa3708d718d6199d27989e60b11015a1227e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585857"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Podporované prostředky pro upozornění na metriky v Azure Monitoru

Azure Monitor teď podporuje [nový typ výstrahy metriky,](../../azure-monitor/platform/alerts-overview.md) který má významné výhody oproti starší [klasické metriky výstrahy](../../azure-monitor/platform/alerts-classic.overview.md). Metriky jsou k dispozici pro [rozsáhlý seznam služeb Azure](../../azure-monitor/platform/metrics-supported.md). Novější výstrahy podporují (rostoucí) podmnožinu typů prostředků. Tento článek uvádí tuto podmnožinu.

Můžete také použít novější upozornění metriky na populární data protokolu uložené v pracovním prostoru Log Analytics extrahované jako metriky. Další informace naleznete [v metrikových výstrahách pro protokoly](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Podpora pro Portal, PowerShell, CLI, REST
V současné době můžete vytvářet novější upozornění metriky pouze na webu Azure Portal, [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)nebo [Templates Správce prostředků](../../azure-monitor/platform/alerts-metric-create-templates.md). Podpora pro konfiguraci novějších výstrah pomocí PowerShellu a Azure CLI verze 2.0 a vyšší je již brzy.

## <a name="metrics-and-dimensions-supported"></a>Podporované metriky a dimenze
Novější upozornění na metriky podporují upozornění na metriky, které používají dimenze. Dimenze můžete použít k filtrování metriky na správnou úroveň. Všechny podporované metriky spolu s příslušnými dimenzemi lze prozkoumat a vizualizovat z [Azure Monitor – Průzkumník metrik](../../azure-monitor/platform/metrics-charts.md).

Tady je úplný seznam zdrojů metrik monitorování Azure podporovaných novějšími výstrahami:

|Typ prostředku  |Podporované dimenze |Výstrahy s více prostředky| Dostupné metriky|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Ano| Ne | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlatform/jaro |Ne| Ano|
|Microsoft.Automation/automationÚčty | Ano| Ne | [Účty služby Automation](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Není dostupné.| Ne | [Účty Batch](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Ano| Ne |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic|Ne|Ano|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/blobServices|Ne|Ano|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/fileServices|Ne|Ano|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/queueServices|Ne|Ano|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/tableServices|Ne|Ano| |
|Microsoft.CognitiveServices/účty| Není dostupné. | Ne | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Ano | Ano | [Virtuální počítače](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |Není dostupné. | Ano |[Škálovací sady virtuálních počítačů](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Ano| Ne | [Skupiny kontejnerů](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/spravované clustery | Ano | Ne | [Spravované clustery](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Ano | Ano | |
|Microsoft.DataFactory/datafactories| Ano| Ne | [Datové továrny V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/továrny |Ano | Ne |[Datové továrny V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/účty |Ne| Ano|
|Microsoft.DBforMySQL/servery |Není dostupné.| Ne |[DB pro MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servery |Není dostupné. | Ne | [DB pro PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | Není dostupné. | Ne |[Metriky IoT Hubu](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Ano | Ne |[Metriky DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domény|Ne|Ano| |
|Microsoft.EventGrid/témata |Ano | Ne |[Témata mřížky událostí](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clustery |Ano| Ne |[Clustery rozbočovačů událostí](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Obory názvů Microsoft.EventHub/Namespaces |Ano| Ne |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Úložiště Microsoft.KeyVault/trezory| Ne |Ne |[Trezory](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/pracovní postupy |Není dostupné. | Ne |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/pracovní prostory|Ano| Ne | [Strojové učení](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |Ano| Ne | [Fondy kapacity Azure NetApp](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes |Ano| Ne | [Svazky Azure NetApp](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|Není dostupné.| Ne |  |
|Microsoft.Network/dnsZones | Není dostupné.| Ne | [Zóny DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Není dostupné. | Ne |[Okruhy ExpressRoute](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (pouze pro standardní skum)| Ano| Ne | [Účetní zatížení](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways|Ne|Ano|
|Microsoft.Network/privateEndpoints|Ne|Ano|
|Microsoft.Network/privateLinkServices|Ne|Ano|
|Microsoft.Network/publicipaddresses |Není dostupné. | Ne |[Veřejné IP adresy](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ano | Ne | [Profily Traffic Manageru](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/pracovní prostory| Ano | Ne | [Pracovní prostory služby Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Obory názvů Microsoft.Relay/Namespaces | Ano | Ne | [Předávání](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Služby Microsoft.Peering/peeringServices|Ne|Ano|
|Microsoft.PowerBIDedicated/kapacity | Není dostupné. | Ne | [Kapacity](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |Není dostupné.|Ne | [Vyhledávací služby](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Obory názvů Microsoft.ServiceBus |Ano| Ne |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servery/elastické bazény |    Ne | Ano |
|Microsoft.Sql/servery/databáze    | Ne | Ano |
|Microsoft.Storage/storageAccounts |Ano | Ne | [Účty úložiště](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Ano| Ne | [Služby blob](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Souborové služby](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [Služby front](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) a [Tabulkové služby](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |Není dostupné.| Ne | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.Microsoft.VMWareCloudSimple/virtualMachines |Ano|Ne |[Virtuální počítače CloudSimple](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | Ano | Ne | [Víceúčelové fondy prostředí aplikačních služeb](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Ano | Ne | [Fondy pracovních procesů prostředí služby App Service](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Farmy Microsoft.Web/server | Ano | Ne | [Plány služeb aplikace](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Web/weby společnosti Microsoft | Ano | Ne | [Služby](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) a [funkce](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions) aplikací|
|Microsoft.Web/weby/sloty | Ano | Ne | [Sloty služby App Service](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Schéma datové části

> [!NOTE]
> Můžete také použít [společné schéma výstrah](https://aka.ms/commonAlertSchemaDocs), které poskytuje výhodu, že mají jednu rozšiřitelnou a jednotnou datovou část výstrahy ve všech službách výstrah v Azure Monitoru pro vaše integrace webhooku. [Seznamte se s definicemi schématu běžných výstrah.](https://aka.ms/commonAlertSchemaDefinitions)


Operace POST obsahuje následující datové části JSON a schéma pro všechny blízké novější upozornění metriky při použití vhodně nakonfigurované [skupiny akcí:](../../azure-monitor/platform/action-groups.md)

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
* Další informace o [výstrahách protokolů v Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Přečtěte si o [výstrahách v Azure](../../azure-monitor/platform/alerts-overview.md).
