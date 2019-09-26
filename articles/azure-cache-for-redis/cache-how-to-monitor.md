---
title: Jak monitorovat Azure cache pro Redis | Microsoft Docs
description: Naučte se monitorovat stav a výkon pro instance Redis v mezipaměti Azure.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: 5ae1827c7af3346a9197a9e28ee442d8aca0e38f
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299524"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Jak monitorovat Azure cache pro Redis
Azure cache pro Redis používá [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) k poskytnutí několika možností monitorování instancí mezipaměti. Můžete zobrazit metriky, připnout grafy metriky na úvodní panel, přizpůsobit datum a časový rozsah grafů monitorování, přidat a odebrat metriky z grafů a nastavit upozornění, když budou splněny určité podmínky. Tyto nástroje vám umožní monitorovat stav mezipaměti Azure pro instance Redis a pomáhat při správě aplikací pro ukládání do mezipaměti.

Metriky pro instance služby Azure cache pro instance Redis se shromažďují pomocí příkazu Redis [info](https://redis.io/commands/info) přibližně dvakrát za minutu a automaticky se ukládají po dobu 30 dnů (Další informace najdete v tématu [Export metrik mezipaměti](#export-cache-metrics) pro konfiguraci jiné zásady uchovávání informací), aby mohly být Zobrazuje se v grafech metrik a vyhodnocuje pomocí pravidel výstrah. Další informace o různých hodnotách informací používaných pro každou metriku mezipaměti najdete v tématu [dostupné metriky a intervaly vytváření sestav](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Pokud chcete zobrazit metriky mezipaměti, [přejděte](cache-configure.md#configure-azure-cache-for-redis-settings) k instanci mezipaměti v [Azure Portal](https://portal.azure.com).  Azure cache pro Redis poskytuje některé integrované grafy v okně **Přehled** a v okně metriky **Redis** . Jednotlivé grafy je možné přizpůsobit přidáním nebo odebráním metrik a změnou intervalu generování sestav.

![Redis metriky](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Zobrazit předem nakonfigurované grafy metrik

Okno **Přehled** obsahuje následující předem nakonfigurované grafy monitorování.

* [Grafy monitorování](#monitoring-charts)
* [Grafy využití](#usage-charts)

### <a name="monitoring-charts"></a>Grafy monitorování
V části **monitorování** v okně **Přehled** jsou k disblokování **a Neúspěšné přístupy**k příkazům, získání **a nastavení**, **připojení**a **Celkový počet příkazů** v grafu.

![Grafy monitorování](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Grafy využití
V části **využití** v okně **Přehled** je **Redis zatížení serveru**, **využití paměti**, **Šířka pásma sítě**a grafy **využití procesoru** a zároveň se zobrazuje cenová **úroveň** instance mezipaměti.

![Grafy využití](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Cenová úroveň** zobrazuje cenovou úroveň mezipaměti a je možné ji použít ke [škálování](cache-how-to-scale.md) mezipaměti na jinou cenovou úroveň.

## <a name="view-metrics-with-azure-monitor"></a>Zobrazení metrik pomocí Azure monitoru
Pokud chcete zobrazit metriky Redis a vytvářet vlastní grafy pomocí Azure Monitor, klikněte na metriky v **nabídce prostředek**a upravte svůj graf pomocí požadovaných metrik, intervalu generování sestav, typu grafu a dalších.

![Redis metriky](./media/cache-how-to-monitor/redis-cache-monitor.png)

Další informace o práci s metrikami pomocí Azure Monitor najdete v tématu [Přehled metrik v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exportovat metriky mezipaměti
Ve výchozím nastavení se metriky mezipaměti v Azure Monitor [ukládají po dobu 30 dnů](../azure-monitor/platform/data-platform-metrics.md) a pak se odstraní. Pokud chcete uchovat metriky mezipaměti po dobu delší než 30 dní, můžete [určit účet úložiště](../azure-monitor/platform/archive-diagnostic-logs.md) a zadat zásady **uchovávání (dnů)** pro metriky mezipaměti. 

Konfigurace účtu úložiště pro metriky mezipaměti:

1. V okně **Azure cache pro Redis** klikněte na **Diagnostika** v **nabídce prostředky** .
2. Klikněte **na**.
3. Ověřte **archiv na účet úložiště**.
4. Vyberte účet úložiště, do kterého se mají ukládat metriky mezipaměti.
5. Zaškrtněte políčko **1 minuta** a zadejte zásady **uchovávání (dnů)** . Pokud nechcete použít žádné zásady uchovávání informací a zachovat data navždy, nastavte dobu **uchování (dny)** na **0**.
6. Klikněte na **Uložit**.

![Diagnostika Redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Kromě archivace metrik mezipaměti do úložiště je můžete také [streamovat do centra událostí nebo je odeslat do protokolů Azure monitor](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>
>

Chcete-li získat přístup k metrikám, můžete je zobrazit v Azure Portal, jak je popsáno výše v tomto článku, a můžete k nim také přistupovat pomocí [Azure Monitorch metrik REST API](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Pokud změníte účty úložiště, data v dříve nakonfigurovaném účtu úložiště zůstanou k dispozici ke stažení, ale nezobrazí se v Azure Portal.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Dostupné metriky a intervaly vytváření sestav
Metriky mezipaměti jsou hlášeny pomocí několika intervalů generování sestav, včetně **minulé hodiny**, dnešního, **minulého týdne**a **vlastního**. Okno **metriky** pro každý graf metrik zobrazuje průměrnou, minimální a maximální hodnotu každé metriky v grafu a některé metriky zobrazují celkem pro interval generování sestav. 

Každá metrika obsahuje dvě verze. Jedna metrika měří výkon celé mezipaměti a pro mezipaměti, které používají [clusteringu](cache-how-to-premium-clustering.md), druhá verze metriky, která zahrnuje `(Shard 0-9)` do názvu, měří výkon pro jeden horizontálních oddílů v mezipaměti. Například pokud má mezipaměť 4 horizontálních oddílů, `Cache Hits` je celkový počet přístupů celé mezipaměti a `Cache Hits (Shard 3)` je to pouze počet přístupů k této horizontálních oddílů mezipaměti.

> [!NOTE]
> I když je mezipaměť nečinná bez připojených aktivních klientských aplikací, může se zobrazit některá aktivita mezipaměti, například připojené klienty, využití paměti a prováděné operace. Tato aktivita je během operace Azure cache pro instanci Redis normální.
> 
> 

| Metrika | Popis |
| --- | --- |
| Přístupy do mezipaměti |Počet úspěšných vyhledání klíčů během zadaného intervalu generování sestav. To se `keyspace_hits` mapuje z příkazu Redis [info](https://redis.io/commands/info) . |
| Latence mezipaměti (Preview) | Latence mezipaměti se vypočítala na základě latence v mezipaměti pro uzel. Tato metrika se měří v mikrosekundách a má tři dimenze: "AVG", "min" a "Max", což představuje průměrnou, minimální a maximální latenci mezipaměti v rámci zadaného intervalu hlášení. |
| Neúspěšné přístupy do mezipaměti |Počet neúspěšných vyhledání klíčů během zadaného intervalu generování sestav. To se `keyspace_misses` mapuje z příkazu Redis info. Neúspěšné přístupy do mezipaměti nutně znamenají, že došlo k potížím s mezipamětí. Například při použití modelu programování s mezipamětí aplikace vypadá jako první v mezipaměti pro položku. Pokud položka není tam (nenalezena mezipaměť), bude položka načtena z databáze a přidána do mezipaměti pro další čas. Neúspěšné přístupy do mezipaměti představují normální chování pro model programování s mezipamětí. Pokud počet neúspěšných přístupů do mezipaměti je vyšší, než se očekávalo, Projděte si logiku aplikace, která naplní a přečte z mezipaměti. Pokud se položky z mezipaměti vyloučí z důvodu přetížení paměti, může dojít k nějakým neúspěšným výjimkám, ale lepší metrika monitorování pro tlak paměti by byla `Used Memory` nebo `Evicted Keys`. |
| Čtení z mezipaměti |Objem dat načtených z mezipaměti v megabajtech za sekundu (MB/s) během zadaného intervalu generování sestav. Tato hodnota je odvozena z síťových adaptérů, které podporují virtuální počítač, který je hostitelem mezipaměti a není Redis konkrétní. **Tato hodnota odpovídá šířce pásma používané touto mezipamětí. Pokud chcete nastavit výstrahy pro omezení šířky pásma sítě na straně serveru, vytvořte je pomocí tohoto `Cache Read` čítače. V [této tabulce](cache-faq.md#cache-performance) najdete zjištěné limity šířky pásma pro různé cenové úrovně a velikosti mezipaměti.** |
| Zápis do mezipaměti |Množství dat zapsaných do mezipaměti v megabajtech za sekundu (MB/s) během zadaného intervalu generování sestav. Tato hodnota je odvozena z síťových adaptérů, které podporují virtuální počítač, který je hostitelem mezipaměti a není Redis konkrétní. Tato hodnota odpovídá šířce pásma dat odesílaných do mezipaměti z klienta. |
| Počet připojených klientů |Počet připojení klientů k mezipaměti během zadaného intervalu generování sestav. To se `connected_clients` mapuje z příkazu Redis info. Po dosažení [limitu připojení](cache-configure.md#default-redis-server-configuration) dojde k selhání následného pokusu o připojení do mezipaměti. Mějte na paměti, že i když neexistují žádné aktivní klientské aplikace, může být v důsledku interních procesů a připojení stále několik instancí připojených klientů. |
| Procesor |Využití CPU v mezipaměti Azure pro server Redis jako procento v zadaném intervalu generování sestav. Tato hodnota se mapuje na čítač výkonu `\Processor(_Total)\% Processor Time` operačního systému. |
| Chyby | Konkrétní chyby a problémy s výkonem, ke kterým mohlo dojít během zadaného intervalu vytváření sestav. Tato metrika má osm dimenzí, které představují různé typy chyb, ale mohou být v budoucnu přičteny. Typy chyb, které jsou nyní zastoupeny, jsou následující: <br/><ul><li>Převzetí služeb při selhání – při **převzetí služeb** při selhání (u podřízeného procesu propaguje na hlavní</li><li>**Dataztráta** – Pokud dojde ke ztrátě informací v mezipaměti</li><li>**UnresponsiveClients** – Pokud klienti nečtou data ze serveru dostatečně rychle</li><li>**AOF** – Pokud dojde k potížím souvisejícím s AOF Persistence</li><li>**RDB** – Pokud existuje problém týkající se TRVALosti RDB</li><li>**Import** – Pokud dojde k potížím souvisejícím s importem RDB</li><li>**Export** – Pokud dojde k potížím souvisejícím s EXPORTem RDB</li></ul> |
| Vyřazení klíčů |Počet položek vyřazených z mezipaměti během zadaného intervalu vytváření sestav z důvodu `maxmemory` limitu. To se `evicted_keys` mapuje z příkazu Redis info. |
| Prošlé klíče |Počet položek, jejichž platnost vypršela z mezipaměti během zadaného intervalu generování sestav. Tato hodnota se `expired_keys` mapuje z příkazu Redis info.|
| Získá |Počet operací GET z mezipaměti během zadaného intervalu generování sestav. Tato hodnota je součtem následujících hodnot z příkazu Redis info All:, `cmdstat_get` `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, a `cmdstat_getrange`a je ekvivalentní součtu přístupů do mezipaměti a neúspěšných přístupů. během intervalu generování sestav. |
| Operace za sekundu | Celkový počet příkazů zpracovávaných serverem mezipaměti během zadaného intervalu vytváření sestav za sekundu.  Tato hodnota se mapuje na "instantaneous_ops_per_sec" z příkazu Redis INFO. |
| Zatížení serveru Redis |Procentuální podíl cyklů, ve kterých je server Redis zaneprázdněný zpracováním, a nečeká na nečinnost u zpráv. Pokud tento čítač dosáhne 100, znamená to, že server Redis dosáhl mezní hodnoty výkonu a procesor nemůže rychleji zpracovat práci. Pokud vidíte vysoké zatížení serveru Redis, zobrazí se v klientovi výjimky časového limitu. V takovém případě byste měli zvážit horizontální navýšení nebo rozdělení dat do více mezipamětí. |
| Sady |Počet operací nastavení do mezipaměti během zadaného intervalu generování sestav. Tato hodnota je součtem následujících hodnot z příkazu Redis INFO All: `cmdstat_set`, `cmdstat_setex` `cmdstat_setbit` `cmdstat_hmset` `cmdstat_hset`,, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`,,, `cmdstat_setrange` a `cmdstat_setnx`. |
| Celkem klíčů  | Maximální počet klíčů v mezipaměti během uplynulých časových období generování sestav. To se `keyspace` mapuje z příkazu Redis info. Z důvodu omezení základního systému metrik pro ukládání do mezipaměti s povoleným clusteringem vrátí funkce Total Keys maximální počet klíčů horizontálních oddílů, který měl maximální počet klíčů během intervalu generování sestav.  |
| Celkem operací |Celkový počet příkazů zpracovaných serverem mezipaměti během zadaného intervalu generování sestav. Tato hodnota se `total_commands_processed` mapuje z příkazu Redis info. Všimněte si, že pokud se mezipaměť Azure pro Redis používá čistě pro Pub/sub, nepoužijí se žádné `Cache Hits`metriky `Gets`pro, `Sets` `Cache Misses`, nebo, ale budou `Total Operations` se zobrazovat metriky, které odrážejí využití mezipaměti pro operace Pub/sub. |
| Využitá paměť |Velikost paměti mezipaměti použitá pro páry klíč-hodnota v mezipaměti v MB během zadaného intervalu generování sestav. Tato hodnota se `used_memory` mapuje z příkazu Redis info. Nezahrnuje metadata ani fragmentace. |
| Procento využité paměti | % Celkové paměti, která se používá během zadaného intervalu generování sestav.  Tím se na hodnotu "used_memory" z příkazu Redis INFO vypočítá procento. |
| RSS využité paměti |Velikost paměti v mezipaměti využitá v MB v zadaném intervalu vytváření sestav, včetně fragmentace a metadat. Tato hodnota se `used_memory_rss` mapuje z příkazu Redis info. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Upozornění
Můžete nakonfigurovat odesílání upozornění na základě metrik a protokolů aktivit. Azure Monitor umožňuje nakonfigurovat upozornění, které při aktivaci provede některé z následujících kroků:

* Odeslání e-mailového oznámení
* Volání webhooku
* Vyvolání aplikace logiky Azure

Pokud chcete nakonfigurovat pravidla upozornění pro vaši mezipaměť, klikněte v **nabídce prostředek**na **pravidla výstrah** .

![Monitorování](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Další informace o konfiguraci a používání výstrah najdete v tématu [Přehled výstrah](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Protokoly aktivit
Protokoly aktivit poskytují přehled o operacích, které byly provedeny v mezipaměti Azure pro instance Redis. Dříve se nazývaly protokoly auditu nebo provozní protokoly. Pomocí protokolů aktivit můžete určit "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) provedené v mezipaměti Azure pro instance Redis. 

> [!NOTE]
> Protokoly aktivit nezahrnují operace čtení (GET).
>
>

Protokoly aktivit pro vaši mezipaměť zobrazíte kliknutím na **protokoly aktivit** v **nabídce prostředek**.

Další informace o protokolech aktivit najdete v tématu [Přehled protokolu aktivit Azure](../azure-monitor/platform/activity-logs-overview.md).











