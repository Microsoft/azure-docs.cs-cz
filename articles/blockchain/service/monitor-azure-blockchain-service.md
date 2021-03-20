---
title: Sledování služby Azure blockchain (ABS)
description: Monitorování služby Azure blockchain prostřednictvím Azure Monitor
ms.date: 01/08/2020
ms.topic: how-to
ms.reviewer: v-umha
ms.openlocfilehash: 05147f48c4cde4cc97bf6cc9cae5c8220a389ebd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594926"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Monitorování služby Azure blockchain prostřednictvím Azure Monitor  

Zákazníci, kteří provozují ve službě Azure blockchain Service (ABS) provozní úrovně blockchain, budou velmi důležité monitorovat prostředky z hlediska dostupnosti, výkonu a provozu. Tento článek popisuje údaje o monitorování vygenerované službou Azure blockchain a o tom, jak může pomocí různých funkcí a integrací Azure Monitor analyzovat a upozorňovat na správu prostředí produkčních stupňů.  

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?

Služba Azure blockchain vytváří data monitorování pomocí Azure Monitor, což je plná služba monitorování zásobníku v Azure, která poskytuje úplnou sadu funkcí pro monitorování prostředků Azure. Další informace o Azure Monitor najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
 

Následující části jsou uvedené v tomto článku, které popisují konkrétní data získaná ze služby Azure blockchain a poskytují příklady pro konfiguraci shromažďování dat a analýzu těchto dat pomocí nástrojů Azure.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Monitorovat data shromážděná ze služby Azure blockchain  

Služba Azure blockchain shromažďuje stejný druh dat monitorování jako jiné prostředky Azure, které jsou popsány v tématu [monitorování dat](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) z prostředků Azure. Podrobné informace o protokolech a metrikách, které vytvořila služba Azure blockchain, najdete v tématu [monitorování referenčních informací o službě Azure blockchain](#monitor-azure-blockchain-service-data-reference) .

Stránka Přehled v Azure Portal pro každý členský prostředek služby Azure blockchain zahrnuje stručný přehled transakcí, včetně požadavků zpracovávaných a zpracovaných bloků. Některá z těchto dat se shromažďují automaticky a jsou dostupná pro analýzu, když vytvoříte prostředek člena služby Azure blockchain, zatímco můžete povolit další shromažďování dat s další konfigurací.

## <a name="diagnostic-settings"></a>Nastavení diagnostiky  

Metriky platforem a protokol aktivit jsou shromažďovány automaticky, ale je nutné vytvořit nastavení diagnostiky pro shromáždění protokolů prostředků nebo jejich přeposílání mimo Azure Monitor. Podrobný postup pro vytvoření nastavení diagnostiky pomocí Azure Portal, CLI nebo PowerShellu najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](../../azure-monitor/essentials/diagnostic-settings.md) .

Při vytváření nastavení diagnostiky určíte, které kategorie protokolů se mají shromáždit. Kategorie pro službu Azure blockchain jsou uvedené níže.

**Protokoly proxy serveru blockchain** – Pokud chcete monitorovat protokoly proxy serveru ngnix, vyberte kategorii. Všechny podrobnosti o transakcích zákazníka jsou k dispozici pro účely auditu a ladění.  

**Blockchain Application logs** – vyberte kategorii pro získání protokolů aplikace blockchain hostované spravovanou službou. Například pro člena ABS-Quorum by tyto protokoly byly protokoly ze samotného kvora.  

**Požadavky na metriky**: vyberte možnost shromažďování dat metrik z Azure Cosmos DB do cílových umístění v nastavení diagnostiky, které se automaticky shromáždí v metrikách Azure. Shromažďovat data metriky pomocí protokolů zdrojů k analýze obou druhů dat a k odesílání dat metriky mimo Azure Monitor.

## <a name="analyze-metric-data"></a>Analýza dat metriky  

Metriky pro službu Azure blockchain můžete analyzovat pomocí Průzkumníka metrik, přejít na kartu metriky v části monitorování v okně prostředků ABS. Podrobnosti o používání tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../../azure-monitor/essentials/metrics-getting-started.md) . Kompletní metriky pro službu Azure blockchain jsou v oboru názvů Standard metrik služby Azure blockchain.

Dimenzi **Node** lze použít při přidávání filtru nebo rozdělení metrik, která v podstatě poskytuje hodnoty metrik na transakční uzly a uzly validátoru člena ABS.

