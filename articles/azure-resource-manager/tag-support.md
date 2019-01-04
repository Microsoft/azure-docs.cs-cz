---
title: Podpora značky Azure Resource Manageru pro prostředky
description: Ukazuje, jaké typy prostředků Azure podporují značky. Poskytuje podrobnosti pro všechny služby Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/02/2019
ms.author: tomfitz
ms.openlocfilehash: 50ea7a2446b5560bd208b2da128fa877068ce452
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000287"
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
| addsservices | Ne |
| aadsupportcases | Ne | 
| agenti | Ne | 
| anonymousapiusers | Ne | 
| konfigurace | Ne | 
| Protokoly | Ne | 
| sestavy | Ne | 
| services | Ne | 
| servicehealthmetrics | Ne | 

## <a name="aks"></a>AKS
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| managedClusters | Ano | 

## <a name="analysis-services"></a>Analysis Services
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| serverů | Ano | 

## <a name="api-hubs"></a>Centra rozhraní API
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| apiManagementAccounts | Ne | 
| apiManagementAccounts/rozhraní API | Ne | 
| apiManagementAccounts/connectionAcls | Ne | 
| apiManagementAccounts/connectionProviders | Ne | 
| apiManagementAccounts/connectionProviderAcls | Ne | 
| apiManagementAccounts/připojení | Ne | 

## <a name="api-management"></a>API Management
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| služba | Ano | 

## <a name="automation"></a>Automation
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| AutomationAccounts | Ano | 
| automationAccounts/konfigurace | Ano | 
| automationAccounts/úloh | Ne | 
| automationAccounts/sady runbook | Ano | 
| automationAccounts/softwareUpdateConfigurations | Ne | 
| automationAccounts/webhooků | Ne | 

## <a name="azure-database-for-mariadb"></a>Azure Database for MariaDB
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| serverů | Ano | 
| servery pro/konfigurace | Ne |
| servery pro/databáze | Ne |
| servery pro/firewallRules | Ne |
| servery pro/recoverableServers | Ne | 
| servery pro/securityAlertPolicies | Ne |
| servery pro/virtualNetworkRules | Ne | 

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| serverů | Ano | 
| servery pro/konfigurace | Ne |
| servery pro/databáze | Ne |
| servery pro/firewallRules | Ne |
| servery pro/recoverableServers | Ne | 
| servery pro/securityAlertPolicies | Ne |
| servery pro/virtualNetworkRules | Ne | 

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| serverů | Ano | 
| servery pro/poradci | Ne | 
| servery pro/konfigurace | Ne |
| servery pro/databáze | Ne |
| servery pro/firewallRules | Ne |
| servery pro/queryTexts | Ne | 
| servery pro/recoverableServers | Ne | 
| servery pro/securityAlertPolicies | Ne |
| servery pro/topQueryStatistics | Ne | 
| servery pro/virtualNetworkRules | Ne | 
| servery pro/waitStatistics | Ne | 

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
| edgenodes | Ne | 
| Profily | Ano | 
| profily a koncových bodů | Ano | 
| profily/koncové body/customdomains | Ne | 
| profily/koncové body/zdroje | Ne | 
| validateProbe | Ne | 

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
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Ne | 
| virtualNetworks/virtualNetworkPeerings | Ne | 

## <a name="classic-storage"></a>Klasického úložiště.
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| storageAccounts/services | Ne | 
| storageAccounts/services/diagnosticSettings | Ne | 

## <a name="compute"></a>Compute
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| availabilitySets | Ano | 
| Disky | Ano | 
| images | Ano | 
| restorePointCollections | Ano | 
| restorePointCollections/restorePoints | Ne | 
| sharedVMImages | Ano | 
| sharedVMImages/verze | Ano | 
| snímky | Ano | 
| virtuálních počítačů | Ano | 
| virtuálních počítačů/diagnosticSettings | Ne | 
| virtuálních počítačů/rozšíření | Ano | 
| virtuálních počítačů/metricDefinitions | Ne | 
| virtualMachineScaleSets | Ano | 
| virtualMachineScaleSets/rozšíření | Ne | 
| virtualMachineScaleSets/síťová | Ne | 
| virtualMachineScaleSets/publicIPAddresses | Ne | 
| virtualMachineScaleSets/virtuálních počítačů | Ne | 
| virtualMachineScaleSets/virtuálních počítačů/síťová | Ne | 

