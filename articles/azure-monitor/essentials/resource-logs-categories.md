---
title: Protokoly prostředků Azure Monitor podporují služby a kategorie.
description: Referenční informace o Azure Monitorch pochopit podporované služby a schéma událostí pro protokoly prostředků Azure.
ms.topic: reference
ms.date: 01/29/2021
ms.openlocfilehash: 9a04d0f470522dd4689d604756ffd25e70c5d456
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033142"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Podporované kategorie pro protokoly prostředků Azure

> [!NOTE]
> Protokoly prostředků se dříve nazývaly diagnostické protokoly. Název se změnil v říjnu 2019, protože typy protokolů shromážděných Azure Monitor se posunuly tak, aby zahrnovaly víc než jenom prostředek Azure.

[Protokoly prostředků Azure monitor](../essentials/platform-logs-overview.md) jsou protokoly emitované službami Azure, které popisují fungování těchto služeb nebo prostředků. Všechny protokoly prostředků dostupné prostřednictvím Azure Monitor sdílejí společné schéma nejvyšší úrovně s flexibilitou pro každou službu, aby vygenerovala jedinečné vlastnosti pro vlastní události.

Kombinace typu prostředku (k dispozici ve `resourceId` Vlastnosti) a `category` jednoznačné určení schématu. K dispozici je společné schéma pro všechny protokoly prostředků s poli pro konkrétní služby, které se pak přidají do různých kategorií protokolů. Další informace najdete v tématech [běžné a specifické schéma pro protokoly prostředků Azure]() .


## <a name="costs"></a>Náklady

Existují náklady spojené s odesíláním a ukládáním dat do Log Analytics, Azure Storage a/nebo centra událostí. Můžete platit za náklady a získat data do těchto umístění a uchovávat je.  Protokoly prostředků jsou jedním z typů dat, která můžete odesílat do těchto umístění. 

K exportu některých kategorií protokolů prostředků do těchto umístění se účtují další náklady. Tyto protokoly s náklady na export jsou uvedeny v následující tabulce. Další informace o těchto cenách najdete v části protokoly platformy na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="supported-log-categories-per-resource-type"></a>Podporované kategorie protokolů na typ prostředku

Následuje seznam typů protokolů, které jsou k dispozici pro každý typ prostředku. 

Některé kategorie lze podporovat pouze pro konkrétní typy prostředků. Pokud se domníváte, že se vám nedaří prostředek, Prohlédněte si dokumentaci ke konkrétním prostředkům. Například kategorie Microsoft. SQL/servery/databáze nejsou k dispozici pro všechny typy databází. Další informace najdete v tématu [informace o protokolování diagnostiky SQL Database](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md). 

Pokud se domníváte, že chybí něco, můžete otevřít komentář k GitHubu v dolní části tohoto článku.
## <a name="microsoftaaddomainservices"></a>Microsoft. AAD/domainServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AccountLogon|AccountLogon|Ne|
|Službu AccountManagement|Službu AccountManagement|Ne|
|DetailTracking|DetailTracking|Ne|
|DirectoryServiceAccess|DirectoryServiceAccess|Ne|
|LogonLogoff|LogonLogoff|Ne|
|ObjectAccess|ObjectAccess|Ne|
|PolicyChange|PolicyChange|Ne|
|PrivilegeUse|PrivilegeUse|Ne|
|SystemSecurity|SystemSecurity|Ne|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servery

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Modul|Modul|Ne|
|Služba|Služba|Ne|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|GatewayLogs|Protokoly související s bránou ApiManagement|Ne|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|HttpRequest|Požadavky HTTP|Ano|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/pružina

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ApplicationConsole|Konzola aplikace|Ne|
|SystemLogs|Systémové protokoly|Ne|


