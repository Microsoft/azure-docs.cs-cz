---
title: Protokoly prostředků Azure podporují služby a schémata.
description: Pochopte podporované služby a schéma událostí pro protokoly prostředků Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: de102c5dc4104aafc44b87b14aeea0b30cb7c083
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359599"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Podporované služby, schémata a kategorie pro protokoly prostředků Azure

> [!NOTE]
> Protokoly prostředků se dříve nazývaly diagnostické protokoly.

[Protokoly prostředků Azure monitor](../../azure-monitor/platform/platform-logs-overview.md) jsou protokoly emitované službami Azure, které popisují fungování těchto služeb nebo prostředků. Všechny protokoly prostředků dostupné prostřednictvím Azure Monitor sdílejí společné schéma nejvyšší úrovně s flexibilitou pro každou službu, aby vygenerovala jedinečné vlastnosti pro vlastní události.

Kombinace typu prostředku (k dispozici ve vlastnosti `resourceId`) a `category` jednoznačně identifikují schéma. Tento článek popisuje schéma nejvyšší úrovně pro protokoly prostředků a odkazy na schémat pro každou službu.

## <a name="top-level-resource-logs-schema"></a>Schéma protokolů prostředků na nejvyšší úrovni

| Název | Požadováno/volitelné | Popis |
|---|---|---|
| time | Požaduje se | Časové razítko (UTC) události. |
| resourceId | Požaduje se | ID prostředku prostředku, který vyvolal událost. Pro služby tenanta se jedná o formulář/tenants/tenant-ID/Providers/Provider-Name. |
| tenantId | Vyžadováno pro protokoly klientů | ID tenanta klienta služby Active Directory, se kterým je tato událost svázána. Tato vlastnost se používá jenom pro protokoly na úrovni tenanta, nezobrazuje se v protokolech na úrovni prostředků. |
| operationName | Požaduje se | Název operace reprezentované touto událostí. Pokud událost představuje operaci RBAC, jedná se o název operace RBAC (např. Microsoft. Storage/storageAccounts/blobServices/BLOBs/Read). Obvykle se modeluje ve formě operace Správce prostředků, i když nejsou skutečné dokumentované Správce prostředků operace (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| operationVersion | Nepovinné | Verze rozhraní API přidružená k operaci, pokud se operace provedla pomocí rozhraní API (např. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Pokud neexistuje žádné rozhraní API, které by odpovídalo této operaci, verze představuje verzi této operace pro případ, že se vlastnosti přidružené k operaci v budoucnu mění. |
| category | Požaduje se | Kategorie protokolu události Kategorie je členitost, na které můžete povolit nebo zakázat protokoly na konkrétním prostředku. Vlastnosti, které se zobrazují v objektu BLOB vlastností události, jsou stejné v rámci konkrétní kategorie protokolu a typu prostředku. Typické kategorie protokolu jsou "Audit" "provozní" "provádění" a "Request". |
| resultType | Nepovinné | Stav události Mezi obvyklé hodnoty patří počáteční, probíhající, úspěšná, neúspěšná, aktivní a vyřešená. |
| resultSignature | Nepovinné | Stav sub události Pokud tato operace odpovídá REST API volání, jedná se o stavový kód HTTP odpovídajícího volání REST. |
| resultDescription | Nepovinné | Statický textový popis této operace, např. "Získat soubor úložiště" |
| durationMs | Nepovinné | Doba trvání operace v milisekundách. |
| callerIpAddress | Nepovinné | IP adresa volajícího, pokud operace odpovídá volání rozhraní API, které pochází z entity s veřejně dostupnou IP adresou. |
| correlationId | Nepovinné | Identifikátor GUID použitý k seskupení sady souvisejících událostí. Obvykle Pokud mají dvě události stejný OperationName, ale dva různé stavy (např. "Spuštěno" a "úspěch") sdílí stejné ID korelace. To může také představovat jiné vztahy mezi událostmi. |
| identity | Nepovinné | Objekt BLOB JSON, který popisuje identitu uživatele nebo aplikace, která provedla operaci. Obvykle se jedná o tokeny autorizace a deklarace identity/JWT ze služby Active Directory. |
| Úroveň | Nepovinné | Úroveň závažnosti události Musí se jednat o jednu z informativních, varovných, chybových nebo kritických. |
| location | Nepovinné | Oblast zdroje, která vyvolala událost, např. "Východní USA" nebo "Francie – jih" |
| properties | Nepovinné | Všechny rozšířené vlastnosti související s touto konkrétní kategorií událostí. Všechny vlastní a jedinečné vlastnosti musí být vloženy do tohoto "části B" schématu. |

## <a name="service-specific-schemas-for-resource-logs"></a>Schémata pro protokoly prostředků pro konkrétní služby
Schéma pro protokoly diagnostiky prostředků se liší v závislosti na kategoriích prostředků a protokolů. V tomto seznamu jsou uvedeny všechny služby, které zpřístupňují dostupné protokoly prostředků, a odkazy na schéma specifické pro službu a kategorii, kde je k dispozici.

| Služba | Schéma & docs |
| --- | --- |
| Azure Active Directory | [Přehled](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schéma protokolu auditu](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) a [schéma přihlášení](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Protokoly prostředků API Management](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Brány Application Gateway |[Protokolování pro Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics pro Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Protokolování Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Protokoly diagnostiky Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Protokoly Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Průzkumník dat Azure | [Protokoly Průzkumník dat Azure](../../data-explorer/using-diagnostic-logs.md) |
| Kognitivní služby | [Protokolování pro Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Protokolování pro Azure Container Registry](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [Protokoly Azure pro CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Protokolování Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitorování datových továren pomocí Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Přístup k protokolům pro Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Přístup k protokolům pro Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Protokoly Event Hubs Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schéma není k dispozici. |
| Brána Azure Firewall | Schéma není k dispozici. |
| IoT Hub | [IoT Hub operace](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Protokolování v Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Protokolování Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Log Analytics pro Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Vlastní schéma sledování B2B Logic Apps](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Skupiny zabezpečení sítě) |[Analýza protokolu pro skupiny zabezpečení sítě (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Spravovat Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Vyhrazená služba Power BI | [Protokolování Power BI Embedded v Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Datový model pro Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Hledání |[Povolení a použití Analýza provozu vyhledávání](../../search/search-traffic-analytics.md) |
| Service Bus |[Protokoly Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Protokolování Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Protokoly úloh](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Schéma Traffic Managerho protokolu](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuální sítě | Schéma není k dispozici. |
| Brány virtuálních sítí | Schéma není k dispozici. |

## <a name="supported-log-categories-per-resource-type"></a>Podporované kategorie protokolů na typ prostředku

Některé kategorie lze podporovat pouze pro konkrétní typy prostředků. Toto je seznam všech dostupných v některém formuláři.  Například kategorie Microsoft. SQL/servery/databáze nejsou k dispozici pro všechny typy databází. Další informace najdete v tématu [informace o protokolování diagnostiky SQL Database](../../sql-database/sql-database-metrics-diag-logging.md). 

|Typ prostředku|Kategorie|Zobrazovaný název kategorie|
|---|---|---|
|Microsoft. AAD/domainServices|SystemSecurity|SystemSecurity|
|Microsoft. AAD/domainServices|Službu AccountManagement|Službu AccountManagement|
|Microsoft. AAD/domainServices|LogonLogoff|LogonLogoff|
|Microsoft. AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft. AAD/domainServices|PolicyChange|PolicyChange|
|Microsoft. AAD/domainServices|PrivilegeUse|PrivilegeUse|
|Microsoft. AAD/domainServices|DetailTracking|DetailTracking|
|Microsoft. AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft. AAD/domainServices|AccountLogon|AccountLogon|
|Microsoft. aadiam/tenantů|Přihlášení|Přihlášení|
|Microsoft.AnalysisServices/servers|Modul|Modul|
|Microsoft.AnalysisServices/servers|Služba|Služba|
|Microsoft.ApiManagement/service|GatewayLogs|Protokoly související s bránou ApiManagement|
|Microsoft. AppPlatform/pružina|ApplicationConsole|Konzola aplikace|
|Microsoft. Automation/automationAccounts|JobLogs|Protokoly úloh|
|Microsoft. Automation/automationAccounts|JobStreams|Datové proudy úloh|
|Microsoft. Automation/automationAccounts|DscNodeStatus|Stav uzlu DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Protokoly služby|
|Microsoft. BatchAI/pracovní prostory|BaiClusterEvent|BaiClusterEvent|
|Microsoft. BatchAI/pracovní prostory|BaiClusterNodeEvent|BaiClusterNodeEvent|
|Microsoft. BatchAI/pracovní prostory|BaiJobEvent|BaiJobEvent|
|Microsoft. blockchain/blockchainMembers|BlockchainApplication|Aplikace blockchain|
|Microsoft. blockchain/blockchainMembers|Proxy server|Proxy server|
|Microsoft. CDN/Profiles/koncových bodů|CoreAnalytics|Získá metriky koncového bodu, například šířku pásma, odchozí přenos atd.|
|Microsoft.ClassicNetwork/networksecuritygroups|Událost toku pravidla skupiny zabezpečení sítě|Událost toku pravidla skupiny zabezpečení sítě|
|Microsoft.CognitiveServices/accounts|Auditování|Protokoly auditu|
|Microsoft.CognitiveServices/accounts|Operace RequestResponse|Protokoly žádostí a odpovědí|
|Microsoft.ContainerRegistry/registries|ContainerRegistryRepositoryEvents|Protokoly RepositoryEvent (Preview)|
|Microsoft.ContainerRegistry/registries|ContainerRegistryLoginEvents|Události přihlášení (Preview)|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Server Kubernetes API|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Správce kontroleru Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Plánovač Kubernetes|
|Microsoft.ContainerService/managedClusters|Kube – audit|Kubernetes audit|
|Microsoft.ContainerService/managedClusters|cluster – automatické škálování|Automatické škálování clusteru Kubernetes|
|Microsoft. datacihly/pracovní prostory|dBFS|Systém souborů Databricks|
|Microsoft. datacihly/pracovní prostory|clusters|Clustery datacihlů|
|Microsoft. datacihly/pracovní prostory|accounts|Účty datacihlů|
|Microsoft. datacihly/pracovní prostory|jobs|Úlohy datacihlů|
|Microsoft. datacihly/pracovní prostory|poznámkového bloku|Poznámkový blok Databricks|
|Microsoft. datacihly/pracovní prostory|SSH|SSH datacihlů|
|Microsoft. datacihly/pracovní prostory|pracovní prostor|Pracovní prostor Databricks|
|Microsoft. datacihly/pracovní prostory|záleží|Tajné kódy datacihlů|
|Microsoft. datacihly/pracovní prostory|sqlPermissions|Datacihly SQLPermissions|
|Microsoft. datacihly/pracovní prostory|instancePools|Fondy instancí|
|Microsoft. datacatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft. DataFactory/továrny|ActivityRuns|Spuštění protokolu aktivity kanálu|
|Microsoft. DataFactory/továrny|PipelineRuns|Protokol spuštění kanálu|
|Microsoft. DataFactory/továrny|TriggerRuns|Aktivační událost spuštění protokolu|
|Microsoft.DataLakeAnalytics/accounts|Auditování|Protokoly auditu|
|Microsoft.DataLakeAnalytics/accounts|Žádosti|Protokoly žádostí|
|Microsoft.DataLakeStore/accounts|Auditování|Protokoly auditu|
|Microsoft.DataLakeStore/accounts|Žádosti|Protokoly žádostí|
|Microsoft. datashare/Accounts|Sdílené složky|Sdílené složky|
|Microsoft. datashare/Accounts|ShareSubscriptions|Sdílet předplatná|
|Microsoft. datashare/Accounts|SentShareSnapshots|Odeslané sdílené snímky|
|Microsoft. datashare/Accounts|ReceivedShareSnapshots|Přijaté snímky sdílené složky|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Protokoly serveru MySQL|
|Microsoft.DBforMySQL/servers|MySqlAuditLogs|Protokoly auditu MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Protokoly serveru PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|QueryStoreWaitStatistics|Statistiky čekání na úložiště dotazů PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs|Protokoly serveru PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|QueryStoreWaitStatistics|Statistiky čekání na úložiště dotazů PostgreSQL|
|Microsoft. DesktopVirtualization/pracovní prostory|CheckPoint|CheckPoint|
|Microsoft. DesktopVirtualization/pracovní prostory|Chyba|Chyba|
|Microsoft. DesktopVirtualization/pracovní prostory|Správa|Správa|
|Microsoft. DesktopVirtualization/pracovní prostory|informační kanál|informační kanál|
|Microsoft. DesktopVirtualization/applicationGroups|CheckPoint|CheckPoint|
|Microsoft. DesktopVirtualization/applicationGroups|Chyba|Chyba|
|Microsoft. DesktopVirtualization/applicationGroups|Správa|Správa|
|Microsoft. DesktopVirtualization/hostPools|CheckPoint|CheckPoint|
|Microsoft. DesktopVirtualization/hostPools|Chyba|Chyba|
|Microsoft. DesktopVirtualization/hostPools|Správa|Správa|
|Microsoft. DesktopVirtualization/hostPools|Připojení|Připojení|
|Microsoft. DesktopVirtualization/hostPools|HostRegistration|HostRegistration|
|Microsoft.Devices/IotHubs|Připojení|Připojení|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetrie zařízení|
|Microsoft.Devices/IotHubs|C2DCommands|Příkazy C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operace identity zařízení|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operace nahrávání souborů|
|Microsoft.Devices/IotHubs|Trasy|Trasy|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D – zdvojené operace|
|Microsoft.Devices/IotHubs|TwinQueries|Zdvojené dotazy|
|Microsoft.Devices/IotHubs|JobsOperations|Operace úloh|
|Microsoft.Devices/IotHubs|DirectMethods|Přímé metody|
|Microsoft.Devices/IotHubs|DistributedTracing|Distribuované trasování (Preview)|
|Microsoft.Devices/IotHubs|Konfigurace|Konfigurace|
|Microsoft.Devices/IotHubs|DeviceStreams|Datové proudy zařízení (Preview)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operace zařízení|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operace služby|
|Microsoft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft. DocumentDB/databaseAccounts|PartitionKeyStatistics|PartitionKeyStatistics|
|Microsoft. DocumentDB/databaseAccounts|ControlPlaneRequests|ControlPlaneRequests|
|Microsoft. EnterpriseKnowledgeGraph/Services|AuditEvent|Protokol AuditEvent|
|Microsoft. EnterpriseKnowledgeGraph/Services|Dataproblém|Protokol dataissues|
|Microsoft. EnterpriseKnowledgeGraph/Services|Žádosti|Konfigurační protokol|
|Microsoft.EventHub/namespaces|ArchiveLogs|Protokoly archivu|
|Microsoft.EventHub/namespaces|OperationalLogs|Provozní protokoly|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Protokoly automatického škálování|
|Microsoft.EventHub/namespaces|KafkaCoordinatorLogs|Protokoly koordinátora Kafka|
|Microsoft.EventHub/namespaces|KafkaUserErrorLogs|Protokoly chyb uživatele Kafka|
|Microsoft.EventHub/namespaces|EventHubVNetConnectionEvent|Protokoly připojení k virtuální síti nebo filtrování IP adres|
|Microsoft.EventHub/namespaces|CustomerManagedKeyUserLogs|Protokoly zákaznických klíčů spravovaných zákazníky|
|Microsoft. HealthcareApis/Services|AuditLogs|Protokoly auditu|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Vyhodnocení automatického škálování|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Akce škálování automatického škálování|
|Microsoft. IoTSpaces/Graph|Trasování|Trasování|
|Microsoft. IoTSpaces/Graph|Provozní|Provozní|
|Microsoft. IoTSpaces/Graph|Auditování|Auditování|
|Microsoft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. IoTSpaces/Graph|Příchozí přenos dat|Příchozí přenos dat|
|Microsoft. IoTSpaces/Graph|Výchozí přenos dat|Výchozí přenos dat|
|Microsoft.KeyVault/vaults|AuditEvent|Protokoly auditu|
|Microsoft.Kusto/Clusters|SucceededIngestion|Úspěšné operace ingestování|
|Microsoft.Kusto/Clusters|FailedIngestion|Neúspěšné operace ingestování|
|Microsoft.Logic/workflows|Modul|Diagnostické události modulu runtime pracovního postupu|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Účet pro integraci sleduje události|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterEvent|AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/workspaces|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft. Media/MediaServices|KeyDeliveryRequests|Požadavky na doručení klíčů|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Událost skupiny zabezpečení sítě|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Čítač pravidla skupiny zabezpečení sítě|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupFlowEvent|Událost toku pravidla skupiny zabezpečení sítě|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Oznámení o ochraně DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Protokoly DDoSch rozhodnutí o zmírnění rizik|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Sestavy zmírnění hrozeb DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Výstrahy ochrany virtuálních počítačů|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Protokol Application Gateway Access|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Protokol výkonu Application Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Protokol brány Application Gateway firewall|
|Microsoft. Network/azurefirewalls|AzureFirewallApplicationRule|Azure Firewall pravidlo aplikace|
|Microsoft. Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall síťové pravidlo|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Diagnostické protokoly brány|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Protokoly diagnostiky tunelu|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Protokoly diagnostiky směrování|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Diagnostické protokoly IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Diagnostické protokoly P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Událost výsledků testu stavu Traffic Manager|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Protokoly tabulky směrování partnerských vztahů|
|Microsoft. Network/vpnGateways|GatewayDiagnosticLog|Diagnostické protokoly brány|
|Microsoft. Network/vpnGateways|TunnelDiagnosticLog|Protokoly diagnostiky tunelu|
|Microsoft. Network/vpnGateways|RouteDiagnosticLog|Protokoly diagnostiky směrování|
|Microsoft. Network/vpnGateways|IKEDiagnosticLog|Diagnostické protokoly IKE|
|Microsoft. Network/frontdoors|FrontdoorAccessLog|Protokol přístupu Frontdoor|
|Microsoft. Network/frontdoors|FrontdoorWebApplicationFirewallLog|Protokol Frontdoor firewallu webových aplikací|
|Microsoft. Network/p2sVpnGateways|GatewayDiagnosticLog|Diagnostické protokoly brány|
|Microsoft. Network/p2sVpnGateways|IKEDiagnosticLog|Diagnostické protokoly IKE|
|Microsoft. Network/p2sVpnGateways|P2SDiagnosticLog|Diagnostické protokoly P2S|
|Microsoft. Network/bastionHosts|BastionAuditLogs|Protokoly auditu bastionu|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer události výstrahy|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stav testu Load Balancer|
|Microsoft.PowerBIDedicated/capacities|Modul|Modul|
|Microsoft. RecoveryServices/trezory|AzureBackupReport|Azure Backup data vytváření sestav|
|Microsoft. RecoveryServices/trezory|CoreAzureBackup|Základní data Azure Backup|
|Microsoft. RecoveryServices/trezory|AddonAzureBackupJobs|Doplněk Azure Backup data úlohy|
|Microsoft. RecoveryServices/trezory|AddonAzureBackupAlerts|Doplněk Azure Backup data výstrah|
|Microsoft. RecoveryServices/trezory|AddonAzureBackupPolicy|Doplněk Azure Backup data zásad|
|Microsoft. RecoveryServices/trezory|AddonAzureBackupStorage|Doplněk Azure Backup data úložiště|
|Microsoft. RecoveryServices/trezory|AddonAzureBackupProtectedInstance|Doplněk Azure Backup data chráněné instance|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryJobs|Úlohy Azure Site Recovery|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryEvents|Události Azure Site Recovery|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikované položky|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryReplicationStats|Statistika replikace Azure Site Recovery|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery bodů obnovení|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryReplicationDataUploadRate|Frekvence nahrávání dat replikace Azure Site Recovery|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery přenos dat chráněných disků|
|Microsoft.Search/searchServices|OperationLogs|Protokoly operací|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Provozní protokoly|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|AutomaticTuning|Automatické ladění|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Statistika čekání na úložiště dotazů|
|Microsoft.Sql/servers/databases|Chyby|Chyby|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Statistika čekání databáze|
|Microsoft.Sql/servers/databases|Časové limity|Časové limity|
|Microsoft.Sql/servers/databases|Čeká|Čeká|
|Microsoft.Sql/servers/databases|Zablokování|Zablokování|
|Microsoft.Sql/servers/databases|Auditování|Protokoly auditu|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|Pracovní procesy DMS|
|Microsoft.Sql/servers/databases|ExecRequests|Požadavky exec|
|Microsoft.Sql/servers/databases|RequestSteps|Kroky žádosti|
|Microsoft.Sql/servers/databases|SqlRequests|Požadavky SQL|
|Microsoft.Sql/servers/databases|Čeká|Čeká|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Statistika využití prostředků|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Statistiky za běhu úložiště dotazů|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Statistika čekání na úložiště dotazů|
|Microsoft.Sql/managedInstances/databases|Chyby|Chyby|
|Microsoft.Storage/storageAccounts/tableServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/blobServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft. Storage/storageAccounts/služby|StorageRead|StorageRead|
|Microsoft. Storage/storageAccounts/služby|StorageWrite|StorageWrite|
|Microsoft. Storage/storageAccounts/služby|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/queueServices|StorageRead|StorageRead|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft.StreamAnalytics/streamingjobs|Spouštěcí|Spouštěcí|
|Microsoft.StreamAnalytics/streamingjobs|Vytváření obsahu|Vytváření obsahu|
|Microsoft. Web/hostingenvironments|AppServiceEnvironmentPlatformLogs|App Service Environment protokoly platformy|
|microsoft.web/sites|FunctionAppLogs|Protokoly aplikací funkcí|
|microsoft.web/sites|AppServiceHTTPLogs|Protokoly HTTP|
|microsoft.web/sites|AppServiceConsoleLogs|Protokoly konzoly App Service|
|microsoft.web/sites|AppServiceAppLogs|App Service protokoly aplikací|
|microsoft.web/sites|AppServiceFileAuditLogs|Protokoly změny obsahu webu|
|microsoft.web/sites|AppServiceAuditLogs|Přístup k protokolům auditu|
|microsoft.web/sites/slots|FunctionAppLogs|Protokoly aplikací funkcí|
|microsoft.web/sites/slots|AppServiceHTTPLogs|Protokoly HTTP|
|microsoft.web/sites/slots|AppServiceConsoleLogs|Protokoly konzoly|
|microsoft.web/sites/slots|AppServiceAppLogs|Protokoly aplikací|
|microsoft.web/sites/slots|AppServiceFileAuditLogs|Protokoly změny obsahu webu|
|microsoft.web/sites/slots|AppServiceAuditLogs|Přístup k protokolům auditu|

## <a name="next-steps"></a>Další kroky

* [Další informace o protokolech prostředků](../../azure-monitor/platform/platform-logs-overview.md)
* [**Event Hubs** protokolů prostředku prostředku streamování](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Změna nastavení diagnostiky protokolu prostředků pomocí Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analýza protokolů z Azure Storage pomocí Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
