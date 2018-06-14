---
title: Tom, jak monitorovat Azure Redis Cache | Microsoft Docs
description: Naučte se monitorovat stav a výkon vaší instance služby Azure Redis Cache
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: 3a68435866e6fb5bf0210144e53918c35b416449
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910645"
---
# <a name="how-to-monitor-azure-redis-cache"></a>Jak monitorovat Azure Redis Cache
Používá Azure Redis Cache [Azure monitorování](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) zajistit několik možností pro monitorování vaše instance mezipaměti. Můžete zobrazit metriky, připnout grafy metriky na úvodní panel, přizpůsobení datum a čas rozsah monitorování grafy, přidat a metriky odebrání grafy a nastavit upozornění, pokud jsou splněny určité podmínky. Tyto nástroje vám umožňují sledovat stav vaší instance služby Azure Redis Cache a vám pomohou při správě aplikace ukládání do mezipaměti.

Metriky pro instance služby Azure Redis Cache jsou shromažďována pomocí Redis [informace](http://redis.io/commands/info) příkaz přibližně dvakrát za minutu a automaticky uložené 30 dní (najdete v části [exportovat mezipaměti metriky](#export-cache-metrics) ke konfiguraci jiné zásady uchovávání informací) tak, aby se zobrazí v grafech metriky a vyhodnocuje pravidla výstrah. Další informace o různých informace hodnoty používané pro jednotlivé metriky mezipaměti najdete v tématu [k dostupným metrikám a vytváření sestav intervaly](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Chcete-li zobrazit mezipaměti metriky, [Procházet](cache-configure.md#configure-redis-cache-settings) do instance mezipaměti v [portál Azure](https://portal.azure.com).  Azure Redis Cache obsahuje několik předdefinovaných grafy **přehled** okno a **Redis metriky** okno. Každý graf lze přizpůsobit přidáním nebo odebráním metriky a změna reporting intervalu.

![Metrika mezipaměti Redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Zobrazení předem nakonfigurované metriky grafy

**Přehled** okno obsahuje následující předem nakonfigurované monitorování grafy.

* [Monitorování grafy](#monitoring-charts)
* [Použití grafů](#usage-charts)

### <a name="monitoring-charts"></a>Monitorování grafy
**Monitorování** tématu **přehled** okno obsahuje **přístupy a nezdařených přístupů k**, **získá a nastaví**, **připojení**, a **celkový příkazy** grafy.

![Monitorování grafy](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Použití grafů
**Využití** tématu **přehled** okno obsahuje **zatížení serveru Redis**, **využití paměti**, **šířka pásma sítě**, a **využití procesoru** grafy a také zobrazuje **cenová úroveň** pro instanci mezipaměti.

![Použití grafů](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Cenová úroveň** zobrazí mezipaměti cenovou úroveň a lze provádět [škálování](cache-how-to-scale.md) mezipaměti na jinou cenovou úroveň.

## <a name="view-metrics-with-azure-monitor"></a>Zobrazit metriky s Azure monitorování
Chcete-li zobrazit Redis metriky a vytvářet vlastní grafy pomocí Azure monitorování, klikněte na tlačítko **metriky** z **prostředků nabídky**a přizpůsobit graf pomocí požadovaného metriky, vytváření sestav interval, typ grafu a další.

![Metrika mezipaměti Redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Další informace o práci s metriky pomocí Azure monitorování najdete v tématu [přehled metriky v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Export mezipaměti metriky
Ve výchozím nastavení, mezipaměti metriky v Azure monitorování jsou [uchovávají po dobu 30 dnů](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) a odstraní. Chcete-li zachovat metriky vaší mezipaměti po dobu delší než 30 dní, můžete [určit účet úložiště](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) a zadejte **uchovávání dat (dny)** zásady pro vaše mezipaměť metriky. 

Konfigurace účtu úložiště pro vaše metriky mezipaměti:

1. Klikněte na tlačítko **diagnostiky** z **prostředků nabídky** v **Redis Cache** okno.
2. Klikněte na tlačítko **na**.
3. Zkontrolujte **archivu do účtu úložiště**.
4. Vyberte účet úložiště pro uložení metriky mezipaměti.
5. Zkontrolujte **1 minuta** zaškrtávací políčko a zadejte **uchovávání dat (dny)** zásad. Pokud nechcete použít všechny zásady uchovávání informací a navždy zachování dat, nastavte **uchovávání dat (dny)** k **0**.
6. Klikněte na **Uložit**.

![Redis diagnostiky](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Kromě archivace vaše mezipaměť metriky pro úložiště, můžete také [stream je do centra událostí nebo poslat k analýze protokolů](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

Pro přístup k vaší metriky, můžete je zobrazit na portálu Azure podle předchozích pokynů v tomto článku a můžete taky přejít pomocí [REST API služby Azure monitorování metriky](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

> [!NOTE]
> Pokud změníte účty úložiště, data v účtu úložiště dříve nakonfigurované zůstane k dispozici ke stažení, ale nezobrazí se na portálu Azure.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>K dostupným metrikám a vytváření sestav intervaly
Metriky mezipaměti jsou hlásila použití intervalech několik sestav, včetně **po hodině**, **Dnes**, **za minulý týden**, a **vlastní**. **Metrika** pro každý metriky graf zobrazuje průměrné, minimální a maximální hodnoty pro jednotlivé metriky v grafu a některé metriky zobrazení celkem pro generování sestav interval. 

Jednotlivé metriky zahrnuje dvě verze. Jedna metrika měří výkonu pro celou mezipaměť a mezipaměti, které používají [clustering](cache-how-to-premium-clustering.md), druhá verze metriku, která zahrnuje `(Shard 0-9)` výkonu míry název pro jednu horizontálního oddílu v mezipaměti. Například pokud mezipaměť obsahuje 4 horizontálních oddílů `Cache Hits` je celková velikost přístupů pro celou mezipaměť, a `Cache Hits (Shard 3)` je právě přístupů pro tento horizontálního oddílu mezipaměti.

> [!NOTE]
> I v případě mezipaměti nečinný bez aktivních klientů připojených aplikací, může se zobrazit některé mezipaměti aktivitou, například s připojenými klienty, využití paměti a operace provádí. Tato aktivita je normální během operace instanci služby Azure Redis Cache.
> 
> 

| Metrika | Popis |
| --- | --- |
| Přístupy do mezipaměti |Počet úspěšných hledání klíče během zadaného intervalu sestavy. To se mapuje na `keyspace_hits` z Redis [informace](http://redis.io/commands/info) příkaz. |
| Neúspěšné přístupy do mezipaměti |Počet neúspěšných klíč hledání během zadaného intervalu sestavy. To se mapuje na `keyspace_misses` z Redis informace o příkazu. Neúspěšné přístupy do mezipaměti neznamenají, že se vyskytl problém s mezipamětí. Například při použití mezipaměti vyhraďte programovací vzor, aplikace vypadá první do mezipaměti pro položku. Pokud položka není (neúspěšných přístupů do mezipaměti), položka je načtena z databáze a přidány do mezipaměti pro další použití. Neúspěšné přístupy do mezipaměti jsou normální chování pro mezipaměť vyhraďte programovací vzor. Pokud počet nezdařených přístupů k mezipaměti je vyšší, než se očekávalo, zkontrolujte aplikační logiky, která naplní a přečte z mezipaměti. Pokud jsou určený k vyloučení položek z mezipaměti z důvodu přetížení paměti, může být některé Neúspěšné přístupy do mezipaměti, ale lepší Metrika pro monitorování přetížení paměti by `Used Memory` nebo `Evicted Keys`. |
| Připojení klienti |Počet připojení klientů k mezipaměti během zadaného intervalu sestavy. To se mapuje na `connected_clients` z Redis informace o příkazu. Jednou [limitu připojení](cache-configure.md#default-redis-server-configuration) se dosáhlo maximálního následující pokusy o připojení k mezipaměti se nezdaří. Všimněte si, že i v případě, že neexistují žádné aktivní klientská aplikace, může stále nastat několik instancí připojených klientů z důvodu vnitřní procesy a připojení. |
| Vyloučené klíče |Počet položek odstraněna z mezipaměti během zadaného intervalu sestavy z důvodu `maxmemory` limit. To se mapuje na `evicted_keys` z Redis informace o příkazu. |
| Prošlé klíče |Počet položek, které vypršela během zadaného intervalu sestavy z mezipaměti. Tato hodnota se mapuje na `expired_keys` z Redis informace o příkazu. |
| Celkový počet klíčů  | Maximální počet klíčů v mezipaměti během posledních časové období sestavy. To se mapuje na `keyspace` z Redis informace o příkazu. Z důvodu omezení systému základní metriky pro mezipaměti s clusteringem povolené vrátí celkový počet klíčů maximální počet klíčů horizontálního oddílu, který měl maximální počet klíčů během vytváření sestav intervalu.  |
| Klíče Get |Počet operací get z mezipaměti během zadaného intervalu sestavy. Tato hodnota je součtem těchto hodnot z informace o Redis všechny příkaz: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, a `cmdstat_getrange`a je ekvivalentní součtem velikostí mezipaměti úspěchy a neúspěchy během generování sestav intervalu. |
| Zatížení serveru Redis |Procento cykly, ve kterých je zaneprázdněný zpracovávají a nečeká nečinnosti pro zprávy serveru Redis. Pokud tento čítač dosáhne 100, znamená to, narazil na serveru Redis mezní hodnoty výkonu a nemůže zpracovat procesoru fungovat všechny rychlejší. Pokud vidíte vysoké zatížení serveru Redis uvidíte výjimkám časového limitu v klientovi. V tomto případě zvažte vertikálním navýšení kapacity nebo dělení dat do více mezipamětí. |
| Klíče Set |Počet operací sady do mezipaměti během zadaného intervalu sestavy. Tato hodnota je součtem následující hodnoty z informace o Redis vše, příkaz: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` , a `cmdstat_setnx`. |
| Operace celkem |Celkový počet příkazy, které jsou zpracovány serverem mezipaměti během zadaného intervalu sestavy. Tato hodnota se mapuje na `total_commands_processed` z Redis informace o příkazu. Všimněte si, že při použití Azure Redis Cache výhradně pro protokol pub nebo sub bude žádné metriky pro `Cache Hits`, `Cache Misses`, `Gets`, nebo `Sets`, ale bude `Total Operations` metriky, které odráží využití mezipaměti pro operace pub nebo sub. |
| Použitá paměť |Velikost mezipaměti paměť použitá pro dvojice klíč/hodnota v mezipaměti v MB během zadaného intervalu sestavy. Tato hodnota se mapuje na `used_memory` z Redis informace o příkazu. Nezahrnuje metadata nebo fragmentace. |
| Použitá paměť RSS |Množství paměti mezipaměti v MB během zadaného intervalu sestavy, včetně fragmentace a metadata. Tato hodnota se mapuje na `used_memory_rss` z Redis informace o příkazu. |
| Procesor |Využití procesoru serveru Azure Redis Cache v procentech během zadaného intervalu sestavy. Tato hodnota se mapuje na operační systém `\Processor(_Total)\% Processor Time` čítače výkonu. |
| Čtení z mezipaměti |Množství dat načten z mezipaměti v MB za sekundu (MB/s) během zadaného intervalu sestavy. Tato hodnota se odvozuje od karty síťového rozhraní, které podporují virtuální počítač, který hostuje mezipaměti a není konkrétní Redis. **Tato hodnota odpovídá šířku pásma sítě používané této mezipaměti. Pokud chcete nastavit výstrahy pro omezení šířky pásma sítě straně serveru, vytvořte ji pomocí této `Cache Read` čítače. V tématu [Tato tabulka](cache-faq.md#cache-performance) mezí zjištěnou šířky pásma pro různé mezipaměti cenové úrovně a velikosti.** |
| Zápis do mezipaměti |Reporting množství dat, které jsou zapsány do mezipaměti v MB za sekundu (MB/s) během zadaného intervalu. Tato hodnota se odvozuje od karty síťového rozhraní, které podporují virtuální počítač, který hostuje mezipaměti a není konkrétní Redis. Tato hodnota odpovídá na šířku pásma sítě data odeslaná do mezipaměti z klienta. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Výstrahy
Můžete nakonfigurovat přijímat výstrahy založené na protokolech metriky a aktivity. Azure monitorování umožňuje nakonfigurovat výstrahu při aktivaci, proveďte následující:

* Poslat e-mailové oznámení
* Volat webhook, jehož
* Vyvolání aplikace logiky Azure

Chcete-li nakonfigurovat pravidla výstrah pro mezipaměť, klikněte na tlačítko **výstrah pravidla** z **prostředků nabídky**.

![Monitorování](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Další informace o konfiguraci a používání výstrah najdete v tématu [přehled výstrah](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Protokoly aktivit
Protokoly aktivity získat přehled o činnosti, které byly provedeny v vaší instance služby Azure Redis Cache. Se dřív označovala jako "protokoly auditu" nebo "operační protokoly". Pomocí protokolů z aktivity, můžete určit ", kdo a kdy" pro všechny zápisu operace (PUT, POST, DELETE) na vaše instance služby Azure Redis Cache. 

> [!NOTE]
> Protokoly aktivity nezahrnují operace čtení (GET).
>
>

Chcete-li zobrazit protokoly aktivity pro mezipaměť, klikněte na tlačítko **protokoly aktivity** z **prostředků nabídky**.

Další informace o protokoly aktivity najdete v tématu [přehled protokol činnosti Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).