## <a name="analyze-log-data"></a>Analýza dat protokolů

Tady jsou některé dotazy, které můžete zadat do panelu hledání v protokolu, abyste mohli monitorovat členy služby Azure blockchain. Tyto dotazy fungují s [novým jazykem](../../azure-monitor/logs/log-query-overview.md).

Pokud chcete zadat dotaz na chybové podmínky v protokolech aplikace blockchain, použijte následující dotaz:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Pokud chcete zadat dotaz na chybové podmínky v protokolech proxy blockchain, použijte následující dotaz.  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Filtry času dostupné v protokolech Azure můžete použít k filtrování dotazu pro určitý časový rozsah.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Sledovat odkaz na data služby Azure blockchain  

Tento článek poskytuje přehled dat protokolů a metrik shromažďovaných k analýze výkonu a dostupnosti služby Azure blockchain.  

### <a name="resource-logs"></a>Protokoly prostředků

Všechny protokoly prostředků sdílejí společné schéma nejvyšší úrovně s malým počtem jedinečných vlastností, které jsou specifické pro službu blockchain. Můžete se podívat na článek o [schématu protokolů prostředků nejvyšší úrovně](../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema), podrobnosti o vlastnostech specifických pro službu Azure blockchain jsou uvedené níže.  

V následující tabulce jsou uvedeny vlastnosti pro protokoly proxy serveru Azure blockchain při jejich shromažďování v protokolu Azure Monitor nebo Azure Storage.  


| Název vlastnosti  | Description |
|:---|:---|
| time | Datum a čas (UTC), kdy došlo k operaci. |
| Prostředku  | Prostředek služby Azure blockchain, pro který jsou protokoly povolené.  |
| category  |Pro službu Azure blockchain jsou možné hodnoty **Proxylogs** a **Applicationlogs**. |
| operationName  | Název operace reprezentované touto událostí.   |
| Úroveň protokolování  | Ve výchozím nastavení služba Azure blockchain povoluje úroveň **informačního** protokolu.   |
| NodeLocation  | Oblast Azure, ve které je nasazený člen blockchain.  |
| BlockchainNodeName  | Název uzlu členu služby Azure blockchain, na kterém je operace prováděna.   |
| EthMethod  | Metoda, kterou volá podkladový protokol blockchain, může být v kvoru eth_sendTransactions, eth_getBlockByNumber atd.  |
| Agent  | Uživatelský agent, který funguje jménem uživatele, například webové prohlížeče Mozilla, Edge atd. Příklady těchto hodnot: "Mozilla/5.0 (Linux x64) node.js/8.16.0 V8/6.2.414.77"  |
| Kód   | Kódy chyb HTTP. 4XX a 5XX jsou obvykle chybové stavy.  |
| NodeHost  | Název DNS uzlu   |
| RequestMethodName | Metoda HTTP s názvem, možné hodnoty jsou zde uvedeny pro možnost vytvořit člena, získat podrobnosti o existujícím členovi, odstranit člena odstranit, opravit člena.   |
| BlockchainMemberName  | Název členu služby Azure blockchain zadaný uživatelem  |
| Konsorcium | Název konsorcia, jak poskytuje uživatel.   |
| Vzdálené  | IP adresa klienta, na kterém žádost přichází.  |
| RequestSize  | Velikost žádosti vytvořená v bajtech  |
| RequestTime  | Doba trvání žádosti v milisekundách.|




V následující tabulce jsou uvedeny vlastnosti pro protokoly aplikací Azure blockchain.


| Název vlastnosti  | Description |
|:---|:---|
| time | Datum a čas (UTC), kdy došlo k operaci. |
| Prostředku  | Prostředek služby Azure blockchain, pro který jsou protokoly povolené.|
| category  |Pro službu Azure Blockchain je možné použít hodnotu **Proxylogs** a **Applicationlogs**.  |
| operationName  | Název operace reprezentované touto událostí.   |
| Úroveň protokolování  | Ve výchozím nastavení služba Azure blockchain povoluje úroveň **informačního** protokolu.   |
| NodeLocation  | Oblast Azure, ve které je nasazený člen blockchain.  |
| BlockchainNodeName  | Název uzlu členu služby Azure blockchain, na kterém je operace prováděna.   |
| BlockchainMessage    | Toto pole bude obsahovat protokol aplikace blockchain, který představuje jednoduché protokoly dat. Pro ABS – kvorum by mělo mít protokoly kvora. Obsahuje informace o tom, jaký typ záznamu protokolu je to informativní, chyba, upozornění a řetězec, který poskytuje další informace o provedené akci.   |
| TenantID    | Tenant pro konkrétní oblast služby Azure blockchain. Formát tohoto pole je https://westlake-rp-prod . <region> . cloudapp.azure.com WHERE určuje oblast Azure nasazeného člena.       |
| SourceSystem   | Systém tyto protokoly naplní, v tomto případě je to **Azure**.    |



