---
title: Podpora značky Azure Resource Manageru pro prostředky
description: Ukazuje, jaké typy prostředků Azure podporují značky. Poskytuje podrobnosti pro všechny služby Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: 580955d3c6fd7a33c152e49e601d8078eb169a22
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409662"
---
# <a name="tag-support-for-azure-resources"></a>Podpora značek pro prostředky Azure
Tento článek popisuje, zda typ prostředku podporuje [označování](resource-group-using-tags.md).

## <a name="aad-domain-services"></a>Služby AAD Domain Services
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| domény | Ne | 

## <a name="ad-hybrid-health-service"></a>Hybridní AD Health Service
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| services | Ne | 
| addsservices | Ne | 
| konfigurace | Ne | 
| agenti | Ne | 
| aadsupportcases | Ne | 
| sestavy | Ne | 
| servicehealthmetrics | Ne | 
| Protokoly | Ne | 
| anonymousapiusers | Ne | 

## <a name="analysis-services"></a>Analysis Services
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| serverů | Ano | 

## <a name="api-hubs"></a>Centra rozhraní API
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| apiManagementAccounts | Ne | 
| apiManagementAccounts/connectionProviders | Ne | 
| apiManagementAccounts/připojení | Ne | 
| apiManagementAccounts/connectionAcls | Ne | 
| apiManagementAccounts/connectionProviderAcls | Ne | 
| apiManagementAccounts/rozhraní API | Ne | 

## <a name="api-management"></a>API Management
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| služba | Ano | 

## <a name="automation"></a>Automation
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| AutomationAccounts | Ano | 
| automationAccounts/sady runbook | Ano | 
| automationAccounts/konfigurace | Ano | 
| automationAccounts/webhooků | Ne | 
| automationAccounts/softwareUpdateConfigurations | Ne | 
| automationAccounts/úloh | Ne | 

## <a name="batch"></a>Batch
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| batchAccounts | Ano | 

## <a name="bing-maps"></a>Mapy Bingu
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| mapApis | Ano | 

## <a name="biztalk-services"></a>Biztalk Services
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| BizTalk | Ano | 

## <a name="cache"></a>Mezipaměť
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Redis | Ano | 

## <a name="cdn"></a>CDN
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Profily | Ano | 
| profily a koncových bodů | Ano | 
| profily/koncové body/zdroje | Ne | 
| profily/koncové body/customdomains | Ne | 
| validateProbe | Ne | 
| edgenodes | Ne | 

## <a name="classic-compute"></a>Klasické výpočetní prostředky
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| domainNames | Ne | 
| domainNames/sloty | Ne | 
| domainNames/sloty/role | Ne | 
| virtuálních počítačů | Ne | 
| virtuálních počítačů/diagnosticSettings | Ne | 
| virtuálních počítačů/metricDefinitions | Ne | 
| virtuálních počítačů/metriky | Ne | 

## <a name="classic-infrastructure-migrate"></a>Migrace klasického infrastruktury
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| classicInfrastructureResources | Ne | 

## <a name="classic-network"></a>Klasickou síť
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| virtualNetworks | Ne | 
| virtualNetworks/virtualNetworkPeerings | Ne | 
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Ne | 

## <a name="classic-storage"></a>Klasického úložiště.
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| storageAccounts/services | Ne | 
| storageAccounts/services/diagnosticSettings | Ne | 

## <a name="compute"></a>Compute
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| availabilitySets | Ano | 
| virtuálních počítačů | Ano | 
| virtuálních počítačů/rozšíření | Ano | 
| virtualMachineScaleSets | Ano | 
| virtualMachineScaleSets/rozšíření | Ne | 
| virtualMachineScaleSets/virtuálních počítačů | Ne | 
| virtualMachineScaleSets/síťová | Ne | 
| virtualMachineScaleSets/virtuálních počítačů/síťová | Ne | 
| virtualMachineScaleSets/publicIPAddresses | Ne | 
| restorePointCollections | Ano | 
| restorePointCollections/restorePoints | Ne | 
| virtuálních počítačů/diagnosticSettings | Ne | 
| virtuálních počítačů/metricDefinitions | Ne | 
| sharedVMImages | Ano | 
| sharedVMImages/verze | Ano | 
| Disky | Ano | 
| snímky | Ano | 
| images | Ano | 

