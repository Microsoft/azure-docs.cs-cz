---
title: Monitorování a metriky – Azure Database for PostgreSQL – flexibilní Server
description: Tento článek popisuje funkce monitorování a metriky v Azure Database for PostgreSQL-flexibilním serveru.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1519e0b5cef6055cf8d8b0aded0d8ad323d548a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707842"
---
# <a name="monitor-metrics-on-azure-database-for-postgresql---flexible-server"></a>Monitorování metrik na Azure Database for PostgreSQL – flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Sledování dat o vašich serverech vám pomůže při řešení potíží a optimalizaci pro vaše úlohy. Azure Database for PostgreSQL poskytuje různé možnosti monitorování, které poskytují přehled o chování serveru.

## <a name="metrics"></a>Metriky
Azure Database for PostgreSQL poskytuje různé metriky, které poskytují přehled o chování prostředků, které podporují server PostgreSQL. Každá metrika je vygenerována s jednou minutovou frekvencí a má až [93 dní v historii](../../azure-monitor/platform/data-platform-metrics.md#retention-of-metrics). Výstrahy můžete nakonfigurovat pro metriky. Mezi další možnosti patří nastavení automatizovaných akcí, provádění pokročilých analýz a archivace historie. Další informace najdete v tématu [Přehled metrik Azure](../../azure-monitor/platform/data-platform-metrics.md).

### <a name="list-of-metrics"></a>Seznam metrik
Pro PostgreSQL flexibilní Server jsou k dispozici následující metriky:


|Metrika|Zobrazovaný název metriky|Jednotka|Description|
|---|---|---|---|
| active_connections | Aktivní připojení | Počet | Počet připojení k serveru | 
| backup_storage_used | Využité úložiště záloh | Bajty | Velikost využitého úložiště záloh Tato metrika představuje součet úložiště spotřebovaného všemi úplnými zálohami databáze, rozdílové zálohy a zálohy protokolů, které jsou zachovány na základě nastaveného období uchovávání záloh pro server. Frekvence zálohování je spravována službou. V případě geograficky redundantního úložiště je využití úložiště zálohování dvakrát místní redundantní úložiště. |
| connections_failed | Neúspěšná připojení | Počet | Připojení se nezdařila. |
| connections_succeeded | Úspěšná připojení | Počet | Úspěšná připojení. |
| cpu_credits_consumed | Spotřebované kredity procesoru | Počet | Počet kreditů používaných flexibilním serverem. Platí pro vrstvu s shluky. |
| cpu_credits_remaining | Zbývající kredity procesoru | Počet | Počet kreditů dostupných pro shlukování. Platí pro vrstvu s shluky. |
| cpu_percent | Procento využití procesoru | Procento | Procento využití procesoru. | 
| disk_queue_depth | Hloubka fronty disku | Počet | Počet nezpracovaných vstupně-výstupních operací na datový disk. |
| IOPS | IOPS | Počet | Počet vstupně-výstupních operací na disk za sekundu. |
| maximum_used_transactionIDs | Maximální počet použitých ID transakcí | Počet | Je použito maximální ID transakce. |
| memory_percent | Procentuální hodnota paměti | Procento | Procento využité paměti |
| network_bytes_egress | Síťové výstupy | Bajty | Množství odchozího síťového provozu. |
| network_bytes_ingress | Síťové vstupy | Bajty | Množství příchozího síťového provozu. |
| read_iops | Čtení IOPS | Počet | Počet vstupně-výstupních operací čtení datových disků za sekundu |
| read_throughput | Propustnost čtení | Bajty | Přečtených bajtů za sekundu z disku. |
| storage_free | Volné úložiště | Bajty | Velikost dostupného prostoru úložiště. |
| storage_percent | Procento úložiště | Procento | Procento využitého prostoru úložiště Úložiště používané službou může zahrnovat soubory databáze, protokoly transakcí a protokoly serveru.|
| storage_used | Využité úložiště | Bajty | Procento využitého prostoru úložiště Úložiště používané službou může zahrnovat soubory databáze, protokoly transakcí a protokoly serveru. |
| txlogs_storage_used | Využité úložiště protokolu transakcí | Bajty | Velikost úložného prostoru, který používají transakční protokoly. | 
| write_throughput | Propustnost zápisu | Bajty | Bajtů zapsaných za sekundu na disk. |
| write_iops | Zápis IOPS | Počet | Počet vstupně-výstupních operací zápisu datových disků za sekundu |

## <a name="server-logs"></a>Protokoly serveru
Azure Database for PostgreSQL umožňuje konfiguraci a přístup ke standardním protokolům Postgres. Další informace o protokolech najdete v [dokumentu koncepty protokolování](concepts-logging.md).
