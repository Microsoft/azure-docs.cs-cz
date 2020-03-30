---
title: Monitorování – databáze Azure pro MariaDB
description: Tento článek popisuje metriky pro monitorování a upozorňování pro Azure Database pro MariaDB, včetně statistiky procesoru, úložiště a připojení.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20a2066dc0228fe9c2fee09387d96bf6aafeb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531970"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitorování v databázi Azure pro MariaDB
Monitorování dat o serverech vám pomůže řešit problémy a optimalizovat úlohu. Azure Database for MariaDB poskytuje různé metriky, které poskytují přehled o chování vašeho serveru.

## <a name="metrics"></a>Metriky
Všechny metriky Azure mají frekvenci jedné minuty a každá metrika poskytuje 30 denní historii. Můžete nakonfigurovat výstrahy na metriky. Mezi další úkoly patří nastavení automatizovaných akcí, provádění pokročilých analýz a archivace historie. Další informace najdete v tématu [Přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Pokyny krok za krokem najdete v tématu [Jak nastavit výstrahy](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Seznam metrik
Tyto metriky jsou k dispozici pro Azure Database pro MariaDB:

|Metrika|Zobrazovaný název metriky|Jednotka|Popis|
|---|---|---|---|
|cpu_percent|Procento procesoru|Procento|Procento procesoru v provozu.|
|memory_percent|Procento paměti|Procento|Procento paměti, která se používá.|
|io_consumption_percent|IO procent|Procento|Procento vo v provozu.|
|storage_percent|Procento úložiště|Procento|Procento úložiště použitého mimo maximální hodnotu serveru.|
|storage_used|Použité úložiště|Bajty|Množství úložiště v provozu. Úložiště používané službou může zahrnovat databázové soubory, protokoly transakcí a protokoly serveru.|
|serverlog_storage_percent|Procento úložiště protokolu serveru|Procento|Procento úložiště protokolu serveru použitého mimo maximální úložiště protokolu serveru.|
|serverlog_storage_usage|Použité úložiště protokolu serveru|Bajty|Velikost úložiště protokolu serveru v provozu.|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Maximální úložiště protokolu serveru pro tento server.|
|storage_limit|Limit úložiště|Bajty|Maximální úložiště pro tento server.|
|active_connections|Aktivní připojení|Počet|Počet aktivních připojení k serveru.|
|connections_failed|Neúspěšná připojení|Počet|Počet neúspěšných připojení k serveru.|
|network_bytes_egress|Síťové výstupy|Bajty|Síť vynažte přes aktivní připojení.|
|network_bytes_ingress|Síťové vstupy|Bajty|Síť v přes aktivní připojení.|

## <a name="server-logs"></a>Protokoly serveru

Můžete povolit pomalé protokolování dotazů na serveru. Tyto protokoly jsou taky dostupné prostřednictvím diagnostických protokolů Azure v protokolech Azure Monitor, event hubů a účtu úložiště. Další informace o protokolování naleznete na stránce [protokolů serveru.](concepts-server-logs.md)

## <a name="query-store"></a>Úložiště dotazů

[Úložiště dotazů](concepts-query-store.md) sleduje výkon dotazů v průběhu času, včetně statistik y běhu dotazu a událostí čekání. Tato funkce zachová informace o výkonu modulu runtime dotazu ve schématu **mySQL.** Sběr a ukládání dat můžete ovládat pomocí různých konfiguračních knoflíků.

## <a name="query-performance-insight"></a>Query Performance Insight

[Přehled výkonu dotazů](concepts-query-performance-insight.md) funguje ve spojení s Úložištěm dotazů a poskytuje vizualizace přístupné z webu Azure Portal. Tyto grafy umožňují identifikovat klíčové dotazy, které ovlivňují výkon. Přehled výkonu dotazů je přístupný v části **Inteligentní výkon** na stránce portálu serveru Azure pro mariadb.

## <a name="performance-recommendations"></a>Doporučení k výkonu

Funkce [Doporučení pro zvýšení výkonu](concepts-performance-recommendations.md) identifikuje příležitosti ke zlepšení výkonu pracovního vytížení. Doporučení výkonu poskytuje doporučení pro vytváření nových indexů, které mají potenciál zlepšit výkon úloh. Chcete-li vytvořit doporučení indexu, funkce bere v úvahu různé vlastnosti databáze, včetně jeho schéma a zatížení, jak je uvedeno v úložišti dotazů. Po implementaci jakékoli doporučení výkonu, zákazníci by měli otestovat výkon vyhodnotit dopad těchto změn.

## <a name="planned-maintenance-notification"></a>Oznámení o plánované údržbě

**Plánovaná oznámení o údržbě** umožňují přijímat upozornění na nadcházející plánovanou údržbu do databáze Azure pro MariaDB. Tato oznámení jsou integrována s plánovanou údržbou [služby Service Health](../service-health/overview.md) a umožňují zobrazit veškerou plánovanou údržbu pro vaše předplatná na jednom místě. Pomáhá také škálovat oznámení na správné cílové skupiny pro různé skupiny prostředků, protože můžete mít různé kontakty odpovědné za různé zdroje. Oznámení o nadcházející údržbě obdržíte 72 hodin před akcí.

> [!Note]
> Provedeme každý pokus o poskytnutí **oznámení o plánované údržbě** 72 hodin předem pro všechny události. V případě kritických nebo bezpečnostních záplat však mohou být oznámení odeslána blíže k události nebo mohou být vynechána.

### <a name="to-receive-planned-maintenance-notification"></a>Chcete-li dostávat oznámení o plánované údržbě

1. Na [portálu](https://portal.azure.com)vyberte **možnost Stav služby**.
2. V části **Výstrahy** vyberte **výstrahy stavu**.
3. Vyberte **+ Přidat výstrahu stavu služby** a vyplňte pole.
4. Vyplňte požadovaná pole. 
5. Zvolte **typ události**, vyberte **Plánovaná údržba** nebo **Vybrat vše**
6. Ve **skupinách akcí** definujte, jak chcete dostávat upozornění (získat e-mail, aktivovat aplikaci logiky atd.)  
7. Ujistěte se, že pravidlo povolit při vytváření je nastaveno na ano.
8. Chcete-li **výstrahu** dokončit, vyberte možnost Vytvořit pravidlo výstrahy.

Podrobné kroky k vytvoření **výstrah stavu služby**naleznete v části [Vytvoření výstrah protokolu aktivit v oznámeních služby](../service-health/alerts-activity-log-service-notifications.md).

> [!IMPORTANT]
> Oznámení o plánované údržbě jsou aktuálně ve verzi Preview.

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak získat přístup a exportovat metriky pomocí portálu Azure, rozhraní REST API nebo rozhraní příkazového příkazového příkazu, najdete v tématu [Přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Viz [Jak nastavit upozornění](howto-alert-metric.md) pro pokyny k vytvoření výstrahy na metriku.
