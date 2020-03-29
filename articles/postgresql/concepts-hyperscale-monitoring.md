---
title: Monitorování a ladění – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Tento článek popisuje funkce monitorování a ladění v Azure Database for PostgreSQL – Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2e9fcd6f6292c1da76e725e90deda4547b3682d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975512"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Monitorování a ladění databáze Azure pro PostgreSQL – hyperškálování (Citus)

Monitorování dat o serverech vám pomůže řešit problémy a optimalizovat úlohu. Hyperscale (Citus) poskytuje různé možnosti monitorování poskytnout přehled o chování uzlů ve skupině serverů.

## <a name="metrics"></a>Metriky

Hyperscale (Citus) poskytuje metriky pro každý uzel ve skupině serverů. Metriky poskytují přehled o chování podpůrných prostředků. Každá metrika je vyzařována s frekvencí jedné minuty a má až 30 dní historie.

Kromě zobrazení grafů metrik můžete nakonfigurovat výstrahy. Pokyny krok za krokem najdete v tématu [Jak nastavit výstrahy](howto-hyperscale-alert-on-metric.md).  Mezi další úkoly patří nastavení automatizovaných akcí, spuštění pokročilé analýzy a historie archivace. Další informace najdete v tématu [Přehled metrik Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Seznam metrik

Tyto metriky jsou k dispozici pro uzly Hyperscale (Citus):

|Metrika|Zobrazovaný název metriky|Jednotka|Popis|
|---|---|---|---|
|active_connections|Aktivní připojení|Počet|Počet aktivních připojení k serveru.|
|cpu_percent|Procento procesoru|Procento|Procento procesoru v provozu.|
|Iops|IOPS|Počet|Podívejte se na [definici VOPS](../virtual-machines/linux/premium-storage-performance.md#iops) a [propustnost hyperškálování](concepts-hyperscale-configuration-options.md)|
|memory_percent|Procento paměti|Procento|Procento paměti, která se používá.|
|network_bytes_ingress|Síťové vstupy|Bajty|Síť v přes aktivní připojení.|
|network_bytes_egress|Síťové výstupy|Bajty|Síť vynažte přes aktivní připojení.|
|storage_percent|Procento úložiště|Procento|Procento úložiště použitého mimo maximální hodnotu serveru.|
|storage_used|Použité úložiště|Bajty|Množství úložiště v provozu. Úložiště používané službou může zahrnovat databázové soubory, protokoly transakcí a protokoly serveru.|

Azure poskytuje žádné agregační metriky pro cluster jako celek, ale metriky pro více uzlů lze umístit do stejného grafu.

## <a name="next-steps"></a>Další kroky

- Přečtěte [si, jak nastavit upozornění](howto-hyperscale-alert-on-metric.md) pro pokyny k vytvoření výstrahy na metriku.