## <a name="container"></a>Kontejner
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| containerGroups | Ano | 

## <a name="container-instance"></a>Instance kontejneru
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| containerGroups | Ano | 
| serviceAssociationLinks | Ne | 

## <a name="container-registry"></a>Container Registry
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Registry | Ano | 
| Registry/replikace | Ano |
| Registry nebo úloh | Ano |
| Registry a webhooky | Ano |

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

## <a name="data-box"></a>Data Box
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| úlohy | Ano | 

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
| dataFactories/diagnosticSettings | Ne | 
| dataFactories/metricDefinitions | Ne | 
| dataFactorySchema | Ne | 
| objekty pro vytváření | Ano | 
| objekty pro vytváření/integrationRuntimes | Ne | 

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
| praktická cvičení/artifactsources | Ano |
| praktická cvičení a náklady | Ano |
| praktická cvičení/customimages | Ano |
| praktická cvičení a vzorců | Ano |
| praktická cvičení/notificationchannels | Ano |
| praktická cvičení/policysets/zásady | Ano |
| praktická cvičení a plány | Ano |
| praktická cvičení/serviceRunners | Ano | 
| praktická cvičení/uživatelé | Ano |
| praktická cvičení nebo uživatele/disky | Ano |
| praktická cvičení nebo uživatele/prostředími | Ano |
| praktická cvičení/uživatele/tajných klíčů | Ano |
| praktická cvičení/uživatele/servicefabrics | Ano |
| praktická cvičení/uživatele/servicefabrics/plány | Ano |
| testovací prostředí a virtuálních počítačů | Ano | 
| testovací prostředí a virtuálních počítačů a plány | Ano |
| praktická cvičení/virtualnetworks | Ano |
| Plány | Ano | 

## <a name="dynamics-lcs"></a>Dynamics LCS
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| lcsprojects | Ne | 
| lcsprojects nebo konektory | Ne | 
| lcsprojects/clouddeployments | Ne | 

## <a name="event-grid"></a>Event Grid
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| domény | Ano | 
| domény a témat | Ne | 
| eventSubscriptions | Ne | 
| extensionTopics | Ne | 
| témata | Ano | 
| topicTypes | Ne | 

## <a name="event-hub"></a>Centrum událostí
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Clustery | Ano | 
| Obory názvů | Ano | 
| obory názvů a autorizačních pravidel | Ne |
| obory názvů/disasterRecoveryConfigs | Ne |
| obory názvů/eventhubs | Ne |
| obory názvů/eventhubs/autorizačních pravidel | Ne |
| obory názvů/eventhubs/consumergroups | Ne |

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
| actionGroups | Ano |
| Upozorněníprotokoluaktivit | Ano |
| alertrules | Ano |
| automatedExportSettings | Ne | 
| Součásti | Ano | 
| součásti/události | Ne | 
| součásti/metriky | Ne | 
| součásti/pricingPlans | Ne | 
| součásti či dotazu | Ne | 
| Protokoly | Ne | 
| metricAlerts | Ano |
| migrateToNewPricingModel | Ne | 
| myWorkbooks | Ne | 
| dotazy | Ne | 
| rollbackToLegacyPricingModel | Ne | 
| scheduledqueryrules | Ano | 
| webové testy | Ano | 
| Sešity | Ano | 

## <a name="key-vault"></a>Key Vault
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| deletedVaults | Ne | 
| trezory služby | Ano | 
| trezory/accessPolicies | Ne | 
| Trezory/tajných klíčů | Ne | 

## <a name="log-analytics"></a>Log Analytics
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Protokoly | Ne | 

## <a name="logic"></a>Logika
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| integrationAccounts | Ano | 
| Pracovní postupy | Ano | 

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
| mediaservices/accountFilters | Ne | 
| mediaservices/prostředky | Ne | 
| mediaservices/prostředky/assetFilters | Ne | 
| mediaservices/contentKeyPolicies | Ne | 
| mediaservices/eventGridFilters | Ne | 
| mediaservices/liveEventOperations | Ne | 
| mediaservices/liveEvents | Ano | 
| mediaservices/liveEvents/liveOutputs | Ne | 
| mediaservices/liveOutputOperations | Ne | 
| mediaservices/koncové body streamování | Ano | 
| mediaservices/streamingEndpointOperations | Ne | 
| mediaservices/streamingLocators | Ne | 
| mediaservices/streamingPolicies | Ne | 
| mediaservices nebo transformací | Ne | 
| mediaservices/transformace/úloh | Ne | 

