---
title: Podporované typy prostředků prostřednictvím Azure Resource Health | Microsoft Docs
description: Podporované typy prostředků prostřednictvím služby Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 1b0958732a96786b2fb86b2b145f5cb8d36c0c5a
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883363"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Typy prostředků a kontroly stavu v Azure Resource Health
Níže je uvedený úplný seznam všech kontrol provedených prostřednictvím stavu prostředků podle typů prostředků.

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servery
|Provedené kontroly|
|---|
|<ul><li>Je server v provozu a běží?</li><li>Má server nedostatek paměti?</li><li>Spouští se server?</li><li>Obnovuje se server?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Provedené kontroly|
|---|
|<ul><li>Je služba API Management zapnutá a spuštěná?</li></ul>|

## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/pružina
|Provedené kontroly|
|---|
|<ul><li>Je k dispozici instance služby jarního cloudu Azure?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Provedené kontroly|
|---|
|<ul><li>Je účet Batch zapnutý a spuštěný?</li><li>Překročila se kvóta fondu pro tento účet Batch?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis
|Provedené kontroly|
|---|
|<ul><li>Jsou všechny uzly mezipaměti v provozu a jsou spuštěné?</li><li>Je možné dosáhnout mezipaměti v datovém centru?</li><li>Dosáhla by mezipaměť maximálního počtu připojení?</li><li> Vyčerpala mezipaměť dostupnou paměť? </li><li>Dochází v mezipaměti k velkému počtu chyb stránek?</li><li>Je mezipaměť v případě velkého zatížení?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft. CDN/Profile
|Provedené kontroly|
|---|
|<ul> <li>Je doplňkový portál přístupný pro operace konfigurace CDN?</li><li>Dochází k průběžným problémům s doručováním s koncovými body CDN?</li><li>Můžou uživatelé měnit konfiguraci svých prostředků CDN?</li><li>Šíří se změny konfigurace za očekávanou rychlost?</li><li>Můžou uživatelé spravovat konfiguraci CDN pomocí Azure Portal, PowerShellu nebo rozhraní API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. classiccompute/VirtualMachines
|Provedené kontroly|
|---|
|<ul><li>Je hostitelský server v provozu a běží?</li><li>Je spuštění hostitelského operačního systému dokončeno?</li><li>Je kontejner virtuálních počítačů zřízený a zapnutý?</li><li>Je mezi hostitelem a účtem úložiště připojení k síti?</li><li>Bylo spuštění hostovaného operačního systému dokončeno?</li><li>Probíhá plánovaná údržba?</li><li>Je hardwarově snížená a předpokládaná chyba v brzké době?</li></ul>|

## <a name="microsoftclassiccomputedomainnames"></a>Microsoft. classiccompute/DomainNames
|Provedené kontroly|
|---|
|<ul><li>Je nasazení produkčního slotu v rámci všech instancí rolí v pořádku?</li><li>Je role v pořádku napříč všemi jejími instancemi virtuálních počítačů?</li><li>Jaký je stav každého virtuálního počítače v rámci role cloudové služby?</li><li>Změnil se stav virtuálního počítače v důsledku operace iniciované platformou nebo zákazníkem?</li><li>Bylo spuštění hostovaného operačního systému dokončeno?</li><li>Probíhá plánovaná údržba?</li><li>Je hardwarově snížená a předpokládaná chyba v brzké době?</li><li>[Další informace](../cloud-services/resource-health-for-cloud-services.md) o provedených kontrolách</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. cognitiveservices Account/účty
|Provedené kontroly|
|---|
|<ul><li>Je možné, že se k účtu dostane v datacentru?</li><li>Je poskytovatel prostředků Cognitive Services k dispozici?</li><li>Je služba rozpoznávání dostupná v příslušné oblasti?</li><li>Můžou se operace čtení provádět na účtu úložiště, který uchovává metadata prostředků?</li><li>Bylo dosaženo kvóty volání rozhraní API?</li><li>Dosáhlo se limitu pro čtení, volání rozhraní API?</li></ul>|

