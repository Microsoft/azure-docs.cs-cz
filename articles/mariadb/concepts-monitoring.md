---
title: Monitorování v Azure Database pro MariaDB
description: Tento článek popisuje metriky pro monitorování a výstrah pro službu Azure Database pro MariaDB, včetně statistiky CPU, úložiště a připojení.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/10/2018
ms.openlocfilehash: 2ad641ae054f9542ec1ef42f5ebbe724ba4ecf87
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354021"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Monitorování v Azure Database pro MariaDB
Data o vašich serverech monitorování vám pomůže řešit a optimalizovat pro vaši úlohu. Azure Database pro MariaDB poskytuje různé metriky, které poskytují přehled o chování vašeho serveru.

## <a name="metrics"></a>Metriky
Všechny metriky Azure máte minutových frekvence a jednotlivé metriky poskytuje 30 dní historie. Můžete nakonfigurovat výstrahy o metrikách. Další úkoly zahrnují nastavením automatizovaných akcí, provádění pokročilých analýz a pro archivaci historie. Další informace najdete v článku [přehled o metriky Azure] (.. /Monitoring-and-Diagnostics/Monitoring-Overview-Metrics.MD).

Pokyny krok za krokem, najdete v článku [jak nastavit výstrahy](howto-alert-metric.md).

### <a name="list-of-metrics"></a>Seznam metrik
Tyto metriky jsou k dispozici pro službu Azure Database pro MariaDB:

|Metrika|Metriky zobrazovaný název|Jednotka|Popis|
|---|---|---|---|---|
|cpu_percent|Procento CPU|Procento|Procento procesoru používá.|
|memory_percent|Paměť v procentech|Procento|Procento paměti používá.|
|io_consumption_percent|V/v úrovně procent|Procento|Procentuální podíl vstupně-výstupní operace používá.|
|storage_percent|Procento úložiště|Procento|Je maximální procento využité místo mimo server.|
|storage_used|Využité úložiště|B|Množství využívaného úložiště. Úložiště využívané ve službě mohou být soubory databáze, protokoly transakcí a v protokolech serveru.|
|serverlog_storage_percent|Procento úložiště protokolů serveru|Procento|Procento úložiště protokolů serveru používat úložiště protokolů maximální server na server.|
|serverlog_storage_usage|Využité úložiště protokolů serveru|B|Množství využívaného úložiště protokolu serveru.|
|serverlog_storage_limit|Limit úložiště protokolů serveru|B|Maximální serverové úložiště protokolu pro tento server.|
|storage_limit|Limit úložiště.|B|Maximální velikost úložiště pro tento server.|
|active_connections|Aktivní připojení|Počet|Počet aktivních připojení k serveru.|
|connections_failed|Chyba připojení|Počet|Počet selhání připojení k serveru.|
|network_bytes_egress|Síťové výstupy|B|Odchozí síťový provoz mezi aktivních připojení.|
|network_bytes_ingress|Síťové vstupy|B|Sítě v rámci aktivních připojení.|

## <a name="server-logs"></a>Protokoly serveru
Můžete povolit na vašem serveru protokolování pomalých dotazů. Další informace o protokolování, najdete [protokoly serveru](concepts-server-logs.md) stránky.

## <a name="next-steps"></a>Další postup
- Další informace o tom, jak získat přístup a exportovat metriky pomocí webu Azure portal, rozhraní REST API nebo rozhraní příkazového řádku najdete v tématu [přehled metriky Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
 - Zobrazit [jak nastavit výstrahy](howto-alert-metric.md) pokyny k vytvoření upozornění na metriku.
