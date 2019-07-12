---
title: Monitorování v Azure Database pro MariaDB
description: Tento článek popisuje metriky pro monitorování a výstrah pro službu Azure Database pro MariaDB, včetně statistiky CPU, úložiště a připojení.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: fb998edffed290bb7bc59945163f0fd48c55cbf5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612533"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitorování v Azure Database pro MariaDB
Data o vašich serverech monitorování vám pomůže řešit a optimalizovat pro vaši úlohu. Azure Database pro MariaDB poskytuje různé metriky, které poskytují přehled o chování vašeho serveru.

## <a name="metrics"></a>Metriky
Všechny metriky Azure máte minutových frekvence a jednotlivé metriky poskytuje 30 dní historie. Můžete nakonfigurovat výstrahy o metrikách. Další úkoly zahrnují nastavením automatizovaných akcí, provádění pokročilých analýz a pro archivaci historie. Další informace najdete v tématu [přehled metriky Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Pokyny krok za krokem, najdete v článku [jak nastavit výstrahy](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Seznam metrik
Tyto metriky jsou k dispozici pro službu Azure Database pro MariaDB:

|Metrika|Metriky zobrazovaný název|Jednotka|Popis|
|---|---|---|---|
|cpu_percent|Procento CPU|Percent|Procento procesoru používá.|
|memory_percent|Paměť v procentech|Percent|Procento paměti používá.|
|io_consumption_percent|V/v úrovně procent|Percent|Procentuální podíl vstupně-výstupní operace používá.|
|storage_percent|Procento úložiště|Percent|Je maximální procento využité místo mimo server.|
|storage_used|Využité úložiště|B|Množství využívaného úložiště. Úložiště využívané ve službě mohou být soubory databáze, protokoly transakcí a v protokolech serveru.|
|serverlog_storage_percent|Procento úložiště protokolů serveru|Percent|Procento úložiště protokolů serveru používat úložiště protokolů maximální server na server.|
|serverlog_storage_usage|Využité úložiště protokolů serveru|B|Množství využívaného úložiště protokolu serveru.|
|serverlog_storage_limit|Limit úložiště protokolů serveru|B|Maximální serverové úložiště protokolu pro tento server.|
|storage_limit|Limit úložiště.|B|Maximální velikost úložiště pro tento server.|
|active_connections|Aktivní připojení|Count|Počet aktivních připojení k serveru.|
|connections_failed|Neúspěšná připojení|Count|Počet selhání připojení k serveru.|
|network_bytes_egress|Síťové výstupy|B|Odchozí síťový provoz mezi aktivních připojení.|
|network_bytes_ingress|Síťové vstupy|B|Sítě v rámci aktivních připojení.|

## <a name="server-logs"></a>Protokoly serveru

Můžete povolit na vašem serveru protokolování pomalých dotazů. Tyto protokoly jsou také k dispozici prostřednictvím Azure diagnostické protokoly v protokolech, Event Hubs a účet úložiště Azure Monitor. Další informace o protokolování, najdete [protokoly serveru](concepts-server-logs.md) stránky.

## <a name="query-store"></a>Úložiště dotazů

[Query Store](concepts-query-store.md) je funkce ve verzi public preview, která uchovává informace o dotazu dotazování statistické údaje o výkonu v průběhu času včetně a počkejte události. Funkci nevyřeší dotazu informace o výkonu modulu runtime v **mysql** schématu. Můžete řídit shromažďování a ukládání dat prostřednictvím různých knoflíky konfigurace.

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](concepts-query-performance-insight.md) funguje ve spojení s Query Store vizualizace, které jsou přístupné z portálu Azure portal. Tyto grafy umožňují identifikovat dotazy klíčů tohoto ovlivnit výkon. Query Performance Insight je ve verzi public preview a je přístupná **inteligentní výkonu** část Azure Database pro MariaDB server stránky portálu.

## <a name="performance-recommendations"></a>Doporučení k výkonu

[Doporučení k výkonu](concepts-performance-recommendations.md) funkce identifikuje příležitosti pro zlepšení výkonu úloh. Verze public preview služby doporučení k výkonu poskytuje doporučení pro vytvoření nové indexy, které by mohly zvýšit výkon vašich úloh. K vytvoření doporučení indexu, tato funkce vezme v úvahu různé vlastnosti databáze, včetně jeho schématu a úloh podle Query Store. Po implementaci jakékoli doporučení výkon, by měl zákazníkům testování výkonu vyhodnotit její dopad těchto změn.

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak získat přístup a exportovat metriky pomocí webu Azure portal, rozhraní REST API nebo rozhraní příkazového řádku najdete v tématu [přehled metriky Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
  - Zobrazit [jak nastavit výstrahy](howto-alert-metric.md) pokyny k vytvoření upozornění na metriku.
