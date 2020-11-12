---
title: Monitorování – Azure Database for MariaDB
description: Tento článek popisuje metriky pro monitorování a upozorňování na Azure Database for MariaDB, včetně informací o PROCESORech, úložištích a připojeních.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: 5790094571563d3ca8576480a6296a73756fb6a6
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540412"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitorování v Azure Database for MariaDB
Sledování dat o vašich serverech vám pomůže při řešení potíží a optimalizaci pro vaše úlohy. Azure Database for MariaDB poskytuje různé metriky, které poskytují přehled o chování serveru.

## <a name="metrics"></a>Metriky
Všechny metriky Azure mají četnost jednosměrných minut a každá metrika poskytuje historii 30 dnů. Výstrahy můžete nakonfigurovat pro metriky. Mezi další úlohy patří nastavení automatizovaných akcí, provádění pokročilých analýz a archivace historie. Další informace najdete v tématu [Přehled metrik Azure](../azure-monitor/platform/data-platform.md).

Podrobné pokyny najdete v tématu [jak nastavit výstrahy](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Seznam metrik
Tyto metriky jsou k dispozici pro Azure Database for MariaDB:

|Metrika|Zobrazovaný název metriky|Jednotka|Popis|
|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Procento využití procesoru.|
|memory_percent|Procentuální hodnota paměti|Procento|Procentuální podíl používané paměti.|
|io_consumption_percent|V/v procenta|Procento|Procento využití v/v. (Neplatí pro servery úrovně Basic.)|
|storage_percent|Procento úložiště|Procento|Procento využitého limitu úložiště z maxima serveru.|
|storage_used|Využité úložiště|Bajty|Velikost využitého úložiště. Úložiště používané službou může zahrnovat soubory databáze, protokoly transakcí a protokoly serveru.|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Procento využití úložiště protokolu serveru z maximálního úložiště protokolu serveru serveru.|
|serverlog_storage_usage|Využité úložiště protokolu serveru|Bajty|Velikost používaného úložiště protokolu serveru.|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Bajty|Maximální úložiště protokolu serveru pro tento server.|
|storage_limit|Omezení úložiště|Bajty|Maximální úložiště pro tento server.|
|active_connections|Aktivní připojení|Počet|Počet aktivních připojení k serveru.|
|connections_failed|Neúspěšná připojení|Počet|Počet neúspěšných připojení k serveru|
|seconds_behind_master|Prodleva replikace v sekundách|Počet|Doba v sekundách, po kterou server repliky odchází na zdrojovém serveru. (Neplatí pro servery úrovně Basic.)|
|network_bytes_egress|Síťové výstupy|Bajty|Síť vyprší napříč aktivními připojeními.|
|network_bytes_ingress|Síťové vstupy|Bajty|Síť v rámci aktivních připojení.|
|backup_storage_used|Využité úložiště záloh|Bajty|Velikost využitého úložiště záloh. Tato metrika představuje součet úložiště spotřebovaného všemi úplnými zálohami databáze, rozdílové zálohy a zálohy protokolů, které jsou zachovány na základě nastaveného období uchovávání záloh pro server. Frekvence zálohování je spravována službou a je vysvětlena v [článku koncepty](concepts-backup.md). V případě geograficky redundantního úložiště je využití úložiště zálohování dvakrát místní redundantní úložiště.|

## <a name="server-logs"></a>Protokoly serveru

Můžete povolit pomalé protokolování dotazů na serveru. Tyto protokoly jsou dostupné taky prostřednictvím diagnostických protokolů Azure v Azure Monitor protokoly, Event Hubs a účet úložiště. Další informace o protokolování najdete na stránce [protokoly serveru](concepts-server-logs.md) .

## <a name="query-store"></a>Úložiště dotazů

[Úložiště dotazů](concepts-query-store.md) udržuje přehled o výkonu dotazů v čase včetně událostí čekání na dotaz za běhu a čekacích událostí. Funkce dál ukládá informace o výkonu modulu runtime dotazu ve schématu **MySQL** . Můžete řídit shromažďování a ukládání dat přes různé konfigurační ovladače.

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](concepts-query-performance-insight.md) funguje ve spojení s úložištěm dotazů, aby poskytovala vizualizace dostupné z Azure Portal. Tyto grafy umožňují identifikovat klíčové dotazy, které mají vliv na výkon. Query Performance Insight k dispozici v části **inteligentní výkon** na stránce portálu Azure Database for MariaDBho serveru.

## <a name="performance-recommendations"></a>Doporučení k výkonu

Funkce [doporučení k výkonu](concepts-performance-recommendations.md) identifikuje příležitosti pro zlepšení výkonu úloh. Doporučení k výkonu poskytují doporučení pro vytváření nových indexů, které mají potenciál pro zlepšení výkonu vašich úloh. Pro vytvoření doporučení indexu bere Tato funkce v úvahu různé charakteristiky databáze, včetně schématu a zatížení, jak je uvedeno v úložišti dotazů. Po implementaci doporučení výkonu by zákazníci měli testovat výkon, aby vyhodnotili dopad těchto změn.

## <a name="planned-maintenance-notification"></a>Oznámení o plánované údržbě

[Plánovaná oznámení údržby](./concepts-planned-maintenance-notification.md) vám umožní dostávat oznámení o nadcházející plánované údržbě Azure Database for MariaDB. Tato oznámení jsou integrovaná s plánovanou údržbou [Service Health](../service-health/overview.md) a umožňují zobrazit veškerou plánovanou údržbu vašich předplatných na jednom místě. Pomáhá také škálovat oznámení do správných cílových skupin pro různé skupiny prostředků, protože u různých zdrojů můžete mít zodpovědné různé kontakty. Zobrazí se oznámení o nadcházející údržbě 72 hodin před událostí.

Přečtěte si další informace o tom, jak nastavit oznámení v dokumentu [oznámení o plánované údržbě](./concepts-planned-maintenance-notification.md) .

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak přistupovat k metrikám a jak je exportovat pomocí Azure Portal, REST API nebo CLI, najdete v tématu [Přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Pokyny k vytvoření výstrahy na metrikě najdete v tématu [Nastavení výstrah](howto-alert-metric.md) .
- Přečtěte si další informace o [plánovaných oznámeních údržby](./concepts-planned-maintenance-notification.md) v Azure Database for MariaDB.

