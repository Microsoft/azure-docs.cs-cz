---
title: Podporované prostředky pro výstrahy metriky v Azure Monitor
description: Referenční informace o metrikách a protokolech podpory pro výstrahy metrik v Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 24298734a46b8339a2a8818692641b4c10812294
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104877"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Podporované prostředky pro výstrahy metriky v Azure Monitor

Azure Monitor teď podporuje [nový typ výstrahy metriky](./alerts-overview.md) , který má významné výhody oproti starším [klasickým výstrahám metrik](./alerts-classic.overview.md). K dispozici jsou metriky pro [velký seznam služeb Azure](../essentials/metrics-supported.md). Novější výstrahy podporují (rostoucí) podmnožinu typů prostředků. Tento článek obsahuje seznam této podmnožiny.

Můžete také použít novější výstrahy metriky pro oblíbená data protokolu uložená v Log Analytics pracovním prostoru extrahované jako metriky. Další informace najdete v [upozorněních metriky pro protokoly](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portál, PowerShell, CLI, podpora REST
V současné době můžete vytvořit novější výstrahy metrik pouze v [šablonách](./alerts-metric-create-templates.md)Azure Portal, [REST API](/rest/api/monitor/metricalerts/)nebo správce prostředků. Brzy se připravuje podpora pro konfiguraci novějších výstrah pomocí PowerShellu a Azure CLI verze 2,0 a novější.

## <a name="metrics-and-dimensions-supported"></a>Podporované metriky a dimenze
Novější výstrahy metrik podporují upozorňování na metriky, které používají dimenze. Dimenze můžete použít k filtrování metriky na správnou úroveň. Všechny podporované metriky spolu s použitelnými rozměry lze prozkoumat a rozlišit z [Azure monitor-Průzkumník metrik](../essentials/metrics-charts.md).

Tady je úplný seznam Azure Monitor zdrojů metrik podporovaných novějšími výstrahami:

|Typ prostředku  |Podporované dimenze |Výstrahy s více prostředky| Dostupné metriky|
|---------|---------|-----|----------|
|Microsoft. Aadiam/azureADMetrics | Yes | No | [Azure AD](../essentials/metrics-supported.md#microsoftaadiamazureadmetrics) |
|Microsoft.ApiManagement/service | Yes | No | [API Management](../essentials/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft. AppConfiguration/configurationStores |Yes | No | [App Configuration](../essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft. AppPlatform/pružina | Yes | No | [Azure Spring Cloud](../essentials/metrics-supported.md#microsoftappplatformspring) |
|Microsoft. Automation/automationAccounts | Yes| No | [Účty služby Automation](../essentials/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft. AVS/privateClouds | No | No | [Azure VMware Solution](../essentials/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | Yes | No | [Účty Batch](../essentials/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft. BotService/botServices | Yes | No | [Služby robota](../essentials/metrics-supported.md#microsoftbotservicebotservices) |
|Microsoft. cache/Redis | Yes | Yes | [Azure Cache for Redis](../essentials/metrics-supported.md#microsoftcacheredis) |
|Microsoft. CDN/profily | Yes | No | [Profily CDN](../essentials/metrics-supported.md#microsoftcdnprofiles) |
|Microsoft. ClassicCompute/domainNames/sloty/role | No | No | [Klasický Cloud Services](../essentials/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft. ClassicCompute/virtualMachines | No | No | [Klasický Virtual Machines](../essentials/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft. ClassicStorage/storageAccounts | Yes | No | [Účty úložiště (klasické)](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft. ClassicStorage/storageAccounts/blobServices | Yes | No | [Účty úložiště (klasické) – objekty blob](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft. ClassicStorage/storageAccounts/služby | Yes | No | [Účty úložiště (klasické) – soubory](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft. ClassicStorage/storageAccounts/queueServices | Yes | No | [Účty úložiště (klasické) – fronty](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft. ClassicStorage/storageAccounts/tableServices | Yes | No | [Účty úložiště (klasické) – tabulky](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft. Cognitiveservices Account/účty | Yes | No | [Cognitive Services](../essentials/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft. COMPUTE/cloudServices | Yes | No |  [Cloudové služby](../essentials/metrics-supported.md#microsoftcomputecloudservices) |
|Microsoft. COMPUTE/cloudServices/role | Yes | No |  [Role cloudové služby](../essentials/metrics-supported.md#microsoftcomputecloudservicesroles) |
|Microsoft.Compute/virtualMachines | Yes | Ano<sup>1</sup> | [Virtual Machines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Yes | No |[Virtual Machine Scale Sets](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft. ContainerInstance/containerGroups | Yes| No | [Skupiny kontejnerů](../essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft. ContainerRegistry/Registry | No | No | [Registry kontejnerů](../essentials/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft. ContainerService/managedClusters | Yes | No | [Spravované clustery](../essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft. DataBoxEdge/dataBoxEdgeDevices | Yes | Yes | [Data Box](../essentials/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft. DataFactory/DataFactory| Yes| No | [Datové továrny v1](../essentials/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft. DataFactory/továrny |Yes | No | [Datové továrny v2](../essentials/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft. datashare/Accounts | Yes | No | [Sdílené složky dat](../essentials/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft. DBforMariaDB/servery | No | No | [DB pro MariaDB](../essentials/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft. DBforMySQL/servery | No | No |[DB pro MySQL](../essentials/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft. DBforPostgreSQL/flexibleServers | Yes | No | [DB pro PostgreSQL (flexibilní servery)](../essentials/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft. DBforPostgreSQL/serverGroupsv2 | Yes | No | DB pro PostgreSQL (škálování) |
|Microsoft. DBforPostgreSQL/servery | No | No | [DB pro PostgreSQL](../essentials/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft. DBforPostgreSQL/serversv2 | No | No | [DB pro PostgreSQL v2](../essentials/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft. Devices/IotHubs | Yes | No |[IoT Hub](../essentials/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft. Devices/provisioningServices| Yes | No | [Služby Device Provisioning](../essentials/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft. DigitalTwins/digitalTwinsInstances | Yes | No | [Digital Twins](../essentials/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Yes | No | [Databáze Cosmos](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft. EventGrid/domény | Yes | No | [Domény Event Gridu](../essentials/metrics-supported.md#microsofteventgriddomains) |
|Microsoft. EventGrid/systemTopics | Yes | No | [Témata týkající se Event Grid systému](../essentials/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft. EventGrid/témata |Yes | No | [Témata Event Gridu](../essentials/metrics-supported.md#microsofteventgridtopics) |
|Microsoft. EventHub/clustery |Yes| No | [Clustery Event Hubs](../essentials/metrics-supported.md#microsofteventhubclusters) |
|Microsoft. EventHub/obory názvů |Yes| No | [Event Hubs](../essentials/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft. HDInsight/clustery | Yes | No | [Clustery HDInsight](../essentials/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft. Insights/Components | Yes | No | [Application Insights](../essentials/metrics-supported.md#microsoftinsightscomponents) |
|Trezory a trezory Microsoft. | Yes |Yes |[Trezory](../essentials/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft. Kusto/clustery | Yes |No |[Clustery Průzkumník dat](../essentials/metrics-supported.md#microsoftkustoclusters)|
|Microsoft. Logic/integrationServiceEnvironments | Yes | No |[Prostředí integrační služby](../essentials/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft. Logic/Workflows | No | No |[Logic Apps](../essentials/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft. MachineLearningServices/pracovní prostory | Yes | No | [Machine Learning](../essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft. MachineLearningServices/pracovní prostory/onlineEndpoints | Yes | No | Machine Learning – koncové body |
|Microsoft. MachineLearningServices/pracovní prostory/onlineEndpoints/nasazení | Yes | No | Nasazení Machine Learningho koncového bodu |
|Microsoft. Maps/Accounts | Yes | No | [Účty mapy](../essentials/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft. Media/MediaServices | No | No | [Media Services](../essentials/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft. Media/MediaServices/starají | Yes | No | [Koncové body streamování Media Services](../essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft. NetApp/netAppAccounts/capacityPools | Yes | Yes | [Fondy kapacit Azure NetApp](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft. NetApp/netAppAccounts/capacityPools/svazky | Yes | Yes | [Svazky NetApp Azure](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft. Network/applicationGateways | Yes | No | [Brány Application Gateway](../essentials/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft. Network/azurefirewalls | Yes | No | [brány firewall,](../essentials/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft. Network/dnsZones | No | No | [Zóny DNS](../essentials/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft. Network/expressRouteCircuits | Yes | No |[Okruhy ExpressRoute](../essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/expressRoutePorts | Yes | No |[ExpressRoute Direct](../essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft. Network/loadBalancers (pouze pro standardní SKU)| Yes| No | [Nástroje pro vyrovnávání zatížení](../essentials/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways| No | No | [Brány NAT](../essentials/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft. Network/privateEndpoints| No | No | [Soukromé koncové body](../essentials/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft. Network/privateLinkServices| No | No | [Služby privátního propojení](../essentials/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft. Network/publicipaddresses | No | No | [Veřejné IP adresy](../essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft. Network/trafficManagerProfiles | Yes | No | [Profily Traffic Manageru](../essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft. OperationalInsights/pracovní prostory| Yes | No | [Pracovní prostory služby Log Analytics](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. peering/peering | Yes | No | [Partnerské vztahy](../essentials/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft. peering/peeringServices | Yes | No | [Služby Peering Service](../essentials/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft. PowerBIDedicated/– kapacity | No | No | [Capacities](../essentials/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft. Relay/obory názvů | Yes | No | [Předávání](../essentials/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft. Search/searchServices | No | No | [Hledat služby](../essentials/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft. ServiceBus/obory názvů | Yes | No | [Service Bus](../essentials/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft. SQL/managedInstances | No | Yes | [Spravované instance SQL](../essentials/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft. SQL/servery/databáze | No | Yes | [Databáze SQL](../essentials/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft. SQL/servery/elasticPools | No | Yes | [Elastické fondy SQL](../essentials/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft. Storage/storageAccounts |Yes | No | [Účty úložiště](../essentials/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft. Storage/storageAccounts/blobServices | Yes| No | [Účty úložiště – objekty blob](../essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft. Storage/storageAccounts/služby | Yes| No | [Účty úložiště – soubory](../essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft. Storage/storageAccounts/queueServices | Yes| No | [Účty úložiště – fronty](../essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft. Storage/storageAccounts/tableServices | Yes| No | [Účty úložiště – tabulky](../essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft. StorageCache/caches | Yes | No | [Mezipaměti HPC](../essentials/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft. StorageSync/storageSyncServices | Yes | No | [Služby synchronizace úložiště](../essentials/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft. StreamAnalytics/streamingjobs | Yes | No | [Stream Analytics](../essentials/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft. synapse/pracovní prostory | Yes | No | [Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft. synapse/pracovní prostory/bigDataPools | Yes | No | [Synapse Analytics Apache Spark fondy](../essentials/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft. synapse/pracovní prostory/sqlPools | Yes | No | [Synapse Analytics – fondy SQL](../essentials/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft. VMWareCloudSimple/virtualMachines | Yes | No | [Virtuální počítače CloudSimple](../essentials/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft. Web/hostingEnvironments/multiRolePools | Yes | No | [App Service Environment fondů s více rolemi](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft. Web/hostingEnvironments/workerPools | Yes | No | [App Service Environment fondy pracovních procesů](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft. Web/serverových farem | Yes | No | [Plány App Service](../essentials/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft. Web/weby | Yes | No | [App Services a funkce](../essentials/metrics-supported.md#microsoftwebsites)|
|Microsoft. Web/weby/sloty | Yes | No | [Sloty App Service](../essentials/metrics-supported.md#microsoftwebsitesslots)|

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
