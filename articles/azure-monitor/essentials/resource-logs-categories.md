---
title: Protokoly prostředků Azure Monitor podporují služby a kategorie.
description: Referenční informace o Azure Monitorch pochopit podporované služby a schéma událostí pro protokoly prostředků Azure.
ms.topic: reference
ms.date: 03/30/2021
ms.openlocfilehash: a4ab4a2e425b752198223da5efd1b07466ab83d1
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166934"
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

## <a name="microsoftaaddomainservices"></a>Microsoft. AAD/DomainServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AccountLogon|AccountLogon|No|
|Službu AccountManagement|Službu AccountManagement|No|
|DetailTracking|DetailTracking|No|
|DirectoryServiceAccess|DirectoryServiceAccess|No|
|LogonLogoff|LogonLogoff|No|
|ObjectAccess|ObjectAccess|No|
|PolicyChange|PolicyChange|No|
|PrivilegeUse|PrivilegeUse|No|
|SystemSecurity|SystemSecurity|No|


## <a name="microsoftaadiamtenants"></a>Microsoft. aadiam/tenantů

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Přihlášení|Přihlášení|Yes|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servery

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Modul|Modul|No|
|Služba|Služba|No|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|GatewayLogs|Protokoly související s bránou ApiManagement|No|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|HttpRequest|Požadavky HTTP|Yes|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/pružina

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ApplicationConsole|Konzola aplikace|No|
|SystemLogs|Systémové protokoly|No|


## <a name="microsoftattestationattestationproviders"></a>Microsoft. Attestation/attestationProviders

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AuditEvent|Kategorie protokolu zpráv AuditEvent|No|
|Výměna|Kategorie protokolu chybových zpráv.|No|
|INF|Kategorie protokolu informativní zprávy|No|
|WRN|Kategorie protokolu zpráv s upozorněním|No|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DscNodeStatus|Stav uzlu DSC|No|
|JobLogs|Protokoly úloh|No|
|JobStreams|Datové proudy úloh|No|


## <a name="microsoftautonomousdevelopmentplatformaccounts"></a>Microsoft. AutonomousDevelopmentPlatform/účty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Auditování|Yes|
|Provoz|Provoz|Yes|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ServiceLog|Protokoly služby|No|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/Workspaces

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BaiClusterEvent|BaiClusterEvent|No|
|BaiClusterNodeEvent|BaiClusterNodeEvent|No|
|BaiJobEvent|BaiJobEvent|No|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BlockchainApplication|Aplikace blockchain|No|
|FabricOrderer|Řazení prostředků infrastruktury|No|
|FabricPeer|Partnerský uzel Fabric|No|
|Proxy server|Proxy server|No|


## <a name="microsoftblockchaincordamembers"></a>Microsoft. blockchain/cordaMembers

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BlockchainApplication|Aplikace blockchain|No|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BotRequest|Požadavky z kanálů na robota|No|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|WebApplicationFirewallLogs|Protokoly brány firewall pro web spustí aplikace|No|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profily

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AzureCdnAccessLog|Protokol přístupu k síti Azure CDN|No|
|FrontDoorAccessLog|Protokol přístupu FrontDoor|Yes|
|FrontDoorHealthProbeLog|Protokol sondy stavu FrontDoor|Yes|
|FrontDoorWebApplicationFirewallLog|Protokol WebApplicationFirewall FrontDoor|Yes|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft. CDN/Profiles/koncových bodů

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|CoreAnalytics|Získá metriky koncového bodu, například šířku pásma, odchozí přenos atd.|No|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft. ClassicNetwork/networksecuritygroups

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Událost toku pravidla skupiny zabezpečení sítě|Událost toku pravidla skupiny zabezpečení sítě|No|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. Cognitiveservices Account/účty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Protokoly auditu|No|
|Operace RequestResponse|Protokoly žádostí a odpovědí|No|
|Trasování|Protokoly trasování|No|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AuthOperational|Protokoly provozního ověřování|Yes|
|ChatOperational|Protokoly provozního chatu|No|
|SMSOperational|Provozní protokoly SMS|No|
|Využití|Záznamy o využití|No|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/Registry

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ContainerRegistryLoginEvents|Události přihlášení|No|
|ContainerRegistryRepositoryEvents|Protokoly RepositoryEvent|No|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|cluster – automatické škálování|Automatické škálování clusteru Kubernetes|No|
|chráněn|chráněn|No|
|kube-apiserver|Server Kubernetes API|No|
|Kube – audit|Kubernetes audit|No|
|Kube – audit – správce|Protokoly správce auditu Kubernetes|No|
|Kube-Controller-Manager|Správce kontroleru Kubernetes|No|
|Kube – Scheduler|Plánovač Kubernetes|No|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AuditLogs|Protokoly auditu pro volání MiniRP|No|