## <a name="microsoftattestationattestationproviders"></a>Microsoft. Attestation/attestationProviders

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AuditEvent|Kategorie protokolu zpráv AuditEvent|Ne|
|Výměna|Kategorie protokolu chybových zpráv.|Ne|
|INF|Kategorie protokolu informativní zprávy|Ne|
|WRN|Kategorie protokolu zpráv s upozorněním|Ne|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DscNodeStatus|Stav uzlu DSC|Ne|
|JobLogs|Protokoly úloh|Ne|
|JobStreams|Datové proudy úloh|Ne|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ServiceLog|Protokoly služby|Ne|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/Workspaces

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|Ne|
|BaiClusterNodeEvent|BaiClusterNodeEvent|Ne|
|BaiJobEvent|BaiJobEvent|Ne|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BlockchainApplication|Aplikace blockchain|Ne|
|FabricOrderer|Řazení prostředků infrastruktury|Ne|
|FabricPeer|Partnerský uzel Fabric|Ne|
|Proxy server|Proxy server|Ne|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. blockchain/cordaMembers

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BlockchainApplication|Aplikace blockchain|Ne|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BotRequest|Požadavky z kanálů na robota|Ne|
|DependencyRequest|Požadavky na závislosti|Ne|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|WebApplicationFirewallLogs|Protokoly brány firewall pro web spustí aplikace|Ne|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profily

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AzureCdnAccessLog|Protokol přístupu k síti Azure CDN|Ne|
|FrontDoorAccessLog|Protokol přístupu FrontDoor|Ano|
|FrontDoorHealthProbeLog|Protokol sondy stavu FrontDoor|Ano|
|FrontDoorWebApplicationFirewallLog|Protokol WebApplicationFirewall FrontDoor|Ano|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/Profiles/koncových bodů

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|CoreAnalytics|Získá metriky koncového bodu, například šířku pásma, odchozí přenos atd.|Ne|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Událost toku pravidla skupiny zabezpečení sítě|Událost toku pravidla skupiny zabezpečení sítě|Ne|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. Cognitiveservices Account/účty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Protokoly auditu|Ne|
|Operace RequestResponse|Protokoly žádostí a odpovědí|Ne|
|Trasování|Protokoly trasování|Ne|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ChatOperational|Protokoly provozního chatu|Ne|
|SMSOperational|Provozní protokoly SMS|Ne|
|Využití|Záznamy o využití|Ne|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/Registry

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ContainerRegistryLoginEvents|Události přihlášení|Ne|
|ContainerRegistryRepositoryEvents|Protokoly RepositoryEvent|Ne|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|cluster – automatické škálování|Automatické škálování clusteru Kubernetes|Ne|
|chráněn|chráněn|Ne|
|kube-apiserver|Server Kubernetes API|Ne|
|Kube – audit|Kubernetes audit|Ne|
|Kube – audit – správce|Protokoly správce auditu Kubernetes|Ne|
|Kube-Controller-Manager|Správce kontroleru Kubernetes|Ne|
|Kube – Scheduler|Plánovač Kubernetes|Ne|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AuditLogs|Protokoly auditu pro volání MiniRP|Ne|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft. D365CustomerInsights/instance

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Auditování událostí|Ne|
|Provoz|Provozní události|Ne|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. datacihly/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|accounts|Účty datacihlů|Ne|
|existující|Clustery datacihlů|Ne|
|dBFS|Systém souborů Databricks|Ne|
|instancePools|Fondy instancí|Ne|
|úlohy|Úlohy datacihlů|Ne|
|poznámkový blok|Poznámkový blok Databricks|Ne|
|záleží|Tajné kódy datacihlů|Ne|
|sqlPermissions|Datacihly SQLPermissions|Ne|
|protokoly|SSH datacihlů|Ne|
|pracovní prostor|Pracovní prostor datacihly|Ne|


## <a name="microsoftdatacollaborationworkspaces"></a>Spolupráce a pracovní prostory Microsoft.

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|CollaborationAudit|Audit spolupráce|Ano|
|Datové prostředky|Datové Assety|Ne|
|Pipelines|Pipelines|Ne|
|Nabídek|Nabídek|Ne|
|Skripty|Skripty|Ne|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/továrny

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ActivityRuns|Spuštění protokolu aktivity kanálu|Ne|
|PipelineRuns|Protokol spuštění kanálu|Ne|
|SSISIntegrationRuntimeLogs|Protokoly SSIS Integration runtime|Ne|
|SSISPackageEventMessageContext|Kontext zprávy události balíčku SSIS|Ne|
|SSISPackageEventMessages|Zprávy o událostech balíčku SSIS|Ne|
|SSISPackageExecutableStatistics|SSIS – Statistika spustitelných balíčků|Ne|
|SSISPackageExecutionComponentPhases|Fáze součástí provádění balíčku SSIS|Ne|
|SSISPackageExecutionDataStatistics|Statistiky dat exeution balíčku SSIS|Ne|
|TriggerRuns|Aktivační událost spuštění protokolu|Ne|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/účty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Protokoly auditu|Ne|
|Žádosti|Protokoly žádostí|Ne|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/účty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Protokoly auditu|Ne|
|Žádosti|Protokoly žádostí|Ne|


