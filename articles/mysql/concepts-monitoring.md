---
title: Monitorování – Azure Database for MySQL
description: Tento článek popisuje metriky pro monitorování a upozorňování na Azure Database for MySQL, včetně informací o PROCESORech, úložištích a připojeních.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: ec99db9406c5c83cdcbf322c45cea40c6643ee8f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770879"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitorování v Azure Database for MySQL
Sledování dat o vašich serverech vám pomůže při řešení potíží a optimalizaci pro vaše úlohy. Azure Database for MySQL poskytuje různé metriky, které poskytují přehled o chování serveru.

## <a name="metrics"></a>Metriky
Všechny metriky Azure mají četnost jednosměrných minut a každá metrika poskytuje historii 30 dnů. Výstrahy můžete nakonfigurovat pro metriky. Podrobné pokyny najdete v tématu [jak nastavit výstrahy](howto-alert-on-metric.md). Mezi další úlohy patří nastavení automatizovaných akcí, provádění pokročilých analýz a archivace historie. Další informace najdete v tématu [Přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Seznam metrik
Tyto metriky jsou k dispozici pro Azure Database for MySQL:

|Metrika|Zobrazovaný název metriky|Jednotka|Popis|
|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Procento využití procesoru.|
|memory_percent|Procentuální hodnota paměti|Procento|Procentuální podíl používané paměti.|
|io_consumption_percent|V/v procenta|Procento|Procento využití v/v.|
|storage_percent|Procento úložiště|Procento|Procento využitého limitu úložiště z maxima serveru.|
|storage_used|Využité úložiště|Psaný|Velikost využitého úložiště. Úložiště používané službou může zahrnovat soubory databáze, protokoly transakcí a protokoly serveru.|
|serverlog_storage_percent|Procentuální hodnota úložiště protokolu serveru|Procento|Procento využití úložiště protokolu serveru z maximálního úložiště protokolu serveru serveru.|
|serverlog_storage_usage|Využité úložiště protokolu serveru|Psaný|Velikost používaného úložiště protokolu serveru.|
|serverlog_storage_limit|Limit úložiště protokolu serveru|Psaný|Maximální úložiště protokolu serveru pro tento server.|
|storage_limit|Omezení úložiště|Psaný|Maximální úložiště pro tento server.|
|active_connections|Aktivní připojení|Počet|Počet aktivních připojení k serveru.|
|connections_failed|Neúspěšná připojení|Počet|Počet neúspěšných připojení k serveru|
|seconds_behind_master|Prodleva replikace v sekundách|Počet|Doba v sekundách, po kterou server repliky zaostává na hlavním serveru.|
|network_bytes_egress|Síťové výstupy|Psaný|Síť vyprší napříč aktivními připojeními.|
|network_bytes_ingress|Síťové vstupy|Psaný|Síť v rámci aktivních připojení.|
|backup_storage_used|Využité úložiště záloh|Psaný|Velikost využitého úložiště záloh.|

## <a name="server-logs"></a>Protokoly serveru
Na serveru můžete povolit protokolování pomalých dotazů a audit. Tyto protokoly jsou dostupné taky prostřednictvím diagnostických protokolů Azure v Azure Monitor protokoly, Event Hubs a účet úložiště. Další informace o protokolování najdete v článcích [protokoly auditu](concepts-audit-logs.md) a [pomalé dotazy v protokolech](concepts-server-logs.md) .

## <a name="query-store"></a>Úložiště dotazů
[Úložiště dotazů](concepts-query-store.md) je funkce, která průběžně sleduje výkon dotazů v čase včetně událostí čekání na dotaz za běhu a čekacích událostí. Funkce dál ukládá informace o výkonu modulu runtime dotazu ve schématu **MySQL** . Můžete řídit shromažďování a ukládání dat přes různé konfigurační ovladače.

## <a name="query-performance-insight"></a>Přehled o výkonu dotazů
[Query Performance Insight](concepts-query-performance-insight.md) funguje ve spojení s úložištěm dotazů, aby poskytovala vizualizace dostupné z Azure Portal. Tyto grafy umožňují identifikovat klíčové dotazy, které mají vliv na výkon. Query Performance Insight k dispozici v části **inteligentní výkon** na stránce portálu Azure Database for MySQLho serveru.

## <a name="performance-recommendations"></a>Doporučení k výkonu
Funkce [doporučení k výkonu](concepts-performance-recommendations.md) identifikuje příležitosti pro zlepšení výkonu úloh. Doporučení k výkonu poskytují doporučení pro vytváření nových indexů, které mají potenciál pro zlepšení výkonu vašich úloh. Pro vytvoření doporučení indexu bere Tato funkce v úvahu různé charakteristiky databáze, včetně schématu a zatížení, jak je uvedeno v úložišti dotazů. Po implementaci doporučení výkonu by zákazníci měli testovat výkon, aby vyhodnotili dopad těchto změn.

## <a name="service-health"></a>Stav služby
[Služba Azure Service Health](../service-health/overview.md) poskytuje zobrazení všech oznámení o stavu služby v rámci vašeho předplatného. Můžete nastavit výstrahy Service Health, které vás informují prostřednictvím preferovaných komunikačních kanálů, když dojde k problémům nebo změnám, které mohou ovlivnit služby a oblasti Azure, které používáte.

Pomocí typu události **plánované údržby** můžete zobrazit naplánované události údržby pro Azure Database for MySQL. Pokud se chcete dozvědět, jak vytvořit výstrahy týkající se **stavu služby**, přečtěte si článek [vytvoření výstrah protokolu aktivit v oznámeních o službě](../service-health/alerts-activity-log-service-notifications.md) .

> [!IMPORTANT]
> Oznámení o plánované údržbě jsou k dispozici ve verzi Preview pro východní USA a Velká Británie – jih.

## <a name="next-steps"></a>Další kroky
- Pokyny k vytvoření výstrahy na metrikě najdete v tématu [Nastavení výstrah](howto-alert-on-metric.md) .
- Další informace o tom, jak přistupovat k metrikám a jak je exportovat pomocí Azure Portal, REST API nebo CLI, najdete v tématu [Přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- V našem blogu najdete [osvědčené postupy pro monitorování vašeho serveru](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-mysql-monitoring/).