## <a name="microsoftd365customerinsightsinstances"></a>Microsoft. D365CustomerInsights/instance

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Auditování událostí|No|
|Provoz|Provozní události|No|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft. datacihly/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|accounts|Účty datacihlů|No|
|existující|Clustery datacihlů|No|
|dBFS|Systém souborů Databricks|No|
|instancePools|Fondy instancí|No|
|úlohy|Úlohy datacihlů|No|
|poznámkový blok|Poznámkový blok Databricks|No|
|záleží|Tajné kódy datacihlů|No|
|sqlPermissions|Datacihly SQLPermissions|No|
|protokoly|SSH datacihlů|No|
|pracovní prostor|Pracovní prostor datacihly|No|


## <a name="microsoftdatacollaborationworkspaces"></a>Spolupráce a pracovní prostory Microsoft.

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|CollaborationAudit|Audit spolupráce|Yes|
|Datové prostředky|Datové Assety|No|
|Pipelines|Pipelines|No|
|Nabídek|Nabídek|No|
|Skripty|Skripty|No|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/továrny

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ActivityRuns|Spuštění protokolu aktivity kanálu|No|
|PipelineRuns|Protokol spuštění kanálu|No|
|SandboxActivityRuns|Aktivita izolovaného prostoru (sandbox) spustí protokol|Yes|
|SandboxPipelineRuns|Spuštění protokolu kanálu izolovaného prostoru|Yes|
|SSISIntegrationRuntimeLogs|Protokoly SSIS Integration runtime|No|
|SSISPackageEventMessageContext|Kontext zprávy události balíčku SSIS|No|
|SSISPackageEventMessages|Zprávy o událostech balíčku SSIS|No|
|SSISPackageExecutableStatistics|SSIS – Statistika spustitelných balíčků|No|
|SSISPackageExecutionComponentPhases|Fáze součástí provádění balíčku SSIS|No|
|SSISPackageExecutionDataStatistics|Statistiky dat exeution balíčku SSIS|No|
|TriggerRuns|Aktivační událost spuštění protokolu|No|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/účty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Protokoly auditu|No|
|Žádosti|Protokoly žádostí|No|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/účty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Protokoly auditu|No|
|Žádosti|Protokoly žádostí|No|


