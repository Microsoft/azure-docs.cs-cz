---
title: Sledování a ladění ve službě Azure Database for PostgreSQL
description: Tento článek popisuje monitorování a ladění funkcí ve službě Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: c412ace453eb37646654375454d80a5c4e8dc3a6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540907"
---
# <a name="monitor-and-tune"></a>Monitorování a ladění
Data o vašich serverech monitorování vám pomůže řešit a optimalizovat pro vaši úlohu. Azure Database for PostgreSQL nabízí různé možnosti monitorování k poskytování přehledů o chování vašeho serveru.

## <a name="metrics"></a>Metriky
Azure Database for PostgreSQL poskytuje různé metriky, které poskytují přehled o chování prostředků podporujících PostgreSQL server. Jednotlivé metriky je vygenerován v minutových intervalech a má až 30 dní. Můžete nakonfigurovat výstrahy o metrikách. Pokyny krok za krokem, najdete v článku [jak nastavit výstrahy](howto-alert-on-metric.md). Další úkoly zahrnují nastavením automatizovaných akcí, provádění pokročilých analýz a pro archivaci historie. Další informace najdete v tématu [přehled metriky Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Seznam metrik
Tyto metriky jsou k dispozici pro službu Azure Database for PostgreSQL:

|Metrika|Metriky zobrazovaný název|Jednotka|Popis|
|---|---|---|---|---|
|cpu_percent|Procento CPU|Procento|Procento procesoru používá.|
|memory_percent|Paměť v procentech|Procento|Procento paměti používá.|
|io_consumption_percent|V/v úrovně procent|Procento|Procentuální podíl vstupně-výstupní operace používá.|
|storage_percent|Procento úložiště|Procento|Je maximální procento využité místo mimo server.|
|storage_used|Využité úložiště|B|Množství využívaného úložiště. Úložiště využívané ve službě mohou být soubory databáze, protokoly transakcí a v protokolech serveru.|
|storage_limit|Limit úložiště.|B|Maximální velikost úložiště pro tento server.|
|serverlog_storage_percent|Procento úložiště protokolů serveru|Procento|Procento úložiště protokolů serveru používat úložiště protokolů maximální server na server.|
|serverlog_storage_usage|Využité úložiště protokolů serveru|B|Množství využívaného úložiště protokolu serveru.|
|serverlog_storage_limit|Limit úložiště protokolů serveru|B|Maximální serverové úložiště protokolu pro tento server.|
|active_connections|Aktivní připojení|Počet|Počet aktivních připojení k serveru.|
|connections_failed|Chyba připojení|Počet|Počet selhání připojení k serveru.|
|network_bytes_egress|Síťové výstupy|B|Odchozí síťový provoz mezi aktivních připojení.|
|network_bytes_ingress|Síťové vstupy|B|Sítě v rámci aktivních připojení.|
|backup_storage_used|Využité úložiště záloh|B|Velikost úložiště záloh používá.|

## <a name="server-logs"></a>Protokoly serveru
Můžete povolit protokolování na server. Tyto protokoly jsou také k dispozici prostřednictvím Azure diagnostické protokoly v [Log Analytics](../azure-monitor/log-query/log-query-overview.md), Event Hubs a účet úložiště. Další informace o protokolování, najdete [protokoly serveru](concepts-server-logs.md) stránky.

## <a name="query-store"></a>Úložiště dotazů
[Query Store](concepts-query-store.md) je funkce ve verzi public preview, která uchovává informace o dotazu dotazování statistické údaje o výkonu v průběhu času včetně a počkejte události. Funkci nevyřeší dotazu informace o výkonu modulu runtime v systémové databázi s názvem **azure_sys** pod query_store schématu. Můžete řídit shromažďování a ukládání dat prostřednictvím různých knoflíky konfigurace.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) funguje ve spojení s Query Store vizualizace, které jsou přístupné z portálu Azure portal. Tyto grafy umožňují identifikovat dotazy klíčů tohoto ovlivnit výkon. Query Performance Insight je ve verzi public preview a je přístupná **podpora a řešení potíží** část Azure Database for PostgreSQL server stránky portálu.

## <a name="performance-recommendations"></a>Doporučení k výkonu
[Doporučení k výkonu](concepts-performance-recommendations.md) funkce identifikuje příležitosti pro zlepšení výkonu úloh. Verze public preview služby doporučení k výkonu poskytuje doporučení pro vytvoření nové indexy, které by mohly zvýšit výkon vašich úloh. K vytvoření doporučení indexu, tato funkce vezme v úvahu různé vlastnosti databáze, včetně jeho schématu a úloh podle Query Store. Po implementaci jakékoli doporučení výkon, by měl zákazníkům testování výkonu vyhodnotit její dopad těchto změn. 

## <a name="next-steps"></a>Další postup
- Zobrazit [jak nastavit výstrahy](howto-alert-on-metric.md) pokyny k vytvoření upozornění na metriku.
- Další informace o tom, jak získat přístup a exportovat metriky pomocí webu Azure portal, rozhraní REST API nebo rozhraní příkazového řádku najdete v tématu [přehled metriky Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Přečtěte si náš blog o [osvědčené postupy pro monitorování serveru](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