## <a name="microsoftdatashareaccounts"></a>Microsoft. datashare/Accounts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ReceivedShareSnapshots|Přijaté snímky sdílené složky|Ne|
|SentShareSnapshots|Odeslané sdílené snímky|Ne|
|Sdílené složky|Sdílené složky|Ne|
|ShareSubscriptions|Sdílet předplatná|Ne|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servery

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|MySqlAuditLogs|Protokoly auditu MariaDB|Ne|
|MySqlSlowLogs|Protokoly serveru MariaDB|Ne|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|MySqlAuditLogs|Protokoly auditu MySQL|Ne|
|MySqlSlowLogs|Pomalé protokoly MySQL|Ne|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servery

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|MySqlAuditLogs|Protokoly auditu MySQL|Ne|
|MySqlSlowLogs|Protokoly serveru MySQL|Ne|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|PostgreSQLLogs|Protokoly serveru PostgreSQL|Ne|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servery

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|PostgreSQLLogs|Protokoly serveru PostgreSQL|Ne|
|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů PostgreSQL|Ne|
|QueryStoreWaitStatistics|Statistiky čekání na úložiště dotazů PostgreSQL|Ne|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|PostgreSQLLogs|Protokoly serveru PostgreSQL|Ne|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|CheckPoint|CheckPoint|Ne|
|Chyba|Chyba|Ne|
|Správa|Správa|Ne|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|Ne|
|CheckPoint|CheckPoint|Ne|
|Připojení|Připojení|Ne|
|Chyba|Chyba|Ne|
|HostRegistration|HostRegistration|Ne|
|Správa|Správa|Ne|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|CheckPoint|CheckPoint|Ne|
|Chyba|Chyba|Ne|
|Informační kanál|Informační kanál|Ne|
|Správa|Správa|Ne|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/ElasticPools/IotHubTenants

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|C2DCommands|Příkazy C2D|Ne|
|C2DTwinOperations|C2D – zdvojené operace|Ne|
|Konfigurace|Konfigurace|Ne|
|Připojení|Připojení|Ne|
|D2CTwinOperations|D2CTwinOperations|Ne|
|DeviceIdentityOperations|Operace identity zařízení|Ne|
|DeviceStreams|Datové proudy zařízení (Preview)|Ne|
|DeviceTelemetry|Telemetrie zařízení|Ne|
|DirectMethods|Přímé metody|Ne|
|DistributedTracing|Distribuované trasování (Preview)|Ne|
|FileUploadOperations|Operace nahrávání souborů|Ne|
|JobsOperations|Operace úloh|Ne|
|Trasy|Trasy|Ne|
|TwinQueries|Zdvojené dotazy|Ne|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|C2DCommands|Příkazy C2D|Ne|
|C2DTwinOperations|C2D – zdvojené operace|Ne|
|Konfigurace|Konfigurace|Ne|
|Připojení|Připojení|Ne|
|D2CTwinOperations|D2CTwinOperations|Ne|
|DeviceIdentityOperations|Operace identity zařízení|Ne|
|DeviceStreams|Datové proudy zařízení (Preview)|Ne|
|DeviceTelemetry|Telemetrie zařízení|Ne|
|DirectMethods|Přímé metody|Ne|
|DistributedTracing|Distribuované trasování (Preview)|Ne|
|FileUploadOperations|Operace nahrávání souborů|Ne|
|JobsOperations|Operace úloh|Ne|
|Trasy|Trasy|Ne|
|TwinQueries|Zdvojené dotazy|Ne|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeviceOperations|Operace zařízení|Ne|
|ServiceOperations|Operace služby|Ne|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|Ne|
|EventRoutesOperation|EventRoutesOperation|Ne|
|ModelsOperation|ModelsOperation|Ne|
|QueryOperation|QueryOperation|Ne|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|CassandraRequests|CassandraRequests|Ne|
|ControlPlaneRequests|ControlPlaneRequests|Ne|
|DataPlaneRequests|DataPlaneRequests|Ne|
|GremlinRequests|GremlinRequests|Ne|
|MongoRequests|MongoRequests|Ne|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|Ne|
|PartitionKeyStatistics|PartitionKeyStatistics|Ne|
|QueryRuntimeStatistics|QueryRuntimeStatistics|Ne|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domény

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeliveryFailures|Protokoly chyb doručení|Ne|
|PublishFailures|Protokoly chyb publikování|Ne|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeliveryFailures|Protokoly chyb doručení|Ne|
|PublishFailures|Protokoly chyb publikování|Ne|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeliveryFailures|Protokoly chyb doručení|Ne|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeliveryFailures|Protokoly chyb doručení|Ne|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témata

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeliveryFailures|Protokoly chyb doručení|Ne|
|PublishFailures|Protokoly chyb publikování|Ne|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/obory názvů

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ArchiveLogs|Protokoly archivu|Ne|
|AutoScaleLogs|Protokoly automatického škálování|Ne|
|CustomerManagedKeyUserLogs|Protokoly zákaznických klíčů spravovaných zákazníky|Ne|
|EventHubVNetConnectionEvent|Protokoly připojení k virtuální síti nebo filtrování IP adres|Ne|
|KafkaCoordinatorLogs|Protokoly koordinátora Kafka|Ne|
|KafkaUserErrorLogs|Protokoly chyb uživatele Kafka|Ne|
|OperationalLogs|Provozní protokoly|Ne|


