---
title: Podporované typy prostředků prostřednictvím stavu prostředků Azure | Dokumenty společnosti Microsoft
description: Podporované typy prostředků prostřednictvím stavu prostředků Azure
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 5cc80147730fdc97b1181690f6e70fc538d4afcc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478898"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Typy prostředků a kontroly stavu ve stavu prostředků Azure
Níže je uveden úplný seznam všech kontrol provedených prostřednictvím stavu prostředků podle typů prostředků.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servery
|Provedené kontroly|
|---|
|<ul><li>Je server v provozu?</li><li>Došlo serveru k nedostatku paměti?</li><li>Spouští se server?</li><li>Obnovuje se server?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Provedené kontroly|
|---|
|<ul><li>Je služba Api Management spuštěna?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Provedené kontroly|
|---|
|<ul><li>Je účet Batch spuštěn?</li><li>Byla pro tento dávkový účet překročena kvóta fondu?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/Redis
|Provedené kontroly|
|---|
|<ul><li>Jsou všechny uzly mezipaměti spuštěny?</li><li>Může být do cache dosaženo z datového centra?</li><li>Dosáhla mezipaměť maximálního počtu připojení?</li><li> Vyčerpala mezipaměť dostupnou paměť? </li><li>Dochází v mezipaměti k vysokému počtu chyb stránky?</li><li>Je mezipaměť pod velkým zatížením?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profil
|Provedené kontroly|
|---|
|<ul> <li>Je doplňkový portál přístupný pro operace konfigurace CDN?</li><li>Dochází k přetrvávajícím problémům s doručováním koncových bodů CDN?</li><li>Mohou uživatelé změnit konfiguraci svých prostředků CDN?</li><li>Jsou změny konfigurace šířeny očekávaným tempem?</li><li>Můžou uživatelé spravovat konfiguraci CDN pomocí portálu Azure, PowerShellu nebo rozhraní API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Provedené kontroly|
|---|
|<ul><li>Je hostitelský server v provozu?</li><li>Bylo spuštěno spuštění hostitelského operačního systému?</li><li>Je kontejner virtuálního počítače zřízena a napájena?</li><li>Existuje síťové připojení mezi hostitelem a účtem úložiště?</li><li>Bylo dokončeno zavedení hostovaného operačního systému?</li><li>Probíhá plánovaná údržba?</li><li>Je hostitelský hardware degradován a předpokládá se, že brzy selže?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Provedené kontroly|
|---|
|<ul><li>Může být účet dostupný z datového centra?</li><li>Je poskytovatel prostředků služeb cognitive services k dispozici?</li><li>Je služba Cognitive Service dostupná v příslušné oblasti?</li><li>Lze číst operace na účtu úložiště, který má metadata prostředku?</li><li>Bylo dosaženo kvóty volání rozhraní API?</li><li>Bylo dosaženo limitu čtení a volání rozhraní API?</li></ul>|

