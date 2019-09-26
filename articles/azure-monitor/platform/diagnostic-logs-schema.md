---
title: Diagnostické protokoly Azure a podporované služby a schémata
description: Pochopte podporované služby a schéma událostí pro diagnostické protokoly Azure.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/11/2018
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: fdcfcbaf99d48a345d2be4da297be1c9139da15c
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71308115"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Podporované služby, schémata a kategorie pro diagnostické protokoly Azure

[Protokoly diagnostiky Azure monitor](../../azure-monitor/platform/resource-logs-overview.md) jsou protokoly emitované službami Azure, které popisují fungování těchto služeb nebo prostředků. Všechny diagnostické protokoly dostupné prostřednictvím Azure Monitor sdílejí společné schéma nejvyšší úrovně s flexibilitou pro každou službu, aby vygenerovala jedinečné vlastnosti pro vlastní události.

Kombinace typu prostředku (k dispozici ve `resourceId` vlastnosti) `category` a jednoznačné určení schématu. Tento článek popisuje schéma nejvyšší úrovně pro diagnostické protokoly a odkazy na schémat pro každou službu.

## <a name="top-level-diagnostic-logs-schema"></a>Schéma diagnostických protokolů nejvyšší úrovně

| Name | Požadováno/volitelné | Popis |
|---|---|---|
| time | Požadováno | Časové razítko (UTC) události. |
| resourceId | Požadováno | ID prostředku prostředku, který vyvolal událost. Pro služby tenanta se jedná o formulář/tenants/tenant-ID/Providers/Provider-Name. |
| tenantId | Vyžadováno pro protokoly klientů | ID tenanta klienta služby Active Directory, se kterým je tato událost svázána. Tato vlastnost se používá jenom pro protokoly na úrovni tenanta, nezobrazuje se v protokolech na úrovni prostředků. |
| operationName | Požadováno | Název operace reprezentované touto událostí. Pokud událost představuje operaci RBAC, jedná se o název operace RBAC (např. Microsoft. Storage/storageAccounts/blobServices/BLOBs/Read). Obvykle se modeluje ve formě operace Správce prostředků, i když nejsou skutečné dokumentované Správce prostředků operace (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| operationVersion | volitelná, | Verze rozhraní API přidružená k operaci, pokud se operace provedla pomocí rozhraní API (např. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Pokud neexistuje žádné rozhraní API, které by odpovídalo této operaci, verze představuje verzi této operace pro případ, že se vlastnosti přidružené k operaci v budoucnu mění. |
| category | Požadováno | Kategorie protokolu události Kategorie je členitost, na které můžete povolit nebo zakázat protokoly na konkrétním prostředku. Vlastnosti, které se zobrazují v objektu BLOB vlastností události, jsou stejné v rámci konkrétní kategorie protokolu a typu prostředku. Typické kategorie protokolu jsou "Audit" "provozní" "provádění" a "Request". |
| resultType | volitelná, | Stav události Mezi obvyklé hodnoty patří počáteční, probíhající, úspěšná, neúspěšná, aktivní a vyřešená. |
| resultSignature | volitelná, | Stav sub události Pokud tato operace odpovídá REST API volání, jedná se o stavový kód HTTP odpovídajícího volání REST. |
| resultDescription | volitelná, | Statický textový popis této operace, např. "Získat soubor úložiště" |
| durationMs | volitelná, | Doba trvání operace v milisekundách. |
| callerIpAddress | volitelná, | IP adresa volajícího, pokud operace odpovídá volání rozhraní API, které pochází z entity s veřejně dostupnou IP adresou. |
| correlationId | volitelná, | Identifikátor GUID použitý k seskupení sady souvisejících událostí. Obvykle Pokud mají dvě události stejný OperationName, ale dva různé stavy (např. "Spuštěno" a "úspěch") sdílí stejné ID korelace. To může také představovat jiné vztahy mezi událostmi. |
| identity | volitelná, | Objekt BLOB JSON, který popisuje identitu uživatele nebo aplikace, která provedla operaci. Obvykle se jedná o tokeny autorizace a deklarace identity/JWT ze služby Active Directory. |
| Level | volitelná, | Úroveň závažnosti události Musí se jednat o jednu z informativních, varovných, chybových nebo kritických. |
| location | volitelná, | Oblast zdroje, která vyvolala událost, např. "Východní USA" nebo "Francie – jih" |
| properties | volitelná, | Všechny rozšířené vlastnosti související s touto konkrétní kategorií událostí. Všechny vlastní a jedinečné vlastnosti musí být vloženy do tohoto "části B" schématu. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Schémata specifická pro službu pro diagnostické protokoly prostředků
Schéma pro protokoly diagnostiky prostředků se liší v závislosti na kategoriích prostředků a protokolů. Tento seznam obsahuje všechny služby, které zpřístupňují diagnostické protokoly, a odkazy na schéma specifické pro službu a kategorii, kde je k dispozici.

| Služba | Schéma & docs |
| --- | --- |
| Azure Active Directory | [Přehled](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schéma protokolu auditu](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) a [schéma přihlášení](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Protokoly diagnostiky API Management](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Brány Application Gateway |[Protokolování diagnostiky pro Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log Analytics pro Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Protokolování diagnostiky Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Protokoly diagnostiky Azure Database for MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Protokoly diagnostiky Azure Database for PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | [Protokolování diagnostiky pro Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Content Delivery Network | [Diagnostické protokoly Azure pro CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Protokolování Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitorování datových továren pomocí Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Přístup k protokolům diagnostiky pro Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Přístup k diagnostickým protokolům pro Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Diagnostické protokoly Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schéma není k dispozici. |
| Brána Azure Firewall | Schéma není k dispozici. |
| IoT Hub | [IoT Hub operace](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Protokolování v Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Kubernetes Service |[Protokolování Azure Kubernetes](../../aks/view-master-logs.md#log-event-schema) |
| Nástroj pro vyrovnávání zatížení |[Log Analytics pro Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Vlastní schéma sledování B2B Logic Apps](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Skupiny zabezpečení sítě) |[Analýza protokolu pro skupiny zabezpečení sítě (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Spravovat Azure DDoS Protection Standard](../../virtual-network/manage-ddos-protection.md) |
| Vyhrazená služba Power BI | [Protokolování diagnostiky pro Power BI Embedded v Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Datový model pro Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Search |[Povolení a použití Analýza provozu vyhledávání](../../search/search-traffic-analytics.md) |
| Service Bus |[Protokoly diagnostiky Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Protokolování diagnostiky Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Protokoly diagnostiky úlohy](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Schéma Traffic Managerho protokolu](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuální sítě | Schéma není k dispozici. |
| Brány virtuálních sítí | Schéma není k dispozici. |

## <a name="supported-log-categories-per-resource-type"></a>Podporované kategorie protokolů na typ prostředku
|Typ prostředku|Category|Zobrazovaný název kategorie|
|---|---|---|
|Microsoft.AnalysisServices/servers|Modul|Modul|
|Microsoft.AnalysisServices/servers|Služba|Služba|
|Microsoft.ApiManagement/service|GatewayLogs|Protokoly související s bránou ApiManagement|
|Microsoft. Automation/automationAccounts|JobLogs|Protokoly úloh|
|Microsoft. Automation/automationAccounts|JobStreams|Datové proudy úloh|
|Microsoft. Automation/automationAccounts|DscNodeStatus|Stav uzlu DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Protokoly služby|
|Microsoft. CDN/Profiles/koncových bodů|CoreAnalytics|Získá metriky koncového bodu, například šířku pásma, odchozí přenos atd.|
|Microsoft.ClassicNetwork/networksecuritygroups|Událost toku pravidla skupiny zabezpečení sítě|Událost toku pravidla skupiny zabezpečení sítě|
|Microsoft.CognitiveServices/accounts|Auditování|Protokoly auditu|
|Microsoft.CognitiveServices/accounts|Operace RequestResponse|Protokoly žádostí a odpovědí|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Server rozhraní API Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Správce kontroleru Kubernetes|
|Microsoft.ContainerService/managedClusters|cluster – automatické škálování|Automatické škálování clusteru Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Plánovač Kubernetes|
|Microsoft.ContainerService/managedClusters|chráněn|Ověřovací webhook|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft. DataFactory/továrny|ActivityRuns|Spuštění protokolu aktivity kanálu|
|Microsoft. DataFactory/továrny|PipelineRuns|Protokol spuštění kanálu|
|Microsoft. DataFactory/továrny|TriggerRuns|Aktivační událost spuštění protokolu|
|Microsoft.DataLakeAnalytics/accounts|Auditování|Protokoly auditu|
|Microsoft.DataLakeAnalytics/accounts|Požadavky|Protokoly žádostí|
|Microsoft.DataLakeStore/accounts|Auditování|Protokoly auditu|
|Microsoft.DataLakeStore/accounts|Požadavky|Protokoly žádostí|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Protokoly serveru MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Protokoly serveru PostgreSQL|
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
|Microsoft.Devices/IotHubs|E2EDiagnostics|Diagnostika E2E (Preview)|
|Microsoft.Devices/IotHubs|Konfigurace|Konfigurace|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operace zařízení|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operace služby|
|Microsoft. DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft. DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft. DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Protokoly archivu|
|Microsoft.EventHub/namespaces|OperationalLogs|Provozní protokoly|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Protokoly automatického škálování|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Vyhodnocení automatického škálování|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Akce automatického škálování|
|Microsoft. IoTSpaces/Graph|Trasování|Trasování|
|Microsoft. IoTSpaces/Graph|Provozní|Provozní|
|Microsoft. IoTSpaces/Graph|Auditování|Auditování|
|Microsoft. IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft. IoTSpaces/Graph|Příchozí přenos dat|Příchozí přenos dat|
|Microsoft. IoTSpaces/Graph|Výchozí přenos|Výchozí přenos|
|Microsoft.KeyVault/vaults|AuditEvent|Protokoly auditu|
|Microsoft.Logic/workflows|Modul|Diagnostické události modulu runtime pracovního postupu|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Účet pro integraci sleduje události|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Událost skupiny zabezpečení sítě|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Čítač pravidla skupiny zabezpečení sítě|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Load Balancer události výstrahy|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stav testu Load Balancer|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Oznámení o ochraně DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Protokoly DDoSch rozhodnutí o zmírnění rizik|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Sestavy zmírnění hrozeb DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Výstrahy ochrany virtuálních počítačů|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Protokol Application Gateway Access|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Protokol výkonu Application Gateway|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Protokol brány Application Gateway firewall|
|Microsoft. Network/securegateways|AzureFirewallApplicationRule|Azure Firewall pravidlo aplikace|
|Microsoft. Network/securegateways|AzureFirewallNetworkRule|Azure Firewall síťové pravidlo|
|Microsoft. Network/azurefirewalls|AzureFirewallApplicationRule|Azure Firewall pravidlo aplikace|
|Microsoft. Network/azurefirewalls|AzureFirewallNetworkRule|Azure Firewall síťové pravidlo|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Diagnostické protokoly brány|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Protokoly diagnostiky tunelu|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Protokoly diagnostiky směrování|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Diagnostické protokoly IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Diagnostické protokoly P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Událost výsledků testu stavu Traffic Manager|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Protokoly tabulky směrování partnerských vztahů|
|Microsoft. Network/frontdoors|FrontdoorAccessLog|Protokol přístupu Frontdoor|
|Microsoft. Network/frontdoors|FrontdoorWebApplicationFirewallLog|Protokol Frontdoor firewallu webových aplikací|
|Microsoft.PowerBIDedicated/capacities|Modul|Modul|
|Microsoft. RecoveryServices/trezory|AzureBackupReport|Data pro generování sestav Azure Backup|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryJobs|Úlohy Azure Site Recovery|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryEvents|Události Azure Site Recovery|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryReplicatedItems|Replikované položky Azure Site Recovery|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryReplicationStats|Statistika replikace Azure Site Recovery|
|Microsoft. RecoveryServices/trezory|AzureSiteRecoveryRecoveryPoints|Body obnovení Azure Site Recovery|
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
|Microsoft.Sql/servers/databases|Vypršení časových limitů|Vypršení časových limitů|
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
|Microsoft.StreamAnalytics/streamingjobs|Spuštění|Spuštění|
|Microsoft.StreamAnalytics/streamingjobs|Vytváření obsahu|Vytváření obsahu|
|microsoft.web/sites|FunctionExecutionLogs|Protokoly spouštění funkcí|
|microsoft.web/sites/slots|FunctionExecutionLogs|Protokoly spouštění funkcí|

## <a name="next-steps"></a>Další kroky

* [Další informace o diagnostických protokolech](../../azure-monitor/platform/resource-logs-overview.md)
* [Stream protokolů diagnostiky prostředků k **Event Hubs**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Změnit nastavení diagnostiky prostředků pomocí REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analýza protokolů ze služby Azure storage s využitím Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)
