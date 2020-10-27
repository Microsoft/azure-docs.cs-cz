---
title: Monitorování – Azure Database for MySQL – flexibilní Server
description: Tento článek popisuje metriky pro monitorování a upozorňování Azure Database for MySQL flexibilního serveru, včetně informací o PROCESORech, úložištích a připojeních.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 5fe1d5a5a472b47abd364a89d1a65f1249c67c0d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538659"
---
# <a name="monitor-azure-database-for-mysql-flexible-servers-with-built-in-metrics"></a>Monitorování Azure Database for MySQL flexibilních serverů s integrovanými metrikami

> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Azure Database for MySQL flexibilní Server poskytuje monitorování serverů prostřednictvím Azure Monitor. Metriky jsou číselné hodnoty, které popisují určitý aspekt prostředků serveru v určitou dobu. Monitorování prostředků vašeho serveru vám pomůže vyřešit problémy a optimalizovat vaše zatížení tím, že vám umožní monitorovat, co je pro vás nejvhodnější. Monitorování správných metrik pomáhá udržet výkon, spolehlivost a dostupnost vašeho serveru a aplikací.

V tomto článku se dozvíte o různých metrikách dostupných pro váš flexibilní Server, které poskytují přehled o chování serveru.

## <a name="available-metrics"></a>Dostupné metriky

Azure Database for MySQL flexibilní Server poskytuje různé metriky pro pochopení, jak vaše zatížení provádí a na základě těchto dat můžete pochopit dopad na server a aplikaci. V případě flexibilního serveru můžete například monitorovat **procento využití procesoru hostitele** , **aktivní připojení** , **vstupně-výstupní operace** a **procento paměti hostitele** k určení, kdy dojde k dopadu na výkon. Odtud bude možná potřeba optimalizovat vaše úlohy, škálovat je vertikálně změnou výpočetních vrstev nebo horizontálním škálováním pomocí repliky pro čtení.

Všechny metriky Azure mají četnost jednosměrných minut a každá metrika poskytuje historii 30 dnů. Výstrahy můžete nakonfigurovat pro metriky. Podrobné pokyny najdete v tématu [jak nastavit výstrahy](./how-to-alert-on-metric.md). Mezi další úlohy patří nastavení automatizovaných akcí, provádění pokročilých analýz a archivace historie. Další informace najdete v tématu [Přehled metrik Azure](../../azure-monitor/platform/data-platform.md).

### <a name="list-of-metrics"></a>Seznam metrik
Tyto metriky jsou k dispozici pro Azure Database for MySQL:

|Zobrazovaný název metriky|Metrika|Jednotka|Popis|
|---|---|---|---|
|Procento využití procesoru hostitele|cpu_percent|Procento|Procento využití procesoru na serveru, včetně využití procesoru z úlohy zákazníka i procesů Azure MySQL|
|Síť hostitele v |network_bytes_ingress|Bajty|Příchozí síťový provoz na serveru, včetně provozu z databáze zákazníků i funkcí Azure MySQL, jako je replikace, monitorování, protokoly atd.|
|Síťové výstupy hostitele|network_bytes_egress|Bajty|Odchozí síťový provoz na serveru, včetně provozu z databáze zákazníků i funkcí Azure MySQL, jako je replikace, monitorování, protokoly atd.|
|Prodleva replikace|replication_lag|Sekundy|Čas od poslední opakované transakce. Tato metrika je k dispozici pouze pro servery repliky.|
|Aktivní připojení|active_connection|Počet|Počet aktivních připojení k serveru.|
|Využité úložiště záloh|backup_storage_used|Bajty|Velikost využitého úložiště záloh.|
|V/v procenta|io_consumption_percent|Procento|Procento využití v/v.|
|Procentuální hodnota paměti hostitele|memory_percent|Procento|Procento paměti používané na serveru, včetně využití paměti od úloh zákazníků i procesů Azure MySQL|
|Omezení úložiště|storage_limit|Bajty|Maximální úložiště pro tento server.|
|Procento úložiště|storage_percent|Procento|Procento využitého limitu úložiště z maxima serveru.|
|Využité úložiště|storage_used|Bajty|Velikost využitého úložiště. Úložiště používané službou může zahrnovat soubory databáze, protokoly transakcí a protokoly serveru.|
|Celkový počet připojení|total_connections|Počet|Celkový počet připojení k serveru|
|Přerušená připojení|aborted_connections|Počet|Počet neúspěšných pokusů o připojení k MySQL, například nezdařené připojení z důvodu chybných přihlašovacích údajů.|
|Dotazy|odešle|Počet|Počet dotazů za sekundu|

## <a name="next-steps"></a>Další kroky
- Pokyny k vytvoření výstrahy na metrikě najdete v tématu [Nastavení výstrah](./how-to-alert-on-metric.md) .
- Přečtěte si další informace o [ŠKÁLOVÁNÍ IOPS](./concepts/../concepts-compute-storage.md#iops) za účelem zvýšení výkonu.