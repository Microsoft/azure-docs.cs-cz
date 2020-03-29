---
title: Monitorování služby Azure Blockchain (ABS)
description: Monitorování služby Azure Blockchain prostřednictvím Azure Monitoru
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293245"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Monitorování služby Azure Blockchain prostřednictvím služby Azure Monitor  

Vzhledem k tomu, že zákazníci ve službě Azure Blockchain Service (ABS) provozují scénáře blockchainu produkční třídy, je důležité sledovat prostředky dostupnosti, výkonu a operací. Tento článek popisuje data monitorování generovaná službou Azure Blockchain Service a jak můžete použít různé funkce a integrace Azure Monitoru k analýze a výstrahám pro správu produkčních prostředí.  

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?

Služba Azure Blockchain service vytváří data monitorování pomocí Azure Monitoru, což je služba monitorování celého zásobníku v Azure, která poskytuje kompletní sadu funkcí pro monitorování vašich prostředků Azure. Další informace o Azure Monitoru najdete [v tématu Monitorování prostředků Azure pomocí Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource).
 

Následující části vycházejí z tohoto článku tím, že popisují konkrétní data shromážděná ze služby Azure Blockchain service a poskytují příklady pro konfiguraci shromažďování dat a analýzu těchto dat pomocí nástrojů Azure.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Monitorování dat shromážděných ze služby Azure Blockchain Service  