## <a name="network"></a>Síť
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| applicationGateways | Ano | 
| Jenom | Ano | 
| azureFirewalls | Ano | 
| připojení | Ano | 
| ddosProtectionPlans | Ano | 
| expressRouteCircuits | Ano | 
| frontdoors | Ano | 
| frontdoorWebApplicationFirewallPolicies | Ano | 
| interfaceEndpoints | Ano | 
| Load Balancerech | Ano | 
| localNetworkGateways | Ano | 
| networkIntentPolicies | Ano | 
| Síťová | Ano | 
| networkProfiles | Ano | 
| networkSecurityGroups | Ano | 
| networkWatchers | Ano | 
| networkWatchers/connectionMonitors | Ano | 
| networkWatchers/přehledů | Ano | 
| networkWatchers/pingMeshes | Ano | 
| privateLinkServices | Ano | 
| publicIPAddresses | Ano | 
| publicIPPrefixes | Ano | 
| routeFilters | Ano | 
| routeTables | Ano | 
| serviceEndpointPolicies | Ano | 
| virtualHubs | Ano | 
| virtualNetworks | Ano | 
| virtualNetworkGateways | Ano | 
| virtualNetworkTaps | Ano | 
| virtualWans | Ano | 
| vpnGateways | Ano | 
| vpnSites | Ano | 
| webApplicationFirewallPolicies | Ano | 

## <a name="notification-hubs"></a>Notification Hubs
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Obory názvů | Ano | 
| obory názvů a notificationHubs | Ano | 

## <a name="operational-insights"></a>Operational Insights
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| pracovní prostory | Ano |
| pracovní prostory/zdroje dat | Ano |
| pracovní prostory/linkedServices | Ano |
| pracovní prostory/savedSearches | Ne |
| pracovní prostory/storageInsightConfigs | Ano |

## <a name="operations-management"></a>Správa operací
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Řešení | Ne |

## <a name="portal"></a>Portál
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| Řídicí panely | Ano | 

## <a name="portal-sdk"></a>Portál SDK
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| rootResources | Ano | 

## <a name="power-bi"></a>Power BI
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| workspaceCollections | Ano | 

## <a name="recovery-services"></a>Recovery Services
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| backupProtectedItems | Ne | 
| trezory služby | Ano | 

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
| resourceHealthMetadata | Ne | 
| searchServices | Ano | 

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

## <a name="sql"></a>SQL
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| umístění/instanceFailoverGroups | Ne |
| managedInstances | Ano |
| managedInstances/databáze | Ano |
| managedInstances/databází/backupShortTermRetentionPolicies | Ne |
| managedInstances/databází/schémata/tabulek/sloupce/sensitivityLabels | Ne |
| managedInstances/databází/vulnerabilityAssessments | Ne |
| managedInstances/databází/vulnerabilityAssessments/pravidel/směrné plány | Ne |
| managedInstances/encryptionProtector | Ne |
| managedInstances/klíče | Ne |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Ne |
| managedInstances/vulnerabilityAssessments | Ne |
| serverů | Ano |
| servery pro/správci | Ne |
| servery pro/poradci | Ne |
| servery pro/auditingSettings | Ne |
| servery pro/backupLongTermRetentionVaults | Ne |
| servery pro/communicationLinks | Ne |
| servery pro/connectionPolicies | Ne |
| servery pro/databáze | Ano |
| servery pro/databáze nebo poradci | Ne |
| servery pro/databází/auditingSettings | Ne |
| servery pro/databází/backupLongTermRetentionPolicies | Ne |
| servery pro/databází/backupShortTermRetentionPolicies | Ne |
| servery pro/databází/connectionPolicies | Ne |
| servery pro/databází/dataMaskingPolicies | Ne |
| servery pro/databází/dataMaskingPolicies/pravidla | Ne |
| servery pro/databází/extendedAuditingSettings | Ne |
| servery pro/databází/rozšíření | Ne |
| servery pro/databází/geoBackupPolicies | Ne |
| servery pro/databází/schémata/tabulek/sloupce/sensitivityLabels | Ne |
| servery pro/databází/securityAlertPolicies | Ne |
| servery pro/databází/syncGroups | Ne |
| servery pro/databází/syncGroups/syncMembers | Ne |
| servery pro/databází/transparentDataEncryption | Ne |
| servery pro/databází/vulnerabilityAssessments | Ne |
| servery pro/databází/vulnerabilityAssessments/pravidel/směrné plány | Ne |
| servery pro/disasterRecoveryConfiguration | Ne |
| servery pro/dnsAliases | Ne |
| servery pro/elasticPools | Ano |
| servery pro/encryptionProtector | Ne |
| servery pro/extendedAuditingSettings | Ne |
| servery pro/failoverGroups | Ano |
| servery pro/firewallRules | Ne |
| servery pro/jobAgents | Ano |
| servery pro/jobAgents/přihlašovacích údajů | Ne |
| servery pro/jobAgents/úloh | Ne |
| servery pro/jobAgents/úlohy/spuštění | Ne |
| servery pro/jobAgents/úlohy/kroky | Ne |
| servery pro/jobAgents/targetGroups | Ne |
| servery pro/klíče | Ne |
| servery pro/securityAlertPolicies | Ne |
| servery pro/syncAgents | Ne |
| servery pro/virtualNetworkRules | Ne |
| servery pro/vulnerabilityAssessments | Ne |