## <a name="microsoftdatashareaccounts"></a>Microsoft. datashare/Accounts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ReceivedShareSnapshots|Přijaté snímky sdílené složky|No|
|SentShareSnapshots|Odeslané sdílené snímky|No|
|Sdílené složky|Sdílené složky|No|
|ShareSubscriptions|Sdílet předplatná|No|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servery

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|MySqlAuditLogs|Protokoly auditu MariaDB|No|
|MySqlSlowLogs|Protokoly serveru MariaDB|No|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|MySqlAuditLogs|Protokoly auditu MySQL|No|
|MySqlSlowLogs|Pomalé protokoly MySQL|No|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servery

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|MySqlAuditLogs|Protokoly auditu MySQL|No|
|MySqlSlowLogs|Protokoly serveru MySQL|No|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|PostgreSQLLogs|Protokoly serveru PostgreSQL|No|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft. DBForPostgreSQL/serverGroupsv2

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|PostgreSQLLogs|Protokoly serveru PostgreSQL|Yes|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servery

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|PostgreSQLLogs|Protokoly serveru PostgreSQL|No|
|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů PostgreSQL|No|
|QueryStoreWaitStatistics|Statistiky čekání na úložiště dotazů PostgreSQL|No|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|PostgreSQLLogs|Protokoly serveru PostgreSQL|No|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft. DesktopVirtualization/applicationgroups

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|CheckPoint|CheckPoint|No|
|Chyba|Chyba|No|
|Správa|Správa|No|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft. DesktopVirtualization/hostpools

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AgentHealthStatus|AgentHealthStatus|No|
|CheckPoint|CheckPoint|No|
|Připojení|Připojení|No|
|Chyba|Chyba|No|
|HostRegistration|HostRegistration|No|
|Správa|Správa|No|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft. DesktopVirtualization/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|CheckPoint|CheckPoint|No|
|Chyba|Chyba|No|
|Informační kanál|Informační kanál|No|
|Správa|Správa|No|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/ElasticPools/IotHubTenants

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|C2DCommands|Příkazy C2D|No|
|C2DTwinOperations|C2D – zdvojené operace|No|
|Konfigurace|Konfigurace|No|
|Připojení|Připojení|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Operace identity zařízení|No|
|DeviceStreams|Datové proudy zařízení (Preview)|No|
|DeviceTelemetry|Telemetrie zařízení|No|
|DirectMethods|Přímé metody|No|
|DistributedTracing|Distribuované trasování (Preview)|No|
|FileUploadOperations|Operace nahrávání souborů|No|
|JobsOperations|Operace úloh|No|
|Trasy|Trasy|No|
|TwinQueries|Zdvojené dotazy|No|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|C2DCommands|Příkazy C2D|No|
|C2DTwinOperations|C2D – zdvojené operace|No|
|Konfigurace|Konfigurace|No|
|Připojení|Připojení|No|
|D2CTwinOperations|D2CTwinOperations|No|
|DeviceIdentityOperations|Operace identity zařízení|No|
|DeviceStreams|Datové proudy zařízení (Preview)|No|
|DeviceTelemetry|Telemetrie zařízení|No|
|DirectMethods|Přímé metody|No|
|DistributedTracing|Distribuované trasování (Preview)|No|
|FileUploadOperations|Operace nahrávání souborů|No|
|JobsOperations|Operace úloh|No|
|Trasy|Trasy|No|
|TwinQueries|Zdvojené dotazy|No|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeviceOperations|Operace zařízení|No|
|ServiceOperations|Operace služby|No|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DigitalTwinsOperation|DigitalTwinsOperation|No|
|EventRoutesOperation|EventRoutesOperation|No|
|ModelsOperation|ModelsOperation|No|
|QueryOperation|QueryOperation|No|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|CassandraRequests|CassandraRequests|No|
|ControlPlaneRequests|ControlPlaneRequests|No|
|DataPlaneRequests|DataPlaneRequests|No|
|GremlinRequests|GremlinRequests|No|
|MongoRequests|MongoRequests|No|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|No|
|PartitionKeyStatistics|PartitionKeyStatistics|No|
|QueryRuntimeStatistics|QueryRuntimeStatistics|No|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domény

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeliveryFailures|Protokoly chyb doručení|No|
|PublishFailures|Protokoly chyb publikování|No|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeliveryFailures|Protokoly chyb doručení|No|
|PublishFailures|Protokoly chyb publikování|No|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeliveryFailures|Protokoly chyb doručení|No|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeliveryFailures|Protokoly chyb doručení|No|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témata

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DeliveryFailures|Protokoly chyb doručení|No|
|PublishFailures|Protokoly chyb publikování|No|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/obory názvů

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ArchiveLogs|Protokoly archivu|No|
|AutoScaleLogs|Protokoly automatického škálování|No|
|CustomerManagedKeyUserLogs|Protokoly zákaznických klíčů spravovaných zákazníky|No|
|EventHubVNetConnectionEvent|Protokoly připojení k virtuální síti nebo filtrování IP adres|No|
|KafkaCoordinatorLogs|Protokoly koordinátora Kafka|No|
|KafkaUserErrorLogs|Protokoly chyb uživatele Kafka|No|
|OperationalLogs|Provozní protokoly|No|


