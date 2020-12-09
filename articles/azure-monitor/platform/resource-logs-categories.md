---
title: Protokoly prostředků Azure Monitor podporují služby a kategorie.
description: Referenční informace o Azure Monitorch pochopit podporované služby a schéma událostí pro protokoly prostředků Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 06/03/2020
ms.openlocfilehash: 463b1d9d9c3ed1d94728874ba814554deb4f97c6
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920844"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Podporované kategorie pro protokoly prostředků Azure

> [!NOTE]
> Protokoly prostředků se dříve nazývaly diagnostické protokoly. Název se změnil v říjnu 2019, protože typy protokolů shromážděných Azure Monitor se posunuly tak, aby zahrnovaly víc než jenom prostředek Azure.

[Protokoly prostředků Azure monitor](./platform-logs-overview.md) jsou protokoly emitované službami Azure, které popisují fungování těchto služeb nebo prostředků. Všechny protokoly prostředků dostupné prostřednictvím Azure Monitor sdílejí společné schéma nejvyšší úrovně s flexibilitou pro každou službu, aby vygenerovala jedinečné vlastnosti pro vlastní události.

Kombinace typu prostředku (k dispozici ve `resourceId` Vlastnosti) a `category` jednoznačné určení schématu. Pro všechny protokoly prostředků s poli se specifickými službami se pak přidalo společné schéma pro různé kategorie protokolů. Další informace najdete v tématu [společné schéma a schéma specifické pro službu pro protokoly prostředků Azure]() .