## <a name="microsoftexperimentationexperimentworkspaces"></a>Microsoft. experimentování/experimentWorkspaces

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Žádost|Žádost|Ne|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/Services

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AuditLogs|Protokoly auditu|Ne|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/autoscalesettings

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AutoscaleEvaluations|Vyhodnocení automatického škálování|Ne|
|AutoscaleScaleActions|Akce škálování automatického škálování|Ne|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/Components

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AppAvailabilityResults|Výsledky dostupnosti|Ne|
|AppBrowserTimings|Časování prohlížeče|Ne|
|AppDependencies|Závislosti|Ne|
|AppEvents|Události|Ne|
|AppExceptions|Výjimky|Ne|
|AppMetrics|Metriky|Ne|
|AppPageViews|Zobrazení stránek|Ne|
|AppPerformanceCounters|Čítače výkonu|Ne|
|AppRequests|Žádosti|Ne|
|AppSystemEvents|Systémové události|Ne|
|AppTraces|Trasování|Ne|


## <a name="microsoftiotspacesgraph"></a>Microsoft. IoTSpaces/Graph

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Auditování|Ne|
|Výchozí přenos dat|Výchozí přenos dat|Ne|
|Příchozí přenos dat|Příchozí přenos dat|Ne|
|Provoz|Provoz|Ne|
|Trasování|Trasování|Ne|
|UserDefinedFunction|UserDefinedFunction|Ne|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. managedhsms – trezor/

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AuditEvent|Událost auditu|Ne|