## <a name="microsoftcomputehostgroupshosts"></a>Microsoft.compute/hostgroups/hosts
|Provedené kontroly|
|---|
|<ul><li>Je hostitel v provozu</li><li>Je hostitelský hardware poškozen?</li><li>Je hostitel deallocated?</li><li>Uzdravila se hostitelská hardwarová služba na jiný hardware?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Provedené kontroly|
|---|
|<ul><li>Je server hostující tento virtuální počítač v provozu?</li><li>Bylo spuštěno spuštění hostitelského operačního systému?</li><li>Je kontejner virtuálního počítače zřízena a napájena?</li><li>Existuje síťové připojení mezi hostitelem a účtem úložiště?</li><li>Bylo dokončeno zavedení hostovaného operačního systému?</li><li>Probíhá plánovaná údržba?</li><li>Je hostitelský hardware degradován a předpokládá se, že brzy selže?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/továrny
|Provedené kontroly|
|---|
|<ul><li>Došlo k selhání spuštění kanálu?</li><li>Je cluster hostující data factory v pořádku?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/účty
|Provedené kontroly|
|---|
|<ul><li>Vyskytly se uživatelům problémy s odesíláním nebo seseznamováním pracovních míst v data lake analytics?</li><li>Nejsou úlohy Služby Data Lake Analytics z důvodu systémových chyb možné dokončit?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/účty
|Provedené kontroly|
|---|
|<ul><li>Došlo k problémům s nahráváním dat do úložiště Data Lake Store?</li><li>Došlo k potížím se stahováním dat z úložiště Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Provedené kontroly|
|---|
|<ul><li>Služba migrace databáze se nepodařilo zřídit?</li><li>Byla služba migrace databáze zastavena z důvodu nečinnosti nebo požadavku uživatele?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/účty
|Provedené kontroly|
|---|
|<ul><li>Je účet Sdílení dat spuštěn?</li><li>Je cluster hostující sdílenou složku dat k dispozici?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servery
|Provedené kontroly|
|---|
|<ul><li>Je server nedostupný z důvodu údržby?</li><li>Je server nedostupný z důvodu změny konfigurace?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servery
|Provedené kontroly|
|---|
|<ul><li>Je server nedostupný z důvodu údržby?</li><li>Je server nedostupný z důvodu změny konfigurace?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servery
|Provedené kontroly|
|---|
|<ul><li>Je server nedostupný z důvodu údržby?</li><li>Je server nedostupný z důvodu změny konfigurace?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Provedené kontroly|
|---|
|<ul><li>Je centrum IoT zprovozněno?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Provedené kontroly|
|---|
|<ul><li>Byly nějaké požadavky na databázi nebo kolekce, které se neobsluhovaly z důvodu nedostupnosti služby Azure Cosmos DB?</li><li>Byly nějaké žádosti o dokumenty nedoručené z důvodu nedostupnosti služby Azure Cosmos DB?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Obory názvů Microsoft.eventhub/Namespaces
|Provedené kontroly|
|---|
|<ul><li>Dochází v oboru názvů Event Hubs k chybám generovaným uživatelem?</li><li>Je obor názvů Event Hubs právě upgradován?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clustery
|Provedené kontroly|
|---|
|<ul><li>Jsou základní služby dostupné v clusteru HDInsight?</li><li>Může cluster HDInsight přistupovat ke klíči pro šifrování BYOK v klidovém stavu?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Úložiště Microsoft.KeyVault/trezory
|Provedené kontroly|
|---|
|<ul><li>Jsou požadavky na trezor klíčů neúspěšné z důvodu problémů s platformou Azure KeyVault?</li><li>Jsou požadavky na trezor klíčů omezeny z důvodu příliš mnoha požadavků ze strany zákazníka?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Provedené kontroly|
|---|
|<ul><li>Je webová služba v provozu?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices
|Provedené kontroly|
|---|
|<ul><li>Je mediální služba v provozu?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Provedené kontroly|
|---|
|<ul><li>Je výkon aplikační brány snížen?</li><li>Je k dispozici aplikační brána?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Provedené kontroly|
|---|
|<ul><li>Je vpn tunel připojen?</li><li>Existují konflikty konfigurace v připojení?</li><li>Jsou předsdílené klíče správně nakonfigurovány?</li><li>Je místní zařízení VPN dostupné?</li><li>Došlo k neshodám v zásadách zabezpečení protokolu IPSec/IKE?</li><li>Je připojení S2S VPN správně zřízeno nebo ve stavu se nezdařilo?</li><li>Je připojení virtuální sítě k virtuální síti správně zřízeno nebo ve stavu selhání?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|Provedené kontroly|
|---|
|<ul><li>Je okruh ExpressRoute v pořádku?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Provedené kontroly|
|---|
|<ul><li>Jsou back-endy předních dveří reagující s chybami na sondách stavu?</li><li>Zpožďují se změny konfigurace?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.network/LoadBalancers
|Provedené kontroly|
|---|
|<ul><li>Jsou k dispozici koncové body vyrovnávání zatížení?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Provedené kontroly|
|---|
|<ul><li>Je brána VPN dostupná z internetu?</li><li>Je brána VPN v pohotovostním režimu?</li><li>Je služba VPN spuštěna na bráně?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/obor názvů
|Provedené kontroly|
|---|
|<ul><li>Lze v oboru názvů provádět operace runtime, jako je registrace, instalace nebo odeslání?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/pracovní prostory
|Provedené kontroly|
|---|
|<ul><li>Existují zpoždění indexování pracovního prostoru?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Kapacity
|Provedené kontroly|
|---|
|<ul><li>Je zdroj kapacity v provozu?</li><li>Jsou všechny úlohy v provozu?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Kolekce Microsoft.PowerBI/workspaceCollections
|Provedené kontroly|
|---|
|<ul><li>Je hostitelský operační spoje v provozu?</li><li>Je workspaceCollection dosažitelný mimo datové centrum?</li><li>Je poskytovatel prostředků Power BI k dispozici?</li><li>Je služba Power BI dostupná v příslušné oblasti?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Provedené kontroly|
|---|
|<ul><li>Lze v clusteru provádět diagnostické operace?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Obory názvů Microsoft.ServiceBus
|Provedené kontroly|
|---|
|<ul><li>Dochází k chybám sběrnice Service Bus vygenerovanému uživateli?</li><li>Dochází k nárůstu přechodných chyb uživatelů v důsledku upgradu oboru názvů service bus?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft.ServiceFabric/clustery
|Provedené kontroly|
|---|
|<ul><li>Je cluster Service Fabric zprovozněn?</li><li>Může být cluster Service Fabric spravován prostřednictvím Správce prostředků Azure?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.SQL/managedInstances/databases
|Provedené kontroly|
|---|
|<ul><li>Je databáze v provozu?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft.SQL/Server/databáze
|Provedené kontroly|
|---|
|<ul><li>Došlo k přihlášení do databáze?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Provedené kontroly|
|---|
|<ul><li>Jsou požadavky na čtení dat z účtu úložiště selhání z důvodu problémů platformy Azure Storage?</li><li>Jsou požadavky na zápis dat do účtu úložiště selhání z důvodu problémů platformy Azure Storage?</li><li>Je cluster úložiště, kde se nachází účet úložiště, nedostupný?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Provedené kontroly|
|---|
|<ul><li>Jsou všichni hostitelé, kde je úloha spuštěna?</li><li>Nebylo možné úlohu spustit?</li><li>Existují průběžné inovace za běhu?</li><li>Je úloha v očekávaném stavu (například spuštěna nebo zastavena zákazníkem)?</li><li>Došlo k nedostatku paměti k úloze úlohy?</li><li>Probíhají plánované aktualizace výpočetních prostředků?</li><li>Je k dispozici Správce spuštění (plán řízení)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarmy
|Provedené kontroly|
|---|
|<ul><li>Je hostitelský server v provozu?</li><li>Je spuštěna Internetová informační služba?</li><li>Je spuštěn balancer na zatížení?</li><li>Může být plán služby App Service dostupný z datového centra?</li><li>Je účet úložiště hostující obsah webů pro serverFarm k dispozici?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/weby
|Provedené kontroly|
|---|
|<ul><li>Je hostitelský server v provozu?</li><li>Je spuštěn internetový informační server?</li><li>Je spuštěn balancer na zatížení?</li><li>Může být webová aplikace dostupná z datového centra?</li><li>Je k dispozici účet úložiště hostující obsah webu?</li></ul>|

## <a name="next-steps"></a>Další kroky
-  Najdete [v tématu Úvod do Azure Service Health řídicí panel](service-health-overview.md) a úvod do Azure Resource [Health](resource-health-overview.md) pochopit více o nich. 
-  [Nejčastější dotazy týkající se stavu prostředků Azure](resource-health-faq.md)
- Nastavte výstrahy tak, abyste byli upozorněni na problémy se stavem. Další informace naleznete v [tématu Konfigurace výstrah pro události stavu služby](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