Služba Azure Blockchain service shromažďuje stejný druh dat monitorování jako ostatní prostředky Azure, které jsou popsané v [části Monitorování dat](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) z prostředků Azure. Podrobný odkaz na protokoly a metriky vytvořené službou Azure Blockchain Service najdete v [tématu Sledování dat služby Azure](#monitor-azure-blockchain-service-data-reference) Blockchain Service.

Stránka s přehledem na portálu Azure pro každý prostředek člena služby Azure Blockchain service obsahuje stručný přehled transakcí, včetně požadavků zpracovaných a zpracovaných bloků. Některá z těchto dat se shromažďují automaticky a jsou k dispozici pro analýzu po vytvoření prostředku člena služby Azure Blockchain Service, zatímco můžete povolit další shromažďování dat s další konfigurací.

## <a name="diagnostic-settings"></a>Nastavení diagnostiky  

Metriky platformy a protokol aktivit se shromažďují automaticky, ale musíte vytvořit diagnostické nastavení pro shromažďování protokolů prostředků nebo jejich předávání mimo Azure Monitor. Viz [Vytvoření diagnostického nastavení pro shromažďování protokolů platformy a metrik v Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) pro podrobný proces pro vytvoření diagnostického nastavení pomocí portálu Azure, CLI nebo PowerShellu.

Při vytváření nastavení diagnostiky určíte, které kategorie protokolů mají být shromažďovány. Kategorie pro službu Azure Blockchain jsou uvedeny níže.

**Blockchain proxy protokoly** - Vyberte kategorii, pokud chcete sledovat protokoly proxy NGNIX. Všechny podrobnosti o transakci odběratele jsou k dispozici pro účely auditu a ladění.  

**Protokoly aplikací Blockchain** – Vyberte kategorii pro získání protokolů blockchainové aplikace hostované spravovanou službou. Například pro člen ABS-Quorum by tyto protokoly protokoly z kvora samotného.  

**Požadavky na metriky**: Vyberte možnost shromažďovat metrická data z Azure Cosmos DB do cílů v diagnostickém nastavení, které se automaticky shromažďují v Azure Metrics. Shromažďujte metrická data pomocí protokolů prostředků, abyste společně analyzovali oba druhy dat a odesílli metrická data mimo Azure Monitor.

## <a name="analyze-metric-data"></a>Analýza dat metrik  

Metriky pro službu Azure Blockchain service můžete analyzovat pomocí průzkumníka metrik, přejděte na kartu Metriky v části Monitorování v okně prostředků ABS. Podrobnosti o používání nástroje najdete v tématu [Začínáme s Průzkumníkem metrik Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) Kompletní metriky pro službu Azure Blockchain jsou ve standardních metrikách oboru názvů Služby Azure Blockchain Service.

Kótu **uzlu** můžete použít při přidávání filtru nebo rozdělení metrik, které v podstatě poskytují metrické hodnoty na uzly transakce a uzly validátoru člena ABS.

## <a name="analyze-log-data"></a>Analýza dat protokolů

Tady jsou některé dotazy, které můžete zadat na panelu hledání protokolu, které vám pomůžou sledovat členy služby Azure Blockchain Service. Tyto dotazy pracují s [novým jazykem](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Chcete-li zadat dotaz na chybové stavy v protokolech aplikací Blockchain, použijte následující dotaz:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Chcete-li zadat dotaz na chybové stavy v protokolech proxy blockchainu, použijte níže uvedený dotaz  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Časové filtry dostupné v protokolech Azure můžete použít k filtrování dotazu pro určitý časový rozsah.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Sledování odkazu na data služby Azure Blockchain Service  

Tento článek obsahuje odkaz na protokola a metriky dat shromážděných k analýze výkonu a dostupnosti služby Azure Blockchain Service.  

### <a name="resource-logs"></a>Protokoly prostředků

Všechny protokoly prostředků sdílejí běžné schéma nejvyšší úrovně s několika jedinečnými vlastnostmi specifickými pro službu blockchain. Můžete odkazovat na článek [Schéma protokolů prostředků nejvyšší úrovně](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema), podrobnosti o specifických vlastnostech služby Azure Blockchain service jsou uvedeny níže  

V následující tabulce jsou uvedeny vlastnosti pro protokoly proxy Azure Blockchain, když se shromažďují v protokolech monitorování Azure nebo azure storage.  


| Název vlastnosti  | Popis |
|:---|:---|
| time | Datum a čas (UTC), kdy došlo k operaci. |
| Resourceid  | Prostředek služby Azure Blockchain Service, pro které jsou povoleny protokoly.  |
| category  |Pro službu Azure Blockchain service jsou možné hodnoty **Proxylogs** a **Applicationlogs**. |
| operationName  | Název operace reprezentované touto událostí.   |
| Úroveň protokolu  | Ve výchozím nastavení služba Azure Blockchain service umožňuje úroveň **informačního** protokolu.   |
| Umístění uzlu  | Oblast Azure, kde se nasadí člen blockchainu.  |
| BlockchainNodeName  | Název uzlu člena služby Azure Blockchain Service, na kterém se provádí operace.   |
| EthMethod  | Metoda, která je nazývána základním blockchainovým protokolem, v Kvoru by mohla být eth_sendTransactions, eth_getBlockByNumber atd.  |
| Agent  | Uživatelský agent, který jedná jménem uživatele, jako je webový prohlížeč Mozilla, Edge atd. Příklady hodnot jsou: "Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414.77"  |
| kód   | Kódy chyb PROTOKOLU HTTP. Obvykle 4XX a 5XX jsou chybové stavy.  |
| NodeHost  | Název DNS uzlu.   |
| Název_metody RequestMethodName | Metoda HTTP volána, možné hodnoty zde jsou PUT pro create member, GET pro získání podrobností o existujícím členu, DELETE for delete member, PATCH for updating member.   |
| BlockchainMemberName  | Název člena služby Azure Blockchain Service poskytovaný uživatelem.  |
| Konsorcium | Název konsorcia podle potřeby uživatele.   |
| Remote  | IP adresa klienta, kam přichází požadavek.  |
| RequestSize  | Velikost požadavku v bajtů.  |
| RequestTime  | Doba trvání požadavku v milisekundách.|




V následující tabulce jsou uvedeny vlastnosti protokolů aplikací Azure Blockchain.


| Název vlastnosti  | Popis |
|:---|:---|
| time | Datum a čas (UTC), kdy došlo k operaci. |
| Resourceid  | Prostředek služby Azure Blockchain Service, pro které jsou povoleny protokoly.|
| category  |Pro službu Azure Blockchain service je možná hodnota **Proxylogs** a **Applicationlogs**.  |
| operationName  | Název operace reprezentované touto událostí.   |
| Úroveň protokolu  | Ve výchozím nastavení služba Azure Blockchain service umožňuje úroveň **informačního** protokolu.   |
| Umístění uzlu  | Oblast Azure, kde se nasadí člen blockchainu.  |
| BlockchainNodeName  | Název uzlu člena služby Azure Blockchain Service, na kterém se provádí operace.   |
| BlockchainMessage    | Toto pole bude obsahovat protokol aplikace Blockchain, který je protokoly prostých dat. Pro ABS-Quorum by to mít kvora protokoly. Obsahuje informace o tom, jaký typ položky protokolu je informační, chyba, upozornění a řetězec, který poskytuje další informace o provedené akci.   |
| ID tenanta    | Klient azure blockchainové služby pro konkrétní oblast. Formát tohoto pole https://westlake-rp-prodje . <region>.cloudapp.azure.com kde oblast určuje oblast Azure nasazeného člena.       |
| SourceSystem   | Systém naplní protokoly, v tomto případě je **Azure**.    |



### <a name="metrics"></a>Metriky

V následujících tabulkách jsou uvedeny metriky platformy shromážděné pro službu Azure Blockchain. Všechny metriky jsou uloženy ve standardních metrikách oboru názvů **Služby Azure Blockchain Service.**

Seznam všech podporovaných metrik Azure Monitoru (včetně služby Azure Blockchain) najdete v [tématu Azure Monitor podporované metriky](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).

### <a name="blockchain-metrics"></a>Metriky blockchainu

Následující tabulka určuje seznam metrik blockchain, které se shromažďují pro prostředek člena služby Azure Blockchain Service.


| Název metriky | Jednotka  |  Typ agregace| Popis   |
|---|---|---|---|
| Čekající transakce   | Počet  |  Průměr | Počet transakcí, které čekají na odminování.   |
| Zpracované bloky   | Počet  | Součet  |  Počet bloků zpracovaných v každém časovém intervalu. V současné době je velikost bloku 5 sekund, proto za minutu každý uzel zpracuje 12 bloků a 60 bloků za 5 minut.   |
|Zpracované transakce    | Počet  | Součet  | Počet transakcí zpracovaných v bloku.    |
|Transakce ve frontě    |  Počet | Průměr  | Počet transakcí, které nelze okamžitě zminout. Může to být proto, že dorazili mimo objednávku a budoucí čeká na předchozí transakci. Nebo to může být dvě transakce mají stejné číslo použít pouze jednou (nonce) a stejnou hodnotu plynu, proto druhá nemůže být zminit.   |

### <a name="connection-metrics"></a>Metriky připojení  

V následující tabulce jsou uvedeny různé metriky připojení, které se shromažďují pro prostředek člena služby Azure Blockchain Service. Jedná se o proxy metriky NGINX.


| Název metriky | Jednotka  |  Typ agregace| Popis |
|---|---|---|---|
| Přijatá připojení   | Počet  |  Součet | Celkový počet přijatých připojení klientů.   |
| Aktivní připojení  | Počet  | Průměr  |  Aktuální počet aktivních připojení klientů včetně čekajících připojení.    |
|Manipulovní připojení    | Počet  | Součet  | Celkový počet zpracovaných připojení. Obecně platí, že hodnota parametru je stejná jako přijatá připojení, pokud nebylo dosaženo některých omezení prostředků.     |
|Zpracované požadavky     |  Počet | Součet  | Celkový počet požadavků klientů.  |


### <a name="performance-metrics"></a>Metriky výkonu

V následující tabulce jsou uvedeny metriky výkonu, které se shromažďují pro každý uzel prostředku člena Azure Blockchain.  


| Název metriky | Jednotka  |  Typ agregace| Popis   |
|---|---|---|---|
| Procento využití procesoru   | Procento  |  Maximum | Procento využití procesoru.     |
| Čtení bajtů v iO   | Kilobajtech   | Součet  |  Součet vsuzování bajtů napříč všemi uzly prostředku člena blockchainu.      |
|Vypoučovat bajty     | Kilobajtech   | Součet  | Součet vi zápisy bajtů napříč všemi uzly prostředku člena blockchainu.     |
|Limit paměti       |  Gigabajty   | Průměr    | Maximální dostupná paměť pro proces blockchainu na uzel. |
|Využití paměti     | Gigabajty  |  Průměr | Množství paměti použité zprůměrované ve všech uzlech.  |
| Procento využití paměti     | Procento   | Průměr  |  Procento použité paměti zprůměrováno ve všech uzlech.       |
|Využití úložiště      | Gigabajty   | Průměr  | Gb úložiště používá zprůměrované ve všech uzlech.       |


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [Správci blockchainových dat](https://docs.microsoft.com/azure/blockchain/service/data-manager) pro sběr a transformaci dat blockchainu do Azure Event Grid.