## <a name="container"></a>Kontejner
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| containerGroups | Ano | 

## <a name="container-instance"></a>Instance kontejneru
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| containerGroups | Ano | 
| serviceAssociationLinks | Ne | 

## <a name="container-service"></a>Container Service
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| containerServices | Ano | 

## <a name="cortana-analytics"></a>Cortana Analytics
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| accounts | Ano | 

## <a name="cosmos-db"></a>Databáze Cosmos
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| databaseAccounts | Ano | 
| databaseAccountNames | Ne | 

## <a name="cost-management"></a>Cost Management
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Konektory | Ano | 

## <a name="data-box-edge"></a>Data Box Edge
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| DataBoxEdgeDevices | Ano | 

## <a name="data-catalog"></a>Data Catalog
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| katalogy | Ano | 

## <a name="data-connect"></a>Připojení dat
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| connectionManagers | Ano | 

## <a name="data-factory"></a>Data Factory
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| dataFactories | Ano | 
| objekty pro vytváření | Ano | 
| objekty pro vytváření/integrationRuntimes | Ne | 
| dataFactories/diagnosticSettings | Ne | 
| dataFactories/metricDefinitions | Ne | 
| dataFactorySchema | Ne | 

## <a name="devices"></a>Zařízení
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| IotHubs | Ano | 
| IotHubs/eventGridFilters | Ne | 
| ProvisioningServices | Ano | 

## <a name="devspaces"></a>Devspaces
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Kontrolery | Ano | 

## <a name="devtest-lab"></a>Devtest Lab
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Praktická cvičení | Ano | 
| Plány | Ano | 
| testovací prostředí a virtuálních počítačů | Ano | 
| praktická cvičení/serviceRunners | Ano | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| lcsprojects | Ne | 
| lcsprojects nebo konektory | Ne | 
| lcsprojects/clouddeployments | Ne | 

## <a name="event-grid"></a>Event Grid
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| eventSubscriptions | Ne | 
| témata | Ano | 
| domény | Ano | 
| domény a témat | Ne | 
| topicTypes | Ne | 
| extensionTopics | Ne | 

## <a name="event-hub"></a>Centrum událostí
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Obory názvů | Ano | 
| Clustery | Ano | 

## <a name="hana-on-azure"></a>Hana v Azure
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| hanaInstances | Ano | 

## <a name="hdinsight"></a>HDInsight
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Clustery | Ano | 
| clustery/aplikace | Ne | 

## <a name="import-export"></a>Import a export
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| úlohy | Ano | 

## <a name="insights"></a>Insights
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Součásti | Ano | 
| součásti či dotazu | Ne | 
| součásti/metriky | Ne | 
| součásti/události | Ne | 
| webové testy | Ano | 
| dotazy | Ne | 
| scheduledqueryrules | Ano | 
| součásti/pricingPlans | Ne | 
| migrateToNewPricingModel | Ne | 
| rollbackToLegacyPricingModel | Ne | 
| automatedExportSettings | Ne | 
| Sešity | Ano | 
| myWorkbooks | Ne | 
| Protokoly | Ne | 

## <a name="key-vault"></a>Key Vault
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| trezory služby | Ano | 
| Trezory/tajných klíčů | Ne | 
| trezory/accessPolicies | Ne | 
| deletedVaults | Ne | 

## <a name="log-analytics"></a>Log Analytics
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Protokoly | Ne | 

## <a name="logic"></a>Logika
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Pracovní postupy | Ano | 
| integrationAccounts | Ano | 

## <a name="machine-learning-services"></a>Machine Learning Services
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| pracovní prostory | Ano | 
| pracovní prostory a výpočetní prostředí | Ne | 

## <a name="managed-identity"></a>Spravovaná identita
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Identity | Ne | 
| userAssignedIdentities | Ano | 

## <a name="mariadb"></a>MariaDB
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| serverů | Ano | 
| servery pro/recoverableServers | Ne | 
| servery pro/virtualNetworkRules | Ne | 

## <a name="marketplace-apps"></a>Aplikace z Marketplace
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| classicDevServices | Ano | 

