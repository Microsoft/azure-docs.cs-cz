---
title: Sledování a ladění ve službě Azure Database for PostgreSQL – jeden Server
description: Tento článek popisuje monitorování a ladění funkcí ve službě Azure Database for PostgreSQL – jeden Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: c69ffb30a37de8e6dc3e15aa1f7dcd6a9311d614
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274300"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Sledování a ladění – Azure Database for PostgreSQL – jeden Server
Data o vašich serverech monitorování vám pomůže řešit a optimalizovat pro vaši úlohu. Azure Database for PostgreSQL nabízí různé možnosti monitorování k poskytování přehledů o chování vašeho serveru.

## <a name="metrics"></a>Metriky
Azure Database for PostgreSQL poskytuje různé metriky, které poskytují přehled o chování prostředků podporujících PostgreSQL server. Jednotlivé metriky je vygenerován v minutových intervalech a má až 30 dní. Můžete nakonfigurovat výstrahy o metrikách. Pokyny krok za krokem, najdete v článku [jak nastavit výstrahy](howto-alert-on-metric.md). Další úkoly zahrnují nastavením automatizovaných akcí, provádění pokročilých analýz a pro archivaci historie. Další informace najdete v tématu [přehled metriky Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Seznam metrik
Tyto metriky jsou k dispozici pro službu Azure Database for PostgreSQL:

|Metrika|Metriky zobrazovaný název|Jednotka|Popis|
|---|---|---|---|
|cpu_percent|Procento CPU|Procento|Procento procesoru používá.|
|memory_percent|Paměť v procentech|Procento|Procento paměti používá.|
|io_consumption_percent|V/v úrovně procent|Procento|Procentuální podíl vstupně-výstupní operace používá.|
|storage_percent|Procento úložiště|Procento|Je maximální procento využité místo mimo server.|
|storage_used|Využité úložiště|B|Množství využívaného úložiště. Úložiště využívané ve službě mohou být soubory databáze, protokoly transakcí a v protokolech serveru.|
|storage_limit|Limit úložiště.|B|Maximální velikost úložiště pro tento server.|
|serverlog_storage_percent|Procento úložiště protokolů serveru|Procento|Procento úložiště protokolů serveru používat úložiště protokolů maximální server na server.|
|serverlog_storage_usage|Využité úložiště protokolů serveru|B|Množství využívaného úložiště protokolu serveru.|
|serverlog_storage_limit|Limit úložiště protokolů serveru|B|Maximální serverové úložiště protokolu pro tento server.|
|active_connections|Aktivní připojení|Count|Počet aktivních připojení k serveru.|
|connections_failed|Neúspěšná připojení|Počet|Počet selhání připojení k serveru.|
|network_bytes_egress|Síťové výstupy|B|Odchozí síťový provoz mezi aktivních připojení.|
|network_bytes_ingress|Síťové vstupy|B|Sítě v rámci aktivních připojení.|
|backup_storage_used|Využité úložiště záloh|B|Velikost úložiště záloh používá.|
|pg_replica_log_delay_in_bytes|Maximální prodleva mezi repliky|B|Prodleva v bajtech mezi hlavní a většina obložení repliky. Tato metrika je k dispozici na hlavní server.|
|pg_replica_log_delay_in_seconds|Prodleva repliky|Sekundy|Čas od poslední přehrály transakce. Tato metrika je k dispozici pouze servery repliky.|

## <a name="server-logs"></a>Protokoly serveru
Můžete povolit protokolování na server. Tyto protokoly jsou také k dispozici prostřednictvím Azure diagnostické protokoly v [protokoly Azure monitoru](../azure-monitor/log-query/log-query-overview.md), Event Hubs a účet úložiště. Další informace o protokolování, najdete [protokoly serveru](concepts-server-logs.md) stránky.

## <a name="query-store"></a>Úložiště dotazů
[Query Store](concepts-query-store.md) uchovává informace o dotazu dotazování statistické údaje o výkonu v průběhu času včetně a počkejte události. Funkci nevyřeší dotazu informace o výkonu modulu runtime v systémové databázi s názvem **azure_sys** pod query_store schématu. Můžete řídit shromažďování a ukládání dat prostřednictvím různých knoflíky konfigurace.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) funguje ve spojení s Query Store vizualizace, které jsou přístupné z portálu Azure portal. Tyto grafy umožňují identifikovat dotazy klíčů tohoto ovlivnit výkon. Dotazování výkonu Insightis přístupná **podpora a řešení potíží** část Azure Database for PostgreSQL server stránky portálu.

## <a name="performance-recommendations"></a>Doporučení k výkonu
[Doporučení k výkonu](concepts-performance-recommendations.md) funkce identifikuje příležitosti pro zlepšení výkonu úloh. Doporučení k výkonu poskytuje doporučení pro vytvoření nové indexy, které by mohly zvýšit výkon vašich úloh. K vytvoření doporučení indexu, tato funkce vezme v úvahu různé vlastnosti databáze, včetně jeho schématu a úloh podle Query Store. Po implementaci jakékoli doporučení výkon, by měl zákazníkům testování výkonu vyhodnotit její dopad těchto změn. 

## <a name="next-steps"></a>Další postup
- Zobrazit [jak nastavit výstrahy](howto-alert-on-metric.md) pokyny k vytvoření upozornění na metriku.
- Další informace o tom, jak získat přístup a exportovat metriky pomocí webu Azure portal, rozhraní REST API nebo rozhraní příkazového řádku najdete v tématu [přehled metriky Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Přečtěte si náš blog o [osvědčené postupy pro monitorování serveru](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