## <a name="microsoftkeyvaultvaults"></a>Trezory a trezory Microsoft.

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AuditEvent|Protokoly auditu|Ne|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clustery

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Příkaz|Příkaz|Ne|
|FailedIngestion|Neúspěšné operace ingestování|Ne|
|IngestionBatching|Dávkování příjmu dat|Ne|
|Dotaz|Dotaz|Ne|
|SucceededIngestion|Úspěšné operace ingestování|Ne|
|TableDetails|Podrobnosti tabulky|Ne|
|TableUsageStatistics|Statistiky využití tabulek|Ne|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|IntegrationAccountTrackingEvents|Účet pro integraci sleduje události|Ne|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/Workflows

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Modul|Diagnostické události modulu runtime pracovního postupu|Ne|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|Ne|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|Ne|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|Ne|
|AmlComputeJobEvent|AmlComputeJobEvent|Ne|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|Ne|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|KeyDeliveryRequests|Požadavky na doručení klíčů|Ne|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ApplicationGatewayAccessLog|Protokol Application Gateway Access|Ne|
|ApplicationGatewayFirewallLog|Protokol brány Application Gateway firewall|Ne|
|ApplicationGatewayPerformanceLog|Protokol výkonu Application Gateway|Ne|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AzureFirewallApplicationRule|Azure Firewall pravidlo aplikace|Ne|
|AzureFirewallDnsProxy|Azure Firewall DNS proxy|Ne|
|AzureFirewallNetworkRule|Azure Firewall síťové pravidlo|Ne|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BastionAuditLogs|Protokoly auditu bastionu|Ne|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|PeeringRouteLog|Protokoly tabulky směrování partnerských vztahů|Ne|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|FrontdoorAccessLog|Protokol přístupu Frontdoor|Ne|
|FrontdoorWebApplicationFirewallLog|Protokol Frontdoor firewallu webových aplikací|Ne|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|LoadBalancerAlertEvent|Load Balancer události výstrahy|Ne|
|LoadBalancerProbeHealthStatus|Stav testu Load Balancer|Ne|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|NetworkSecurityGroupEvent|Událost skupiny zabezpečení sítě|Ne|
|NetworkSecurityGroupFlowEvent|Událost toku pravidla skupiny zabezpečení sítě|Ne|
|NetworkSecurityGroupRuleCounter|Čítač pravidla skupiny zabezpečení sítě|Ne|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|GatewayDiagnosticLog|Diagnostické protokoly brány|Ne|
|IKEDiagnosticLog|Diagnostické protokoly IKE|Ne|
|P2SDiagnosticLog|Diagnostické protokoly P2S|Ne|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DDoSMitigationFlowLogs|Protokoly DDoSch rozhodnutí o zmírnění rizik|Ne|
|DDoSMitigationReports|Sestavy zmírnění hrozeb DDoS|Ne|
|DDoSProtectionNotifications|Oznámení o ochraně DDoS|Ne|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ProbeHealthStatusEvents|Událost výsledků testu stavu Traffic Manager|Ne|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|GatewayDiagnosticLog|Diagnostické protokoly brány|Ne|
|IKEDiagnosticLog|Diagnostické protokoly IKE|Ne|
|P2SDiagnosticLog|Diagnostické protokoly P2S|Ne|
|RouteDiagnosticLog|Protokoly diagnostiky směrování|Ne|
|TunnelDiagnosticLog|Protokoly diagnostiky tunelu|Ne|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|VMProtectionAlerts|Výstrahy ochrany virtuálních počítačů|Ne|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|GatewayDiagnosticLog|Diagnostické protokoly brány|Ne|
|IKEDiagnosticLog|Diagnostické protokoly IKE|Ne|
|RouteDiagnosticLog|Protokoly diagnostiky směrování|Ne|
|TunnelDiagnosticLog|Protokoly diagnostiky tunelu|Ne|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft. NotificationHubs/obory názvů

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|OperationalLogs|Provozní protokoly|Ne|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Protokoly auditu|Ne|


## <a name="microsoftpowerbitenants"></a>Microsoft. PowerBI/tenants

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Modul|Modul|Ne|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft. PowerBI/klienti/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Modul|Modul|Ne|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/– kapacity

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Modul|Modul|Ne|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/účty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Ne|


