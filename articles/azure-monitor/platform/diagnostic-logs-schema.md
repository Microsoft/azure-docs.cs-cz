---
title: Podporované služby a schémata azure resourcelogs
description: Seznamte se s podporovanými službami a schématem událostí pro protokoly prostředků Azure.
ms.subservice: logs
ms.topic: reference
ms.date: 10/22/2019
ms.openlocfilehash: 352310a6e489a96c38e85b16e9504d8eb9be38b1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457242"
---
# <a name="supported-services-schemas-and-categories-for-azure-resource-logs"></a>Podporované služby, schémata a kategorie pro protokoly prostředků Azure

> [!NOTE]
> Protokoly prostředků byly dříve označovány jako diagnostické protokoly.

[Protokoly prostředků Azure Monitor](../../azure-monitor/platform/platform-logs-overview.md) jsou protokoly vyzařované službami Azure, které popisují provoz těchto služeb nebo prostředků. Všechny protokoly prostředků dostupné prostřednictvím Služby Azure Monitor sdílejí společné schéma nejvyšší úrovně s flexibilitou pro každou službu k vyzařování jedinečných vlastností pro své vlastní události.

Kombinace typu prostředku (k dispozici `resourceId` ve vlastnosti) `category` a jednoznačně identifikovat schéma. Tento článek popisuje schéma nejvyšší úrovně pro protokoly prostředků a odkazy na schéma pro každou službu.

## <a name="top-level-resource-logs-schema"></a>Schéma protokolů prostředků nejvyšší úrovně