### <a name="metrics"></a>Metriky

V následujících tabulkách jsou uvedeny metriky platforem shromážděné pro službu Azure blockchain. Všechny metriky se ukládají do oboru názvů Standard metrik **služby Azure blockchain** .

Seznam všech podporovaných Azure Monitor metriky (včetně služby Azure blockchain) najdete v článku [Azure monitor podporované metriky](../../azure-monitor/essentials/metrics-supported.md).

### <a name="blockchain-metrics"></a>Blockchain metriky

Následující tabulka uvádí seznam blockchain metrik, které se shromažďují pro prostředek člena služby Azure blockchain.


| Název metriky | Jednotka  |  Typ agregace| Description   |
|---|---|---|---|
| Nedokončené transakce   | Počet  |  Průměr | Počet transakcí, které čekají na dolována za účely.   |
| Zpracované bloky   | Počet  | Sum  |  Počet bloků zpracovaných v každém časovém intervalu. Velikost bloku je v současné době 5 sekund, proto za minutu každý uzel zpracuje 12 bloků a 60 bloků za 5 minut.   |
|Zpracované transakce    | Počet  | Sum  | Počet transakcí zpracovaných v bloku.    |
|Transakce ve frontě    |  Počet | Průměr  | Počet transakcí, které nemohou být okamžitě dolována za účelyy. Může to být proto, že se dostanou mimo pořadí a budoucí verze čeká na doručení předchozí transakce. Nebo může se jednat o dvě transakce, které mají stejné číslo jako jenom jednou (hodnota nonce) a stejnou hodnotu plynu, takže druhá z nich nemůže být dolována za účely.   |

### <a name="connection-metrics"></a>Metriky připojení  

V následující tabulce jsou uvedeny různé metriky připojení, které jsou shromažďovány pro prostředek člena služby Azure blockchain. Jedná se o metriky NGINX proxy serveru.


| Název metriky | Jednotka  |  Typ agregace| Description |
|---|---|---|---|
| Přijatá připojení   | Počet  |  Sum | Celkový počet přijatých připojení klientů.   |
| Aktivní připojení  | Počet  | Průměr  |  Aktuální počet aktivních připojení klientů, včetně čekání na připojení.    |
|Zpracovaná připojení    | Počet  | Sum  | Celkový počet zpracovaných připojení. Obecně platí, že hodnota parametru je stejná jako přijatá připojení, pokud se nedosáhne omezení prostředků.     |
|Zpracované žádosti     |  Počet | Sum  | Celkový počet požadavků klientů.  |


### <a name="performance-metrics"></a>Metriky výkonu

V následující tabulce jsou uvedeny metriky výkonu, které jsou shromažďovány pro každý uzel členského prostředku Azure blockchain.  


| Název metriky | Jednotka  |  Typ agregace| Description   |
|---|---|---|---|
| Procento využití procesoru   | Procento  |  Maximum | Procento využití procesoru.     |
| Bajty čtení v/v   | Kilobajtů   | Sum  |  Součet čtených vstupně-výstupních bajtů ve všech uzlech členského prostředku blockchain      |
|Bajty zápisu v/v     | Kilobajtů   | Sum  | Součet v/v zapisuje bajty ve všech uzlech členského prostředku blockchain.     |
|Omezení paměti       |  Paměti   | Průměr    | Maximální velikost paměti dostupné pro proces blockchain na uzel. |
|Využití paměti     | Paměti  |  Průměr | Velikost využité paměti ve všech uzlech.  |
| Procento využití paměti     | Procento   | Průměr  |  Procentuální podíl využité paměti ve všech uzlech       |
|Využití úložiště      | Paměti   | Průměr  | Průměrná velikost využitého úložiště ve všech uzlech.       |


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [Blockchain data Manager](./data-manager.md) pro zachycení a transformaci blockchain dat do Azure Event Grid.
