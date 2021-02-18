---
title: Podporované prostředky pro výstrahy metriky v Azure Monitor
description: Referenční informace o metrikách a protokolech podpory pro výstrahy metrik v Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 02/10/2021
ms.subservice: alerts
ms.openlocfilehash: 7b4cdd37cefb628c03129a990d52ed348acd3ac1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609627"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Podporované prostředky pro výstrahy metriky v Azure Monitor

Azure Monitor teď podporuje [nový typ výstrahy metriky](../platform/alerts-overview.md) , který má významné výhody oproti starším [klasickým výstrahám metrik](./alerts-classic.overview.md). K dispozici jsou metriky pro [velký seznam služeb Azure](../platform/metrics-supported.md). Novější výstrahy podporují (rostoucí) podmnožinu typů prostředků. Tento článek obsahuje seznam této podmnožiny.

Můžete také použít novější výstrahy metriky pro oblíbená data protokolu uložená v Log Analytics pracovním prostoru extrahované jako metriky. Další informace najdete v [upozorněních metriky pro protokoly](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, podpora REST
V současné době můžete vytvořit novější výstrahy metrik pouze v [šablonách](./alerts-metric-create-templates.md)Azure Portal, [REST API](/rest/api/monitor/metricalerts/)nebo správce prostředků. Brzy se připravuje podpora pro konfiguraci novějších výstrah pomocí PowerShellu a Azure CLI verze 2,0 a novější.

## <a name="metrics-and-dimensions-supported"></a>Podporované metriky a dimenze
Novější výstrahy metrik podporují upozorňování na metriky, které používají dimenze. Dimenze můžete použít k filtrování metriky na správnou úroveň. Všechny podporované metriky spolu s použitelnými rozměry lze prozkoumat a rozlišit z [Azure monitor-Průzkumník metrik](../essentials/metrics-charts.md).

Tady je úplný seznam Azure Monitor zdrojů metrik podporovaných novějšími výstrahami:

|Typ prostředku  |Podporované dimenze |Výstrahy s více prostředky| Dostupné metriky|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Yes | No | |
|Microsoft.ApiManagement/service | Yes | No | [API Management](../platform/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Yes | No | [App Configuration](../platform/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/pružina | Yes | No | [Azure Spring Cloud](../platform/metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Yes| No | [Účty služby Automation](../platform/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | No | No | [Azure VMware Solution](../platform/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | Yes | No | [Účty Batch](../platform/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. cache/Redis | Yes | Yes | [Azure Cache for Redis](../platform/metrics-supported.md#microsoftcacheredis) |
|Microsoft. ClassicCompute/domainNames/sloty/role | No | No | [Klasický Cloud Services](../platform/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | No | No | [Klasický Virtual Machines](../platform/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Yes | No | [Účty úložiště (klasické)](../platform/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Yes | No | [Účty úložiště (klasické) – objekty blob](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft. ClassicStorage/storageAccounts/služby | Yes | No | [Účty úložiště (klasické) – soubory](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Yes | No | [Účty úložiště (klasické) – fronty](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Yes | No | [Účty úložiště (klasické) – tabulky](../platform/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft. Cognitiveservices Account/účty | Yes | No | [Cognitive Services](../platform/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Yes | Ano<sup>1</sup> | [Virtual Machines](../platform/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Yes | No |[Virtual Machine Scale Sets](../platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Yes| No | [Skupiny kontejnerů](../platform/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/Registry | No | No | [Registry kontejnerů](../platform/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. ContainerService/managedClusters | Yes | No | [Spravované clustery](../platform/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Yes | Yes | [Data Box](../platform/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/DataFactory| Yes| No | [Datové továrny v1](../platform/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/továrny |Yes | No | [Datové továrny v2](../platform/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. datashare/Accounts | Yes | No | [Sdílené složky dat](../platform/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft. DBforMariaDB/servery | No | No | [DB pro MariaDB](../platform/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/servery | No | No |[DB pro MySQL](../platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/servery | No | No | [DB pro PostgreSQL](../platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | No | No | [DB pro PostgreSQL v2](../platform/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. DBforPostgreSQL/flexibleServers | Yes | No | [DB pro PostgreSQL (flexibilní servery)](../platform/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. Devices/IotHubs | Yes | No |[IoT Hub](../platform/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Yes | No | [Služby Device Provisioning](../platform/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Yes | No | [Digital Twins](../platform/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Yes | No | [Databáze Cosmos](../platform/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/domény | Yes | No | [Domény Event Gridu](../platform/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Yes | No | [Témata týkající se Event Grid systému](../platform/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/témata |Yes | No | [Témata Event Gridu](../platform/metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/clustery |Yes| No | [Clustery Event Hubs](../platform/metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/obory názvů |Yes| No | [Event Hubs](../platform/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/clustery | Yes | No | [Clustery HDInsight](../platform/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. Insights/Components | Yes | No | [Application Insights](../platform/metrics-supported.md#microsoftinsightscomponents) |
|Trezory a trezory Microsoft. | Yes |Yes |[Trezory](../platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/clustery | Yes |No |[Clustery Průzkumník dat](../platform/metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Yes | No |[Prostředí integrační služby](../platform/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/Workflows | No | No |[Logic Apps](../platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/pracovní prostory | Yes | No | [Machine Learning](../platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. Maps/Accounts | Yes | No | [Účty mapy](../platform/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/MediaServices | No | No | [Media Services](../platform/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/MediaServices/starají | Yes | No | [Koncové body streamování Media Services](../platform/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Yes | Yes | [Fondy kapacit Azure NetApp](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/svazky | Yes | Yes | [Svazky NetApp Azure](../platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Yes | No | [Aplikační brány](../platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Yes | No | [brány firewall,](../platform/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | No | No | [Zóny DNS](../platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | Yes | No |[Okruhy ExpressRoute](../platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/expressRoutePorts | Yes | No |[ExpressRoute Direct](../platform/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft. Network/loadBalancers (pouze pro standardní SKU)| Yes| No | [Nástroje pro vyrovnávání zatížení](../platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| No | No | [Brány NAT](../platform/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| No | No | [Privátní koncové body](../platform/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft. Network/privateLinkServices| No | No | [Služby privátního propojení](../platform/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft. Network/publicipaddresses | No | No | [Veřejné IP adresy](../platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Yes | No | [Profily Traffic Manageru](../platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/pracovní prostory| Yes | No | [Pracovní prostory služby Log Analytics](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. peering/peering | Yes | No | [Partnerské vztahy](../platform/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. peering/peeringServices | Yes | No | [Služby Peering Service](../platform/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/– kapacity | No | No | [Capacities](../platform/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/obory názvů | Yes | No | [Předávání](../platform/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. Search/searchServices | No | No | [Hledat služby](../platform/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. ServiceBus/obory názvů | Yes | No | [Service Bus](../platform/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | No | Yes | [Spravované instance SQL](../platform/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/servery/databáze | No | Yes | [Databáze SQL](../platform/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/servery/elasticPools | No | Yes | [Elastické fondy SQL](../platform/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Yes | No | [Účty úložiště](../platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/blobServices | Yes| No | [Účty úložiště – objekty blob](../platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft. Storage/storageAccounts/služby | Yes| No | [Účty úložiště – soubory](../platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft. Storage/storageAccounts/queueServices | Yes| No | [Účty úložiště – fronty](../platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft. Storage/storageAccounts/tableServices | Yes| No | [Účty úložiště – tabulky](../platform/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft. StorageCache/caches | Yes | No | [Mezipaměti HPC](../platform/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Yes | No | [Služby synchronizace úložiště](../platform/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Yes | No | [Stream Analytics](../platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. synapse/pracovní prostory | Yes | No | [Synapse Analytics](../platform/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. synapse/pracovní prostory/bigDataPools | Yes | No | [Synapse Analytics Apache Spark fondy](../platform/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. synapse/pracovní prostory/sqlPools | Yes | No | [Synapse Analytics – fondy SQL](../platform/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Yes | No | [Virtuální počítače CloudSimple](../platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Yes | No | [App Service Environment fondů s více rolemi](../platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Yes | No | [App Service Environment fondy pracovních procesů](../platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/serverových farem | Yes | No | [Plány App Service](../platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/weby | Yes | No | [App Services a funkce](../platform/metrics-supported.md#microsoftwebsites)|
|Microsoft. Web/weby/sloty | Yes | No | [Sloty App Service](../platform/metrics-supported.md#microsoftwebsitesslots)|

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

* Přečtěte si další informace o novém [prostředí výstrah](../platform/alerts-overview.md).
* Přečtěte si informace o [upozorněních protokolu v Azure](./alerts-unified-log.md).
* Přečtěte si o [výstrahách v Azure](../platform/alerts-overview.md).