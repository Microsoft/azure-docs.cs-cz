---
title: Jak monitorovat Azure mezipaměti Redis | Dokumentace Microsoftu
description: Další informace o monitorování stavu a výkonu Azure pro instance Redis Cache
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: 5ab2f1df72f04b6456f1083de6870fb86bad6616
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438275"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Jak monitorovat Azure mezipaměti Redis
Mezipaměť Azure pro použití Redis [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) poskytnout několik možností, jak pro monitorování vaší instance mezipaměti. Můžete zobrazit metriky, připnout grafy metrik na úvodní panel, přizpůsobení datum a čas, množství grafy monitorování, přidání a odebrání grafy metrik a nastavit upozornění při splnění určitých podmínek. Tyto nástroje umožňují monitorování stavu vaší mezipaměti Azure pro instance Redis a snadněji tak můžete spravovat ukládání do mezipaměti aplikace.

Shromažďování metrik pro mezipaměť Azure pro instance Redis pomocí Redis [informace](http://redis.io/commands/info) příkaz přibližně dvakrát za minutu a automaticky ukládají po dobu 30 dnů (naleznete v tématu [exportovat metriky mezipaměti](#export-cache-metrics) ke konfiguraci jiné zásady uchovávání informací), mohou být zobrazeny v grafy metrik a vyhodnoceny pomocí pravidel upozornění. Další informace o různých informace hodnoty používané pro jednotlivé metriky mezipaměti najdete v tématu [dostupných metrik a vytváření sestav intervalech](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Pokud chcete zobrazit metriky mezipaměti [Procházet](cache-configure.md#configure-azure-cache-for-redis-settings) k vaší instanci mezipaměti [webu Azure portal](https://portal.azure.com).  Azure mezipaměti Redis obsahuje několik předdefinovaných grafy **přehled** blade a **metrika mezipaměti Redis** okno. Každý graf lze přizpůsobit přidáním nebo odebráním metriky a změna interval sestavy.

![Metrika mezipaměti Redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Zobrazit grafy předem nakonfigurované metriky

**Přehled** okno obsahuje následující předem nakonfigurované grafy monitorování.

* [Grafy monitorování](#monitoring-charts)
* [Použití grafů](#usage-charts)

### <a name="monitoring-charts"></a>Grafy monitorování
**Monitorování** tématu **přehled** obsahuje okno **volání a výpadky**, **získá a nastaví**, **připojení**, a **příkazy celkem** grafy.

![Grafy monitorování](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Použití grafů
**Využití** tématu **přehled** obsahuje okno **zatížení serveru Redis**, **využití paměti**, **šířkypásmasítě**, a **využití procesoru** grafů a zobrazí se také **cenová úroveň** pro instanci mezipaměti.

![Použití grafů](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Cenová úroveň** zobrazí mezipaměti cenovou úroveň a je možné použít k [škálování](cache-how-to-scale.md) mezipaměti na jinou cenovou úroveň.

## <a name="view-metrics-with-azure-monitor"></a>Zobrazení metriky ve službě Azure monitor
Zobrazit metriky pro Redis a vytvářet vlastní grafy používat Azure Monitor, klikněte na tlačítko **metriky** z **nabídce prostředků**a přizpůsobit graf pomocí požadované metriky reporting interval, grafy a další.

![Metrika mezipaměti Redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Další informace o práci s metrikami použití Azure monitoru, naleznete v tématu [přehled metrik v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exportovat metriky mezipaměti
Ve výchozím nastavení, metriky mezipaměti ve službě Azure Monitor jsou [uchovávají po dobu 30 dní](../azure-monitor/platform/data-collection.md#metrics) a pak odstranit. Chcete-li zachovat metriky mezipaměti po dobu delší než 30 dní, můžete [určit účet úložiště](../azure-monitor/platform/archive-diagnostic-logs.md) a zadejte **uchování (dny)** zásady pro metriky mezipaměti. 

Jak nakonfigurovat účet úložiště pro metriky mezipaměti:

1. Klikněte na tlačítko **diagnostiky** z **nabídce prostředků** v **mezipaměti Azure Redis** okno.
2. Klikněte na tlačítko **na**.
3. Zkontrolujte **archivovat do účtu úložiště**.
4. Vyberte účet úložiště, ve kterých se mají ukládat metriky mezipaměti.
5. Zkontrolujte **1 minuta** zaškrtávací políčko a zadejte **uchování (dny)** zásad. Pokud nechcete použít žádnou zásadu uchovávání informací a uchovat si data navždy, nastavte **uchování (dny)** k **0**.
6. Klikněte na **Uložit**.

![Redis diagnostiky](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Kromě archivace metriky mezipaměti do úložiště, můžete také [Streamovat do centra událostí nebo odeslat do služby Log Analytics](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>
>

Pro přístup k metriky, můžete je zobrazit na webu Azure Portal, jak to popisuje tento článek a můžete také přistupovat pomocí [rozhraní REST API služby Azure Monitor Metrics](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Pokud změníte účet úložiště, zůstávají data v dříve nakonfigurovaný účet úložiště k dispozici ke stažení, ale nezobrazí se na webu Azure Portal.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Dostupné metriky a vytváření sestav intervaly
Mezipaměť metriky se vykazují pomocí několika sestav intervalech, včetně **po celé hodině**, **Dnes**, **poslední týden**, a **vlastní**. **Metrika** okno pro každý graf metrik v grafu zobrazí průměrné, minimální a maximální hodnoty pro jednotlivé metriky a některé metriky zobrazit celkem pro interval sestavy. 

Jednotlivé metriky zahrnuje dvě verze. Jedna metrika měří výkon pro celou mezipaměť a mezipamětí, které používají [clustering](cache-how-to-premium-clustering.md), druhou verzi, která obsahuje metriky `(Shard 0-9)` název míry výkonu pro jeden horizontální oddíl v mezipaměti. Například pokud mezipaměti má 4 horizontální oddíly `Cache Hits` je celkový počet přístupů pro celou mezipaměť, a `Cache Hits (Shard 3)` je právě přístupů pro tohoto horizontálního oddílu mezipaměti.

> [!NOTE]
> I když mezipaměti nečinnosti bez aplikací připojených aktivního klienta, může se zobrazit některé mezipaměti aktivity, jako je například připojení klienti, využití paměti a prováděných operací. Tato aktivita je normální během operace Azure Cache pro instanci Redis.
> 
> 

| Metrika | Popis |
| --- | --- |
| Přístupy do mezipaměti |Počet úspěšných hledání klíčů během zadaného intervalu sestavy. To se mapuje na `keyspace_hits` z Redis [informace](http://redis.io/commands/info) příkazu. |
| Latence mezipaměti (Preview) | Latence mezipaměti počítají vypnout latence mezi uzly mezipaměti. Tato metrika se měří v mikrosekundách a má tři dimenze: "Avg", "Min" a "Max", které představují průměrné, minimální a maximální čekací doba mezipaměti v uvedeném pořadí během zadaného intervalu sestavy. |
| Neúspěšné přístupy do mezipaměti |Počet neúspěšných hledání klíčů během zadaného intervalu sestavy. To se mapuje na `keyspace_misses` z Redis informace o příkazu. Neúspěšné přístupy do mezipaměti neznamená nutně, že se vyskytl problém s mezipamětí. Například při použití s doplňováním mezipaměti programovací model, aplikace vyhledá první do mezipaměti pro položku. Pokud položka není (Neúspěšné přístupy do mezipaměti), položka je načtena z databáze a přidají se do mezipaměti pro další použití. Neúspěšné přístupy do mezipaměti jsou normálního chování pro doplňování mezipaměti programovací model. Pokud se počet nezdařených přístupů k mezipaměti je vyšší, než se očekávalo, prozkoumejte aplikační logiky, která naplní a načte z mezipaměti. Pokud položek se vyřazuje z mezipaměti z důvodu tlaku na paměť pak mohou být některé Neúspěšné přístupy do mezipaměti, ale lepší metriku k monitorování pro tlaku na paměť by `Used Memory` nebo `Evicted Keys`. |
| Čtení z mezipaměti |Objem dat je načten z mezipaměti v MB za sekundu (MB/s) během zadaného intervalu sestavy. Tato hodnota pochází z karty síťového rozhraní, které podporují virtuální počítač, který je hostitelem mezipaměti a není konkrétní Redis. **Tato hodnota odpovídá šířky pásma sítě používané tuto mezipaměť. Pokud chcete nastavit výstrahy pro omezení šířky pásma sítě na straně serveru, vytvořte ji pomocí tohoto `Cache Read` čítače. Zobrazit [Tato tabulka](cache-faq.md#cache-performance) mezí zjištěnou šířky pásma pro různé cenové úrovně a velikosti mezipaměti.** |
| Zápis do mezipaměti |Vykazování množství dat zapsaných do mezipaměti v MB za sekundu (MB/s) během zadaného intervalu. Tato hodnota pochází z karty síťového rozhraní, které podporují virtuální počítač, který je hostitelem mezipaměti a není konkrétní Redis. Tato hodnota odpovídá šířky pásma sítě dat odeslaných z klienta do mezipaměti. |
| Počet připojených klientů |Počet připojení klientů k mezipaměti během zadaného intervalu sestavy. To se mapuje na `connected_clients` z Redis informace o příkazu. Jakmile [limitu připojení](cache-configure.md#default-redis-server-configuration) je dosaženo následné pokusy o připojení k mezipaměti se nezdaří. Všimněte si, že i když nejsou žádné aktivní klientské aplikace, může stále existovat několik instancí připojených klientů z důvodu interní procesy a připojení. |
| Procesor |Využití výkonu procesoru ukládání do mezipaměti Azure k serveru Redis jako procento během zadaného intervalu sestavy. Tato hodnota se mapuje na operační systém `\Processor(_Total)\% Processor Time` čítače výkonu. |
| Chyby | Konkrétní chyby a problémy s výkonem, které do mezipaměti, mohl nastat během zadaného intervalu sestavy. Tato metrika je osm dimenze, představující typy různých chyb, ale může mít více přidán v budoucnu. Nyní reprezentována typy chyb jsou takto: <br/><ul><li>**Převzetí služeb při selhání** – když je mezipaměť převezme služby při selhání (podřízený server podporuje do hlavní větve)</li><li>**Selhání** – Pokud mezipaměti dochází k chybě neočekávaně některý z uzlů</li><li>**Dataloss** – když je dataloss v mezipaměti</li><li>**UnresponsiveClients** – Pokud klienti nejsou čtení dat ze serveru dostatečně rychle</li><li>**AOF** – Pokud se vyskytl problém související s AOF trvalosti</li><li>**RDB** – Pokud se vyskytl problém související s trvalost RDB</li><li>**Import** – Pokud se vyskytl problém související s Import RDB</li><li>**Export** – Pokud se vyskytl problém související s exportovat RDB</li></ul> |
| Vyloučené klíče |Počet položek během zadaného intervalu sestavy z důvodu odstraněn z mezipaměti `maxmemory` limit. To se mapuje na `evicted_keys` z Redis informace o příkazu. |
| Prošlé klíče |Počet položek, které vypršela během zadaného intervalu sestavy z mezipaměti. Tato hodnota se mapuje na `expired_keys` z Redis informace o příkazu.|
| Operace Get |Počet operací get z mezipaměti během zadaného intervalu sestavy. Tato hodnota je součet z následujících hodnot z informací Redis vše – příkaz: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, a `cmdstat_getrange`a je ekvivalentní součtem velikostí mezipaměti úspěchy a neúspěchy během generování sestav intervalu. |
| Operací za sekundu | Celkový počet zpracovaných za sekundu na server mezipaměti během zadaného intervalu sestavy příkazy.  Tato hodnota se mapuje na "instantaneous_ops_per_sec" Redis informace o příkazu. |
| Zatížení serveru Redis |Procentuální podíl cyklů, ve kterých je zaneprázdněná zpracováním a ne čekání na nečinnost zprávy serveru Redis. Pokud tento čítač dosáhne 100, to znamená, že Redis server překročil mezní hodnotu výkonu a nemůže zpracovat procesoru fungovat některé rychleji. Pokud dochází k vysoké zatížení Redis serveru se zobrazí výjimkám časového limitu v klientovi. V tomto případě byste měli zvážit vertikální navýšení kapacity nebo dělení dat mezi více mezipamětí. |
| Sady |Počet množinové operace do mezipaměti během zadaného intervalu sestavy. Tato hodnota je součtem následující hodnoty z informací Redis vše – příkaz: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` , a `cmdstat_setnx`. |
| Celkový počet klíčů  | Maximální počet klíčů v mezipaměti během posledních časové období sestavy. To se mapuje na `keyspace` z Redis informace o příkazu. Z důvodu omezení systému základní metriky pro mezipaměť pomocí clusterování povolené vrátí celkový počet klíčů maximální počet klíčů horizontálního oddílu, který měl maximální počet klíčů během generování sestav intervalu.  |
| Operace celkem |Celkový počet příkazů, které jsou zpracovány serverem mezipaměti během zadaného intervalu sestavy. Tato hodnota se mapuje na `total_commands_processed` z Redis informace o příkazu. Všimněte si, že při použití mezipaměti Azure Redis čistě pro publikování a odběr bude žádné metriky pro `Cache Hits`, `Cache Misses`, `Gets`, nebo `Sets`, ale bude existovat `Total Operations` metriky, které odrážejí využití mezipaměti pro operace publikování a odběr. |
| Použitá paměť |Velikost mezipaměti paměť použitá pro dvojice klíč/hodnota v mezipaměti v MB během zadaného intervalu sestavy. Tato hodnota se mapuje na `used_memory` z Redis informace o příkazu. To nezahrnuje metadata nebo fragmentace. |
| Procento využité paměti | % Celkové paměti, který se používá během zadaného intervalu sestavy.  Toto odkazuje na hodnotu "used_memory" z příkazu Redis informace k výpočtu procentuální hodnotu. |
| Využitá paměť RSS |Velikost mezipaměti využité MB během zadaného intervalu sestavy, včetně fragmentace a metadata. Tato hodnota se mapuje na `used_memory_rss` z Redis informace o příkazu. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Výstrahy
Můžete nakonfigurovat odesílání upozornění na základě metrik a protokolů aktivit. Azure Monitor umožňuje nakonfigurovat upozornění, které při aktivaci provede některé z následujících kroků:

* Odeslání e-mailového oznámení
* Volání webhooku
* Vyvolání aplikace logiky Azure

Ke konfiguraci pravidla upozornění pro mezipaměť, klikněte na tlačítko **pravidla upozornění** z **nabídce prostředků**.

![Monitorování](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Další informace o konfiguraci oznámení naleznete v tématu [přehled výstrah](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Protokoly aktivit
Protokoly aktivit poskytují přehled o operacích, které byly provedeny v mezipaměti Azure pro instance Redis. Se dřív označovalo jako "protokolů auditu" nebo "provozní protokoly". Pomocí protokolů aktivit můžete určit "co, kdo a kdy" veškerých operací (PUT, POST, DELETE) provedených v mezipaměti Azure pro instance Redis zápisu. 

> [!NOTE]
> Protokoly aktivit nezahrnují operace čtení (GET).
>
>

Zobrazení protokolů aktivit pro mezipaměť, klikněte na tlačítko **protokoly aktivit** z **nabídce prostředků**.

Další informace o protokolech aktivit najdete v tématu [přehled protokolu aktivit Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).