## <a name="sql-virtual-machine"></a>Virtuálnímu počítači SQL
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| DWVM | Ano | 

## <a name="storage"></a>Storage
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| storageAccounts | Ano | 
| storageAccounts/blobServices | Ne | 
| storageAccounts/fileServices | Ne | 
| storageAccounts/queueServices | Ne | 
| storageAccounts/services | Ne | 
| storageAccounts/services/metricDefinitions | Ne | 
| storageAccounts/tableServices | Ne | 

## <a name="storage-sync"></a>Synchronizace úložiště
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| storageSyncServices | Ano | 
| storageSyncServices/registeredServers | Ne | 
| storageSyncServices/syncGroups | Ne | 
| storageSyncServices/syncGroups/cloudEndpoints | Ne | 
| storageSyncServices/syncGroups/serverEndpoints | Ne | 
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
| účet a rozšíření | Ano | 
| účet nebo projektu | Ano | 

## <a name="web"></a>Web
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| apiManagementAccounts | Ne | 
| apiManagementAccounts/apiAcls | Ne | 
| apiManagementAccounts/rozhraní API | Ne | 
| apiManagementAccounts/API/apiAcls | Ne | 
| apiManagementAccounts/API/connectionAcls | Ne | 
| apiManagementAccounts/API/připojení | Ne | 
| apiManagementAccounts/API/připojení/connectionAcls | Ne | 
| apiManagementAccounts/API/localizedDefinitions | Ne | 
| apiManagementAccounts/connectionAcls | Ne | 
| apiManagementAccounts/připojení | Ne | 
| billingMeters | Ne | 
| certifikáty | Ano | 
| connectionGateways | Ano | 
| připojení | Ano | 
| customApis | Ano | 
| deletedSites | Ne | 
| functions | Ne | 
| hostingEnvironments | Ano | 
| hostingEnvironments/metriky | Ne | 
| hostingEnvironments/multiRolePools | Ne | 
| hostingEnvironments/workerPools | Ne | 
| publishingUsers | Ne | 
| serverových farem | Ano | 
| serverových farem/pracovních procesů | Ne | 
| Weby | Ano | 
| servery/domainOwnershipIdentifiers | Ne | 
| servery/hostNameBindings | Ne | 
| servery a instancí | Ne | 
| servery/instance/rozšíření | Ne | 
| servery/metriky | Ne | 
| servery/premieraddons | Ano | 
| servery a sloty | Ano | 
| servery/sloty/hostNameBindings | Ne | 
| servery/sloty/instance | Ne | 
| servery/sloty/instance/rozšíření | Ne | 
| servery/sloty/metriky | Ne | 
| sourceControls | Ne | 
| Ověření | Ne | 
| verifyHostingEnvironmentVnet | Ne | 

## <a name="xrm"></a>XRM
| Typ prostředku | Podporuje značky |
| ------------- | ----------- |
| organizations | Ne | 

## <a name="next-steps"></a>Další postup
Zjistěte, jak použít značky na prostředky, najdete v článku [používání značek k uspořádání prostředků Azure](resource-group-using-tags.md).