## <a name="microsoftexperimentationexperimentworkspaces"></a>Microsoft. experimentování/experimentWorkspaces

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Žádost|Žádost|No|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/Services

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AuditLogs|Protokoly auditu|No|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/autoscalesettings

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AutoscaleEvaluations|Vyhodnocení automatického škálování|No|
|AutoscaleScaleActions|Akce škálování automatického škálování|No|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/Components

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AppAvailabilityResults|Výsledky dostupnosti|No|
|AppBrowserTimings|Časování prohlížeče|No|
|AppDependencies|Závislosti|No|
|AppEvents|Události|No|
|AppExceptions|Výjimky|No|
|AppMetrics|Metriky|No|
|AppPageViews|Zobrazení stránek|No|
|AppPerformanceCounters|Čítače výkonu|No|
|AppRequests|Žádosti|No|
|AppSystemEvents|Systémové události|No|
|AppTraces|Trasování|No|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. managedhsms – trezor/

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AuditEvent|Událost auditu|No|


## <a name="microsoftkeyvaultvaults"></a>Trezory a trezory Microsoft.

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AuditEvent|Protokoly auditu|No|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clustery

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Příkaz|Příkaz|No|
|FailedIngestion|Neúspěšné operace ingestování|No|
|IngestionBatching|Dávkování příjmu dat|No|
|Dotaz|Dotaz|No|
|SucceededIngestion|Úspěšné operace ingestování|No|
|TableDetails|Podrobnosti tabulky|No|
|TableUsageStatistics|Statistiky využití tabulek|No|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft. Logic/integrationAccounts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|IntegrationAccountTrackingEvents|Účet pro integraci sleduje události|No|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/Workflows

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Modul|Diagnostické události modulu runtime pracovního postupu|No|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|No|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|No|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|No|
|AmlComputeJobEvent|AmlComputeJobEvent|No|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|No|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|KeyDeliveryRequests|Požadavky na doručení klíčů|No|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ApplicationGatewayAccessLog|Protokol Application Gateway Access|No|
|ApplicationGatewayFirewallLog|Protokol brány Application Gateway firewall|No|
|ApplicationGatewayPerformanceLog|Protokol výkonu Application Gateway|No|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AzureFirewallApplicationRule|Azure Firewall pravidlo aplikace|No|
|AzureFirewallDnsProxy|Azure Firewall DNS proxy|No|
|AzureFirewallNetworkRule|Azure Firewall síťové pravidlo|No|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionHosts

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BastionAuditLogs|Protokoly auditu bastionu|No|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|PeeringRouteLog|Protokoly tabulky směrování partnerských vztahů|No|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|FrontdoorAccessLog|Protokol přístupu Frontdoor|No|
|FrontdoorWebApplicationFirewallLog|Protokol Frontdoor firewallu webových aplikací|No|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|LoadBalancerAlertEvent|Load Balancer události výstrahy|No|
|LoadBalancerProbeHealthStatus|Stav testu Load Balancer|No|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft. Network/networksecuritygroups

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|NetworkSecurityGroupEvent|Událost skupiny zabezpečení sítě|No|
|NetworkSecurityGroupFlowEvent|Událost toku pravidla skupiny zabezpečení sítě|No|
|NetworkSecurityGroupRuleCounter|Čítač pravidla skupiny zabezpečení sítě|No|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|GatewayDiagnosticLog|Diagnostické protokoly brány|No|
|IKEDiagnosticLog|Diagnostické protokoly IKE|No|
|P2SDiagnosticLog|Diagnostické protokoly P2S|No|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DDoSMitigationFlowLogs|Protokoly DDoSch rozhodnutí o zmírnění rizik|No|
|DDoSMitigationReports|Sestavy zmírnění hrozeb DDoS|No|
|DDoSProtectionNotifications|Oznámení o ochraně DDoS|No|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ProbeHealthStatusEvents|Událost výsledků testu stavu Traffic Manager|No|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|GatewayDiagnosticLog|Diagnostické protokoly brány|No|
|IKEDiagnosticLog|Diagnostické protokoly IKE|No|
|P2SDiagnosticLog|Diagnostické protokoly P2S|No|
|RouteDiagnosticLog|Protokoly diagnostiky směrování|No|
|TunnelDiagnosticLog|Protokoly diagnostiky tunelu|No|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|VMProtectionAlerts|Výstrahy ochrany virtuálních počítačů|No|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|GatewayDiagnosticLog|Diagnostické protokoly brány|No|
|IKEDiagnosticLog|Diagnostické protokoly IKE|No|
|RouteDiagnosticLog|Protokoly diagnostiky směrování|No|
|TunnelDiagnosticLog|Protokoly diagnostiky tunelu|No|


