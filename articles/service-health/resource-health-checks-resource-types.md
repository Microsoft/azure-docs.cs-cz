---
title: Podporované typy prostředků prostřednictvím Azure Resource Health | Dokumentace Microsoftu
description: Podporované typy prostředků prostřednictvím Azure Resource health
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.openlocfilehash: 0f79a1eed044814d6c2e27f4eadb5ba68a47303f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622279"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Typy prostředků a kontroly stavu ve službě Azure resource health
Níže je úplný seznam všech kontrol provést prostřednictvím služby resource health podle typu prostředku.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Provedený kontroly|
|---|
|<ul><li>Vytvoření a spuštění je na serveru?</li><li>Má server dostatek paměti?</li><li>Se spouští na serveru?</li><li>Obnovení serveru</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Provedený kontroly|
|---|
|<ul><li>Služba Api Management je v provozu a spuštění?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Provedený kontroly|
|---|
|<ul><li>Zprovoznění, jsou všechny uzly mezipaměti?</li><li>Mezipaměť dosažitelný z v rámci datacentra?</li><li>Mezipaměti byl dosažen maximální počet připojení?</li><li> Dostupné paměti vyčerpán mezipaměti? </li><li>Mezipaměti se vyskytuje vysoký počet chyb stránek?</li><li>Mezipaměť je v případě velkého zatížení?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Provedený kontroly|
|---|
|<ul> <li>Doplňkový portál je přístupný pro operace konfigurace CDN?</li><li>Existují problémy průběžné doručování s koncovými body CDN?</li><li>Uživatelům můžete změnit konfiguraci prostředků CDN?</li><li>Jsou očekávané rychlostí šíření změn konfigurace?</li><li>Mohou uživatelé spravovat konfiguraci CDN pomocí webu Azure portal, Powershellu nebo rozhraní API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Provedený kontroly|
|---|
|<ul><li>Vytvoření a spuštění je hostitelský server?</li><li>Spuštění operačního systému hostitele dokončil?</li><li>Je kontejner virtuálního počítače zřízené a zapnuté?</li><li>Je k dispozici síťové připojení mezi hostitelem a účet úložiště?</li><li>Spuštění hostovaného operačního systému dokončí?</li><li>Existuje probíhající plánované údržby?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Provedený kontroly|
|---|
|<ul><li>Účet dosažitelný z v rámci datacentra?</li><li>Poskytovateli prostředků služby Cognitive Services je k dispozici?</li><li>Je k dispozici služby Cognitive Services v příslušné oblasti?</li><li>Může číst operace provádět na účtu úložiště, která uchovává metadata prostředku?</li><li>Bylo dosaženo kvóty volání rozhraní API?</li><li>Bylo dosaženo limitu rozhraní API volání čtení?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.COMPUTE/virtualmachines
|Provedený kontroly|
|---|
|<ul><li>Je server hostování tohoto virtuálního počítače si a spuštěna?</li><li>Spuštění operačního systému hostitele dokončil?</li><li>Je kontejner virtuálního počítače zřízené a zapnuté?</li><li>Je k dispozici síťové připojení mezi hostitelem a účet úložiště?</li><li>Spuštění hostovaného operačního systému dokončí?</li><li>Existuje probíhající plánované údržby?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/factories
|Provedený kontroly|
|---|
|<ul><li>Existuje byly selhání spuštění kanálu?</li><li>Cluster je hostitelem služby Data Factory v pořádku?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Provedený kontroly|
|---|
|<ul><li>Máte problémy zkušení uživatelé odeslání nebo výpis svých úloh Data Lake Analytics?</li><li>Jsou úlohy Data Lake Analytics nelze dokončit z důvodu chyby systému?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Provedený kontroly|
|---|
|<ul><li>Mají uživatelé došlo k potížím nahrávání dat do Data Lake Store?</li><li>Mají uživatelé došlo k potížím, stahování dat z Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Provedený kontroly|
|---|
|<ul><li>Má službu database migration service se nepodařilo zřídit?</li><li>Se služba database migration service zastavila kvůli neaktivitě nebo uživatele na žádost?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Provedený kontroly|
|---|
|<ul><li>Server nedostupný kvůli údržbě?</li><li>Není k dispozici z důvodu změny konfigurace serveru?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Provedený kontroly|
|---|
|<ul><li>Server nedostupný kvůli údržbě?</li><li>Není k dispozici z důvodu změny konfigurace serveru?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Provedený kontroly|
|---|
|<ul><li>Server nedostupný kvůli údržbě?</li><li>Není k dispozici z důvodu změny konfigurace serveru?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Provedený kontroly|
|---|
|<ul><li>Vytvoření a spuštění je IoT hub?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Provedený kontroly|
|---|
|<ul><li>Zde byly všechny databáze nebo kolekce žádosti nelze zpracovat v důsledku nedostupnosti služby Azure Cosmos DB?</li><li>Existuje byly všechny žádosti dokumentu nelze zpracovat v důsledku nedostupnosti služby Azure Cosmos DB?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/namespaces
|Provedený kontroly|
|---|
|<ul><li>Dochází k oboru názvů Event Hubs chybám generovaným uživateli?</li><li>Obor názvů Event Hubs aktuálně probíhá upgrade?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Provedený kontroly|
|---|
|<ul><li>Základní služby jsou k dispozici v clusteru HDInsight?</li><li>Můžete clusteru HDInsight přístupový klíč pro BYOK šifrování v klidovém stavu?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Provedený kontroly|
|---|
|<ul><li>Selhává kvůli problémům s Azure Key Vaultu platformy žádosti o služby key vault?</li><li>Se žádostí o služby key vault omezují z důvodu příliš mnoho požadavků provedených zákazníky?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Provedený kontroly|
|---|
|<ul><li>Je výkon služby Application Gateway snížený výkon?</li><li>Application Gateway je k dispozici?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Provedený kontroly|
|---|
|<ul><li>Je připojen tunelového připojení sítě VPN?</li><li>Existují konfliktům v konfiguraci v připojení?</li><li>Jsou předsdílené klíče správně nakonfigurovaná?</li><li>Je dostupný v místním zařízení VPN?</li><li>Existují problémy v zásadách zabezpečení protokolu IPSec/IKE?</li><li>Správně zřízené nebo v chybovém stavu, je připojení S2S VPN?</li><li>Správně zřízené nebo v chybovém stavu, je připojení VNET-to-VNET?</li></ul>|