## <a name="costs"></a>Náklady

 Existují náklady spojené s odesíláním a ukládáním dat do do Log Analytics a/nebo centra událostí. Protokoly prostředků jsou jedním z typů dat, která můžete odesílat do těchto umístění. [Pro export některých kategorií protokolů prostředků](https://azure.microsoft.com/pricing/details/monitor/)se účtují další náklady. Jiné jsou zdarma vývozní náklady. Konkrétní části jsou uvedeny v následující tabulce.

## <a name="supported-log-categories-per-resource-type"></a>Podporované kategorie protokolů na typ prostředku

Následuje seznam typů protokolů, které jsou k dispozici pro každý typ prostředku. 

Některé kategorie lze podporovat pouze pro konkrétní typy prostředků. Pokud se domníváte, že se vám nedaří prostředek, Prohlédněte si dokumentaci ke konkrétním prostředkům. Například kategorie Microsoft. SQL/servery/databáze nejsou k dispozici pro všechny typy databází. Další informace najdete v tématu [informace o protokolování diagnostiky SQL Database](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Pokud ještě chybí něco, můžete otevřít komentář k GitHubu na konci tohoto článku.
## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servery

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|Modul|Modul|
|Služba|Služba|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|GatewayLogs|Protokoly související s bránou ApiManagement|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/pružina

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|ApplicationConsole|Konzola aplikace|
|SystemLogs|Systémové protokoly|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|DscNodeStatus|Stav uzlu DSC|
|JobLogs|Protokoly úloh|
|JobStreams|Datové proudy úloh|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|ServiceLog|Protokoly služby|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/Workspaces

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|BlockchainApplication|Aplikace blockchain|
|FabricOrderer|Řazení prostředků infrastruktury|
|FabricPeer|Partnerský uzel Fabric|
|Proxy server|Proxy server|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. blockchain/cordaMembers

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|BlockchainApplication|Aplikace blockchain|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|WebApplicationFirewallLogs|Protokoly brány firewall pro web spustí aplikace|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profily

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AzureCdnAccessLog|Protokol přístupu k síti Azure CDN|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/Profiles/koncových bodů

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|CoreAnalytics|Získá metriky koncového bodu, například šířku pásma, odchozí přenos atd.|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|Událost toku pravidla skupiny zabezpečení sítě|Událost toku pravidla skupiny zabezpečení sítě|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. Cognitiveservices Account/účty

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|Auditování|Protokoly auditu|
|Operace RequestResponse|Protokoly žádostí a odpovědí|
|Trasování|Protokoly trasování|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/Registry

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|ContainerRegistryLoginEvents|Události přihlášení|
|ContainerRegistryRepositoryEvents|Protokoly RepositoryEvent|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|cluster – automatické škálování|Automatické škálování clusteru Kubernetes|
|kube-apiserver|Server Kubernetes API|
|Kube – audit|Kubernetes audit|
|Kube-Controller-Manager|Správce kontroleru Kubernetes|
|Kube – Scheduler|Plánovač Kubernetes|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AuditLogs|Protokoly auditu pro volání MiniRP|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. datacihly/pracovní prostory

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|accounts|Účty datacihlů|
|existující|Clustery datacihlů|
|dBFS|Systém souborů Databricks|
|instancePools|Fondy instancí|
|úlohy|Úlohy datacihlů|
|poznámkový blok|Poznámkový blok Databricks|
|záleží|Tajné kódy datacihlů|
|sqlPermissions|Datacihly SQLPermissions|
|protokoly|SSH datacihlů|
|pracovní prostor|Pracovní prostor datacihly|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/továrny

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|ActivityRuns|Spuštění protokolu aktivity kanálu|
|PipelineRuns|Protokol spuštění kanálu|
|TriggerRuns|Aktivační událost spuštění protokolu|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/účty

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|Auditování|Protokoly auditu|
|Žádosti|Protokoly žádostí|


## <a name="microsoftdatashareaccounts"></a>Microsoft. datashare/Accounts

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|ReceivedShareSnapshots|Přijaté snímky sdílené složky|
|SentShareSnapshots|Odeslané sdílené snímky|
|Sdílené složky|Sdílené složky|
|ShareSubscriptions|Sdílet předplatná|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servery

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|MySqlAuditLogs|Protokoly auditu MariaDB|
|MySqlSlowLogs|Protokoly serveru MariaDB|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|MySqlAuditLogs|Protokoly auditu MySQL|
|MySqlSlowLogs|Pomalé protokoly MySQL|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servery

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|MySqlAuditLogs|Protokoly auditu MySQL|
|MySqlSlowLogs|Protokoly serveru MySQL|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|PostgreSQLLogs|Protokoly serveru PostgreSQL|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servery

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|PostgreSQLLogs|Protokoly serveru PostgreSQL|
|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů PostgreSQL|
|QueryStoreWaitStatistics|Statistiky čekání na úložiště dotazů PostgreSQL|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|PostgreSQLLogs|Protokoly serveru PostgreSQL|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|CheckPoint|CheckPoint|
|Chyba|Chyba|
|Správa|Správa|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|CheckPoint|CheckPoint|
|Připojení|Připojení|
|Chyba|Chyba|
|HostRegistration|HostRegistration|
|Správa|Správa|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/pracovní prostory

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|CheckPoint|CheckPoint|
|Chyba|Chyba|
|Informační kanál|Informační kanál|
|Správa|Správa|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|C2DCommands|Příkazy C2D|
|C2DTwinOperations|C2D – zdvojené operace|
|Konfigurace|Konfigurace|
|Připojení|Připojení|
|D2CTwinOperations|D2CTwinOperations|
|DeviceIdentityOperations|Operace identity zařízení|
|DeviceStreams|Datové proudy zařízení (Preview)|
|DeviceTelemetry|Telemetrie zařízení|
|DirectMethods|Přímé metody|
|DistributedTracing|Distribuované trasování (Preview)|
|FileUploadOperations|Operace nahrávání souborů|
|JobsOperations|Operace úloh|
|Trasy|Trasy|
|TwinQueries|Zdvojené dotazy|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|DeviceOperations|Operace zařízení|
|ServiceOperations|Operace služby|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domény

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|DeliveryFailures|Protokoly chyb doručení|
|PublishFailures|Protokoly chyb publikování|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|DeliveryFailures|Protokoly chyb doručení|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témata

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|DeliveryFailures|Protokoly chyb doručení|
|PublishFailures|Protokoly chyb publikování|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/obory názvů

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|ArchiveLogs|Protokoly archivu|
|AutoScaleLogs|Protokoly automatického škálování|
|CustomerManagedKeyUserLogs|Protokoly zákaznických klíčů spravovaných zákazníky|
|EventHubVNetConnectionEvent|Protokoly připojení k virtuální síti nebo filtrování IP adres|
|KafkaCoordinatorLogs|Protokoly koordinátora Kafka|
|KafkaUserErrorLogs|Protokoly chyb uživatele Kafka|
|OperationalLogs|Provozní protokoly|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/Services

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AuditLogs|Protokoly auditu|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AutoscaleEvaluations|Vyhodnocení automatického škálování|
|AutoscaleScaleActions|Akce škálování automatického škálování|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/Components

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AppAvailabilityResults|Výsledky dostupnosti|
|AppBrowserTimings|Časování prohlížeče|
|AppDependencies|Závislosti|
|AppEvents|Události|
|AppExceptions|Výjimky|
|AppMetrics|Metriky|
|AppPageViews|Zobrazení stránek|
|AppPerformanceCounters|Čítače výkonu|
|AppRequests|Žádosti|
|AppSystemEvents|Systémové události|
|AppTraces|Trasování|


## <a name="microsoftkeyvaultvaults"></a>Trezory a trezory Microsoft.

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AuditEvent|Protokoly auditu|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clustery

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|Příkaz|Příkaz|
|FailedIngestion|Neúspěšné operace ingestování|
|IngestionBatching|Dávkování příjmu dat|
|Dotaz|Dotaz|
|SucceededIngestion|Úspěšné operace ingestování|
|TableDetails|Podrobnosti tabulky|
|TableUsageStatistics|Statistiky využití tabulek|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|IntegrationAccountTrackingEvents|Účet pro integraci sleduje události|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/Workflows

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|Modul|Diagnostické události modulu runtime pracovního postupu|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/pracovní prostory

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|KeyDeliveryRequests|Požadavky na doručení klíčů|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|ApplicationGatewayAccessLog|Protokol Application Gateway Access|
|ApplicationGatewayFirewallLog|Protokol brány Application Gateway firewall|
|ApplicationGatewayPerformanceLog|Protokol výkonu Application Gateway|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AzureFirewallApplicationRule|Azure Firewall pravidlo aplikace|
|AzureFirewallNetworkRule|Azure Firewall síťové pravidlo|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|BastionAuditLogs|Protokoly auditu bastionu|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|PeeringRouteLog|Protokoly tabulky směrování partnerských vztahů|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|FrontdoorAccessLog|Protokol přístupu Frontdoor|
|FrontdoorWebApplicationFirewallLog|Protokol Frontdoor firewallu webových aplikací|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|LoadBalancerAlertEvent|Load Balancer události výstrahy|
|LoadBalancerProbeHealthStatus|Stav testu Load Balancer|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|NetworkSecurityGroupEvent|Událost skupiny zabezpečení sítě|
|NetworkSecurityGroupFlowEvent|Událost toku pravidla skupiny zabezpečení sítě|
|NetworkSecurityGroupRuleCounter|Čítač pravidla skupiny zabezpečení sítě|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|DDoSMitigationFlowLogs|Protokoly DDoSch rozhodnutí o zmírnění rizik|
|DDoSMitigationReports|Sestavy zmírnění hrozeb DDoS|
|DDoSProtectionNotifications|Oznámení o ochraně DDoS|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|ProbeHealthStatusEvents|Událost výsledků testu stavu Traffic Manager|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|GatewayDiagnosticLog|Diagnostické protokoly brány|
|IKEDiagnosticLog|Diagnostické protokoly IKE|
|P2SDiagnosticLog|Diagnostické protokoly P2S|
|RouteDiagnosticLog|Protokoly diagnostiky směrování|
|TunnelDiagnosticLog|Protokoly diagnostiky tunelu|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|VMProtectionAlerts|Výstrahy ochrany virtuálních počítačů|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/– kapacity

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|Modul|Modul|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. RecoveryServices/trezory

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AddonAzureBackupAlerts|Doplněk Azure Backup data výstrah|
|AddonAzureBackupJobs|Doplněk Azure Backup data úlohy|
|AddonAzureBackupPolicy|Doplněk Azure Backup data zásad|
|AddonAzureBackupProtectedInstance|Doplněk Azure Backup data chráněné instance|
|AddonAzureBackupStorage|Doplněk Azure Backup data úložiště|
|AzureBackupReport|Azure Backup data vytváření sestav|
|AzureSiteRecoveryEvents|Události Azure Site Recovery|
|AzureSiteRecoveryJobs|Úlohy Azure Site Recovery|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery přenos dat chráněných disků|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery bodů obnovení|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikované položky|
|AzureSiteRecoveryReplicationDataUploadRate|Frekvence nahrávání dat replikace Azure Site Recovery|
|AzureSiteRecoveryReplicationStats|Statistika replikace Azure Site Recovery|
|CoreAzureBackup|Základní data Azure Backup|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/obory názvů

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|HybridConnectionsEvent|Události HybridConnections|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|OperationLogs|Protokoly operací|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/obory názvů

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|OperationalLogs|Provozní protokoly|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/Signaler

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AllLogs|Protokoly služby signalizace Azure|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|DevOpsOperationsAudit|Protokoly auditu operací DevOps|
|ResourceUsageStats|Statistika využití prostředků|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/databáze

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|Chyby|Chyby|
|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů|
|QueryStoreWaitStatistics|Statistika čekání na úložiště dotazů|
|SQLInsights|Přehledy SQL|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/servery/databáze

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AutomaticTuning|Automatické ladění|
|Bloky|Bloky|
|DatabaseWaitStatistics|Statistika čekání databáze|
|Zablokování|Zablokování|
|DevOpsOperationsAudit|Protokoly auditu operací DevOps|
|DmsWorkers|Pracovní procesy DMS|
|Chyby|Chyby|
|ExecRequests|Požadavky exec|
|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů|
|QueryStoreWaitStatistics|Statistika čekání na úložiště dotazů|
|RequestSteps|Kroky žádosti|
|SQLInsights|Přehledy SQL|
|SqlRequests|Požadavky SQL|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|
|Časové limity|Časové limity|
|Čeká|Čeká|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

Náklady: placeno jako v části protokoly platforem na [stránce s cenami Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/služby

Náklady: placeno jako v části protokoly platforem na [stránce s cenami Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

Náklady: placeno jako v části protokoly platforem na [stránce s cenami Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Kategorie |Zobrazovaný název kategorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

Náklady: placeno jako v části protokoly platforem na [stránce s cenami Azure monitor.](https://azure.microsoft.com/pricing/details/monitor/) 
 
|Kategorie |Zobrazovaný název kategorie|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|Vytváření obsahu|Vytváření obsahu|
|Spuštění|Spuštění|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. synapse/pracovní prostory

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|BuiltinSqlReqsEnded|Byly ukončeny předdefinované požadavky na fond SQL.|
|GatewayApiRequests|Žádosti rozhraní API brány synapse|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|
|SynapseRbacOperations|Synapse operace RBAC|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. synapse/pracovní prostory/bigDataPools

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|BigDataPoolAppsEnded|Aplikace s velkým objemem dat se ukončily|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. synapse/pracovní prostory/sqlPools

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|DmsWorkers|Pracovní procesy DMS|
|ExecRequests|Požadavky exec|
|RequestSteps|Kroky žádosti|
|SqlRequests|Požadavky SQL|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|
|Čeká|Čeká|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

Náklady: zdarma 

|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment protokoly platformy|


## <a name="microsoftwebsites"></a>Microsoft. Web/weby

Náklady: zdarma 


|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AppServiceAppLogs|App Service protokoly aplikací|
|AppServiceAuditLogs|Přístup k protokolům auditu|
|AppServiceConsoleLogs|Protokoly konzoly App Service|
|AppServiceFileAuditLogs|Protokoly změny obsahu webu|
|AppServiceHTTPLogs|Protokoly HTTP|
|FunctionAppLogs|Protokoly aplikací funkcí|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/weby/sloty

Náklady: zdarma 


|Kategorie |Zobrazovaný název kategorie|
|---|---|
|AppServiceAppLogs|App Service protokoly aplikací|
|AppServiceAuditLogs|Přístup k protokolům auditu|
|AppServiceConsoleLogs|Protokoly konzoly App Service|
|AppServiceFileAuditLogs|Protokoly změny obsahu webu|
|AppServiceHTTPLogs|Protokoly HTTP|
|FunctionAppLogs|Protokoly aplikací funkcí|


## <a name="next-steps"></a>Další kroky

* [Další informace o protokolech prostředků](./platform-logs-overview.md)
* [**Event Hubs** protokolů prostředku prostředku streamování](./resource-logs.md#send-to-azure-event-hubs)
* [Změna nastavení diagnostiky protokolu prostředků pomocí Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Analýza protokolů z Azure Storage pomocí Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

