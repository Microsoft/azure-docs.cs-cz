---
title: Doporučené postupy úložiště dotazů v Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje osvědčené postupy pro úložiště dotazů v Azure Database for PostgreSQL – jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 51239f4cf49784dd47470e1272b90508eaf25e6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70764223"
---
# <a name="best-practices-for-query-store"></a>Doporučené postupy pro úložiště dotazů

**Platí pro:** Databáze Azure pro PostgreSQL – verze s jedním serverem 9.6, 10, 11

Tento článek popisuje osvědčené postupy pro použití Query Store v Azure Database for PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Nastavení optimálního režimu sběru dotazů
Uchvátíúložiště dotazů zachytí data, která jsou pro vás důležitá. 

|**pg_qs.query_capture_mode** | **Scénář**|
|---|---|
|_Všechny_  |Důkladně analyzujte úlohu z hlediska všech dotazů a jejich frekvencí provádění a dalších statistik. Identifikujte nové dotazy ve vaší pracovní zátěži. Zjistěte, zda se dotazy ad hoc používají k identifikaci příležitostí pro parametrizaci uživatele nebo automatické parametrizace. _Vše_ je dodáváno se zvýšenými náklady na spotřebu zdrojů. |
|_Top_  |Zaměřte svou pozornost na špičkové dotazy - ty, které vydávají klienti.
|_Žádné_ |Již jste zachytili sadu dotazů a časové okno, které chcete prozkoumat a chcete odstranit rozptýlení, které mohou představovat jiné dotazy. _Žádný z nich_ není vhodný pro testovací prostředí a prostředí pro značení na lavičce. _Žádný z nich_ by neměl být používán s opatrností, protože byste mohli přijít o možnost sledovat a optimalizovat důležité nové dotazy. Nelze obnovit data v těchto minulých časových oknech. |

Úložiště dotazů také obsahuje úložiště pro statistiky čekání. K dispozici je další dotaz režimu sběru, který řídí statistiky čekání: **pgms_wait_sampling.query_capture_mode** lze nastavit na _žádný_ nebo _všechny_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** nahrazuje **pgms_wait_sampling.query_capture_mode**. Pokud pg_qs.query_capture_mode _není žádný_, nastavení pgms_wait_sampling.query_capture_mode nemá žádný vliv. 


## <a name="keep-the-data-you-need"></a>Uchovávejte data, která potřebujete
Parametr **pg_qs.retention_period_in_days** určuje ve dnech dobu uchovávání dat pro úložiště dotazů. Starší data dotazů a statistik jsou odstraněna. Ve výchozím nastavení je úložiště dotazů nakonfigurováno tak, aby uchovávala data po dobu 7 dnů. Vyhněte se uchovávání historických dat, která neplánujete používat. Zvyšte hodnotu, pokud potřebujete uchovávat data déle.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Nastavení četnosti vzorkování statistik čekání 
Parametr **pgms_wait_sampling.history_period** určuje, jak často (v milisekundách) wait události jsou vzorkovány. Čím kratší je doba, tím častější je odběr vzorků. Další informace jsou načteny, ale to přichází s náklady na větší spotřebu zdrojů. Zvýšit toto období, pokud je server pod zatížením nebo nepotřebujete rozlišovací schopnost


## <a name="get-quick-insights-into-query-store"></a>Získejte rychlý přehled o Úložišti dotazů
Pomocí [přehledu výkonu dotazů](concepts-query-performance-insight.md) na webu Azure Portal můžete získat rychlý přehled o datech v Úložišti dotazů. Vizualizace povrch nejdelší spuštěné dotazy a nejdelší čekání události v průběhu času.

## <a name="next-steps"></a>Další kroky
- Zjistěte, jak získat nebo nastavit parametry pomocí [portálu Azure nebo](howto-configure-server-parameters-using-portal.md) [rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md).