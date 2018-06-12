---
title: Azure diagnostických protokolů podporovaných služeb a schémat.
description: Porozumět schéma služby a událostí podporovaných pro diagnostických protokolů Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 6/08/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 45595893a199b845c8b010bc1e2545b89aa688cd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264975"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Podporované služby, schémat a kategorie pro diagnostických protokolů Azure.

[Diagnostické protokoly prostředků Azure](monitoring-overview-of-diagnostic-logs.md) protokoly vysílaných vašich prostředků Azure, které popisují operaci prostředku. Tyto protokoly jsou typu prostředku konkrétní. V tomto článku jsme popisují sadu podporované služby a schématu události pro události vygenerované podle jednotlivých služeb. Tento článek také obsahuje úplný seznam dostupných protokolu kategorií podle typu prostředku.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>Podporované služby a schémat pro diagnostické protokoly prostředků
Schéma pro prostředek diagnostických protokolů se liší v závislosti na kategorii prostředků a protokolu.   

| Služba | Schéma & dokumentace |
| --- | --- |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Diagnostické protokoly rozhraní API pro správu](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Brány Application Gateway |[Protokolování diagnostiky pro službu Application Gateway](../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Analýzy protokolů pro Azure Automation.](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Protokolování diagnostiky Azure Batch](../batch/batch-diagnostics.md) |
| Content Delivery Network | [Azure diagnostické protokoly pro CDN](../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Protokolování Azure Cosmos DB](../cosmos-db/logging.md) |
| Data Factory | [Monitorovat pomocí monitorování Azure Data Factory](../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Přístup k protokolům diagnostiky pro Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Přístup k diagnostickým protokolům pro Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| DB pro PostgreSQL |  Schéma není k dispozici. |
| Event Hubs |[Diagnostické protokoly služby Azure Event Hubs](../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schéma není k dispozici. |
| IoT Hub | [Operace služby IoT Hub](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Protokolování v Azure Key Vault](../key-vault/key-vault-logging.md) |
| Load Balancer |[Log Analytics pro Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Vlastní schéma sledování B2B Logic Apps](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Skupiny zabezpečení sítě) |[Analýza protokolu pro skupiny zabezpečení sítě (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS Protection | [Správa Azure DDoS ochrany Standard](../virtual-network/manage-ddos-protection.md) |
| Vyhrazené PowerBI | Schéma není k dispozici. |
| Recovery Services | [Datový Model pro zálohování Azure](../backup/backup-azure-reports-data-model.md)|
| Search |[Povolení a používání Analýza provozu vyhledávání](../search/search-traffic-analytics.md) |
| Service Bus |[Diagnostické protokoly služby Azure Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Protokolování diagnostiky Azure SQL Database](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Diagnostické protokoly úlohy](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | Schéma není k dispozici. |
| Virtuální sítě | Schéma není k dispozici. |
| Brány virtuálních sítí | Schéma není k dispozici. |

## <a name="supported-log-categories-per-resource-type"></a>Podporované kategorií protokolu na typ prostředku
|Typ prostředku|Kategorie|Zobrazovaný název kategorie|
|---|---|---|
|Microsoft.AnalysisServices/servers|Modul|Modul|
|Microsoft.AnalysisServices/servers|Služba|Služba|
|Microsoft.ApiManagement/service|GatewayLogs|Protokoly související s ApiManagement brány|
|Microsoft.Automation/automationAccounts|JobLogs|V protokolech úloh|
|Microsoft.Automation/automationAccounts|JobStreams|Datové proudy úlohy|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Stav uzlu DSC|
|Microsoft.Batch/batchAccounts|ServiceLog|Protokoly služby|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Získá metriky koncového bodu, třeba šířku pásma, výchozí přenos atd.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|Kanál protokolu spuštění aktivit|
|Microsoft.DataFactory/factories|PipelineRuns|Kanál spustí protokolu|
|Microsoft.DataFactory/factories|TriggerRuns|Trigger spouští protokolu|
|Microsoft.DataLakeAnalytics/accounts|Auditování|Protokoly auditu|
|Microsoft.DataLakeAnalytics/accounts|Požadavky|Žádost o protokoly|
|Microsoft.DataLakeStore/accounts|Auditování|Protokoly auditu|
|Microsoft.DataLakeStore/accounts|Požadavky|Žádost o protokoly|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Protokoly serveru PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLBackupEvents|Události PostgreSQL zálohování|
|Microsoft.Devices/IotHubs|Připojení|Připojení|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Zařízení Telemetrie|
|Microsoft.Devices/IotHubs|C2DCommands|Příkazy C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operace Identity zařízení|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operace nahrávání souborů|
|Microsoft.Devices/IotHubs|Trasy|Trasy|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Operace C2D Twin|
|Microsoft.Devices/IotHubs|TwinQueries|Dotazy Twin|
|Microsoft.Devices/IotHubs|JobsOperations|Operace úlohy|
|Microsoft.Devices/IotHubs|DirectMethods|Přímé metody|
|Microsoft.Devices/IotHubs|E2EDiagnostics|Diagnostika e2e (Preview)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Operace zařízení|
|Microsoft.Devices/provisioningServices|Operací služeb|Operace služby|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Protokoly archivu|
|Microsoft.EventHub/namespaces|OperationalLogs|Operační protokoly|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Automatické škálování protokoly|
|Microsoft.KeyVault/vaults|AuditEvent|Protokoly auditu|
|Microsoft.Logic/workflows|Modul runtime pracovního postupu|Diagnostické události modulu runtime pracovního postupu|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Sledovat události u účtu pro integraci|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Událost skupiny zabezpečení sítě|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Čítač pravidel skupiny zabezpečení sítě|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|Události výstrah služby Vyrovnávání zatížení|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stavu kontroly stavu služby Vyrovnávání zatížení|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Oznámení ochrany DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Výstrahy ochrany virtuálních počítačů|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Aplikace v protokolu přístupu brány|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Aplikace v protokolu výkonu brány|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|V protokolu aplikace brány Firewall|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Diagnostické protokoly brány|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Diagnostické protokoly tunelového propojení|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Diagnostické protokoly trasy|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE diagnostické protokoly|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Diagnostické protokoly P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Událost výsledky testu stavu Traffic Manageru|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Tabulka GWM čítačů|
|Microsoft.PowerBIDedicated/capacities|Modul|Modul|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Zálohování Azure Data pro vytváření sestav|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Úlohy Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery události|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery replikované položky|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery replikaci statistiky|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Body obnovení pro obnovení lokality Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Rychlost odesílání Azure Site obnovení replikace dat|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Mísení dat Disk chráněný Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Protokoly operací|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Operační protokoly|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Úložiště dotazů statistiku modulu Runtime|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Úložiště dotazů čekání statistiky|
|Microsoft.Sql/servers/databases|Chyby|Chyby|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Databáze statistiky čekání|
|Microsoft.Sql/servers/databases|Časové limity|Časové limity|
|Microsoft.Sql/servers/databases|Bloky|Bloky|
|Microsoft.Sql/servers/databases|SQLInsights|Přehledy SQL|
|Microsoft.Sql/servers/databases|Auditování|Protokoly auditu|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Události auditu zabezpečení SQL|
|Microsoft.StreamAnalytics/streamingjobs|Spouštěcí|Spouštěcí|
|Microsoft.StreamAnalytics/streamingjobs|Vytváření obsahu|Vytváření obsahu|

## <a name="next-steps"></a>Další kroky

* [Další informace o diagnostických protokolů](monitoring-overview-of-diagnostic-logs.md)
* [Stream prostředků do diagnostickým protokolům **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Změňte nastavení pro diagnostiku prostředků pomocí rozhraní REST API Azure monitorování](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analýza protokolů z úložiště Azure s analýzy protokolů](../log-analytics/log-analytics-azure-storage.md)