## <a name="microsoftnetworkexpressreoutecircuits"></a>Microsoft.network/expressreoutecircuits
|Provedený kontroly|
|---|
|<ul><li>Okruh ExpressRoute je v pořádku?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Provedený kontroly|
|---|
|<ul><li>Back-EndY branou neodpovídá s chybami na sond stavu?</li><li>Zpoždění změn konfigurace?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Provedený kontroly|
|---|
|<ul><li>Brána VPN je dosažitelný z Internetu?</li><li>Brána VPN je v pohotovostním režimu?</li><li>Je spuštěna služba sítě VPN na bráně?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Provedený kontroly|
|---|
|<ul><li>Operace modulu runtime, jako je registrace, instalace a odeslat provést na obor názvů?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Provedený kontroly|
|---|
|<ul><li>Existují indexování zpoždění pro pracovní prostor?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Provedený kontroly|
|---|
|<ul><li>Kapacita prostředků je zprovozněný?</li><li>Jsou všechny úlohy spuštěné?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Provedený kontroly|
|---|
|<ul><li>Vytvoření a spuštění je hostitelský operační systém?</li><li>Je workspaceCollection dosažitelný z mimo datové centrum?</li><li>Poskytovatel prostředků Power BI je k dispozici?</li><li>Je dostupná ve službě Power BI v příslušné oblasti?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Provedený kontroly|
|---|
|<ul><li>Diagnostika operací provést v clusteru?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Provedený kontroly|
|---|
|<ul><li>Jsou zákazníkům dochází k chybám generovaným uživateli služby Service Bus?</li><li>Dochází k uživatelům zvýšení přechodným chybám kvůli upgradu obor názvů služby Service Bus?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Provedený kontroly|
|---|
|<ul><li> Zde se přihlášení k databázi?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts.
|Provedený kontroly|
|---|
|<ul><li>Se žádostí o čtení dat z účtu úložiště nedaří zálohovat z důvodu problémů platformy Azure Storage?</li><li>Se žádostí o zápis dat do účtu úložiště nedaří zálohovat z důvodu problémů platformy Azure Storage?</li><li>Úložiště clusteru, ve které se nachází účet úložiště není k dispozici?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Provedený kontroly|
|---|
|<ul><li>Jsou všichni hostitelé, kdy je úloha provádění nahoru a používá?</li><li>Úlohu se nepovedlo spustit?</li><li>Existují runtime probíhající upgrady?</li><li>Úloha v očekávaném stavu (třeba spuštění nebo zastavení podle zákazníka)?</li><li>Úlohy došlo k navýšení kapacity výjimek paměti?</li><li>Existují probíhající výpočetní plánované aktualizace?</li><li>Správce spuštění (plán řízení) je k dispozici?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Provedený kontroly|
|---|
|<ul><li>Vytvoření a spuštění je hostitelský server?</li><li>Běží Internetová informační služba</li><li>Je spuštěn nástroj pro vyrovnávání zatížení?</li><li>Plán služby App Service dosažitelný z v rámci datacentra?</li><li>Účet úložiště hostuje servery obsahu pro serverovou farmu, která je k dispozici??</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Provedený kontroly|
|---|
|<ul><li>Vytvoření a spuštění je hostitelský server?</li><li>Se systémem Internet Information server?</li><li>Je spuštěn nástroj pro vyrovnávání zatížení?</li><li>Webové aplikace byly z v rámci datacentra?</li><li>Účet úložiště hostuje obsah webu k dispozici?</li></ul>|

# <a name="next-steps"></a>Další kroky
-  Zobrazit [Úvod do řídicího panelu služby Azure Service Health](service-health-overview.md) a [Úvod do Azure Resource Health](resource-health-overview.md) bližší informace o nich. 
-  [Nejčastější dotazy k Azure Resource Health](resource-health-faq.md)
- Nastavení výstrah, takže se zobrazí oznámení problémů se stavem. Další informace najdete v tématu [konfigurovat výstrahy pro události služby service health](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
