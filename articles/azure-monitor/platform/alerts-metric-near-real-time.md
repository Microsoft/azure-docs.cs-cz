---
title: Podporované prostředky pro výstrahy metriky v Azure Monitor
description: Referenční informace o metrikách a protokolech podpory pro výstrahy metrik v Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 8/16/2020
ms.subservice: alerts
ms.openlocfilehash: 877134fb1d4417c04da3bf6a96267b413389ecb2
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258446"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Podporované prostředky pro výstrahy metriky v Azure Monitor

Azure Monitor teď podporuje [nový typ výstrahy metriky](./alerts-overview.md) , který má významné výhody oproti starším [klasickým výstrahám metrik](./alerts-classic.overview.md). K dispozici jsou metriky pro [velký seznam služeb Azure](./metrics-supported.md). Novější výstrahy podporují (rostoucí) podmnožinu typů prostředků. Tento článek obsahuje seznam této podmnožiny.

Můžete také použít novější výstrahy metriky pro oblíbená data protokolu uložená v Log Analytics pracovním prostoru extrahované jako metriky. Další informace najdete v [upozorněních metriky pro protokoly](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, podpora REST
V současné době můžete vytvořit novější výstrahy metrik pouze v [šablonách](./alerts-metric-create-templates.md)Azure Portal, [REST API](/rest/api/monitor/metricalerts/)nebo správce prostředků. Brzy se připravuje podpora pro konfiguraci novějších výstrah pomocí PowerShellu a Azure CLI verze 2,0 a novější.

## <a name="metrics-and-dimensions-supported"></a>Podporované metriky a dimenze
Novější výstrahy metrik podporují upozorňování na metriky, které používají dimenze. Dimenze můžete použít k filtrování metriky na správnou úroveň. Všechny podporované metriky spolu s použitelnými rozměry lze prozkoumat a rozlišit z [Azure monitor-Průzkumník metrik](./metrics-charts.md).

Tady je úplný seznam zdrojů metrik Azure monitorů podporovaných novějšími výstrahami:

|Typ prostředku  |Podporované dimenze |Výstrahy s více prostředky| Dostupné metriky|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Ano | No | |
|Microsoft.ApiManagement/service | Ano | No | [API Management](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Ano | No | [App Configuration](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/pružina | Ano | No | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Ano| No | [Účty služby Automation](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | No | No | |
|Microsoft.Batch/batchAccounts | Ano | No | [Účty Batch](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. cache/Redis | Ano | No | [Azure Cache for Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft. ClassicStorage/storageAccounts | Ano | No | [Účty úložiště (klasické)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Ano | No | |
|Microsoft. ClassicStorage/storageAccounts/služby | Ano | No | |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Ano | No | |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Ano | No | |
|Microsoft. Cognitiveservices Account/účty | Ano | No | [Cognitive Services](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Ano | Ano | [Virtual Machines](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Ano | No |[Virtual Machine Scale Sets](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Ano| No | [Skupiny kontejnerů](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/Registry | No | No | [Registry kontejnerů](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. ContainerService/managedClusters | Ano | No | [Spravované clustery](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Ano | Ano | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/DataFactory| Ano| No | [Datové továrny v1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/továrny |Ano | No | [Datové továrny v2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. datashare/Accounts | Ano | No | |
|Microsoft. DBforMariaDB/servery | No | No | [DB pro MariaDB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/servery | No | No |[DB pro MySQL](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/flexibleServers | Ano | No | |
|Microsoft. DBforPostgreSQL/servery | No | No | [DB pro PostgreSQL](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | No | No | [DB pro PostgreSQL v2](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. DBforPostgreSQL/singleservers | No | No | [DB pro PostgreSQL (jednotlivé servery)](./metrics-supported.md#microsoftdbforpostgresqlsingleservers)|
|Microsoft. Devices/IotHubs | Ano | No |[IoT Hub](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Ano | No | [Služby Device Provisioning](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Ano | No | |
|Microsoft.DocumentDB/databaseAccounts | Ano | No | [Databáze Cosmos](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/domény | Ano | No | [Domény Event Gridu](./metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Ano | No | [Témata týkající se Event Grid systému](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/témata |Ano | No | [Témata Event Gridu](./metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/clustery |Ano| No | [Clustery Event Hubs](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/obory názvů |Ano| No | [Event Hubs](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/clustery | Ano | No | [Clustery HDInsight](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. Insights/Components | Ano | No | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Trezory a trezory Microsoft. | Ano |Ano |[Trezory](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/clustery | Ano |No |[Clustery Průzkumník dat](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Ano | No |[Prostředí integrační služby](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/Workflows | No | No |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/pracovní prostory | Ano | No | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. Maps/Accounts | Ano | No | [Účty mapy](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/MediaServices | No | No | [Media Services](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/MediaServices/starají | Ano | No | [Koncové body streamování Media Services](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Ano | Ano | [Fondy kapacit Azure NetApp](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/svazky | Ano | Ano | [Svazky NetApp Azure](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Ano | No | [Aplikační brány](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Ano | No | [Brány firewall](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | No | No | [Zóny DNS](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | – | No |[Okruhy ExpressRoute](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/loadBalancers (pouze pro standardní SKU)| Ano| No | [Nástroje pro vyrovnávání zatížení](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| No | No | |
|Microsoft. Network/privateEndpoints| No | No | |
|Microsoft. Network/privateLinkServices| No | No |
|Microsoft. Network/publicipaddresses | No | No |[Veřejné IP adresy](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Ano | No | [Profily Traffic Manageru](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/pracovní prostory| Ano | No | [Pracovní prostory služby Log Analytics](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. peering/peering | Ano | No | [Partnerské vztahy](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. peering/peeringServices | Ano | No | [Služby Peering Service](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/– kapacity | No | No | [Capacities](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/obory názvů | Ano | No | [Předávání](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. Search/searchServices | No | No | [Hledat služby](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. ServiceBus/obory názvů | Ano | No | [Service Bus](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | No | Ano | [Spravované instance SQL](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/servery/databáze | No | Ano | [Databáze SQL](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/servery/elasticPools | No | Ano | [Elastické fondy SQL](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Ano | No | [Účty úložiště](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/Services | Ano| No | Služby [BLOB Services](./metrics-supported.md#microsoftstoragestorageaccountsblobservices), [souborové](./metrics-supported.md#microsoftstoragestorageaccountsfileservices)služby, [služby front](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) a [tabulkové služby](./metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft. StorageCache/caches | Ano | No | |
|Microsoft. StorageSync/storageSyncServices | Ano | No | [Služby synchronizace úložiště](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Ano | No | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. VMWareCloudSimple/virtualMachines | Ano | No | [Virtuální počítače CloudSimple](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Ano | No | [App Service Environment fondů s více rolemi](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Ano | No | [App Service Environment fondy pracovních procesů](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/serverových farem | Ano | No | [Plány App Service](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/weby | Ano | No | [App Services](./metrics-supported.md#microsoftwebsites-excluding-functions) a [funkce](./metrics-supported.md#microsoftwebsites-functions)|
|Microsoft. Web/weby/sloty | Ano | No | [Sloty App Service](./metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Schéma datové části

> [!NOTE]
> Můžete také použít [běžné schéma výstrah](https://aka.ms/commonAlertSchemaDocs), které poskytuje pro integraci Webhooku jedinou rozšiřitelnou a jednotnou datovou část napříč všemi službami výstrahy v Azure monitor. [Přečtěte si o běžných definicích schémat výstrah.](https://aka.ms/commonAlertSchemaDefinitions)


Operace POST obsahuje následující datovou část a schéma JSON pro všechny blízké novější výstrahy metriky, když se používá vhodně konfigurovaná [Skupina akcí](./action-groups.md) :

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

* Přečtěte si další informace o novém [prostředí výstrah](./alerts-overview.md).
* Přečtěte si informace o [upozorněních protokolu v Azure](./alerts-unified-log.md).
* Přečtěte si o [výstrahách v Azure](./alerts-overview.md).

