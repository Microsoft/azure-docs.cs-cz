---
title: Podporované prostředky pro výstrahy metriky v Azure Monitor
description: Referenční informace o metrikách a protokolech podpory pro výstrahy metrik v Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 9/30/2020
ms.subservice: alerts
ms.openlocfilehash: d1a1a31d5894338e0deeed27fabcde9fdbf6f588
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566482"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Podporované prostředky pro výstrahy metriky v Azure Monitor

Azure Monitor teď podporuje [nový typ výstrahy metriky](./alerts-overview.md) , který má významné výhody oproti starším [klasickým výstrahám metrik](./alerts-classic.overview.md). K dispozici jsou metriky pro [velký seznam služeb Azure](./metrics-supported.md). Novější výstrahy podporují (rostoucí) podmnožinu typů prostředků. Tento článek obsahuje seznam této podmnožiny.

Můžete také použít novější výstrahy metriky pro oblíbená data protokolu uložená v Log Analytics pracovním prostoru extrahované jako metriky. Další informace najdete v [upozorněních metriky pro protokoly](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, podpora REST
V současné době můžete vytvořit novější výstrahy metrik pouze v [šablonách](./alerts-metric-create-templates.md)Azure Portal, [REST API](/rest/api/monitor/metricalerts/)nebo správce prostředků. Brzy se připravuje podpora pro konfiguraci novějších výstrah pomocí PowerShellu a Azure CLI verze 2,0 a novější.

## <a name="metrics-and-dimensions-supported"></a>Podporované metriky a dimenze
Novější výstrahy metrik podporují upozorňování na metriky, které používají dimenze. Dimenze můžete použít k filtrování metriky na správnou úroveň. Všechny podporované metriky spolu s použitelnými rozměry lze prozkoumat a rozlišit z [Azure monitor-Průzkumník metrik](./metrics-charts.md).

Tady je úplný seznam Azure Monitor zdrojů metrik podporovaných novějšími výstrahami:

|Typ prostředku  |Podporované dimenze |Výstrahy s více prostředky| Dostupné metriky|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Yes | Ne | |
|Microsoft.ApiManagement/service | Yes | Ne | [API Management](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Yes | Ne | [App Configuration](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/pružina | Yes | Ne | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Yes| Ne | [Účty služby Automation](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | Ne | Ne | |
|Microsoft.Batch/batchAccounts | Yes | Ne | [Účty Batch](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. cache/Redis | Yes | Yes | [Azure Cache for Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft. ClassicCompute/domainNames/sloty/role | Ne | Ne | [Klasický Cloud Services](./metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | Ne | Ne | [Klasický Virtual Machines](./metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Yes | Ne | [Účty úložiště (klasické)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Yes | Ne | |
|Microsoft. ClassicStorage/storageAccounts/služby | Yes | Ne | |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Yes | Ne | |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Yes | Ne | |
|Microsoft. Cognitiveservices Account/účty | Yes | Ne | [Cognitive Services](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Yes | Ano<sup>1</sup> | [Virtual Machines](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Yes | Ne |[Virtual Machine Scale Sets](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Yes| Ne | [Skupiny kontejnerů](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/Registry | Ne | Ne | [Registry kontejnerů](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. ContainerService/managedClusters | Yes | Ne | [Spravované clustery](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Yes | Yes | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/DataFactory| Yes| Ne | [Datové továrny v1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/továrny |Yes | Ne | [Datové továrny v2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. datashare/Accounts | Yes | Ne | |
|Microsoft. DBforMariaDB/servery | Ne | Ne | [DB pro MariaDB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/servery | Ne | Ne |[DB pro MySQL](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/flexibleServers | Yes | Ne | |
|Microsoft. DBforPostgreSQL/servery | Ne | Ne | [DB pro PostgreSQL](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | Ne | Ne | [DB pro PostgreSQL v2](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. DBforPostgreSQL/singleservers | Ne | Ne | [DB pro PostgreSQL (jednotlivé servery)](./metrics-supported.md#microsoftdbforpostgresqlsingleservers)|
|Microsoft. Devices/IotHubs | Yes | Ne |[IoT Hub](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Yes | Ne | [Služby Device Provisioning](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Yes | Ne | |
|Microsoft.DocumentDB/databaseAccounts | Yes | Ne | [Databáze Cosmos](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/domény | Yes | Ne | [Domény Event Gridu](./metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Yes | Ne | [Témata týkající se Event Grid systému](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/témata |Yes | Ne | [Témata Event Gridu](./metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/clustery |Yes| Ne | [Clustery Event Hubs](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/obory názvů |Yes| Ne | [Event Hubs](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/clustery | Yes | Ne | [Clustery HDInsight](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. Insights/Components | Yes | Ne | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Trezory a trezory Microsoft. | Yes |Yes |[Trezory](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/clustery | Yes |Ne |[Clustery Průzkumník dat](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Yes | Ne |[Prostředí integrační služby](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/Workflows | Ne | Ne |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/pracovní prostory | Yes | Ne | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. Maps/Accounts | Yes | Ne | [Účty mapy](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/MediaServices | Ne | Ne | [Media Services](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/MediaServices/starají | Yes | Ne | [Koncové body streamování Media Services](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Yes | Yes | [Fondy kapacit Azure NetApp](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/svazky | Yes | Yes | [Svazky NetApp Azure](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Yes | Ne | [Aplikační brány](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Yes | Ne | [brány firewall,](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | Ne | Ne | [Zóny DNS](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | – | Ne |[Okruhy ExpressRoute](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/loadBalancers (pouze pro standardní SKU)| Yes| Ne | [Nástroje pro vyrovnávání zatížení](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| Ne | Ne | |
|Microsoft. Network/privateEndpoints| Ne | Ne | |
|Microsoft. Network/privateLinkServices| Ne | Ne |
|Microsoft. Network/publicipaddresses | Ne | Ne |[Veřejné IP adresy](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Yes | Ne | [Profily Traffic Manageru](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/pracovní prostory| Yes | Ne | [Pracovní prostory služby Log Analytics](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. peering/peering | Yes | Ne | [Partnerské vztahy](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. peering/peeringServices | Yes | Ne | [Služby Peering Service](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/– kapacity | Ne | Ne | [Capacities](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/obory názvů | Yes | Ne | [Předávání](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. Search/searchServices | Ne | Ne | [Hledat služby](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. ServiceBus/obory názvů | Yes | Ne | [Service Bus](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | Ne | Yes | [Spravované instance SQL](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/servery/databáze | Ne | Yes | [Databáze SQL](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/servery/elasticPools | Ne | Yes | [Elastické fondy SQL](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Yes | Ne | [Účty úložiště](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/Services | Yes| Ne | Služby [BLOB Services](./metrics-supported.md#microsoftstoragestorageaccountsblobservices), [souborové](./metrics-supported.md#microsoftstoragestorageaccountsfileservices)služby, [služby front](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) a [tabulkové služby](./metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft. StorageCache/caches | Yes | Ne | |
|Microsoft. StorageSync/storageSyncServices | Yes | Ne | [Služby synchronizace úložiště](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Yes | Ne | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. VMWareCloudSimple/virtualMachines | Yes | Ne | [Virtuální počítače CloudSimple](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Yes | Ne | [App Service Environment fondů s více rolemi](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Yes | Ne | [App Service Environment fondy pracovních procesů](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/serverových farem | Yes | Ne | [Plány App Service](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/weby | Yes | Ne | [App Services](./metrics-supported.md#microsoftwebsites-excluding-functions) a [funkce](./metrics-supported.md#microsoftwebsites-functions)|
|Microsoft. Web/weby/sloty | Yes | Ne | [Sloty App Service](./metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> není podporováno pro metriky sítě virtuálních počítačů (celkem v síti, celkový počet příchozích toků, příchozí toky, odchozí toky, maximální rychlost vytváření toků, maximální rychlost vytváření odchozích toků, maximální rychlost vytvoření u odchozích toků) a vlastní metriky.

## <a name="payload-schema"></a>Schéma datové části

> [!NOTE]
> Můžete také použít [běžné schéma výstrah](./alerts-common-schema.md), které poskytuje pro integraci Webhooku jedinou rozšiřitelnou a jednotnou datovou část napříč všemi službami výstrahy v Azure monitor. [Přečtěte si o běžných definicích schémat výstrah.](./alerts-common-schema-definitions.md)


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
