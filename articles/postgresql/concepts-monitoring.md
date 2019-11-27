---
title: Monitorování a ladění v Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje funkce monitorování a ladění v Azure Database for PostgreSQL-jednom serveru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 799b2b6ee6074472a951e69dec7e9a87056373b4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384027"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Monitorování a ladění Azure Database for PostgreSQL – jeden server
Sledování dat o vašich serverech vám pomůže při řešení potíží a optimalizaci pro vaše úlohy. Azure Database for PostgreSQL poskytuje různé možnosti monitorování, které poskytují přehled o chování serveru.

## <a name="metrics"></a>Metriky
Azure Database for PostgreSQL poskytuje různé metriky, které poskytují přehled o chování prostředků, které podporují server PostgreSQL. Každá metrika je vygenerována s jednou minutovou frekvencí a má až 30 dnů od historie. Výstrahy můžete nakonfigurovat pro metriky. Podrobné pokyny najdete v tématu [jak nastavit výstrahy](howto-alert-on-metric.md). Mezi další úlohy patří nastavení automatizovaných akcí, provádění pokročilých analýz a archivace historie. Další informace najdete v tématu [Přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Seznam metrik
Tyto metriky jsou k dispozici pro Azure Database for PostgreSQL:

|Metrika|Metriky zobrazovaný název|Jednotka|Popis|
|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Procento využití procesoru.|
|memory_percent|Procentuální hodnota paměti|Procento|Procentuální podíl používané paměti.|
|io_consumption_percent|V/v procenta|Procento|Procento využití v/v.|
|storage_percent|Procento úložiště|Procento|Procento využitého limitu úložiště z maxima serveru.|
|storage_used|Využité úložiště|B|Velikost využitého úložiště. Úložiště používané službou může zahrnovat soubory databáze, protokoly transakcí a protokoly serveru.|
|storage_limit|Omezení úložiště|B|Maximální úložiště pro tento server.|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Procento využití úložiště protokolu serveru z maximálního úložiště protokolu serveru serveru.|
|serverlog_storage_usage|Využité úložiště protokolu serveru|B|Velikost používaného úložiště protokolu serveru.|
|serverlog_storage_limit|Limit úložiště protokolu serveru|B|Maximální úložiště protokolu serveru pro tento server.|
|active_connections|Aktivní připojení|Počet|Počet aktivních připojení k serveru.|
|connections_failed|Neúspěšná připojení|Počet|Počet neúspěšných připojení k serveru|
|network_bytes_egress|Síťové výstupy|B|Síť vyprší napříč aktivními připojeními.|
|network_bytes_ingress|Síťové vstupy|B|Síť v rámci aktivních připojení.|
|backup_storage_used|Využité úložiště záloh|B|Velikost využitého úložiště záloh.|
|pg_replica_log_delay_in_bytes|Maximální prodleva napříč replikami|B|Prodleva v bajtech mezi hlavní a nejvyšším zpožděním repliky. Tato metrika je k dispozici pouze na hlavním serveru.|
|pg_replica_log_delay_in_seconds|Prodleva repliky|Sekundy|Čas od poslední opakované transakce. Tato metrika je k dispozici pouze pro servery repliky.|

## <a name="server-logs"></a>Protokoly serveru
Můžete povolit protokolování na serveru. Tyto protokoly jsou dostupné taky prostřednictvím diagnostických protokolů Azure v [Azure monitor protokoly](../azure-monitor/log-query/log-query-overview.md), Event Hubs a účet úložiště. Další informace o protokolování najdete na stránce [protokoly serveru](concepts-server-logs.md) .

## <a name="query-store"></a>Úložiště dotazů
[Úložiště dotazů](concepts-query-store.md) udržuje přehled o výkonu dotazů v čase včetně událostí čekání na dotaz za běhu a čekacích událostí. Funkce uchovává informace o výkonu modulu runtime dotazu v systémové databázi s názvem **azure_sys** v rámci schématu query_store. Můžete řídit shromažďování a ukládání dat přes různé konfigurační ovladače.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) funguje ve spojení s úložištěm dotazů, aby poskytovala vizualizace dostupné z Azure Portal. Tyto grafy umožňují identifikovat klíčové dotazy, které mají vliv na výkon. Dotaz na výkon, který je přístupný z části **Podpora a řešení potíží** na stránce portálu Azure Database for PostgreSQL serveru.

## <a name="performance-recommendations"></a>Doporučení k výkonu
Funkce [doporučení k výkonu](concepts-performance-recommendations.md) identifikuje příležitosti pro zlepšení výkonu úloh. Doporučení k výkonu poskytují doporučení pro vytváření nových indexů, které mají potenciál pro zlepšení výkonu vašich úloh. Pro vytvoření doporučení indexu bere Tato funkce v úvahu různé charakteristiky databáze, včetně schématu a zatížení, jak je uvedeno v úložišti dotazů. Po implementaci doporučení výkonu by zákazníci měli testovat výkon, aby vyhodnotili dopad těchto změn. 

## <a name="service-health"></a>Stav služeb
[Služba Azure Service Health](../service-health/overview.md) poskytuje zobrazení všech oznámení o stavu služby v rámci vašeho předplatného. Můžete nastavit výstrahy Service Health, které vás informují prostřednictvím preferovaných komunikačních kanálů, když dojde k problémům nebo změnám, které mohou ovlivnit služby a oblasti Azure, které používáte.

Pomocí typu události **plánované údržby** můžete zobrazit naplánované události údržby pro Azure Database for PostgreSQL s jedním serverem. Pokud se chcete dozvědět, jak vytvořit výstrahy týkající se **stavu služby**, přečtěte si článek [vytvoření výstrah protokolu aktivit v oznámeních o službě](../service-health/alerts-activity-log-service-notifications.md) .

> [!IMPORTANT]
> Oznámení o plánované údržbě jsou k dispozici ve verzi Preview pro východní USA a Velká Británie – jih.

## <a name="next-steps"></a>Další kroky
- Pokyny k vytvoření výstrahy na metrikě najdete v tématu [Nastavení výstrah](howto-alert-on-metric.md) .
- Další informace o tom, jak přistupovat k metrikám a jak je exportovat pomocí Azure Portal, REST API nebo CLI, najdete v tématu [Přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- V našem blogu najdete [osvědčené postupy pro monitorování vašeho serveru](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