## <a name="microsoftcomputehostgroupshosts"></a>Microsoft. COMPUTE/hostgroups/hostitelů
|Provedené kontroly|
|---|
|<ul><li>Je hostitel v provozu a běží?</li><li>Je hardware hostitele degradován?</li><li>Vydělí se hostitel?</li><li>Je hostitelem hardwarové služby zacelený jiný hardware?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft. COMPUTE/VirtualMachines
|Provedené kontroly|
|---|
|<ul><li>Je server hostující tento virtuální počítač v provozu a běží?</li><li>Je spuštění hostitelského operačního systému dokončeno?</li><li>Je kontejner virtuálních počítačů zřízený a zapnutý?</li><li>Je mezi hostitelem a účtem úložiště připojení k síti?</li><li>Bylo spuštění hostovaného operačního systému dokončeno?</li><li>Probíhá plánovaná údržba?</li><li>Je hardwarově snížená a předpokládaná chyba v brzké době?</li></ul>|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters
|Provedené kontroly|
|---|
|<ul><li>Je cluster zapnutý a spuštěný?</li><li>Jsou v clusteru k dispozici základní služby?</li><li>Jsou všechny uzly clusteru připravené?</li><li>Je objekt služby aktuální a platný?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/továrny
|Provedené kontroly|
|---|
|<ul><li>Došlo k chybám při spuštění kanálu?</li><li>Je cluster hostující Data Factory v pořádku?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. datalakeanalytics/účty
|Provedené kontroly|
|---|
|<ul><li>Měli by uživatelé narazili na problémy při odesílání nebo výpisu svých Data Lake Analyticsch úloh?</li><li>Jsou Data Lake Analytics úlohy nemohly být dokončeny kvůli chybám systému?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. datalakestore/účty
|Provedené kontroly|
|---|
|<ul><li>Měli by uživatelé narazili na problémy při nahrávání dat do Data Lake Store?</li><li>Měli by uživatelé narazili na problémy se stahováním dat z Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Migrace a služby společnosti Microsoft.
|Provedené kontroly|
|---|
|<ul><li>Nepovedlo se zřídit službu Database Migration Service?</li><li>Byla služba Database Migration Service zastavena z důvodu neaktivity nebo žádosti uživatele?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft. datashare/Accounts
|Provedené kontroly|
|---|
|<ul><li>Je účet pro sdílení dat zapnutý a spuštěný?</li><li>Je cluster hostující sdílená složka dat k dispozici?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servery
|Provedené kontroly|
|---|
|<ul><li>Je server z důvodu údržby nedostupný?</li><li>Je server z důvodu překonfigurování nedostupný?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servery
|Provedené kontroly|
|---|
|<ul><li>Je server z důvodu údržby nedostupný?</li><li>Je server z důvodu překonfigurování nedostupný?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servery
|Provedené kontroly|
|---|
|<ul><li>Je server z důvodu údržby nedostupný?</li><li>Je server z důvodu překonfigurování nedostupný?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/iothubs
|Provedené kontroly|
|---|
|<ul><li>Je centrum IoT v provozu?</li></ul>|

## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/DigitalTwinsInstances
|Provedené kontroly|
|---|
|<ul><li>Je instance digitálních vláken Azure v provozu a běží?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Provedené kontroly|
|---|
|<ul><li>Nějaké žádosti o databázi nebo kolekci nesloužily kvůli nedostupnosti Azure Cosmos DB služby?</li><li>Nedošlo k nedoručení požadavků na dokument kvůli nedostupnosti Azure Cosmos DB služby?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft. eventhub/obory názvů
|Provedené kontroly|
|---|
|<ul><li>Má obor názvů Event Hubs u uživatelů generované chyby?</li><li>Je aktuálně upgradovaný obor názvů Event Hubs?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/clustery
|Provedené kontroly|
|---|
|<ul><li>Jsou v clusteru HDInsight k dispozici základní služby?</li><li>Může cluster HDInsight přistupovat ke klíči pro šifrování BYOK v klidovém umístění?</li></ul>|

## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps
|Provedené kontroly|
|---|
|<ul><li>Je aplikace IoT Central k dispozici?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Trezory a trezory Microsoft.
|Provedené kontroly|
|---|
|<ul><li>Jsou požadavky na Trezor klíčů neúspěšné kvůli problémům s platformou Azure datatrezoru?</li><li>Jsou požadavky na Trezor klíčů omezené z důvodu příliš velkého počtu požadavků provedených zákazníkem?</li></ul>|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/clustery
|Provedené kontroly|
|---|
|<ul><li>Dochází v clusteru k nízkým rychlostem úspěšnosti přijímání dat?</li><li>Dochází v clusteru k vysoké latenci přijímání dat?</li><li>Dochází v clusteru k velkému počtu chyb dotazů?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft. MachineLearning/WebServices
|Provedené kontroly|
|---|
|<ul><li>Je webová služba funkční a spuštěná?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices
|Provedené kontroly|
|---|
|<ul><li>Je služba Media Service spuštěná a spuštěná?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationgateways
|Provedené kontroly|
|---|
|<ul><li>Je výkon Application Gateway snížený?</li><li>Je Application Gateway k dispozici?</li></ul>|

