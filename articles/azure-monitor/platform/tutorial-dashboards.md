---
title: Diagnostické protokoly Azure nepodporuje služby a schémata
description: Podporované služby a události schématu lépe porozumět pro diagnostické protokoly Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/11/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: ebadf69d5740dc9b9907880917516419129db170
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468826"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Podporované služby, schémat a kategorie pro diagnostické protokoly Azure

[Diagnostické protokoly Azure monitoru](../../azure-monitor/platform/diagnostic-logs-overview.md) jsou protokoly generované službami Azure, které popisují operace z těchto služeb prostředků. Všechny diagnostické protokoly, které jsou k dispozici prostřednictvím služby Azure Monitor sdílejí společné schéma nejvyšší úrovně, s flexibilitou pro každou službu a vygenerovat jedinečné vlastnosti pro vlastní události.

Kombinace typu prostředku (k dispozici v `resourceId` vlastnost) a `category` jednoznačné identifikaci schéma. Tento článek popisuje nejvyšší úrovni schématu pro diagnostické protokoly a odkazy na schémat pro každou službu.

## <a name="top-level-diagnostic-logs-schema"></a>Schéma nejvyšší úrovně diagnostické protokoly

| Název | Požadované a volitelné | Popis |
|---|---|---|
| time | Požaduje se | Časové razítko (UTC) události. |
| resourceId | Požaduje se | ID prostředku prostředků, které události, protože ho. Pro tenanta služby to je /tenants/tenant-id/providers/provider-name formuláře. |
| ID Tenanta | Vyžaduje se pro tenanta protokoly | ID tenanta, který tato událost se váže na tenanta Active Directory. Tato vlastnost slouží pouze pro protokoly na úrovni tenanta, nezobrazí se v protokolech úrovni prostředků. |
| operationName | Požaduje se | Název operace reprezentovaný touto událostí. Pokud událost představuje operaci RBAC, jedná se o název operace RBAC (např.) Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Obvykle modelována ve formě operaci Resource Manager i v případě, že se nejedná o skutečný zdokumentovaných operace Resource Manageru (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Nepovinné | Přidružené operaci, pokud operationName byla provedena pomocí rozhraní API (např api-version. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Pokud neexistuje žádné rozhraní API, která odpovídá této operace, verze představuje verzi jazyka tuto operaci v případě, že v budoucnu změnit vlastnosti přidružené k operaci. |
| category | Požaduje se | Kategorie protokolu události. Kategorie je týdenní i členitost, ve kterém můžete povolit nebo zakázat přihlásí k určitému prostředku. Vlastnosti, které se zobrazují v rámci objektu blob vlastnosti události jsou stejné v rámci typu protokolu konkrétní kategorie a prostředků. Kategorie typické protokolu jsou "Audit" "provozní" "Spuštění" a "Požadavek." |
| resultType | Nepovinné | Stav události. Mezi typické hodnoty patří spuštěno, v průběhu, úspěšné, neúspěšné, aktivní a vyřešeno. |
| resultSignature | Nepovinné | Dílčí stav události. Pokud tato operace odpovídá volání rozhraní REST API, to je stavový kód HTTP odpovídající volání REST. |
| resultDescription | Nepovinné | Statické textový popis této operace, např. "Získat soubor úložiště." |
| durationMs | Nepovinné | Doba trvání operace v milisekundách. |
| callerIpAddress | Nepovinné | Volající IP adresu, pokud odpovídá operaci volání rozhraní API by pocházející z entity s veřejně dostupnou IP adresu. |
| correlationId | Nepovinné | GUID, který se používá k seskupení sady souvisejících událostí. Obvykle Pokud dvě události mají stejnou operationName ale dvou různých stavů (např.) "Začínáme" a "Úspěchu"), sdílejí stejné ID korelace. To může také představovat jiné vztahy mezi událostmi. |
| identity | Nepovinné | Objekt blob JSON, který popisuje identity uživatele nebo aplikaci, která provádí operaci. Obvykle bude zahrnovat autorizace a deklarace / token JWT ze služby active directory. |
| Úroveň | Nepovinné | Úroveň závažnosti události. Musí být jedna z informační, chyba, upozornění nebo kritický. |
| location | Nepovinné | Oblast prostředku generování událostí, např. "East US" nebo "Francie – jih" |
| properties | Nepovinné | Některé rozšířené vlastnosti týkající se této konkrétní kategorii událostí. Všechny vlastní nebo jedinečné vlastnosti musí být umístěte soubor "B část" schématu. |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>Specifické pro služby schémata pro diagnostické protokoly prostředků
Schéma protokolů diagnostiky prostředků se liší podle kategorie prostředků a protokolu. Tento seznam obsahuje všechny služby, které provádět dostupné diagnostické protokoly a odkazy služby a schématu podle kategorií tam, kde je k dispozici.

| Služba | Schéma a dokumentace |
| --- | --- |
| Azure Active Directory | [Přehled](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schéma protokolu auditu](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) a [Sign in schématu](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Diagnostické protokoly služby API Management](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Brány Application Gateway |[Diagnostika protokolování pro službu Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Log analytics pro Azure Automation.](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Protokolování diagnostiky služby Azure Batch](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL diagnostické protokoly](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL diagnostické protokoly](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Cognitive Services | Schéma není k dispozici. |
| Content Delivery Network | [Diagnostické protokoly Azure CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB Logging](../../cosmos-db/logging.md) |
| Data Factory | [Monitorovat datové továrny pomocí Azure monitoru](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Přístup k protokolům diagnostiky pro Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Zobrazení diagnostických protokolů pro Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Diagnostické protokoly Azure Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schéma není k dispozici. |
| Brána Azure Firewall | Schéma není k dispozici. |
| IoT Hub | [Operací služby IoT Hub](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Protokolování v Azure Key Vault](../../key-vault/key-vault-logging.md) |
| Load Balancer |[Log Analytics pro Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Vlastní schéma sledování B2B Logic Apps](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Skupiny zabezpečení sítě) |[Analýza protokolu pro skupiny zabezpečení sítě (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Správa Azure DDoS Standard Protection](../../virtual-network/manage-ddos-protection.md) |
| PowerBI Dedicated | [Protokolování diagnostiky pro Power BI Embedded v Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Datový Model pro zálohování Azure](../../backup/backup-azure-reports-data-model.md)|
| Search |[Povolení a používání prohledání analýzy provozu](../../search/search-traffic-analytics.md) |
| Service Bus |[Diagnostické protokoly Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Protokolování diagnostiky služby Azure SQL Database](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Diagnostické protokoly úloh](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | Schéma není k dispozici. |
| Virtuální sítě | Schéma není k dispozici. |
| Brány virtuálních sítí | Schéma není k dispozici. |

## <a name="supported-log-categories-per-resource-type"></a>Podporované kategorie protokolu na typ prostředku
|Typ prostředku|Kategorie|Zobrazovaný název kategorie|
|---|---|---|
|Microsoft.AnalysisServices/servers|Modul|Modul|
|Microsoft.AnalysisServices/servers|Služba|Služba|
|Microsoft.ApiManagement/service|GatewayLogs|Protokoly týkající se brány ApiManagement|
|Microsoft.Automation/automationAccounts|JobLogs|Protokoly úloh|
|Microsoft.Automation/automationAccounts|JobStreams|Datové proudy úlohy|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Stav uzlu DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Protokoly služby|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Získá metriky koncového bodu, třeba šířku pásma, výchozí přenos atd.|
|Microsoft.ClassicNetwork/networksecuritygroups|Událost toku pravidel skupiny zabezpečení sítě|Událost toku pravidel skupiny zabezpečení sítě|
|Microsoft.CognitiveServices/accounts|Auditování|Protokoly auditu|
|Microsoft.CognitiveServices/accounts|Operace RequestResponse|Protokoly požadavků a odpovědí|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Server rozhraní API Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Správce kontroleru Kubernetes|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Automatické škálování clusteru Kubernetes|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Plánovač Kubernetes|
|Microsoft.ContainerService/managedClusters|Guard|Ověřovací webhook|
|Microsoft.CustomerInsights/hubs|Použitím objektů Auditevent|Použitím objektů Auditevent|
|Microsoft.DataFactory/factories|ActivityRuns|Kanál protokolu spuštění aktivit|
|Microsoft.DataFactory/factories|PipelineRuns|Spuštění kanálu protokolu|
|Microsoft.DataFactory/factories|TriggerRuns|Spuštění aktivační události protokolu|
|Microsoft.DataLakeAnalytics/accounts|Auditování|Protokoly auditu|
|Microsoft.DataLakeAnalytics/accounts|Požadavky|Vyžádat protokoly|
|Microsoft.DataLakeStore/accounts|Auditování|Protokoly auditu|
|Microsoft.DataLakeStore/accounts|Požadavky|Vyžádat protokoly|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|Protokoly serveru MySQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL Server Logs|
|Microsoft.Devices/IotHubs|Připojení|Připojení|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Telemetrie zařízení|
|Microsoft.Devices/IotHubs|C2DCommands|Příkazy C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operace Identity zařízení|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operace nahrávání souborů|
|Microsoft.Devices/IotHubs|Trasy|Trasy|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Operace Dvojčete C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Dotazy dvojčete|
|Microsoft.Devices/IotHubs|JobsOperations|Operace úloh|
|Microsoft.Devices/IotHubs|DirectMethods|Přímé metody|
|Microsoft.Devices/IotHubs|E2EDiagnostics|Diagnostika e2e (Preview)|
|Microsoft.Devices/IotHubs|Konfigurace|Konfigurace|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operace zařízení|
|Microsoft.Devices/provisioningServices|Operací služeb|Operace služby|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Archivovat protokoly|
|Microsoft.EventHub/namespaces|OperationalLogs|Provozní protokoly|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Automatické škálování protokoly|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|Vyhodnocení automatického škálování|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|Akce automatického škálování|
|Microsoft.IoTSpaces/Graph|Trasování|Trasování|
|Microsoft.IoTSpaces/Graph|Provozní|Provozní|
|Microsoft.IoTSpaces/Graph|Auditování|Auditování|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Příchozí přenos dat|Příchozí přenos dat|
|Microsoft.IoTSpaces/Graph|Výchozí přenos|Výchozí přenos|
|Microsoft.KeyVault/vaults|AuditEvent|Protokoly auditu|
|Microsoft.Logic/workflows|Modul runtime|Diagnostické události modulu runtime pracovního postupu|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Sledovat události u účtu pro integraci|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Událost skupiny zabezpečení sítě|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Čítač pravidlo skupiny zabezpečení sítě|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Události výstrah nástroje pro vyrovnávání zatížení|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stav služby Load Balancer sondy stavu|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Oznámení ochrany před útoky DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|Tok protokoly rozhodnutí o omezení rizik před útoky DDoS|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|Sestavy zmírnění před útoky DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Oznámení ochrany virtuálního počítače|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Protokol přístupu aplikační brány|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Protokol výkon aplikační brány|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Protokol aplikační brány Firewall|
|Microsoft.Network/securegateways|AzureFirewallApplicationRule|Pravidlo brány Firewall Azure aplikace|
|Microsoft.Network/securegateways|AzureFirewallNetworkRule|Pravidlo brány Firewall Azure sítě|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Pravidlo brány Firewall Azure aplikace|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Pravidlo brány Firewall Azure sítě|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Diagnostické protokoly brány|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Diagnostické protokoly tunelového propojení|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Směrování diagnostických protokolů|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE diagnostické protokoly|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Diagnostické protokoly P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Událost výsledky testu stavu Traffic Manageru|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Vytvoření partnerského vztahu protokoly tabulky směrování|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Protokol Frontdoor přístupu|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Protokol brány Firewall Frontdoor webové aplikace|
|Microsoft.PowerBIDedicated/capacities|Modul|Modul|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Data pro generování sestav Azure Backup|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Úlohy Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Události Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikované položky|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Statistika replikace Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Body obnovení pro obnovení lokality Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Rychlost odesílání dat replikace Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Četnost změn dat na Disk chráněný Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Protokoly operací|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Provozní protokoly|
|Microsoft.Sql/servers/databases|SQLInsights|Přehledy SQL|
|Microsoft.Sql/servers/databases|AutomaticTuning|Automatické ladění|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Statistické údaje o Query Store|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Statistiky čekání Query Store|
|Microsoft.Sql/servers/databases|Chyby|Chyby|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Statistiky čekání databáze|
|Microsoft.Sql/servers/databases|Časové limity|Časové limity|
|Microsoft.Sql/servers/databases|bloky|bloky|
|Microsoft.Sql/servers/databases|Zablokování|Zablokování|
|Microsoft.Sql/servers/databases|Auditování|Protokoly auditu|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|
|Microsoft.Sql/servers/databases|DmsWorkers|DMS pracovních procesů|
|Microsoft.Sql/servers/databases|ExecRequests|Exec – požadavky|
|Microsoft.Sql/servers/databases|RequestSteps|Žádostí o kroky|
|Microsoft.Sql/servers/databases|SqlRequests|Požadavky na SQL|
|Microsoft.Sql/servers/databases|Čeká|Čeká|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Statistiky o využití prostředků|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|Událost auditu zabezpečení SQL|
|Microsoft.Sql/managedInstances/databases|SQLInsights|Přehledy SQL|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|Statistické údaje o Query Store|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|Statistiky čekání Query Store|
|Microsoft.Sql/managedInstances/databases|Chyby|Chyby|
|Microsoft.StreamAnalytics/streamingjobs|Spouštěcí|Spouštěcí|
|Microsoft.StreamAnalytics/streamingjobs|Vytváření obsahu|Vytváření obsahu|
|microsoft.web/sites|FunctionExecutionLogs|Protokoly spuštění – funkce|
|microsoft.web/sites/slots|FunctionExecutionLogs|Protokoly spuštění – funkce|

## <a name="next-steps"></a>Další kroky

* [Další informace o diagnostických protokolů](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Stream protokolů diagnostiky prostředků k **Event Hubs**](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)
* [Změnit nastavení diagnostiky prostředků pomocí REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analýza protokolů ze služby Azure storage s využitím Log Analytics](../../azure-monitor/platform/collect-azure-metrics-logs.md)

