---
title: Monitorování v Azure Database for MySQL
description: Tento článek popisuje metriky pro monitorování a upozorňování pro službu Azure Database for MySQL, včetně statistiky CPU, úložiště a připojení.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 10/03/2018
ms.openlocfilehash: c2248f185c5c1c5dd8c4e1d4a24e6145a82ec5cb
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091551"
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitorování v Azure Database for MySQL
Data o vašich serverech monitorování vám pomůže řešit a optimalizovat pro vaši úlohu. Azure Database for MySQL poskytuje různé metriky, které poskytují přehled o chování vašeho serveru.

## <a name="metrics"></a>Metriky
Všechny metriky Azure máte minutových frekvence a jednotlivé metriky poskytuje 30 dní historie. Můžete nakonfigurovat výstrahy o metrikách. Pokyny krok za krokem, najdete v článku [jak nastavit výstrahy](howto-alert-on-metric.md). Další úkoly zahrnují nastavením automatizovaných akcí, provádění pokročilých analýz a pro archivaci historie. Další informace najdete v tématu [přehled metriky Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Seznam metrik
Tyto metriky jsou k dispozici pro službu Azure Database for MySQL:

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
|connections_failed|Neúspěšná připojení|Počet|Počet selhání připojení k serveru.|
|seconds_behind_master|Zpoždění replikace v řádu sekund|Počet|Počet sekund, po které server repliky zaostává s hlavním serverem.|
|network_bytes_egress|Síťové výstupy|B|Odchozí síťový provoz mezi aktivních připojení.|
|network_bytes_ingress|Síťové vstupy|B|Sítě v rámci aktivních připojení.|

## <a name="server-logs"></a>Protokoly serveru
Můžete povolit na vašem serveru protokolování pomalých dotazů. Tyto protokoly jsou také k dispozici prostřednictvím Azure diagnostické protokoly v OMS Log Analytics, Event Hubs a účet úložiště. Další informace o protokolování, najdete [protokoly serveru](concepts-server-logs.md) stránky.

## <a name="next-steps"></a>Další postup
- Zobrazit [jak nastavit výstrahy](howto-alert-on-metric.md) pokyny k vytvoření upozornění na metriku.
- Další informace o tom, jak získat přístup a exportovat metriky pomocí webu Azure portal, rozhraní REST API nebo rozhraní příkazového řádku najdete v tématu [přehled metriky Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