## <a name="microsoftpurviewaccounts"></a>Microsoft. dosah/účty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|Ne|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. RecoveryServices/trezory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AddonAzureBackupAlerts|Doplněk Azure Backup data výstrah|Ne|
|AddonAzureBackupJobs|Doplněk Azure Backup data úlohy|Ne|
|AddonAzureBackupPolicy|Doplněk Azure Backup data zásad|Ne|
|AddonAzureBackupProtectedInstance|Doplněk Azure Backup data chráněné instance|Ne|
|AddonAzureBackupStorage|Doplněk Azure Backup data úložiště|Ne|
|AzureBackupReport|Azure Backup data vytváření sestav|Ne|
|AzureSiteRecoveryEvents|Události Azure Site Recovery|Ne|
|AzureSiteRecoveryJobs|Úlohy Azure Site Recovery|Ne|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery přenos dat chráněných disků|Ne|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery bodů obnovení|Ne|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikované položky|Ne|
|AzureSiteRecoveryReplicationDataUploadRate|Frekvence nahrávání dat replikace Azure Site Recovery|Ne|
|AzureSiteRecoveryReplicationStats|Statistika replikace Azure Site Recovery|Ne|
|CoreAzureBackup|Základní data Azure Backup|Ne|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/obory názvů

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|HybridConnectionsEvent|Události HybridConnections|Ne|
|HybridConnectionsLogs|HybridConnectionsLogs|Ne|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|OperationLogs|Protokoly operací|Ne|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/obory názvů

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|OperationalLogs|Provozní protokoly|Ne|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/Signaler

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AllLogs|Protokoly služby signalizace Azure|Ne|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DevOpsOperationsAudit|Protokoly auditu operací DevOps|Ne|
|ResourceUsageStats|Statistika využití prostředků|Ne|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|Ne|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/databáze

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Chyby|Chyby|Ne|
|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů|Ne|
|QueryStoreWaitStatistics|Statistika čekání na úložiště dotazů|Ne|
|SQLInsights|Přehledy SQL|Ne|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/servery/databáze

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AutomaticTuning|Automatické ladění|Ne|
|Bloky|Bloky|Ne|
|DatabaseWaitStatistics|Statistika čekání databáze|Ne|
|Zablokování|Zablokování|Ne|
|DevOpsOperationsAudit|Protokoly auditu operací DevOps|Ne|
|DmsWorkers|Pracovní procesy DMS|Ne|
|Chyby|Chyby|Ne|
|ExecRequests|Požadavky exec|Ne|
|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů|Ne|
|QueryStoreWaitStatistics|Statistika čekání na úložiště dotazů|Ne|
|RequestSteps|Kroky žádosti|Ne|
|SQLInsights|Přehledy SQL|Ne|
|SqlRequests|Požadavky SQL|Ne|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|Ne|
|Časové limity|Časové limity|Ne|
|Čeká|Čeká|Ne|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|StorageDelete|StorageDelete|Ano|
|StorageRead|StorageRead|Ano|
|StorageWrite|StorageWrite|Ano|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/služby

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|StorageDelete|StorageDelete|Ano|
|StorageRead|StorageRead|Ano|
|StorageWrite|StorageWrite|Ano|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|StorageDelete|StorageDelete|Ano|
|StorageRead|StorageRead|Ano|
|StorageWrite|StorageWrite|Ano|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|StorageDelete|StorageDelete|Ano|
|StorageRead|StorageRead|Ano|
|StorageWrite|StorageWrite|Ano|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Vytváření obsahu|Vytváření obsahu|Ne|
|Spuštění|Spuštění|Ne|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. synapse/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BuiltinSqlReqsEnded|Byly ukončeny předdefinované požadavky na fond SQL.|Ne|
|GatewayApiRequests|Žádosti rozhraní API brány synapse|Ne|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|Ne|
|SynapseRbacOperations|Synapse operace RBAC|Ne|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. synapse/pracovní prostory/bigDataPools

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BigDataPoolAppsEnded|Aplikace s velkým objemem dat se ukončily|Ne|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. synapse/pracovní prostory/sqlPools

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DmsWorkers|Pracovní procesy DMS|Ne|
|ExecRequests|Požadavky exec|Ne|
|RequestSteps|Kroky žádosti|Ne|
|SqlRequests|Požadavky SQL|Ne|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|Ne|
|Čeká|Čeká|Ne|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/prostředí

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Příchozí přenos dat|Příchozí přenos dat|Ne|
|Správa|Správa|Ne|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/prostředí/EventSources

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Příchozí přenos dat|Příchozí přenos dat|Ne|
|Správa|Správa|Ne|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment protokoly platformy|Ne|


## <a name="microsoftwebsites"></a>Microsoft. Web/weby

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Sestava protokolů auditu antivirové ochrany|Ne|
|AppServiceAppLogs|App Service protokoly aplikací|Ne|
|AppServiceAuditLogs|Přístup k protokolům auditu|Ne|
|AppServiceConsoleLogs|Protokoly konzoly App Service|Ne|
|AppServiceFileAuditLogs|Protokoly změny obsahu webu|Ne|
|AppServiceHTTPLogs|Protokoly HTTP|Ne|
|AppServiceIPSecAuditLogs|Protokoly auditu IPSecurity|Ne|
|AppServicePlatformLogs|App Service protokoly platformy|Ne|
|FunctionAppLogs|Protokoly aplikací funkcí|Ne|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/weby/sloty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Sestava protokolů auditu antivirové ochrany|Ne|
|AppServiceAppLogs|App Service protokoly aplikací|Ne|
|AppServiceAuditLogs|Přístup k protokolům auditu|Ne|
|AppServiceConsoleLogs|Protokoly konzoly App Service|Ne|
|AppServiceFileAuditLogs|Protokoly změny obsahu webu|Ne|
|AppServiceHTTPLogs|Protokoly HTTP|Ne|
|AppServiceIPSecAuditLogs|Protokoly auditu IPSecurity|Ne|
|AppServicePlatformLogs|App Service protokoly platformy|Ne|
|FunctionAppLogs|Protokoly aplikací funkcí|Ne|



## <a name="next-steps"></a>Další kroky

* [Další informace o protokolech prostředků](../essentials/platform-logs-overview.md)
* [**Event Hubs** protokolů prostředku prostředku streamování](./resource-logs.md#send-to-azure-event-hubs)
* [Změna nastavení diagnostiky protokolu prostředků pomocí Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Analýza protokolů z Azure Storage pomocí Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

