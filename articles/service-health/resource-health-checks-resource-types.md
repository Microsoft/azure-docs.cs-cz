---
title: Podporované typy prostředků prostřednictvím Azure Resource Health | Microsoft Docs
description: Podporované typy prostředků prostřednictvím služby Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 3a61c8b90f4dc5e52a875488afb5374e3d6b7ce4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717069"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Typy prostředků a kontroly stavu v Azure Resource Health
Níže je uvedený úplný seznam všech kontrol provedených prostřednictvím stavu prostředků podle typů prostředků.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Provedené kontroly|
|---|
|<ul><li>Je server v provozu a běží?</li><li>Má server nedostatek paměti?</li><li>Spouští se server?</li><li>Obnovuje se server?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Provedené kontroly|
|---|
|<ul><li>Je služba API Management zapnutá a spuštěná?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Provedené kontroly|
|---|
|<ul><li>Je účet Batch zapnutý a spuštěný?</li><li>Překročila se kvóta fondu pro tento účet Batch?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Provedené kontroly|
|---|
|<ul><li>Jsou všechny uzly mezipaměti v provozu a jsou spuštěné?</li><li>Je možné dosáhnout mezipaměti v datovém centru?</li><li>Dosáhla by mezipaměť maximálního počtu připojení?</li><li> Vyčerpala mezipaměť dostupnou paměť? </li><li>Dochází v mezipaměti k velkému počtu chyb stránek?</li><li>Je mezipaměť v případě velkého zatížení?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Provedené kontroly|
|---|
|<ul> <li>Je doplňkový portál přístupný pro operace konfigurace CDN?</li><li>Dochází k průběžným problémům s doručováním s koncovými body CDN?</li><li>Můžou uživatelé měnit konfiguraci svých prostředků CDN?</li><li>Šíří se změny konfigurace za očekávanou rychlost?</li><li>Můžou uživatelé spravovat konfiguraci CDN pomocí Azure Portal, PowerShellu nebo rozhraní API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. classiccompute/VirtualMachines
|Provedené kontroly|
|---|
|<ul><li>Je hostitelský server v provozu a běží?</li><li>Je spuštění hostitelského operačního systému dokončeno?</li><li>Je kontejner virtuálních počítačů zřízený a zapnutý?</li><li>Je mezi hostitelem a účtem úložiště připojení k síti?</li><li>Bylo spuštění hostovaného operačního systému dokončeno?</li><li>Probíhá plánovaná údržba?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. cognitiveservices Account/účty
|Provedené kontroly|
|---|
|<ul><li>Je možné, že se k účtu dostane v datacentru?</li><li>Je poskytovatel prostředků Cognitive Services k dispozici?</li><li>Je služba rozpoznávání dostupná v příslušné oblasti?</li><li>Můžou se operace čtení provádět na účtu úložiště, který uchovává metadata prostředků?</li><li>Bylo dosaženo kvóty volání rozhraní API?</li><li>Dosáhlo se limitu pro čtení, volání rozhraní API?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft. COMPUTE/VirtualMachines
|Provedené kontroly|
|---|
|<ul><li>Je server hostující tento virtuální počítač v provozu a běží?</li><li>Je spuštění hostitelského operačního systému dokončeno?</li><li>Je kontejner virtuálních počítačů zřízený a zapnutý?</li><li>Je mezi hostitelem a účtem úložiště připojení k síti?</li><li>Bylo spuštění hostovaného operačního systému dokončeno?</li><li>Probíhá plánovaná údržba?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/továrny
|Provedené kontroly|
|---|
|<ul><li>Došlo k chybám při spuštění kanálu?</li><li>Je cluster hostující Data Factory v pořádku?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. datalakeanalytics/účty
|Provedené kontroly|
|---|
|<ul><li>Měli by uživatelé narazili na problémy při odesílání nebo výpisu svých Data Lake Analyticsch úloh?</li><li>Jsou Data Lake Analytics úlohy nemohly být dokončeny kvůli chybám systému?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Provedené kontroly|
|---|
|<ul><li>Měli by uživatelé narazili na problémy při nahrávání dat do Data Lake Store?</li><li>Měli by uživatelé narazili na problémy se stahováním dat z Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Provedené kontroly|
|---|
|<ul><li>Nepovedlo se zřídit službu Database Migration Service?</li><li>Byla služba Database Migration Service zastavena z důvodu neaktivity nebo žádosti uživatele?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft. datashare/Accounts
|Provedené kontroly|
|---|
|<ul><li>Je účet pro sdílení dat zapnutý a spuštěný?</li><li>Je cluster hostující sdílená složka dat k dispozici?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Provedené kontroly|
|---|
|<ul><li>Je server z důvodu údržby nedostupný?</li><li>Je server z důvodu překonfigurování nedostupný?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Provedené kontroly|
|---|
|<ul><li>Je server z důvodu údržby nedostupný?</li><li>Je server z důvodu překonfigurování nedostupný?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Provedené kontroly|
|---|
|<ul><li>Je server z důvodu údržby nedostupný?</li><li>Je server z důvodu překonfigurování nedostupný?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Provedené kontroly|
|---|
|<ul><li>Je centrum IoT v provozu?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Provedené kontroly|
|---|
|<ul><li>Nějaké žádosti o databázi nebo kolekci nesloužily kvůli nedostupnosti Azure Cosmos DB služby?</li><li>Nedošlo k nedoručení požadavků na dokument kvůli nedostupnosti Azure Cosmos DB služby?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft. eventhub/obory názvů
|Provedené kontroly|
|---|
|<ul><li>Má obor názvů Event Hubs u uživatelů generované chyby?</li><li>Je aktuálně upgradovaný obor názvů Event Hubs?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Provedené kontroly|
|---|
|<ul><li>Jsou v clusteru HDInsight k dispozici základní služby?</li><li>Může cluster HDInsight přistupovat ke klíči pro šifrování BYOK v klidovém umístění?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Provedené kontroly|
|---|
|<ul><li>Jsou požadavky na Trezor klíčů neúspěšné kvůli problémům s platformou Azure datatrezoru?</li><li>Jsou požadavky na Trezor klíčů omezené z důvodu příliš velkého počtu požadavků provedených zákazníkem?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft. MachineLearning/WebServices
|Provedené kontroly|
|---|
|<ul><li>Je webová služba funkční a spuštěná?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices
|Provedené kontroly|
|---|
|<ul><li>Je služba Media Service spuštěná a spuštěná?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Provedené kontroly|
|---|
|<ul><li>Je výkon Application Gateway snížený?</li><li>Je Application Gateway k dispozici?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/připojení
|Provedené kontroly|
|---|
|<ul><li>Je tunel VPN připojený?</li><li>Existují konflikty konfigurace v připojení?</li><li>Jsou předsdílené klíče správně nakonfigurované?</li><li>Je místní zařízení VPN dostupné?</li><li>Existují v zásadách zabezpečení IPSec/IKE neshoda?</li><li>Je připojení VPN S2S správně zřízené nebo ve stavu selhání?</li><li>Je připojení typu VNET-to-VNET správně zřízené nebo ve stavu selhání?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressroutecircuits
|Provedené kontroly|
|---|
|<ul><li>Je okruh ExpressRoute v pořádku?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Provedené kontroly|
|---|
|<ul><li>Jsou přední dveře back-endy v reakci s chybami sond stavu?</li><li>Jsou změny konfigurace zpožděné?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/LoadBalancers
|Provedené kontroly|
|---|
|<ul><li>Jsou k dispozici koncové body vyrovnávání zatížení?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Provedené kontroly|
|---|
|<ul><li>Je Brána VPN dosažitelná z Internetu?</li><li>Je VPN Gateway v pohotovostním režimu?</li><li>Běží v bráně služba VPN?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Provedené kontroly|
|---|
|<ul><li>Mohou být operace za běhu, jako je registrace, instalace nebo odeslání, prováděny v oboru názvů?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Provedené kontroly|
|---|
|<ul><li>Existují pro tento pracovní prostor zpoždění indexování?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/– kapacity
|Provedené kontroly|
|---|
|<ul><li>Je prostředek kapacity v provozu a běží?</li><li>Jsou všechny úlohy spuštěné a spuštěné?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Provedené kontroly|
|---|
|<ul><li>Je operační systém hostitele v provozu a běží?</li><li>Je dostupná aplikace pracovní prostorcollection z oblasti mimo datové centrum?</li><li>Je poskytovatel prostředků Power BI k dispozici?</li><li>Je služba Power BI k dispozici v příslušné oblasti?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Provedené kontroly|
|---|
|<ul><li>Můžou se v clusteru provádět diagnostické operace?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Provedené kontroly|
|---|
|<ul><li>Vycházejí zákazníci s chybami Service Bus generovaných uživatelem?</li><li>Dochází u uživatelů ke zvýšení přechodných chyb v důsledku upgradu oboru názvů Service Bus?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/databáze
|Provedené kontroly|
|---|
|<ul><li>Je databáze spuštěná a spuštěná?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft. SQL/Server/databáze
|Provedené kontroly|
|---|
|<ul><li>Přihlásili jste se k databázi s přihlášením?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Provedené kontroly|
|---|
|<ul><li>Jsou požadavky na čtení dat z účtu úložiště neúspěšné kvůli problémům s Azure Storage platformou?</li><li>Jsou požadavky na zápis dat do účtu úložiště neúspěšné kvůli problémům s Azure Storage platformou?</li><li>Je cluster úložiště, ve kterém se nachází účet úložiště, nedostupný?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Provedené kontroly|
|---|
|<ul><li>Jsou všichni hostitelé, na kterých se úloha spouští a běží?</li><li>Nepovedlo se nám spustit úlohu?</li><li>Probíhá probíhající upgrade za běhu?</li><li>Je úloha v očekávaném stavu (například spuštění nebo zastavení zákazníkem)?</li><li>Zjistila se u úlohy výjimky z paměti?</li><li>Existují průběžné plánované aktualizace COMPUTE?</li><li>Je k dispozici Správce spuštění (plán ovládacích prvků)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Provedené kontroly|
|---|
|<ul><li>Je hostitelský server v provozu a běží?</li><li>Je Internetová informační služba běžet?</li><li>Je spuštěn nástroj pro vyrovnávání zatížení?</li><li>Je možné v rámci datového centra získat App Service plán?</li><li>Je účet úložiště hostující obsah webů pro serverová farma dostupný?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Provedené kontroly|
|---|
|<ul><li>Je hostitelský server v provozu a běží?</li><li>Je spuštěný Internet Information Server?</li><li>Je spuštěn nástroj pro vyrovnávání zatížení?</li><li>Může být webová aplikace dosažitelná v rámci datového centra?</li><li>Je k dispozici účet úložiště hostující obsah webu?</li></ul>|

## <a name="next-steps"></a>Další kroky
-  Další informace najdete v tématu [Úvod do Azure Service Healthho řídicího panelu](service-health-overview.md) a [Úvod do Azure Resource Health](resource-health-overview.md) . 
-  [Nejčastější dotazy týkající se Azure Resource Health](resource-health-faq.md)
- Nastavte výstrahy, abyste byli informováni o problémech se stavem. Další informace najdete v tématu [Konfigurace výstrah pro události služby Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
