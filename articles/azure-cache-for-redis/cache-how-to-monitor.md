---
title: Jak monitorovat Azure Cache pro Redis
description: Přečtěte si, jak sledovat stav a výkon vaší azure cache pro instance Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.openlocfilehash: 86cbeddba699e89ce1127dbac72dac81dcc41449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547485"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Jak monitorovat Azure Cache pro Redis

Azure Cache for Redis používá [Azure Monitor](../azure-monitor/index.yml) k poskytování několika možností pro monitorování instancí mezipaměti. Můžete zobrazit metriky, připnout grafy metrik na úvodní obrazovku, přizpůsobit časové období monitorovacích grafů, přidat a odebrat metriky z grafů a nastavit výstrahy, když jsou splněny určité podmínky. Tyto nástroje umožňují sledovat stav vaší azure cache pro redis instance a pomůže vám spravovat vaše aplikace ukládání do mezipaměti.

Metriky pro instance Azure Cache for Redis se shromažďují pomocí příkazu Redis [INFO](https://redis.io/commands/info) přibližně dvakrát za minutu a automaticky se ukládají po dobu 30 dnů (viz [Metriky mezipaměti exportu](#export-cache-metrics) pro konfiguraci různých zásad uchovávání informací), aby je bylo možné zobrazit v grafech metrik a vyhodnotit podle pravidel výstrah. Další informace o různých hodnotách INFO použitých pro každou metriku mezipaměti naleznete v [tématu Dostupné metriky a intervaly vytváření sestav](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Chcete-li zobrazit metriky mezipaměti, [přejděte](cache-configure.md#configure-azure-cache-for-redis-settings) k instanci mezipaměti na [webu Azure Portal](https://portal.azure.com).  Azure Cache for Redis poskytuje některé integrované grafy v okně **Přehled** a v okně **metrik y Redis.** Každý graf lze přizpůsobit přidáním nebo odebráním metrik a změnou intervalu vytváření sestav.

![Redis metriky](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Zobrazení předkonfigurovaných grafů metrik

Okno **Přehled** obsahuje následující předkonfigurované grafy monitorování.

* [Monitorovací grafy](#monitoring-charts)
* [Grafy použití](#usage-charts)

### <a name="monitoring-charts"></a>Monitorovací grafy

Sekce **Monitorování** v okně **Přehled** obsahuje grafy **Přístupy a počet neúspěšných přístupů**, **počet a nastavení**, **připojení**a celkový **počet příkazů.**

![Monitorovací grafy](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Grafy použití

Část **Využití** v okně **Přehled** obsahuje grafy **Redis Server Load**, **Využití paměti**, **Šířka pásma sítě**a Využití **procesoru** a také se zobrazí **cenová úroveň** pro instanci mezipaměti.

![Grafy použití](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Cenová úroveň** zobrazuje cenovou úroveň mezipaměti a lze ji použít k [škálování](cache-how-to-scale.md) mezipaměti na jinou cenovou úroveň.

## <a name="view-metrics-with-azure-monitor"></a>Zobrazení metrik pomocí Azure Monitoru

Pokud chcete zobrazit metriky Redis a vytvořit vlastní grafy pomocí Azure Monitoru, klikněte na **Metriky** z **nabídky Prostředky**a přizpůsobte graf pomocí požadovaných metrik, intervalu vytváření sestav, typu grafu a dalších.

![Redis metriky](./media/cache-how-to-monitor/redis-cache-monitor.png)

Další informace o práci s metrikami pomocí Azure Monitoru najdete [v tématu Přehled metrik v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Export metrik mezipaměti

Ve výchozím nastavení se metriky mezipaměti ve službě Azure Monitor [ukládají po dobu 30 dnů](../azure-monitor/platform/data-platform-metrics.md) a pak se smažou. Chcete-li zachovat metriky mezipaměti déle než 30 dní, můžete [určit účet úložiště](../azure-monitor/platform/archive-diagnostic-logs.md) a určit zásady uchovávání informací **(dny)** pro metriky mezipaměti. 

Konfigurace účtu úložiště pro metriky mezipaměti:

1. Na stránce **Azure Cache for Redis** vyberte v záhlaví **Monitorování** **položku Diagnostika**.
2. Vyberte **+ Přidat diagnostické nastavení**.
3. Pojmenujte nastavení.
4. Zkontrolujte **archivovat účet úložiště**. Při odesílání diagnostiky na účet úložiště vám budou účtovány běžné sazby za data pro úložiště a transakce.
4. Vyberte **Konfigurovat,** chcete-li zvolit účet úložiště, do kterého chcete uložit metriky mezipaměti.
5. V poli **Metrika**záhlaví tabulky zaškrtněte políčko vedle řádkových položek, které chcete uložit, například **AllMetrics**. Zadejte zásadu **uchovávání informací (dny).** Maximální počet dnů uchovávání, které můžete zadat, je **365 dní**. Pokud však chcete data metrik uchovávat navždy, nastavte **uchovávání (dny)** na **0**.
6. Klikněte na **Uložit**.


![Redisova diagnostika](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Kromě archivace metrik mezipaměti do úložiště je můžete také [streamovat do centra událostí nebo je odeslat do protokolů Azure Monitoru](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>

Chcete-li získat přístup k metrikám, můžete je zobrazit na webu Azure Portal, jak je popsáno v tomto článku, a můžete k nim také přistupovat pomocí [rozhraní REST API metrik azure monitoru](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Pokud změníte účty úložiště, data v dříve nakonfigurovaném účtu úložiště zůstanou dostupná ke stažení, ale nezobrazí se na webu Azure Portal.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Dostupné metriky a intervaly vykazování

Metriky mezipaměti jsou vykazovány pomocí několika intervalů vytváření sestav, včetně **posledních hodin**, **dnešních**, **minulých týdnů**a **vlastních**. Okno **Metrika** pro jednotlivé metriky grafu zobrazuje průměr, minimum a maximální hodnoty pro každou metriku v grafu a některé metriky zobrazují součet intervalu vytváření sestav. 

Každá metrika obsahuje dvě verze. Jedna metrika měří výkon pro celou mezipaměť a pro mezipaměti, které `(Shard 0-9)` používají [clustering](cache-how-to-premium-clustering.md), druhá verze metriky, která zahrnuje v názvu měří výkon pro jeden úlomek v mezipaměti. Například pokud mezipaměť má čtyři `Cache Hits` úlomky, je celkový počet `Cache Hits (Shard 3)` přístupů pro celou mezipaměť a je pouze přístupů pro tento oddíl mezipaměti.

> [!NOTE]
> I v případě, že je mezipaměť nečinná bez připojených aktivních klientských aplikací, může se zobrazit některá aktivita mezipaměti, například připojené klienty, využití paměti a prováděné operace. Tato aktivita je normální během provozu azure cache pro redis instance.
> 
> 

| Metrika | Popis |
| --- | --- |
| Přístupy do mezipaměti |Počet úspěšných vyhledávání klíčů během zadaného intervalu vykazování. Toto číslo `keyspace_hits` se mapuje z příkazu Redis [INFO.](https://redis.io/commands/info) |
| Latence mezipaměti (náhled) | Latence mezipaměti vypočtené na základě internode latence mezipaměti. Tato metrika se měří v mikrosekundách `Avg` `Min`a `Max`má tři dimenze: , , a , které představují průměrnou, minimální a maximální latenci mezipaměti během zadaného intervalu vykazování. |
| Počet neúspěšných přístupů do mezipaměti |Počet neúspěšných vyhledávání klíčů během zadaného intervalu vykazování. Toto číslo `keyspace_misses` se mapuje z příkazu Redis INFO. Počet neúspěšných přístupů do mezipaměti nemusí nutně znamenat, že došlo k problému s mezipamětí. Například při použití programovací vzor stranou cache stranou aplikace vypadá jako první v mezipaměti pro položku. Pokud položka není k dispozici (cache miss), položka je načtena z databáze a přidána do mezipaměti pro příště. Mezipaměť nepřístupy jsou normální chování pro cache stranou programovací vzor. Pokud je počet neúspěšných přístupů do mezipaměti vyšší, než bylo očekáváno, zkontrolujte aplikační logiku, která naplní a čte z mezipaměti. Pokud jsou položky vyřazovány z mezipaměti z důvodu tlaku paměti, může být některé `Used Memory` mezipaměti netrefí, ale lepší metrika pro sledování tlaku paměti by nebo `Evicted Keys`. |
| Čtení mezipaměti |Množství dat přečtených z mezipaměti v megabajtech za sekundu (MB/s) během zadaného intervalu vykazování. Tato hodnota je odvozena z karet síťového rozhraní, které podporují virtuální počítač, který je hostitelem mezipaměti a není redis specifické. **Tato hodnota odpovídá šířce pásma sítě používané touto mezipamětí. Pokud chcete nastavit výstrahy pro omezení šířky pásma sítě `Cache Read` na straně serveru, vytvořte je pomocí tohoto čítače. V [této tabulce](cache-faq.md#cache-performance) naleznete pozorované limity šířky pásma pro různé úrovně a velikosti cen mezipaměti.** |
| Zápis do mezipaměti |Množství dat zapsaných do mezipaměti v megabajtech za sekundu (MB/s) během zadaného intervalu vykazování. Tato hodnota je odvozena z karet síťového rozhraní, které podporují virtuální počítač, který je hostitelem mezipaměti a není redis specifické. Tato hodnota odpovídá šířce pásma sítě dat odeslaných do mezipaměti z klienta. |
| Připojení klienti |Počet klientských připojení ke mezipaměti během zadaného intervalu vykazování. Toto číslo `connected_clients` se mapuje z příkazu Redis INFO. Po dosažení [limitu připojení](cache-configure.md#default-redis-server-configuration) se následné pokusy o připojení do mezipaměti nezdaří. I v případě, že neexistují žádné aktivní klientské aplikace, může být stále několik instancí připojených klientů z důvodu interních procesů a připojení. |
| Procesor |Využití procesoru serveru Azure Cache for Redis v procentech během zadaného intervalu vytváření sestav. Tato hodnota se mapuje na čítač výkonu operačního systému. `\Processor(_Total)\% Processor Time` |
| chyby | Konkrétní selhání a problémy s výkonem, které může dojít k mezipaměti během zadaného intervalu vykazování. Tato metrika má osm dimenzí představujících různé typy chyb, ale v budoucnu může být přidáno více. Typy chyb reprezentované nyní jsou následující: <br/><ul><li>**Převzetí služeb při selhání** – při převzetí služeb při selhání mezipaměti (podřízený povýšení na hlavní server)</li><li>**Dataloss** – pokud dojde ke ztrátě dat v mezipaměti</li><li>**NereagujícíKlienti** – pokud klienti nečtou data ze serveru dostatečně rychle</li><li>**AOF** – pokud se jedná o problém související s přetrvávání AOF</li><li>**RDB** – pokud se jedná o problém související s trvalostí RDB</li><li>**Import** – pokud se jedná o problém související s importem RDB</li><li>**Export** – pokud se jedná o problém související s exportem RDB</li></ul> |
| Vystěhované klíče |Počet položek vyřazených z mezipaměti během zadaného intervalu vykazování z důvodu limitu. `maxmemory` Toto číslo `evicted_keys` se mapuje z příkazu Redis INFO. |
| Klíče s prošlou platností |Počet položek, jejichž platnost vypršela z mezipaměti během zadaného intervalu vykazování. Tato hodnota `expired_keys` se mapuje z příkazu Redis INFO.|
| Získá |Počet operací získání z mezipaměti během zadaného intervalu vykazování. Tato hodnota je součtem následujících hodnot z příkazu Redis `cmdstat_getbit`INFO `cmdstat_getrange`všechny příkazy: `cmdstat_get` `cmdstat_hget`, , `cmdstat_hgetall` `cmdstat_hmget`, `cmdstat_mget`, , a , a je ekvivalentní součtu přístupů a neúspěšných přístupů do mezipaměti během intervalu vykazování. |
| Operace za sekundu | Celkový počet příkazů zpracovaných za sekundu serverem mezipaměti během zadaného intervalu vykazování.  Tato hodnota se mapuje na "instantaneous_ops_per_sec" z příkazu Redis INFO. |
| Zatížení serveru Redis |Procento cyklů, ve kterých je server Redis zaneprázdněn zpracováním a nečeká nečinnosti na zprávy. Pokud tento čítač dosáhne 100, znamená to, že server Redis dosáhl stropu výkonu a procesor nemůže zpracovat práci rychleji. Pokud vidíte vysoké Redis Zatížení serveru, pak uvidíte výjimky časového období v klientovi. V takovém případě byste měli zvážit horizontální navýšení kapacity nebo rozdělení dat do více mezipamětí. |
| Sady |Počet nastavených operací do mezipaměti během zadaného intervalu vykazování. Tato hodnota je součtem následujících hodnot z příkazu Redis `cmdstat_lset` `cmdstat_mset`INFO `cmdstat_msetnx` `cmdstat_setbit`všechny `cmdstat_setex` `cmdstat_setrange`příkazy: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset` `cmdstat_hsetnx`, , , , , , , , a `cmdstat_setnx`. |
| Celkový počet klíčů  | Maximální počet klíčů v mezipaměti během minulého časového období vykazování. Toto číslo `keyspace` se mapuje z příkazu Redis INFO. Z důvodu omezení základní metriky systému pro mezipaměti s clustering povoleno, Total Keys vrátí maximální počet klíčů oddílu, který měl maximální počet klíčů během intervalu vykazování.  |
| Celkový počet operací |Celkový počet příkazů zpracovaných serverem mezipaměti během zadaného intervalu vykazování. Tato hodnota `total_commands_processed` se mapuje z příkazu Redis INFO. Když se azure cache pro Redis používá čistě pro pub/sub, nebudou `Sets`existovat žádné `Total Operations` metriky `Cache Hits`pro , `Cache Misses`, `Gets`nebo , ale budou metriky, které odrážejí využití mezipaměti pro operace pub/sub. |
| Použitá paměť |Velikost mezipaměti používané pro páry klíčů a hodnot v mezipaměti v MB během zadaného intervalu vykazování. Tato hodnota `used_memory` se mapuje z příkazu Redis INFO. Tato hodnota nezahrnuje metadata nebo fragmentace. |
| Procento použité paměti | % celkové paměti, která se používá během zadaného intervalu hlášení.  Tato hodnota odkazuje `used_memory` na hodnotu z příkazu Redis INFO pro výpočet procenta. |
| Použité paměti RSS |Velikost mezipaměti paměti používané v MB během zadaného intervalu vykazování, včetně fragmentace a metadat. Tato hodnota `used_memory_rss` se mapuje z příkazu Redis INFO. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Výstrahy

Můžete nakonfigurovat odesílání upozornění na základě metrik a protokolů aktivit. Azure Monitor umožňuje nakonfigurovat upozornění, které při aktivaci provede některé z následujících kroků:

* Odeslání e-mailového oznámení
* Volání webhooku
* Vyvolání aplikace logiky Azure

Chcete-li nakonfigurovat pravidla výstrah pro mezipaměť, klepněte na tlačítko **Pravidla výstrah** v **nabídce Zdroj**.

![Monitorování](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Další informace o konfiguraci a používání výstrah naleznete v [tématu Přehled výstrah](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Protokoly aktivit
Protokoly aktivit poskytují přehled o operacích, které byly provedeny na vaší azure cache pro redis instance. To bylo dříve známé jako "audit protokoly" nebo "provozní protokoly". Pomocí protokolů aktivit můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) převzaté ve vaší azure mezipaměti pro redis instance. 

> [!NOTE]
> Protokoly aktivit nezahrnují operace čtení (GET).
>

Chcete-li zobrazit protokoly aktivit mezipaměti, klepněte na **položku Protokoly aktivit** v **nabídce Zdroj**.

Další informace o protokolech aktivit najdete [v tématu Přehled protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md).