## <a name="microsoftnetworkbastionhosts"></a>Microsoft. Network/bastionhosts
|Provedené kontroly|
|---|
|<ul><li>Je hostitel bastionu v provozu a běží?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/připojení
|Provedené kontroly|
|---|
|<ul><li>Je tunel VPN připojený?</li><li>Existují konflikty konfigurace v připojení?</li><li>Jsou předsdílené klíče správně nakonfigurované?</li><li>Je místní zařízení VPN dostupné?</li><li>Existují v zásadách zabezpečení IPSec/IKE neshoda?</li><li>Je připojení VPN S2S správně zřízené nebo ve stavu selhání?</li><li>Je připojení typu VNET-to-VNET správně zřízené nebo ve stavu selhání?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressroutecircuits
|Provedené kontroly|
|---|
|<ul><li>Je okruh ExpressRoute v pořádku?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors
|Provedené kontroly|
|---|
|<ul><li>Jsou přední dveře back-endy v reakci s chybami sond stavu?</li><li>Jsou změny konfigurace zpožděné?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/LoadBalancers
|Provedené kontroly|
|---|
|<ul><li>Jsou k dispozici koncové body vyrovnávání zatížení?</li></ul>|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficmanagerprofiles
|Provedené kontroly|
|---|
|<ul><li>Existují nějaké problémy, které mají vliv na profil Traffic Manager?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways
|Provedené kontroly|
|---|
|<ul><li>Je Brána VPN dosažitelná z Internetu?</li><li>Je VPN Gateway v pohotovostním režimu?</li><li>Běží v bráně služba VPN?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft. NotificationHubs/obor názvů
|Provedené kontroly|
|---|
|<ul><li>Mohou být operace za běhu, jako je registrace, instalace nebo odeslání, prováděny v oboru názvů?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. operationalinsights/pracovní prostory
|Provedené kontroly|
|---|
|<ul><li>Existují pro tento pracovní prostor zpoždění indexování?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/– kapacity
|Provedené kontroly|
|---|
|<ul><li>Je prostředek kapacity v provozu a běží?</li><li>Jsou všechny úlohy spuštěné a spuštěné?</li></ul>

## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices
|Provedené kontroly|
|---|
|<ul><li>Můžou se v clusteru provádět diagnostické operace?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/obory názvů
|Provedené kontroly|
|---|
|<ul><li>Vycházejí zákazníci s chybami Service Bus generovaných uživatelem?</li><li>Dochází u uživatelů ke zvýšení přechodných chyb v důsledku upgradu oboru názvů Service Bus?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft. ServiceFabric/clustery
|Provedené kontroly|
|---|
|<ul><li>Je Cluster Service Fabric spuštěný a spuštěný?</li><li>Dá se Service Fabric cluster spravovat prostřednictvím Azure Resource Manager?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/databáze
|Provedené kontroly|
|---|
|<ul><li>Je databáze spuštěná a spuštěná?</li></ul>|

## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/servery/databáze
|Provedené kontroly|
|---|
|<ul><li>Přihlásili jste se k databázi s přihlášením?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts
|Provedené kontroly|
|---|
|<ul><li>Jsou požadavky na čtení dat z účtu úložiště neúspěšné kvůli problémům s Azure Storage platformou?</li><li>Jsou požadavky na zápis dat do účtu úložiště neúspěšné kvůli problémům s Azure Storage platformou?</li><li>Je cluster úložiště, ve kterém se nachází účet úložiště, nedostupný?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs
|Provedené kontroly|
|---|
|<ul><li>Jsou všichni hostitelé, na kterých se úloha spouští a běží?</li><li>Nepovedlo se nám spustit úlohu?</li><li>Probíhá probíhající upgrade za běhu?</li><li>Je úloha v očekávaném stavu (například spuštění nebo zastavení zákazníkem)?</li><li>Zjistila se u úlohy výjimky z paměti?</li><li>Existují průběžné plánované aktualizace COMPUTE?</li><li>Je k dispozici Správce spuštění (plán ovládacích prvků)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft. Web/serverových farem
|Provedené kontroly|
|---|
|<ul><li>Je hostitelský server v provozu a běží?</li><li>Je Internetová informační služba běžet?</li><li>Je spuštěn nástroj pro vyrovnávání zatížení?</li><li>Je možné v rámci datového centra získat App Service plán?</li><li>Je účet úložiště hostující obsah webů pro serverová farma dostupný?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft. Web/weby
|Provedené kontroly|
|---|
|<ul><li>Je hostitelský server v provozu a běží?</li><li>Je spuštěný Internet Information Server?</li><li>Je spuštěn nástroj pro vyrovnávání zatížení?</li><li>Může být webová aplikace dosažitelná v rámci datového centra?</li><li>Je k dispozici účet úložiště hostující obsah webu?</li></ul>|

## <a name="next-steps"></a>Další kroky
-  Další informace najdete v tématu [Úvod do Azure Service Healthho řídicího panelu](service-health-overview.md) a [Úvod do Azure Resource Health](resource-health-overview.md) . 
-  [Nejčastější dotazy týkající se Azure Resource Health](resource-health-faq.md)
- Nastavte výstrahy, abyste byli informováni o problémech se stavem. Další informace najdete v tématu [Konfigurace výstrah pro události služby Service Health](./alerts-activity-log-service-notifications-portal.md).