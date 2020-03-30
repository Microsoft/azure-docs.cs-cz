---
title: Monitorování a ladění – Databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje funkce monitorování a ladění v Azure Database for PostgreSQL – Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 2d2bb5440e6d23a4cb5781244ba33ab4c5be2612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252586"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Monitorování a ladění databáze Azure pro PostgreSQL – jeden server
Monitorování dat o serverech vám pomůže řešit problémy a optimalizovat úlohu. Azure Database for PostgreSQL poskytuje různé možnosti monitorování, které poskytují přehled o chování vašeho serveru.

## <a name="metrics"></a>Metriky
Azure Database for PostgreSQL poskytuje různé metriky, které poskytují přehled o chování prostředků podporujících postgresql server. Každá metrika je vyzařována s frekvencí jedné minuty a má až 30 dní historie. Můžete nakonfigurovat výstrahy na metriky. Pokyny krok za krokem najdete v tématu [Jak nastavit výstrahy](howto-alert-on-metric.md). Mezi další úkoly patří nastavení automatizovaných akcí, provádění pokročilých analýz a archivace historie. Další informace najdete v tématu [Přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Seznam metrik
Tyto metriky jsou k dispozici pro Azure Database pro PostgreSQL:

|Metrika|Zobrazovaný název metriky|Jednotka|Popis|
|---|---|---|---|
|cpu_percent|Procento procesoru|Procento|Procento procesoru v provozu.|
|memory_percent|Procento paměti|Procento|Procento paměti, která se používá.|
|io_consumption_percent|IO procent|Procento|Procento vo v provozu.|
|storage_percent|Procento úložiště|Procento|Procento úložiště použitého mimo maximální hodnotu serveru.|
|storage_used|Použité úložiště|Bajty|Množství úložiště v provozu. Úložiště používané službou může zahrnovat databázové soubory, protokoly transakcí a protokoly serveru.|
|storage_limit|Limit úložiště|Bajty|Maximální úložiště pro tento server.|
|serverlog_storage_percent|Procento úložiště protokolu serveru|Procento|Procento úložiště protokolu serveru použitého mimo maximální úložiště protokolu serveru.|
|serverlog_storage_usage|Použité úložiště protokolu serveru|Bajty|Velikost úložiště protokolu serveru v provozu.|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Maximální úložiště protokolu serveru pro tento server.|
|active_connections|Aktivní připojení|Počet|Počet aktivních připojení k serveru.|
|connections_failed|Neúspěšná připojení|Počet|Počet neúspěšných připojení k serveru.|
|network_bytes_egress|Síťové výstupy|Bajty|Síť vynažte přes aktivní připojení.|
|network_bytes_ingress|Síťové vstupy|Bajty|Síť v přes aktivní připojení.|
|backup_storage_used|Použité úložiště záloh|Bajty|Velikost použitého úložiště záloh.|
|pg_replica_log_delay_in_bytes|Maximální zpoždění mezi replikami|Bajty|Zpoždění v bajtů mezi hlavní a nejvíce zaostávající repliky. Tato metrika je k dispozici pouze na hlavním serveru.|
|pg_replica_log_delay_in_seconds|Zpoždění replik|Sekundy|Čas od poslední přehrané transakce. Tato metrika je k dispozici pouze pro repliky serverů.|

## <a name="server-logs"></a>Protokoly serveru
Můžete povolit protokolování na serveru. Tyto protokoly jsou taky dostupné prostřednictvím diagnostických protokolů Azure v [protokolech Azure Monitor ,](../azure-monitor/log-query/log-query-overview.md)event huby a účet úložiště. Další informace o protokolování naleznete na stránce [protokolů serveru.](concepts-server-logs.md)

## <a name="query-store"></a>Úložiště dotazů
[Úložiště dotazů](concepts-query-store.md) sleduje výkon dotazů v průběhu času, včetně statistik y běhu dotazu a událostí čekání. Tato funkce zachová informace o výkonu za běhu dotazu v systémové databázi s názvem **azure_sys** pod schématem query_store. Sběr a ukládání dat můžete ovládat pomocí různých konfiguračních knoflíků.

## <a name="query-performance-insight"></a>Query Performance Insight
[Přehled výkonu dotazů](concepts-query-performance-insight.md) funguje ve spojení s Úložištěm dotazů a poskytuje vizualizace přístupné z webu Azure Portal. Tyto grafy umožňují identifikovat klíčové dotazy, které ovlivňují výkon. Přehled výkonu dotazu je přístupný z části **Podpora + řešení potíží** na stránce portálu serveru Azure Database for PostgreSQL.

## <a name="performance-recommendations"></a>Doporučení k výkonu
Funkce [Doporučení pro zvýšení výkonu](concepts-performance-recommendations.md) identifikuje příležitosti ke zlepšení výkonu pracovního vytížení. Doporučení výkonu poskytuje doporučení pro vytváření nových indexů, které mají potenciál zlepšit výkon úloh. Chcete-li vytvořit doporučení indexu, funkce bere v úvahu různé vlastnosti databáze, včetně jeho schéma a zatížení, jak je uvedeno v úložišti dotazů. Po implementaci jakékoli doporučení výkonu, zákazníci by měli otestovat výkon vyhodnotit dopad těchto změn. 

## <a name="planned-maintenance-notification"></a>Oznámení o plánované údržbě

**Plánovaná oznámení o údržbě** umožňují přijímat upozornění na nadcházející plánovanou údržbu do databáze Azure pro postgreSQL – jeden server. Tato oznámení jsou integrována s plánovanou údržbou [služby Service Health](../service-health/overview.md) a umožňují zobrazit veškerou plánovanou údržbu pro vaše předplatná na jednom místě. Pomáhá také škálovat oznámení na správné cílové skupiny pro různé skupiny prostředků, protože můžete mít různé kontakty odpovědné za různé zdroje. Oznámení o nadcházející údržbě obdržíte 72 hodin před akcí.

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
- Přečtěte [si, jak nastavit upozornění](howto-alert-on-metric.md) pro pokyny k vytvoření výstrahy na metriku.
- Další informace o tom, jak získat přístup a exportovat metriky pomocí portálu Azure, rozhraní REST API nebo rozhraní příkazového příkazového příkazu, najdete v tématu [Přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Přečtěte si náš blog o [osvědčených postupech pro sledování vašeho serveru](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