## <a name="microsoftnotificationhubsnamespaces"></a>Microsoft. NotificationHubs/obory názvů

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|OperationalLogs|Provozní protokoly|No|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Auditování|Protokoly auditu|No|


## <a name="microsoftpowerbitenants"></a>Microsoft. PowerBI/tenants

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Modul|Modul|No|


## <a name="microsoftpowerbitenantsworkspaces"></a>Microsoft. PowerBI/klienti/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Modul|Modul|No|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/– kapacity

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Modul|Modul|No|


## <a name="microsoftpurviewaccounts"></a>Microsoft. dosah/účty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|ScanStatusLogEvent|ScanStatus|No|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft. RecoveryServices/trezory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AddonAzureBackupAlerts|Doplněk Azure Backup data výstrah|No|
|AddonAzureBackupJobs|Doplněk Azure Backup data úlohy|No|
|AddonAzureBackupPolicy|Doplněk Azure Backup data zásad|No|
|AddonAzureBackupProtectedInstance|Doplněk Azure Backup data chráněné instance|No|
|AddonAzureBackupStorage|Doplněk Azure Backup data úložiště|No|
|AzureBackupReport|Azure Backup data vytváření sestav|No|
|AzureSiteRecoveryEvents|Události Azure Site Recovery|No|
|AzureSiteRecoveryJobs|Úlohy Azure Site Recovery|No|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery přenos dat chráněných disků|No|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery bodů obnovení|No|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikované položky|No|
|AzureSiteRecoveryReplicationDataUploadRate|Frekvence nahrávání dat replikace Azure Site Recovery|No|
|AzureSiteRecoveryReplicationStats|Statistika replikace Azure Site Recovery|No|
|CoreAzureBackup|Základní data Azure Backup|No|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/obory názvů

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|HybridConnectionsEvent|Události HybridConnections|No|
|HybridConnectionsLogs|HybridConnectionsLogs|No|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|OperationLogs|Protokoly operací|No|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/obory názvů

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|OperationalLogs|Provozní protokoly|No|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/Signaler

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AllLogs|Protokoly služby signalizace Azure|No|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft. SignalRService/WebPubSub

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AllLogs|Protokoly služby Azure web PubSub|Yes|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DevOpsOperationsAudit|Protokoly auditu operací DevOps|No|
|ResourceUsageStats|Statistika využití prostředků|No|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|No|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/databáze

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Chyby|Chyby|No|
|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů|No|
|QueryStoreWaitStatistics|Statistika čekání na úložiště dotazů|No|
|SQLInsights|SQL Insights|No|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/servery/databáze

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AutomaticTuning|Automatické ladění|No|
|Bloky|Bloky|No|
|DatabaseWaitStatistics|Statistika čekání databáze|No|
|Zablokování|Zablokování|No|
|DevOpsOperationsAudit|Protokoly auditu operací DevOps|No|
|DmsWorkers|Pracovní procesy DMS|No|
|Chyby|Chyby|No|
|ExecRequests|Požadavky exec|No|
|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů|No|
|QueryStoreWaitStatistics|Statistika čekání na úložiště dotazů|No|
|RequestSteps|Kroky žádosti|No|
|SQLInsights|SQL Insights|No|
|SqlRequests|Požadavky SQL|No|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|No|
|Časové limity|Časové limity|No|
|Čeká|Čeká|No|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/služby

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|StorageDelete|StorageDelete|Yes|
|StorageRead|StorageRead|Yes|
|StorageWrite|StorageWrite|Yes|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Vytváření obsahu|Vytváření obsahu|No|
|Spuštění|Spuštění|No|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. synapse/pracovní prostory

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BuiltinSqlReqsEnded|Byly ukončeny předdefinované požadavky na fond SQL.|No|
|GatewayApiRequests|Žádosti rozhraní API brány synapse|No|
|IntegrationActivityRuns|Spuštění integračních aktivit|Yes|
|IntegrationPipelineRuns|Spuštění kanálu integrace|Yes|
|IntegrationTriggerRuns|Spuštění triggeru integrace|Yes|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|No|
|SynapseRbacOperations|Synapse operace RBAC|No|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. synapse/pracovní prostory/bigDataPools

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|BigDataPoolAppsEnded|Aplikace s velkým objemem dat se ukončily|No|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. synapse/pracovní prostory/sqlPools

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|DmsWorkers|Pracovní procesy DMS|No|
|ExecRequests|Požadavky exec|No|
|RequestSteps|Kroky žádosti|No|
|SqlRequests|Požadavky SQL|No|
|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|No|
|Čeká|Čeká|No|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/prostředí

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Příchozí přenos dat|Příchozí přenos dat|No|
|Správa|Správa|No|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/prostředí/EventSources

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|Příchozí přenos dat|Příchozí přenos dat|No|
|Správa|Správa|No|


