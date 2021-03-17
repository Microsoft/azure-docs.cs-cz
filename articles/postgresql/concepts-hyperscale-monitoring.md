---
title: Monitorování a ladění – Citus (s měřítkem) – Azure Database for PostgreSQL
description: Tento článek popisuje funkce monitorování a ladění v Azure Database for PostgreSQL – Citus (škálování)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 544f871f62481243cda2409db24b0d067df28c32
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580576"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Monitorování a ladění Azure Database for PostgreSQL – Citus (škálování)

Sledování dat o vašich serverech vám pomůže při řešení potíží a optimalizaci pro vaše úlohy. Citus () nabízí různé možnosti monitorování, které poskytují přehled o chování uzlů ve skupině serverů.

## <a name="metrics"></a>Metriky

Citus () poskytuje metriky pro každý uzel ve skupině serverů. Metriky poskytují přehled o chování podpůrných prostředků. Každá metrika je vygenerována s jednou minutovou frekvencí a má až 30 dnů od historie.

Kromě zobrazení grafů metriky můžete nakonfigurovat výstrahy. Podrobné pokyny najdete v tématu [jak nastavit výstrahy](howto-hyperscale-alert-on-metric.md).  Mezi další úlohy patří nastavení automatizovaných akcí, spouštění pokročilých analýz a archivace historie. Další informace najdete v tématu [Přehled metrik Azure](../azure-monitor/data-platform.md).

### <a name="list-of-metrics"></a>Seznam metrik

Tyto metriky jsou k dispozici pro uzly Citus (s měřítkem):

|Metric|Zobrazovaný název metriky|Jednotka|Description|
|---|---|---|---|
|active_connections|Aktivní připojení|Počet|Počet aktivních připojení k serveru.|
|cpu_percent|Procento využití procesoru|Procento|Procento využití procesoru.|
|IOPS|IOPS|Počet|Podívejte se na propustnost [definice IOPS](../virtual-machines/premium-storage-performance.md#iops) a [propustnosti (Citus)](concepts-hyperscale-configuration-options.md) .|
|memory_percent|Procentuální hodnota paměti|Procento|Procentuální podíl používané paměti.|
|network_bytes_ingress|Síťové vstupy|Bajty|Síť v rámci aktivních připojení.|
|network_bytes_egress|Síťové výstupy|Bajty|Síť vyprší napříč aktivními připojeními.|
|storage_percent|Procento úložiště|Procento|Procento využitého limitu úložiště z maxima serveru.|
|storage_used|Využité úložiště|Bajty|Velikost využitého úložiště. Úložiště používané službou může zahrnovat soubory databáze, protokoly transakcí a protokoly serveru.|

Azure poskytuje žádné agregované metriky pro cluster jako celek, ale metriky pro více uzlů lze umístit do stejného grafu.

## <a name="next-steps"></a>Další kroky

- Pokyny k vytvoření výstrahy na metrikě najdete v tématu [Nastavení výstrah](howto-hyperscale-alert-on-metric.md) .