## <a name="marketplace-ordering"></a>Řazení Marketplace
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Smlouvy | Ne | 
| offertypes | Ne | 

## <a name="media"></a>Média
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| mediaservices | Ano | 
| mediaservices/prostředky | Ne | 
| mediaservices/contentKeyPolicies | Ne | 
| mediaservices/streamingLocators | Ne | 
| mediaservices/streamingPolicies | Ne | 
| mediaservices/eventGridFilters | Ne | 
| mediaservices nebo transformací | Ne | 
| mediaservices/transformace/úloh | Ne | 
| mediaservices/koncové body streamování | Ano | 
| mediaservices/liveEvents | Ano | 
| mediaservices/liveEvents/liveOutputs | Ne | 
| mediaservices/streamingEndpointOperations | Ne | 
| mediaservices/liveEventOperations | Ne | 
| mediaservices/liveOutputOperations | Ne | 
| mediaservices/prostředky/assetFilters | Ne | 
| mediaservices/accountFilters | Ne | 

## <a name="mysql"></a>MySQL
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| serverů | Ano | 
| servery pro/recoverableServers | Ne | 
| servery pro/virtualNetworkRules | Ne | 

## <a name="network"></a>Síť
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| virtualNetworks | Ano | 
| publicIPAddresses | Ano | 
| Síťová | Ano | 
| interfaceEndpoints | Ano | 
| Load Balancerech | Ano | 
| networkSecurityGroups | Ano | 
| Jenom | Ano | 
| serviceEndpointPolicies | Ano | 
| networkIntentPolicies | Ano | 
| routeTables | Ano | 
| publicIPPrefixes | Ano | 
| networkWatchers | Ano | 
| networkWatchers/connectionMonitors | Ano | 
| networkWatchers/přehledů | Ano | 
| networkWatchers/pingMeshes | Ano | 
| virtualNetworkGateways | Ano | 
| localNetworkGateways | Ano | 
| připojení | Ano | 
| applicationGateways | Ano | 
| expressRouteCircuits | Ano | 
| routeFilters | Ano | 
| virtualWans | Ano | 
| vpnSites | Ano | 
| virtualHubs | Ano | 
| vpnGateways | Ano | 
| azureFirewalls | Ano | 
| virtualNetworkTaps | Ano | 
| privateLinkServices | Ano | 
| ddosProtectionPlans | Ano | 
| networkProfiles | Ano | 
| frontdoors | Ano | 
| frontdoorWebApplicationFirewallPolicies | Ano | 
| webApplicationFirewallPolicies | Ano | 

## <a name="notification-hubs"></a>Notification Hubs
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Obory názvů | Ano | 
| obory názvů a notificationHubs | Ano | 

## <a name="portal"></a>Portál
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Řídicí panely | Ano | 

## <a name="portal-sdk"></a>Portál SDK
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| rootResources | Ano | 

## <a name="postgresql"></a>PostgreSQL
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| serverů | Ano | 
| servery pro/recoverableServers | Ne | 
| servery pro/virtualNetworkRules | Ne | 
| servery pro/topQueryStatistics | Ne | 
| servery pro/queryTexts | Ne | 
| servery pro/waitStatistics | Ne | 
| servery pro/poradci | Ne | 

## <a name="power-bi"></a>Power BI
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| workspaceCollections | Ano | 

## <a name="recovery-services"></a>Recovery Services
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| trezory služby | Ano | 
| backupProtectedItems | Ne | 

## <a name="relay"></a>Relay
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Obory názvů | Ano | 

## <a name="resources"></a>Zdroje a prostředky
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| resourceGroups | Ano | 
| předplatná nebo skupiny prostředků | Ano | 

## <a name="scheduler"></a>Scheduler
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| nemůžou | Ano | 
| toky | Ano | 

## <a name="search"></a>Search
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| searchServices | Ano | 
| resourceHealthMetadata | Ne | 

## <a name="security"></a>Zabezpečení
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| dataCollectionAgents | Ne | 

## <a name="service-bus"></a>Service Bus
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Obory názvů | Ano | 
| obory názvů/eventgridfilters | Ne | 

## <a name="service-fabric"></a>Service Fabric
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Clustery | Ano | 
| clustery/aplikace | Ne | 