## <a name="microsoftwebhostingenvironments"></a>Microsoft. Web/hostingenvironments

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AppServiceEnvironmentPlatformLogs|App Service Environment protokoly platformy|No|


## <a name="microsoftwebsites"></a>Microsoft. Web/weby

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Sestava protokolů auditu antivirové ochrany|No|
|AppServiceAppLogs|App Service protokoly aplikací|No|
|AppServiceAuditLogs|Přístup k protokolům auditu|No|
|AppServiceConsoleLogs|Protokoly konzoly App Service|No|
|AppServiceFileAuditLogs|Protokoly změny obsahu webu|No|
|AppServiceHTTPLogs|Protokoly HTTP|No|
|AppServiceIPSecAuditLogs|Protokoly auditu IPSecurity|No|
|AppServicePlatformLogs|App Service protokoly platformy|No|
|FunctionAppLogs|Protokoly aplikací funkcí|No|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/weby/sloty

|Kategorie|Zobrazovaný název kategorie|Náklady k exportu|
|---|---|---|
|AppServiceAntivirusScanAuditLogs|Sestava protokolů auditu antivirové ochrany|No|
|AppServiceAppLogs|App Service protokoly aplikací|No|
|AppServiceAuditLogs|Přístup k protokolům auditu|No|
|AppServiceConsoleLogs|Protokoly konzoly App Service|No|
|AppServiceFileAuditLogs|Protokoly změny obsahu webu|No|
|AppServiceHTTPLogs|Protokoly HTTP|No|
|AppServiceIPSecAuditLogs|Protokoly auditu IPSecurity|No|
|AppServicePlatformLogs|App Service protokoly platformy|No|
|FunctionAppLogs|Protokoly aplikací funkcí|No|


## <a name="next-steps"></a>Další kroky

* [Další informace o protokolech prostředků](../essentials/platform-logs-overview.md)
* [**Event Hubs** protokolů prostředku prostředku streamování](./resource-logs.md#send-to-azure-event-hubs)
* [Změna nastavení diagnostiky protokolu prostředků pomocí Azure Monitor REST API](/rest/api/monitor/diagnosticsettings)
* [Analýza protokolů z Azure Storage pomocí Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)