| Název | Požadováno/volitelné | Popis |
|---|---|---|
| time | Požaduje se | Časové razítko (UTC) události. |
| resourceId | Požaduje se | ID prostředku, který událost emitoval. Pro služby tenanta se jedná o formulář /tenants/tenant-id/providers/provider-name. |
| tenantId | Požadováno pro protokoly klienta | ID klienta klienta služby Active Directory, se kterým je tato událost svázána. Tato vlastnost se používá pouze pro protokoly na úrovni klienta, nezobrazí se v protokolech na úrovni prostředků. |
| operationName | Požaduje se | Název operace reprezentované touto událostí. Pokud událost představuje operaci RBAC, toto je název operace RBAC (např. Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Obvykle modelováno ve formě operace Správce prostředků, i když se nejedná`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`o skutečné operace správce prostředků ( ) |
| operationVersion | Nepovinné | Api-verze spojená s operací, pokud operationName byla provedena pomocí API (např. `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Pokud neexistuje žádné rozhraní API, které odpovídá této operaci, verze představuje verzi této operace v případě, že vlastnosti spojené s operací změnit v budoucnu. |
| category | Požaduje se | Kategorie protokolu události. Kategorie je rozlišovací schopnost, při které můžete povolit nebo zakázat protokoly na konkrétní prostředek. Vlastnosti, které se zobrazují v objektu blob vlastností události, jsou stejné v rámci určité kategorie protokolu a typu prostředku. Typické kategorie protokolu jsou "Audit" "Provozní" "Spuštění" a "Požadavek". |
| resultType | Nepovinné | Stav události. Mezi typické hodnoty patří Spuštěno, Probíhá, Úspěšné, Neúspěšné, Aktivní a Vyřešeno. |
| resultSignature | Nepovinné | Dílčí stav události. Pokud tato operace odpovídá volání rozhraní REST API, jedná se o stavový kód HTTP odpovídajícího volání REST. |
| resultDescription | Nepovinné | Statický textový popis této operace, např. "Získejte soubor úložiště." |
| durationMs | Nepovinné | Doba trvání operace v milisekundách. |
| callerIpAddress | Nepovinné | IP adresa volajícího, pokud operace odpovídá volání rozhraní API, které by pocházet z entity s veřejně dostupnou IP adresu. |
| correlationId | Nepovinné | Identifikátor GUID slouží k seskupení sadu souvisejících událostí. Obvykle, pokud dvě události mají stejnou operaciName, ale dva různé stavy (např. "Spuštěno" a "Úspěšné"), sdílejí stejné ID korelace. To může také představovat další vztahy mezi událostmi. |
| identity | Nepovinné | Objekt blob JSON, který popisuje identitu uživatele nebo aplikace, která provedla operaci. Obvykle to bude zahrnovat autorizaci a deklarace identity / Token JWT ze služby Active Directory. |
| Úroveň | Nepovinné | Úroveň závažnosti události. Musí být jeden z informační, upozornění, chyba nebo kritické. |
| location | Nepovinné | Oblast zdroje vyzařující událost, např. "Východní USA" nebo "Francie – jih" |
| properties | Nepovinné | Všechny rozšířené vlastnosti související s touto konkrétní kategorie událostí. Všechny vlastní/jedinečné vlastnosti musí být vloženy do této "části B" schématu. |

## <a name="service-specific-schemas-for-resource-logs"></a>Schémata specifická pro službu pro protokoly prostředků
Schéma pro protokoly diagnostiky prostředků se liší v závislosti na prostředku a kategorii protokolu. Tento seznam zobrazuje všechny služby, které zpřístupňují protokoly prostředků a odkazy na službu a schéma specifické pro kategorii, pokud jsou k dispozici.

| Služba | Dokumenty schématu & |
| --- | --- |
| Azure Active Directory | [Přehled](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [schéma protokolů auditu](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) a schéma [přihlášení](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API Management | [Protokoly prostředků správy rozhraní API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Brány Application Gateway |[Protokolování pro aplikační bránu](../../application-gateway/application-gateway-diagnostics.md) |
| Azure Automation |[Analýza protokolů pro Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Protokolování dávek Azure](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL diagnostické protokoly](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database pro postgreSQL protokoly](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Průzkumník dat Azure | [Protokoly Průzkumníka dat Azure](/azure/data-explorer/using-diagnostic-logs) |
| Cognitive Services | [Protokolování pro Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Container Registry | [Protokolování pro registr kontejnerů Azure](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| Content Delivery Network | [Azure protokoly pro CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Protokolování Azure Cosmos DB](../../cosmos-db/logging.md) |
| Data Factory | [Monitorování datových továren pomocí Azure Monitoru](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Přístup k protokolům pro Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Přístup k protokolům pro Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Event Hubs |[Protokoly centra událostí Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Schéma není k dispozici. |
| Brána Azure Firewall | Schéma není k dispozici. |
| IoT Hub | [Operace ioT hubu](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Protokolování trezoru klíčů Azure](../../key-vault/general/logging.md) |
| Kubernetes Service |[Azure Kubernetes protokolování](../../aks/view-master-logs.md#log-event-schema) |
| Load Balancer |[Log Analytics pro Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Vlastní schéma sledování Logic Apps B2B](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Network Security Groups (Skupiny zabezpečení sítě) |[Analýza protokolu pro skupiny zabezpečení sítě (NSG)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Ochrana před útoky DDoS | [Správa standardu ochrany Azure DDoS](../../virtual-network/manage-ddos-protection.md) |
| Vyhrazená služba Power BI | [Protokolování pro Power BI vložené v Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Datový model pro zálohování Azure](../../backup/backup-azure-reports-data-model.md)|
| Search |[Povolení a používání analýzy návštěvnosti vyhledávání](../../search/search-traffic-analytics.md) |
| Service Bus |[Protokoly Azure Service Bus](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Databáze SQL | [Protokolování databáze Azure SQL](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Protokoly úloh](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Traffic Manager | [Schéma protokolu správce provozu](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Virtuální sítě | Schéma není k dispozici. |
| Brány virtuálních sítí | Schéma není k dispozici. |

## <a name="supported-log-categories-per-resource-type"></a>Podporované kategorie protokolů podle typu prostředku

Některé kategorie mohou být podporovány pouze pro určité typy prostředků. Toto je seznam všech, které jsou k dispozici v nějaké formě.  Například kategorie Microsoft.Sql/servers/databases nejsou k dispozici pro všechny typy databází. Další informace naleznete v [informacích o protokolování diagnostiky databáze SQL](../../sql-database/sql-database-metrics-diag-logging.md). 

|Typ prostředku|Kategorie|Zobrazovaný název kategorie|
|---|---|---|
|Microsoft.AAD/domainServices|Zabezpečení systému|Zabezpečení systému|
|Microsoft.AAD/domainServices|Správa účtů|Správa účtů|
|Microsoft.AAD/domainServices|Odhlášení|Odhlášení|
|Microsoft.AAD/domainServices|ObjectAccess|ObjectAccess|
|Microsoft.AAD/domainServices|Změna zásad|Změna zásad|
|Microsoft.AAD/domainServices|PrivilegeUse|PrivilegeUse|
|Microsoft.AAD/domainServices|Podrobné sledování|Podrobné sledování|
|Microsoft.AAD/domainServices|DirectoryServiceAccess|DirectoryServiceAccess|
|Microsoft.AAD/domainServices|Přihlášení k účtu|Přihlášení k účtu|
|microsoft.aadiam/tenants|Signin|Signin|
|Microsoft.AnalysisServices/servery|Modul|Modul|
|Microsoft.AnalysisServices/servery|Služba|Služba|
|Microsoft.ApiManagement/service|GatewayLogs|Protokoly související s bránou ApiManagement Gateway|
|Microsoft.AppPlatform/jaro|Konzola aplikace|Konzola aplikace|
|Microsoft.Automation/automationÚčty|Pracovní protokoly|Protokoly úloh|
|Microsoft.Automation/automationÚčty|JobStreams|Toky úloh|
|Microsoft.Automation/automationÚčty|DscNodeStatus|Stav uzlu Dsc|
|Microsoft.Batch/batchAccounts|Protokol služeb|Protokoly služeb|
|Pracovní prostory Microsoft.BatchAI|Událost BaiClusterEvent|Událost BaiClusterEvent|
|Pracovní prostory Microsoft.BatchAI|Událost BaiClusterNodeEvent|Událost BaiClusterNodeEvent|
|Pracovní prostory Microsoft.BatchAI|BaiJobEvent|BaiJobEvent|
|Microsoft.Blockchain/blockchainČlenové|BlockchainAplikace|Aplikace Blockchain|
|Microsoft.Blockchain/blockchainČlenové|Proxy server|Proxy server|
|Microsoft.Cdn/profiles/koncové body|CoreAnalytics|Získá metriky koncového bodu, například šířku pásma, odchozí, atd.|
|Microsoft.ClassicNetwork/networksecuritygroups|Událost toku pravidel skupiny zabezpečení sítě|Událost toku pravidel skupiny zabezpečení sítě|
|Microsoft.CognitiveServices/účty|Auditování|Protokoly auditu|
|Microsoft.CognitiveServices/účty|Požadavek na odpověď|Protokoly požadavků a odpovědí|
|Microsoft.ContainerRegistry/registry|Události ContainerRegistryRepositoryEvents|Protokoly repositoryEvent (náhled)|
|Microsoft.ContainerRegistry/registry|ContainerRegistryLoginEvents|Události přihlášení (náhled)|
|Microsoft.ContainerService/spravované clustery|kube-apiserver|Kubernetes API Server|
|Microsoft.ContainerService/spravované clustery|kube-controller-manager|Správce regulátorů Kubernetes|
|Microsoft.ContainerService/spravované clustery|kube-plánovač|Kubernetes Plánovač|
|Microsoft.ContainerService/spravované clustery|kube-audit|Kubernetesův audit|
|Microsoft.ContainerService/spravované clustery|automatické škálování clusteru|Autoscaler clusteru Kubernetes|
|Microsoft.Databricks/pracovní prostory|dbfs|Systém souborů Databricks|
|Microsoft.Databricks/pracovní prostory|Clustery|Clustery datových cihel|
|Microsoft.Databricks/pracovní prostory|accounts|Účty Databricks|
|Microsoft.Databricks/pracovní prostory|Úlohy|Databricks Práce|
|Microsoft.Databricks/pracovní prostory|poznámkový blok|Poznámkový blok Databricks|
|Microsoft.Databricks/pracovní prostory|Ssh|Datové cihly SSH|
|Microsoft.Databricks/pracovní prostory|Pracovní prostor|Pracovní prostor Datové cihly|
|Microsoft.Databricks/pracovní prostory|Tajemství|Databricks Tajemství|
|Microsoft.Databricks/pracovní prostory|sqlPermissions|Databricks SQLPermissions|
|Microsoft.Databricks/pracovní prostory|instancePooly|Fondy instancí|
|Microsoft.DataCatalog/datacatalogs|ScanStatusLogEvent|ScanStatus|
|Microsoft.DataFactory/továrny|Počet aktivit|Protokol aktivity kanálu|
|Microsoft.DataFactory/továrny|PipelineRuns|Protokol spuštění kanálu|
|Microsoft.DataFactory/továrny|Spuštění aktivační události|Protokol spuštění aktivační události|
|Microsoft.DataLakeAnalytics/účty|Auditování|Protokoly auditu|
|Microsoft.DataLakeAnalytics/účty|Žádosti|Protokoly požadavků|
|Microsoft.DataLakeStore/účty|Auditování|Protokoly auditu|
|Microsoft.DataLakeStore/účty|Žádosti|Protokoly požadavků|
|Microsoft.DataShare/účty|Sdílené složky|Sdílené složky|
|Microsoft.DataShare/účty|ShareSubscriptions|Sdílet předplatná|
|Microsoft.DataShare/účty|SentShareSnapshots|Odeslané snímky sdílené složky|
|Microsoft.DataShare/účty|Funkce ReceivedShareSnapshots|Přijaté snímky sdílené složky|
|Microsoft.DBforMySQL/servery|MySqlSlowLogs|Protokoly serveru MySQL|
|Microsoft.DBforMySQL/servery|MySqlAuditLogs|Protokoly auditu MySQL|
|Microsoft.DBforPostgreSQL/servery|PostgreSQLLogs|Protokoly serveru PostgreSQL|
|Microsoft.DBforPostgreSQL/servery|Statistiky běhu dotazu|Statistiky modulu runtime úložiště dotazů PostgreSQL|
|Microsoft.DBforPostgreSQL/servery|Statistiky QueryStoreWait|Statistiky čekání úložiště dotazů PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|PostgreSQLLogs|Protokoly serveru PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|Statistiky běhu dotazu|Statistiky modulu runtime úložiště dotazů PostgreSQL|
|Microsoft.DBforPostgreSQL/serversv2|Statistiky QueryStoreWait|Statistiky čekání úložiště dotazů PostgreSQL|
|Microsoft.DesktopVirtualization/pracovní prostory|CheckPoint|CheckPoint|
|Microsoft.DesktopVirtualization/pracovní prostory|Chyba|Chyba|
|Microsoft.DesktopVirtualization/pracovní prostory|Správa|Správa|
|Microsoft.DesktopVirtualization/pracovní prostory|Informační kanál|Informační kanál|
|Microsoft.DesktopVirtualization/applicationGroups|CheckPoint|CheckPoint|
|Microsoft.DesktopVirtualization/applicationGroups|Chyba|Chyba|
|Microsoft.DesktopVirtualization/applicationGroups|Správa|Správa|
|Microsoft.DesktopVirtualization/hostPools|CheckPoint|CheckPoint|
|Microsoft.DesktopVirtualization/hostPools|Chyba|Chyba|
|Microsoft.DesktopVirtualization/hostPools|Správa|Správa|
|Microsoft.DesktopVirtualization/hostPools|Připojení|Připojení|
|Microsoft.DesktopVirtualization/hostPools|Registrace hostitele|Registrace hostitele|
|Microsoft.Devices/IotHubs|Připojení|Připojení|
|Microsoft.Devices/IotHubs|Telemetrie zařízení|Telemetrie zařízení|
|Microsoft.Devices/IotHubs|C2DPříkazy|Příkazy C2D|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Operace identity zařízení|
|Microsoft.Devices/IotHubs|FileUploadOperations|Operace nahrávání souborů|
|Microsoft.Devices/IotHubs|Trasy|Trasy|
|Microsoft.Devices/IotHubs|D2CTwinOperations|D2CTwinOperations|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Operace dvojčete C2D|
|Microsoft.Devices/IotHubs|TwinQueries|Dvojité dotazy|
|Microsoft.Devices/IotHubs|JobsOperations|Operace úloh|
|Microsoft.Devices/IotHubs|Přímé metody|Přímé metody|
|Microsoft.Devices/IotHubs|Distribuované trasování|Distribuované trasování (náhled)|
|Microsoft.Devices/IotHubs|Konfigurace|Konfigurace|
|Microsoft.Devices/IotHubs|Streamy zařízení|Datové proudy zařízení (náhled)|
|Microsoft.Devices/provisioningServices|Provoz zařízení|Provoz zařízení|
|Microsoft.Devices/provisioningServices|ServiceOperations|Operace služby|
|Microsoft.DocumentDB/databaseAccounts|Požadavky datových letadel|Požadavky datových letadel|
|Microsoft.DocumentDB/databaseAccounts|MongoPožadavky|MongoPožadavky|
|Microsoft.DocumentDB/databaseAccounts|Statistika času dotazu|Statistika času dotazu|
|Microsoft.DocumentDB/databaseAccounts|Statistika partitionKey|Statistika partitionKey|
|Microsoft.DocumentDB/databaseAccounts|Ovládací požadavky na letadlo|Ovládací požadavky na letadlo|
|Microsoft.EnterpriseKnowledgeGraph/služby|Událost auditu|Protokol události auditu|
|Microsoft.EnterpriseKnowledgeGraph/služby|DataIssue|Protokol DataIssue|
|Microsoft.EnterpriseKnowledgeGraph/služby|Žádosti|Konfigurační protokol|
|Obory názvů Microsoft.EventHub/Namespaces|Archivní logy|Archivní protokoly|
|Obory názvů Microsoft.EventHub/Namespaces|Provozní logy|Provozní protokoly|
|Obory názvů Microsoft.EventHub/Namespaces|AutoScaleLogs|Protokoly automatického škálování|
|Obory názvů Microsoft.EventHub/Namespaces|KafkaCoordinatorLogs|Protokoly koordinátora Kafka|
|Obory názvů Microsoft.EventHub/Namespaces|KafkaUserErrorLogs|Protokoly chyb uživatelů kafka|
|Obory názvů Microsoft.EventHub/Namespaces|Událost EventHubVnetConnectionEvent|Protokoly připojení filtrování virtuální sítě/IP|
|Obory názvů Microsoft.EventHub/Namespaces|CustomerManagedKeyUserLogs|Protokoly klíčů spravované zákazníkem|
|Microsoft.HealthcareApis/služby|Auditní protokoly|Protokoly auditu|
|Microsoft.Insights/AutoscaleSettings|Automatické hodnocení|Hodnocení automatického škálování|
|Microsoft.Insights/AutoscaleSettings|Akce automatického škálování|Akce automatického škálování|
|Microsoft.IoTSpaces/Graph|Trasování|Trasování|
|Microsoft.IoTSpaces/Graph|Operační|Operační|
|Microsoft.IoTSpaces/Graph|Auditování|Auditování|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|Příchozí přenos dat|Příchozí přenos dat|
|Microsoft.IoTSpaces/Graph|Výchozí přenos dat|Výchozí přenos dat|
|Úložiště Microsoft.KeyVault/trezory|Událost auditu|Protokoly auditu|
|Microsoft.Kusto/Clustery|SucceededIngestion|Úspěšné operace ingestování|
|Microsoft.Kusto/Clustery|Neúspěšné požití|Neúspěšné operace ingestování|
|Microsoft.Logic/pracovní postupy|Workflowruntime|Diagnostické události za běhu pracovního postupu|
|Microsoft.Logic/integrationÚčty|IntegrationAccountTrackingEvents|Účet integrace sleduje události|
|Microsoft.MachineLearningServices/pracovní prostory|Událost AmlComputeClusterEvent|Událost AmlComputeClusterEvent|
|Microsoft.MachineLearningServices/pracovní prostory|Událost AmlComputeClusterNodeEvent|Událost AmlComputeClusterNodeEvent|
|Microsoft.MachineLearningServices/pracovní prostory|AmlComputeJobEvent|AmlComputeJobEvent|
|Microsoft.Media/mediaservices|KeyDeliveryPožadavky|Požadavky na doručení klíčů|
|Microsoft.Network/networksecuritygroups|Událost NetworkSecurityGroupEvent|Událost skupiny zabezpečení sítě|
|Microsoft.Network/networksecuritygroups|Čítač pravidel networksecuritygroup|Čítač pravidel skupiny zabezpečení sítě|
|Microsoft.Network/networksecuritygroups|Událost NetworkSecurityGroupFlowEvent|Událost toku pravidel skupiny zabezpečení sítě|
|Microsoft.Network/publicIPAdresy|DDoSProtectionNotifications|Oznámení o ochraně DDoS|
|Microsoft.Network/publicIPAdresy|DDoSMitigationFlowLogs|Tok protokoly rozhodnutí o zmírnění DDoS|
|Microsoft.Network/publicIPAdresy|Sestavy DDoSMitigation|Zprávy o zmírnění DDoS|
|Microsoft.Network/virtualNetworks|Výstrahy ochrany virtuálních počítače|Výstrahy ochrany virtuálních počítače|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Protokol přístupu k aplikační bráně|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Protokol výkonu aplikační brány|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Protokol brány firewall brány aplikace|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Pravidlo aplikace Azure Firewall|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Síťové pravidlo Azure Firewall|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Diagnostické protokoly brány|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Diagnostické protokoly tunelového propojení|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Protokoly diagnostiky tras|
|Microsoft.Network/virtualNetworkGateways|Protokol IKEDiagnosticLog|Diagnostické protokoly protokolu IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Diagnostické protokoly P2S|
|Microsoft.Network/trafficManagerProfiles|Události ProbeHealthStatusEvents|Událost výsledků stavu sondy správce provozu|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|Protokoly směrovacích tabulek partnerského vztahu|
|Microsoft.Network/vpnGateways|GatewayDiagnosticLog|Diagnostické protokoly brány|
|Microsoft.Network/vpnGateways|TunnelDiagnosticLog|Diagnostické protokoly tunelového propojení|
|Microsoft.Network/vpnGateways|RouteDiagnosticLog|Protokoly diagnostiky tras|
|Microsoft.Network/vpnGateways|Protokol IKEDiagnosticLog|Diagnostické protokoly protokolu IKE|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Protokol přístupu předních dveří|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Protokol brány firewall webové aplikace frontdoor|
|Microsoft.Network/p2sVpnBrány|GatewayDiagnosticLog|Diagnostické protokoly brány|
|Microsoft.Network/p2sVpnBrány|Protokol IKEDiagnosticLog|Diagnostické protokoly protokolu IKE|
|Microsoft.Network/p2sVpnBrány|P2SDiagnosticLog|Diagnostické protokoly P2S|
|Microsoft.Network/bastionHosts|BastionAuditLogs|Protokoly auditu bašty|
|Microsoft.Network/loadBalancers|Událost LoadBalancerAlertEvent|Události výstrahy pro vyrovnávání zatížení|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Stav sondy pro vyrovnávání zatížení|
|Microsoft.PowerBIDedicated/kapacity|Modul|Modul|
|Microsoft.RecoveryServices/Vaults|Sestava AzureBackupReport|Data azure sestavy zálohování|
|Microsoft.RecoveryServices/Vaults|CoreAzureBackup|Základní data zálohování Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureZálohy|Data zálohovací úlohy Addon Azure|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupAlerts|Data výstrah zálohování Addon Azure|
|Microsoft.RecoveryServices/Vaults|Zásady zálohování AddonAzure|Data zásad zálohování Azure v dodatku|
|Microsoft.RecoveryServices/Vaults|AddonAzureBackupStorage|Addon Azure Data úložiště zálohování|
|Microsoft.RecoveryServices/Vaults|Instance AddonAzureBackupProtectedInstance|Data instance chráněné zálohování Addon Azure|
|Microsoft.RecoveryServices/Vaults|Úlohy AzureSiteRecovery|Úlohy obnovení webu Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Události obnovení webu Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplikované položky|Replikované položky obnovení webu Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Statistiky replikace obnovení lokality Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Body obnovení webu Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Rychlost odesílání dat replikace obnovení webu Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Konve dat disku chráněného obnovení webu Azure|
|Microsoft.Search/searchServices|Protokoly operací|Protokoly operací|
|Obory názvů Microsoft.ServiceBus|Provozní logy|Provozní protokoly|
|Microsoft.Sql/servery/databáze|SqlInsights|Přehledy SQL|
|Microsoft.Sql/servery/databáze|Automatické ladění|Automatické ladění|
|Microsoft.Sql/servery/databáze|Statistiky běhu dotazu|Statistiky běhu úložiště dotazů|
|Microsoft.Sql/servery/databáze|Statistiky QueryStoreWait|Statistiky čekání úložiště dotazů|
|Microsoft.Sql/servery/databáze|chyby|chyby|
|Microsoft.Sql/servery/databáze|Statistiky databasewait|Statistika čekání databáze|
|Microsoft.Sql/servery/databáze|Časové limity|Časové limity|
|Microsoft.Sql/servery/databáze|Bloky|Bloky|
|Microsoft.Sql/servery/databáze|Zablokování|Zablokování|
|Microsoft.Sql/servery/databáze|Auditování|Protokoly auditu|
|Microsoft.Sql/servery/databáze|SqlSecurityAuditEvents|Událost auditu zabezpečení SQL|
|Microsoft.Sql/servery/databáze|Pracovníci DmsWorkers|Dms Pracovníci|
|Microsoft.Sql/servery/databáze|ExecRequests|Požadavky exec|
|Microsoft.Sql/servery/databáze|RequestSteps|Kroky požadavku|
|Microsoft.Sql/servery/databáze|Požadavky sql|Požadavky SQL|
|Microsoft.Sql/servery/databáze|Čeká|Čeká|
|Microsoft.Sql/managedInstances|ResourceUsageStats|Statistika využití zdrojů|
|Microsoft.Sql/managedInstances|SqlSecurityAuditEvents|Událost auditu zabezpečení SQL|
|Microsoft.Sql/managedInstances/databases|SqlInsights|Přehledy SQL|
|Microsoft.Sql/managedInstances/databases|Statistiky běhu dotazu|Statistiky běhu úložiště dotazů|
|Microsoft.Sql/managedInstances/databases|Statistiky QueryStoreWait|Statistiky čekání úložiště dotazů|
|Microsoft.Sql/managedInstances/databases|chyby|chyby|
|Microsoft.Storage/storageAccounts/tableServices|SkladováníČtení|SkladováníČtení|
|Microsoft.Storage/storageAccounts/tableServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/tableServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/blobServices|SkladováníČtení|SkladováníČtení|
|Microsoft.Storage/storageAccounts/blobServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/blobServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/fileServices|SkladováníČtení|SkladováníČtení|
|Microsoft.Storage/storageAccounts/fileServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/fileServices|StorageDelete|StorageDelete|
|Microsoft.Storage/storageAccounts/queueServices|SkladováníČtení|SkladováníČtení|
|Microsoft.Storage/storageAccounts/queueServices|StorageWrite|StorageWrite|
|Microsoft.Storage/storageAccounts/queueServices|StorageDelete|StorageDelete|
|Microsoft.StreamAnalytics/streamingjobs|Spouštěcí|Spouštěcí|
|Microsoft.StreamAnalytics/streamingjobs|Vytváření obsahu|Vytváření obsahu|
|Microsoft.web/hostingenvironments|AppServiceEnvironmentPlatformLogs|Protokoly platformy prostředí služby App Service|
|Microsoft.web/weby|FunctionAppLogs|Protokoly aplikací funkcí|
|Microsoft.web/weby|Protokoly HTTPSlužby AppService|Protokoly HTTP|
|Microsoft.web/weby|AppServiceConsoleLogs|Protokoly konzoly služby App|
|Microsoft.web/weby|AppServiceAppLogs|Protokoly aplikací služby App Service|
|Microsoft.web/weby|AppServiceFileAuditLogs|Protokoly auditu změny obsahu webu|
|Microsoft.web/weby|AppServiceAuditLogs|Protokoly auditování aplikace Access|
|microsoft.web/sites/sloty|FunctionAppLogs|Protokoly aplikací funkcí|
|microsoft.web/sites/sloty|Protokoly HTTPSlužby AppService|Protokoly HTTP|
|microsoft.web/sites/sloty|AppServiceConsoleLogs|Protokoly konzoly|
|microsoft.web/sites/sloty|AppServiceAppLogs|Protokoly aplikací|
|microsoft.web/sites/sloty|AppServiceFileAuditLogs|Protokoly auditu změny obsahu webu|
|microsoft.web/sites/sloty|AppServiceAuditLogs|Protokoly auditování aplikace Access|

## <a name="next-steps"></a>Další kroky

* [Další informace o protokolech prostředků](../../azure-monitor/platform/platform-logs-overview.md)
* [Streamování protokolů prostředků prostředků do **centra událostí**](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [Změna nastavení diagnostiky protokolu prostředků pomocí rozhraní AZURE Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [Analýza protokolů z úložiště Azure pomocí analýzy protokolů](../../azure-monitor/platform/collect-azure-metrics-logs.md)
