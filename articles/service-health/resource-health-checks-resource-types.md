---
title: Podporované typy prostředků prostřednictvím Azure Resource Health | Dokumentace Microsoftu
description: Podporované typy prostředků prostřednictvím Azure Resource health
services: Resource health
documentationcenter: ''
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 10/09/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 633501b03a362fbd07e14e308e18488f03af5af5
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069196"
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

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Provedený kontroly|
|---|
|<ul><li>Máte problémy zkušení uživatelé odeslání nebo výpis svých úloh Data Lake Analytics?</li><li>Jsou úlohy Data Lake Analytics nelze dokončit chyby systému?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Provedený kontroly|
|---|
|<ul><li>Mají uživatelé došlo k potížím nahrávání dat do Data Lake Store?</li><li>Mají uživatelé došlo k potížím, stahování dat z Data Lake Store?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|Provedený kontroly|
|---|
|<ul><li>Vytvoření a spuštění je IoT hub?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Provedený kontroly|
|---|
|<ul><li>Zde byly všechny databáze nebo kolekce žádosti nelze zpracovat v důsledku nedostupnosti služby Azure Cosmos DB?</li><li>Existuje byly všechny žádosti dokumentu nelze zpracovat v důsledku nedostupnosti služby Azure Cosmos DB?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Provedený kontroly|
|---|
|<ul><li>Selhává kvůli problémům s Azure Key Vaultu platformy žádosti o služby key vault?</li><li>Se žádostí o služby key vault omezují z důvodu příliš mnoho požadavků provedených zákazníky?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/Connections
|Provedený kontroly|
|---|
|<ul><li>Je připojen tunelového připojení sítě VPN?</li><li>Existují konfliktům v konfiguraci v připojení?</li><li>Jsou předsdílené klíče správně nakonfigurovaná?</li><li>Je dostupný v místním zařízení VPN?</li><li>Existují problémy v zásadách zabezpečení protokolu IPSec/IKE?</li><li>Správně zřízené nebo v chybovém stavu, je připojení S2S VPN?</li><li>Správně zřízené nebo v chybovém stavu, je připojení VNET-to-VNET?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Provedený kontroly|
|---|
|<ul><li>Brána VPN je dosažitelný z Internetu?</li><li>Brána VPN je v pohotovostním režimu?</li><li>Je spuštěna služba sítě VPN na bráně?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Provedený kontroly|
|---|
|<ul><li> Operace modulu runtime, jako je registrace, instalace a odeslat provést na obor názvů?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Provedený kontroly|
|---|
|<ul><li>Vytvoření a spuštění je hostitelský operační systém?</li><li>Je workspaceCollection dosažitelný z mimo datové centrum?</li><li>Poskytovatel prostředků Power BI je k dispozici?</li><li>Je služba Power BI k dispozici v příslušné oblasti?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Provedený kontroly|
|---|
|<ul><li>Diagnostika operací provést v clusteru?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Provedený kontroly|
|---|
|<ul><li> Zde se přihlášení k databázi?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
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
- Nastavení výstrah, takže se zobrazí oznámení problémů se stavem. Další informace najdete v tématu [konfigurovat výstrahy pro události služby service health](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
