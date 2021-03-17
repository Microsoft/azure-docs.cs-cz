---
title: Migrace vyhrazeného fondu SQL (dřív SQL DW) do Gen2
description: Pokyny pro migraci existujícího vyhrazeného fondu SQL (dříve SQL DW) do Gen2 a plánu migrace podle oblasti.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.subservice: sql-dw
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0ce07ff3ca5fbcc9776792129d3bfb4ef54efe7d
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120117"
---
# <a name="upgrade-your-dedicated-sql-pool-formerly-sql-dw-to-gen2"></a>Upgrade vyhrazeného fondu SQL (dříve SQL DW) na Gen2

Společnost Microsoft pomáhá snižovat náklady na úroveň vstupu na používání vyhrazeného fondu SQL (dříve SQL DW).  Pro vyhrazený fond SQL (dřív SQL DW) jsou teď dostupné nižší výpočetní úrovně, které umožňují zpracování náročných dotazů. Přečtěte si úplné oznámení o [podpoře výpočetních vrstev pro Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Nová nabídka je k dispozici v oblastech uvedených v následující tabulce. Pro podporované oblasti je možné upgradovat stávající Gen1 fond SQL (dřív SQL DW) na Gen2 prostřednictvím:

- **Proces automatického upgradu:** Automatické upgrady se nespustí hned po tom, co je služba dostupná v určité oblasti.  Když automatické upgrady začnou v konkrétní oblasti, během vybraného plánu údržby budou provedeny jednotlivé upgrady datového skladu.
- [**Samoobslužný upgrade na Gen2:**](#self-upgrade-to-gen2) Můžete řídit, kdy se má upgradovat, a to provedením samočinného upgradu na Gen2. Pokud vaše oblast ještě není podporovaná, můžete ji obnovit z bodu obnovení přímo do instance Gen2 v podporované oblasti.

## <a name="automated-schedule-and-region-availability-table"></a>Automatický plán a tabulka dostupnosti oblastí

Následující tabulka shrnuje podle oblasti v případě, že bude k dispozici nižší výpočetní vrstva Gen2 a když se spustí automatické upgrady. Data se mohou změnit. Vraťte se zpět a podívejte se, jestli je vaše oblast k dispozici.

\* indikuje, že konkrétní plán pro oblast není aktuálně k dispozici.

| **Oblast** | **Nižší Gen2 k dispozici** | **Začátek automatických upgradů** |
|:--- |:--- |:--- |
| Kanada – východ |1\. června 2020 |Od 1. července 2020 |
| Čína – východ |\* |\* |
| Čína – sever |\* |\* |
| Německo – střed |\* |\* |
| Německo – středozápad |K dispozici |1. května 2020 |
| Indie – západ |K dispozici |1. května 2020  |

## <a name="automatic-upgrade-process"></a>Proces automatického upgradu

V závislosti na grafu dostupnosti naplánujeme automatizované upgrady pro instance Gen1. Aby nedošlo k neočekávanému přerušení dostupnosti vyhrazeného fondu SQL (dříve SQL DW), automatizované upgrady se naplánují během plánu údržby. Možnost vytvoření nové instance Gen1 se zakáže v oblastech, které provádějí automatický upgrade na Gen2. Po dokončení automatických upgradů se Gen1 přestane používat. Další informace o plánech najdete v tématu [zobrazení plánu údržby.](maintenance-scheduling.md#view-a-maintenance-schedule)

Proces upgradu bude zahrnovat krátké odpojení (přibližně 5 minut), jako je restartování vyhrazeného fondu SQL (dříve SQL DW).  Po restartování vyhrazeného fondu SQL (dříve SQL DW) bude plně dostupný k použití. Nicméně může docházet ke snížení výkonu v době, kdy proces upgradu pokračuje v upgradu datových souborů na pozadí. Celková doba snížení výkonu se bude lišit v závislosti na velikosti datových souborů.

Proces upgradu datového souboru můžete také urychlit spuštěním [příkazu ALTER index Rebuild](sql-data-warehouse-tables-index.md) ve všech primárních tabulkách columnstore s použitím větších tříd objektů slo a prostředků po restartování.

> [!NOTE]
> Příkaz ALTER index Rebuild je offline operace a tabulky nebudou k dispozici, dokud se znovu nedokončí sestavení.

## <a name="self-upgrade-to-gen2"></a>Samoobslužný upgrade na Gen2

Vlastní upgrade můžete provést pomocí následujících kroků na stávajícím Gen1 fondu SQL (dřív SQL DW). Pokud se rozhodnete pro vlastní upgrade, je nutné jej dokončit před zahájením procesu automatického upgradu ve vaší oblasti. Tím zajistíte, že předejdete jakémukoli riziku automatických upgradů, které způsobují konflikt.

Při provádění samoobslužného upgradu jsou k dispozici dvě možnosti.  Můžete buď upgradovat svůj aktuální vyhrazený fond SQL (dřív SQL DW), nebo můžete obnovit vyhrazený fond SQL Gen1 (dříve SQL DW) do instance Gen2.

- [Upgrade na místě](upgrade-to-latest-generation.md) – Tato možnost provede upgrade stávajícího Gen1 fondu SQL (dříve SQL DW) na Gen2. Proces upgradu bude zahrnovat krátké odpojení (přibližně 5 minut), jako je restartování vyhrazeného fondu SQL (dříve SQL DW).  Po restartování bude plně k dispozici pro použití. Pokud při upgradu dojde k problémům, otevřete [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) a referenční informace "Gen2 upgrade" jako možnou příčinu.
- [Upgrade z bodu obnovení](sql-data-warehouse-restore-points.md) – vytvořte uživatelem definovaný bod obnovení ve vašem aktuálním vyhrazeném fondu SQL Gen1 (dříve SQL DW) a pak ho obnovte přímo do instance Gen2. Existující vyhrazený fond SQL Gen1 (dříve SQL DW) zůstane v platnosti. Po dokončení obnovení bude váš vyhrazený Gen2 fond SQL (dřív SQL DW) plně dostupný pro použití.  Po spuštění všech procesů testování a ověřování na obnovené instanci Gen2 je možné odstranit původní instanci Gen1.

  - Krok 1: Vytvořte z Azure Portal [bod obnovení definovaný uživatelem](sql-data-warehouse-restore-active-paused-dw.md).
  - Krok 2: při obnovení z bodu obnovení definovaného uživatelem nastavte úroveň výkonu na upřednostňovanou Gen2 úroveň.

Během toho, co proces upgradu upgraduje datové soubory na pozadí, může na určitou dobu dojít ke snížení výkonu. Celková doba snížení výkonu se bude lišit v závislosti na velikosti datových souborů.

Chcete-li urychlit proces migrace dat na pozadí, můžete ihned vynutit přesun dat spuštěním [příkazu ALTER index Rebuild](sql-data-warehouse-tables-index.md) na všech primárních tabulkách columnstore, které se dotazují na větší úrovni objektů slo a prostředků.

> [!NOTE]
> Příkaz ALTER index Rebuild je offline operace a tabulky nebudou k dispozici, dokud se znovu nedokončí sestavení.

Pokud narazíte na nějaké problémy s vaším vyhrazeným fondem SQL (dřív SQL DW), vytvořte [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) a jako možnou příčinu Reference "Gen2 upgrade".

Další informace najdete v tématu [upgrade na Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Nejčastější dotazy k migraci

**Otázka: má Gen2 náklady stejné jako Gen1?**

- Odpověď: Ano.

**Otázka: jak budou upgrady ovlivněny se skripty pro automatizaci?**

- Odpověď: jakýkoli skript Automation, který odkazuje na cíl na úrovni služby, by měl být změněn tak, aby odpovídal ekvivalentu Gen2.  [Tady](upgrade-to-latest-generation.md#upgrade-in-a-supported-region-using-the-azure-portal)najdete podrobnosti.

**Otázka: jak dlouho obvykle trvá samočinný upgrade?**

- Odpověď: můžete upgradovat na místě nebo upgradovat z bodu obnovení.

  - Upgrade na místě způsobí, že váš vyhrazený fond SQL (dřív SQL DW) bude chvíli pozastaven a bude pokračovat.  Proces na pozadí bude pokračovat, i když bude vyhrazený fond SQL (dřív SQL DW) online.  
  - Pokud provádíte upgrade prostřednictvím bodu obnovení, trvá to déle, protože upgrade projde procesem úplného obnovení.

**Otázka: jak dlouho bude automatický upgrade trvat?**

- Odpověď: skutečný výpadek upgradu je pouze čas potřebný k pozastavení a obnovení služby, což je 5 až 10 minut. Po krátkém výpadku proces na pozadí spustí migraci úložiště. Doba pro proces na pozadí závisí na velikosti vyhrazeného fondu SQL (dříve SQL DW).

**Otázka: kdy bude tento automatický upgrade probíhat?**

- Odpověď: během plánu údržby. Využití zvoleného plánu údržby způsobí minimalizaci přerušení na vaši firmu.

**Otázka: co mám dělat, když se můj proces upgradu na pozadí jeví jako zablokované?**

- Odpověď: aktivuje přeindexaci tabulek columnstore. Všimněte si, že při této operaci bude přeindexace tabulky v režimu offline.

**Otázka: Co když Gen2 nemá cíl na úrovni služby I v Gen1?**

- Odpověď: Pokud používáte úroveň DW600 nebo DW1200 na Gen1, doporučujeme použít DW500c nebo DW1000c, protože Gen2 poskytuje více paměti, prostředků a vyšší výkon než Gen1.

**Otázka: můžu zakázat geografickou zálohu?**

- Odpověď: Ne. Geografická záloha je podniková funkce pro zachování vyhrazené dostupnosti fondu SQL (dříve SQL DW) v případě, že oblast nebude k dispozici. Pokud máte další obavy, otevřete [žádost o podporu](sql-data-warehouse-get-started-create-support-ticket.md) .

**Otázka: existuje rozdíl v syntaxi T-SQL mezi Gen1 a Gen2?**

- Odpověď: v syntaxi jazyka T-SQL z Gen1 na Gen2 se nemění.

**Otázka: podporuje Gen2 okna údržby?**

- Odpověď: Ano.

**Otázka: bude možné vytvořit novou instanci Gen1 po upgradu mojí oblasti?**

- Odpověď: Ne. Po upgradu oblasti budou vytváření nových instancí Gen1 zakázané.

## <a name="next-steps"></a>Další kroky

- [Postup upgradu](upgrade-to-latest-generation.md)
- [Časové intervaly pro správu a údržbu](maintenance-scheduling.md)
- [Monitorování stavu prostředků](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
- [Kontrola před zahájením migrace](upgrade-to-latest-generation.md#before-you-begin)
- [Upgrade na místě a upgrade z bodu obnovení](upgrade-to-latest-generation.md)
- [Vytvoření bodu obnovení definovaného uživatelem](sql-data-warehouse-restore-points.md)
- [Informace o tom, jak obnovit Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Otevřete žádost o podporu Azure synapse Analytics.](./sql-data-warehouse-get-started-create-support-ticket.md)