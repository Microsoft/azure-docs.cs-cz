---
title: "Monitorování v Azure databáze pro databázi MySQL"
description: "Tento článek popisuje metriky pro monitorování a generování výstrah pro databázi Azure pro databázi MySQL, včetně statistik využití procesoru, úložiště a připojení."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: c3cba00077fd65239382d6fdd98e73a55f926b3b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitorování v Azure databáze pro databázi MySQL
Data o serverech monitorování pomáhá řešení a optimalizovat pro úlohy. Databáze pro databázi MySQL Azure poskytuje různé metriky, které poskytují přehled o chování prostředků podporuje MySQL server. 

## <a name="metrics"></a>Metriky
Všechny metriky Azure mají frekvencí jedné minuty a jednotlivé metriky poskytuje 30 dní od historie. Nakonfigurujte upozornění na metriky. Podrobné pokyny najdete v tématu [jak nastavit výstrahy](howto-alert-on-metric.md). Další úlohy zahrnují nastavení automatizované akce, provádění pokročilou analýzu a archivaci historie. Další informace najdete v tématu [přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Seznam metriky
Pro databázi Azure pro databázi MySQL k dispozici jsou tyto metriky:

|Metrika|Metriky zobrazovaný název|Jednotka|Popis|
|---|---|---|---|---|
|cpu_percent|Procento využití procesoru|Procento|Procento procesoru používán.|
|memory_percent|Paměť v procentech|Procento|Procento paměti.|
|io_consumption_percent|Vstupně-výstupní operace v procentech|Procento|Procento vstupů/výstupů používán.|
|storage_percent|Procento úložiště|Procento|Je maximální procento využití úložiště mimo server.|
|storage_used|Využité úložiště|B|Velikost úložiště, které používá. Úložiště používá služba zahrnuje soubory databáze, transakční protokoly a protokoly serveru.|
|storage_limit|Limit úložiště|B|Maximální velikost úložiště pro tento server.|
|active_connections|Celkový počet aktivních připojení|Počet|Počet aktivních připojení k serveru.|
|connections_failed|Celkový počet selhání připojení|Počet|Počet selhání připojení k serveru.|


## <a name="next-steps"></a>Další postup
- V tématu [jak nastavit výstrahy](howto-alert-on-metric.md) pokyny k vytváření výstrahu na metriky.
- Další informace o tom, jak přístup a export metriky pomocí portálu Azure, rozhraní REST API nebo rozhraní příkazového řádku najdete v tématu [přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