## <a name="service-fabric-mesh"></a>Service Fabric Mesh
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| aplikace | Ano | 
| Sítě | Ano | 
| volumes | Ano | 

## <a name="signalr-service"></a>Služba SignalR
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| SignalR | Ano | 

## <a name="site-recovery"></a>Site Recovery
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| SiteRecoveryVault | Ano | 

## <a name="solutions"></a>Řešení
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| aplikace | Ano | 
| applicationDefinitions | Ano | 
| jitRequests | Ano | 

## <a name="sql-virtual-machine"></a>Virtuálnímu počítači SQL
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| DWVM | Ano | 

## <a name="storage"></a>Storage
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| storageAccounts | Ano | 
| storageAccounts/blobServices | Ne | 
| storageAccounts/tableServices | Ne | 
| storageAccounts/queueServices | Ne | 
| storageAccounts/fileServices | Ne | 
| storageAccounts/services | Ne | 
| storageAccounts/services/metricDefinitions | Ne | 

## <a name="storage-sync"></a>Synchronizace úložiště
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| storageSyncServices | Ano | 
| storageSyncServices/syncGroups | Ne | 
| storageSyncServices/syncGroups/cloudEndpoints | Ne | 
| storageSyncServices/syncGroups/serverEndpoints | Ne | 
| storageSyncServices/registeredServers | Ne | 
| storageSyncServices/pracovních postupů | Ne | 

## <a name="storsimple"></a>Storsimple
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Správci | Ano | 

## <a name="stream-analytics"></a>Stream Analytics
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| streamingjobs | Ano | 
| streamingjobs/diagnosticSettings | Ne | 
| streamingjobs/metricDefinitions | Ne | 

## <a name="subscription"></a>Předplatné
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| SubscriptionDefinitions | Ne | 
| SubscriptionOperations | Ne | 

## <a name="support"></a>Podpora
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| supporttickets | Ne | 

## <a name="visual-studio"></a>Visual Studio
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| account | Ano | 
| účet nebo projektu | Ano | 
| účet a rozšíření | Ano | 
| account | Ano | 
| účet nebo projektu | Ano | 
| účet a rozšíření | Ano | 

## <a name="web"></a>Web
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| servery a instancí | Ne | 
| servery/sloty/instance | Ne | 
| servery/instance/rozšíření | Ne | 
| servery/sloty/instance/rozšíření | Ne | 
| publishingUsers | Ne | 
| Ověření | Ne | 
| sourceControls | Ne | 
| servery/hostNameBindings | Ne | 
| servery/domainOwnershipIdentifiers | Ne | 
| servery/sloty/hostNameBindings | Ne | 
| certifikáty | Ano | 
| serverových farem | Ano | 
| serverových farem/pracovních procesů | Ne | 
| Weby | Ano | 
| servery a sloty | Ano | 
| servery/metriky | Ne | 
| servery/sloty/metriky | Ne | 
| servery/premieraddons | Ano | 
| hostingEnvironments | Ano | 
| hostingEnvironments/multiRolePools | Ne | 
| hostingEnvironments/workerPools | Ne | 
| hostingEnvironments/metriky | Ne | 
| functions | Ne | 
| deletedSites | Ne | 
| apiManagementAccounts | Ne | 
| apiManagementAccounts/připojení | Ne | 
| apiManagementAccounts/connectionAcls | Ne | 
| apiManagementAccounts/API/připojení/connectionAcls | Ne | 
| apiManagementAccounts/API/connectionAcls | Ne | 
| apiManagementAccounts/apiAcls | Ne | 
| apiManagementAccounts/API/apiAcls | Ne | 
| apiManagementAccounts/rozhraní API | Ne | 
| apiManagementAccounts/API/localizedDefinitions | Ne | 
| apiManagementAccounts/API/připojení | Ne | 
| připojení | Ano | 
| customApis | Ano | 
| connectionGateways | Ano | 
| billingMeters | Ne | 
| verifyHostingEnvironmentVnet | Ne | 

## <a name="xrm"></a>XRM
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| organizations | Ne | 

## <a name="next-steps"></a>Další postup
Zjistěte, jak použít značky na prostředky, najdete v článku [používání značek k uspořádání prostředků Azure](resource-group-using-tags.md).
